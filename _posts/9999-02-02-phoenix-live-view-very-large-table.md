---
title: A hook for handling very large lists with Phoenix Live View
description: 
author: alex-min
image: /images/phoenix.png
tags:
  - elixir
  - phoenix
---

I'm building a web version of my personal finance application, for that, I've chosen to use Phoenix Live View, it's the perfect tech for my use case.

In this finance application, you do have a lot of very large lists like the list of transactions for example.
Each wallet can have thousands of transactions and it's unreasonable to load everything at once. 
Loading everything slows down the page load with those large queries and then it also slows down the browser.

The solution is to build a list of transactions which is streamed as you go like on Fastmail or Gmail. 
The user has the impression that they can scroll down and that the full list is already there but in reality, everything is being streamed in chunks and loads as they scroll.

I've use [Fattable.js](https://github.com/fulmicoton/fattable/) for that purpose, it's a small library which is making those large tables easier.


First, here is a preview of the end result:

<video src="/videos/infinitescroll.mp4" poster="/images/infinite-scroll-preview.jpg" controls>
  <p>Video preview of the infinite scrolling where I scroll anywhere and you can see the table loading as it goes</p>
</video>

Now let's dive into the code!

I've kept the live view as simple as possible, I've created a bunch of additional attributes that are used in the javascript:

 - ```data-count``` contains the total number of items, this is helpfull for fattable.js to calculate the total height of the div
 - ```data-page-size``` is the chunk size of which we're loading the items, I'm setting it to 40 for my use case but it depends of what you are building, you might want a lower page size if retreiving the data is very expensive. 
 - ```data-row-height``` is the row height in pixels. Unlike a traditional list, with this method, we need to have every row at the exact same height.
 - ```data-loading-block-id``` is an id linking to the HTML which is used when loading the data, it's what's using the "pulse" animation in the video.

```@records``` here is is only the first page of records (so 40 records in my case), this is used to have data when loading the page so that the user can interact directly with the list without waiting for the first page to load. 

```eex
<div class="bg-white flex-1 h-100 lg:block x-space-y-2 overflow-auto relative h-full"
         id="scroll-content"
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

    <!-- here is the loading block, this is the block which is used when the data is still loading -->
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