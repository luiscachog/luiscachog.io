---
# Leave the homepage title empty to use the site title
title: asdasd
headless: true
date: 2022-11-16
type: landing
widget: 'about.avatar'
sections:
  - block: about
    content:
      # Choose a user profile to display (a folder name within `content/authors/`)
      username: luiscachog
      text: |-
        👋 Hi, there! I'm **Alice**, a machine learning researcher at Netflix.
        {style="font-size: 1.2rem; background: #FFB76B; background: linear-gradient(to right, #FFB76B 0%, #FFA73D 30%, #FF7C00 60%, #FF7F04 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent;"}
    design:
      background:
        color: black
        text_color_light: true
        image:
          # Add your image background to `assets/media/`.
          filename: space.jpg
          filters:
            brightness: 0.4
          size: cover
          position: center
          parallax: false
      # css_class: d-flex fullscreen align-items-center
---
