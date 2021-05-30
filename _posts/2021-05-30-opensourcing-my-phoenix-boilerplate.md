---
title: "Opensourcing my battery-included Phoenix boilerplate!"
description:  ExPlatform goes live!
author: alex-min
image: /images/thinking-elixir-cover.png
tags:
  - elixir
  - phoenix
---


<a class="github-preview" href="https://github.com/alex-min/ex_platform" target="_blank">
  <img src="/images/github-logo-white.png" alt="Github" width="100">
  <span>Checkout the ExPlatform boilerplate on GitHub</span>
</a>

During the past year working on my saas budget app, I've accumulated some amount of boilerplate to create my project.
I've chosen to open-source it all, say hello to ExPlatform! Here's what's included out of the box:

- TailwindCSS with Tailwind jit
- Phoenix auth integrated with nice Tailwind pages and transformed to support i18n
- i18n everywhere (PRs are welcomed to add more languages)
- Notifications with a nice animation and auto-clear
- Auth emails are using Bamboo and proper HTML template
- Javascripts tests with Jest (and I've included some built-in tests)
- Pre-commit checks for styling & dialyzer (this boilerplate is ready for team work)
- Github actions checking the pre-commits and the tests for each commit
- Dependabot
- Asdf integration, just use the .tools-versions for all your versions! It's also what's used in the Github actions. 
- Eslint


A few screenshots:

<figure class="screenshot" markdown="1">

<img src="/images/ex_platform_signup.png" alt="An image of the signup page showing a login field and a password field" loading="lazy" />

<figcaption>The signup page</figcaption>
</figure>

<figure class="screenshot" markdown="1">

<img src="/images/ex_platform_settings.png" alt="An image of the user settings page showing that you can change your email and your password" loading="lazy" />

<figcaption>The user settings page</figcaption>
</figure>