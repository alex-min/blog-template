---
title: A hook for handling very large lists with Phoenix Live View
description: 
author: alex-min
image: /images/phoenix-scroll.png
tags:
  - elixir
  - phoenix
---

I'm currently building a web version of my personal finance application, for that, I've chosen to use [Phoenix Live View](https://github.com/phoenixframework/phoenix_live_view), it's the perfect tech for my use case.

In this finance web application, you have a lot of very large lists to display like the list of transactions for example.
Each wallet can have between 2k and 20k transactions and it's unreasonable to load everything at once. 
Loading everything slows down the initial page load with those large queries and then it also slows down the browser.

The solution is to build a list of transactions which is streamed as you go like on Fastmail or Gmail. 
The user has the impression that they can scroll down and that the full list is already there but in reality, everything is being streamed in chunks and loads as they scroll.

I've use [Fattable.js](https://github.com/fulmicoton/fattable/) for that purpose, it's a small library which is making handling those large tables easier.


First, here is a preview of the end result:

<video src="/videos/infinitescroll.mp4" muted autoplay loop controls>
  <p>Video preview of the infinite scrolling where I scroll anywhere and you can see the list loading as I scroll dynamically</p>
</video>

Now let's dive into the code!

## The Live View

Here is first what the live view looks like, it's pretty straightforward and does not change much from a normal list.

```@records``` here only contains the first page of records (so a maximum of 40 records in my case), this is used to display data when loading the page so that the user can interact directly with the list without waiting for the first page to load. 

```eex
<div class="bg-white flex-1 h-100 lg:block x-space-y-2 overflow-auto relative h-full"
         id="scroll-<%= @wallet.id %>"
         phx-hook="InfiniteScroll"
         data-count="<%= @records_count %>"
         data-page-size="<%= @records_per_page %>"
         data-row-height="<%= @row_height %>"
         data-loading-block-id="loading-block">
        <%= for record <- @records do %>
          <%= render(MavioWeb.LayoutView, "record.html",
               conn: @socket,
               record: record,
               locale: @locale,
               timezone: @timezone,
               wallet: @wallet,
               row_height: @row_height
             ) %>
        <% end %>
    </div>

    <!-- 
        here is the loading block, this is the block which is used when the data is still loading.
        This is the "pulse" animation you can see on the video when scrolling.
     -->
    <div id="loading-block" class="hidden" aria-hidden="true">
      <div class="animate-pulse rounded p-5 bg-white" style="height: <%= @row_height %>px">
           <div class="flex space-x-3">

              <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" class="w-8 mr-2 icon-receipt"><path class="primary" d="M9 18.41l-2.3 2.3a1 1 0 0 1-1.4 0l-2-2A1 1 0 0 1 3 18V5c0-1.1.9-2 2-2h14a2 2 0 0 1 2 2v13a1 1 0 0 1-.3.7l-2 2a1 1 0 0 1-1.4 0L15 18.42l-2.3 2.3a1 1 0 0 1-1.4 0L9 18.4z"></path><path class="secondary" d="M7 7h10a1 1 0 0 1 0 2H7a1 1 0 1 1 0-2zm0 4h10a1 1 0 0 1 0 2H7a1 1 0 0 1 0-2z"></path>
              </svg>
              <div class="flex flex-col w-56">
                <div class="h-4 bg-blue-100 rounded mb-1"></div>
                <div class="h-4 bg-blue-100 rounded w-5/6"></div>
              </div>
          </div>
       </div>
      </div>
    </div>
```

As you can see, I've kept the live view as simple as possible, I've created a bunch of additional attributes that are used in the javascript:

 - ```data-count``` contains the total number of items, this is helpfull for *fattable.js* to calculate the total height of the div.
 - ```data-page-size``` is the chunk size which we're loading the items with, I'm setting it to 40 for my use case but it depends of what you are building, you might want a lower page size if retreiving the data is very expensive. 
 - ```data-row-height``` is the row height in pixels. Unlike a traditional list, with this method, we need to have every row at the exact same height.
 - ```data-loading-block-id``` is an id linking to the HTML which is used when loading the data, it's what's using the "pulse" animation in the video.

## The Infinite Scroll Hook


The main chunk of the code is in the new [InfiniteScroll Hook](https://gist.github.com/alex-min/7c3f008f1614fc3448717b32e122bad7), it handles the link between the fattable.js library and the Live View. I've commented every part to make it easier to follow:


```javascript
require('fattable/fattable.js');


/* 
 * This variable is used to keep the scroll position when the live view navigation changes.
 * This is useful for modals.
*/
let keepScroll = {};

export default {
    mounted() {
        /*
         * Here we're loading the first page which is already rendered in the HTML 
         */
        var firstBlock = [];
        for (let i = 0; i < this.el.children.length; i++) {
            firstBlock.push(this.el.children[i].outerHTML);
        }

        /* 
         *  All the attributes are mapped from what we sent in the live view 
         */
        const numberOfRows = parseInt(this.el.getAttribute('data-count'));
        const pageSize = parseInt(this.el.getAttribute('data-page-size'));
        const rowHeight = parseInt(this.el.getAttribute('data-row-height'));
        const loadingBlock = document.getElementById(this.el.getAttribute('data-loading-block-id')).innerHTML;

        let painter = new fattable.Painter();

        painter.fillCell = (cellDiv, data) => cellDiv.innerHTML = data.content;   // filling the data when it's received
        painter.fillCellPending = (cellDiv) => cellDiv.innerHTML = loadingBlock;  // the loading block when there's no data

        let tableModel = new fattable.PagedAsyncTableModel();

        tableModel.cellPageName = (i) => (i / pageSize) | 0;
        tableModel.hasColumn = () => true;
        tableModel.columnHeaders = ["Transaction"]; 
        tableModel.getHeader = (i, cb) => cb(tableModel.columnHeaders[i]);


        /*
         * This is where we fetch the current page to render the header
         * We're using Live View events instead of HTTP requests since the socket is already opened
         * If it's the first page, we render the elements we gathered already from the server side rendering, no need to fetch them again
         */
        tableModel.fetchCellPage = (offset, cb) => {
            if (offset == 0) {
                cb(function (i) {
                    return {
                        rowId: i,
                        content: firstBlock[i]
                    }
                });
            } else {
                this.pushEventTo(`#${this.el.id}`, "load-table", { offset: offset });
                this.handleEvent(`${this.el.id}-receive-table-${offset}`, payload => {
                    cb(function (i) {
                        return {
                            rowId: i,
                            content: payload.html[i - payload.offset * pageSize]
                        }
                    });
                });
            }
        }

        /* 
         * This is used to resize the list if the window size changes 
         */
        let getColumnWidth = () => {
            return [this.el.getClientRects()[0].width];
        }

        this.table = fattable({
            container: `#${this.el.id}`,
            model: tableModel,
            nbRows: numberOfRows,
            rowHeight,
            headerHeight: 0,
            painter,
            columnWidths: getColumnWidth()
        });

        window.addEventListener('resize', () => {
            this.table.columnWidths = getColumnWidth()
        });

        /*
         * We set the scroll to where it was before, this object isn't stored in the localStorage,
         * this is by design, we want the list to be scrolled top when the user actually reloads the page, same as an actual list.
         */
        if (keepScroll[this.el.id]) {
            this.table.scroll.setScrollXY(0, keepScroll[this.el.id]);
        }
    },

    /*
     * This will be called before the table is destroyed to save the scroll position, this is very useful for modals.
     */
    beforeDestroy() {
        keepScroll[this.el.id] = this.table.scroll.scrollTop;
    }
}
```

And we load the hook in the LiveSocket, like any other Live View hook:

```javascript
import { LiveSocket } from "phoenix_live_view";
import InfiniteScroll from './hooks/InfiniteScroll';

