---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Ansible Galaxy Roles"
summary: Collection of Ansible Roles published in Ansible Galaxy
authors: []
tags: [ Ansible, Galaxy, Python, CircleCI, Sonobuoy, Kubernetes, Cloud Native, Open Source]
categories: [ Ansible, Kubernetes, Project ]
date: 2019-10-25

# Optional external URL for project (replaces project detail page).
external_link: ""

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: "Ansible Galaxy Logo"
  focal_point: "Center"
  preview_only: false

# Custom links (optional).
#   Uncomment and edit lines below to show custom links.
# Links
url_code: ""
url_pdf: ""
url_slides: ""
url_video: ""

links:
- name: Ansible Galaxy
  url: https://galaxy.ansible.com/luiscachog
  icon_pack: fas
  icon: cog
- name: CircleCI Role
  url: https://github.com/luiscachog/ansible-circleci-cli
  icon_pack: fab
  icon: github
- name: Sonobuoy Role
  url: https://github.com/luiscachog/ansible-sonobuoy
  icon_pack: fab
  icon: github

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: ""
---

- CircleCI Role: Ansible role that installs [CircleCI CLI](https://circleci-public.github.io/circleci-cli/) on your server/workstation. [![Build Status](https://travis-ci.com/luiscachog/ansible-circleci-cli.svg?branch=master)](https://travis-ci.com/luiscachog/ansible-circleci-cli)

- Sonobuoy Role: Ansible role that installs [sonobuoy](https://sonobuoy.io/) on your server/workstation. [![Build Status](https://travis-ci.com/luiscachog/ansible-sonobuoy.svg?branch=master)](https://travis-ci.com/luiscachog/ansible-sonobuoy)
