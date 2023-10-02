---
title: LiveView native with Flutter preview
description: A LiveView native client built in Flutter!
author: alex-min
image: /images/flutter-live-view-cover.png
tags:
  - flutter
  - elixir
  - phoenix
---

## What is LiveView native?

LiveView native is a new way to build mobile apps with Phoenix LiveView in Elixir.
There's two native clients being built at the moment, one with Swift for the Apple platform (which is basically almost complete) and one in Kotlin mainly targetting Android (which is currently being built).

I wanted a client using Flutter and since nobody made any, I'm building it!

## What does it look like?

Here is a sneak peek on my computer of a LiveView flutter client with a simple view:

<video src="/videos/live-view-flutter-demo.mp4" muted autoplay loop controls>
  <p>Video demo of the live view flutter client</p>
</video>

It almost look like it's running with a web browser!

### Where is the code?

The full source code will be released as soon as it's ready, it's too much of an alpha state now to use it in a production environment.

If you want more news about this upcoming release, please subscribe to the newsletter just below! I will update you regularly.

<div id="mc_embed_shell" class="article-block">
<div id="mc_embed_signup" class="article-block">
    <form action="https://alex-min.us14.list-manage.com/subscribe/post?u=f6bcf97c08a49d86cc02b4ae1&amp;id=a4ac57ae75&amp;f_id=0033a3e0f0" method="post" id="mc-embedded-subscribe-form" name="mc-embedded-subscribe-form" class="validate" target="_blank">
        <div id="mc_embed_signup_scroll"><h2>S'abonner</h2>
            <div class="indicates-required"><span class="asterisk">*</span> indicates required</div>
            <div class="mc-field-group"><label for="mce-EMAIL">Adresse e-mail <span class="asterisk">*</span></label><input type="email" name="EMAIL" class="required email" id="mce-EMAIL" required="" value=""><span id="mce-EMAIL-HELPERTEXT" class="helper_text"></span></div>
        <div id="mce-responses" class="clear foot">
            <div class="response" id="mce-error-response" style="display: none;"></div>
            <div class="response" id="mce-success-response" style="display: none;"></div>
        </div>
    <div style="position: absolute; left: -5000px;" aria-hidden="true">
        /* real people should not fill this in and expect good things - do not remove this or risk form bot signups */
        <input type="text" name="b_f6bcf97c08a49d86cc02b4ae1_a4ac57ae75" tabindex="-1" value="">
    </div>
        <div class="optionalParent">
            <div class="clear foot">
                <input type="submit" name="subscribe" id="mc-embedded-subscribe" class="button" value="Subscribe">
                <p style="margin: 0px auto;"><a href="http://eepurl.com/iATudg" title="Avec Mailchimp, les campagnes de marketing par e-mail sont un jeu d'enfant"><span style="display: inline-block; background-color: transparent; border-radius: 4px;"><img class="refferal_badge" src="https://digitalasset.intuit.com/render/content/dam/intuit/mc-fe/en_us/images/intuit-mc-rewards-text-dark.svg" alt="Intuit&nbsp;Mailchimp" style="width: 220px; height: 40px; display: flex; padding: 2px 0px; justify-content: center; align-items: center;"></span></a></p>
            </div>
        </div>
    </div>
</form>
</div>
<script type="text/javascript" src="//s3.amazonaws.com/downloads.mailchimp.com/js/mc-validate.js"></script><script type="text/javascript">(function($) {window.fnames = new Array(); window.ftypes = new Array();fnames[0]='EMAIL';ftypes[0]='email';fnames[1]='FNAME';ftypes[1]='text';fnames[2]='LNAME';ftypes[2]='text';fnames[3]='ADDRESS';ftypes[3]='address';fnames[4]='PHONE';ftypes[4]='phone';fnames[5]='BIRTHDAY';ftypes[5]='birthday';}(jQuery));var $mcj = jQuery.noConflict(true);</script></div>


### What's missing?

Pretty much everything is missing at the moment, I need more styling, forms, live interop, hook, themes... The road ahead is pretty long.
I have a lot of experience with flutter since I built a full mobile app with it (~30k lines of code) so I know where I'm going. 

This is the priorities I'm working on right now:
 - Implement all LiveView events
 - All the Flutter components (only a subset are implemented in this demo)
 - Routing
 - Making the full Flutter styling API accessible
 - LiveView hooks with an embed js engine
 - Themes
 - ...

Stay tuned!