/* ... */

let csrfToken = document.querySelector("meta[name='csrf-token']").getAttribute("content")
let liveSocket = new LiveSocket("/live", Socket, {
    hooks: {
        InfiniteScroll
    },
    params: {
        _csrf_token: csrfToken,
    }
});
```

## The Elixir side of things

Most of the code being on the hook, the Elixir side is very simple, we just listen to the hook event asking for data and we reply with what is needed.


```elixir
  def handle_event(
        "load-table",
        %{"offset" => offset},
        %{assigns: %{user: user, wallet: wallet, locale: locale, timezone: timezone}} = socket
      ) do
    {:noreply,
     socket
     |> push_event(
       "scroll-#{wallet.id}-receive-table-#{offset}",
       %{
         offset: offset,
         html:
           list_records_from_wallet(
             user: user,
             wallet_id: wallet.id,
             page: offset + 1,
             per_page: @records_per_page
           )
           |> Enum.map(fn record ->
             render_to_string(MavioWeb.LayoutView, "record.html",
               conn: socket,
               record: record,
               locale: locale,
               timezone: timezone,
               wallet: wallet,
               row_height: @row_height
             )
           end)
       }
     )}
  end
```

Some additional explanation:

- ```list_records_from_wallet``` is simply a function which does a sql query to fetch data with a page offset  
- We return a list of each record rendered into HTML, the hook is then using that to replace the loading blocks.


And here you go! We can create a list of 20k records without slowing down the browser and the page load!