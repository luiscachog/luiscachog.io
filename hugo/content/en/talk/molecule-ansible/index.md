---
title: Improve Ansible Roles with Molecule
event: DoxMX Talk
event_url: https://github.com/doxmx/talks

location: DoxMX
# address:
#   street: "450 Serra Mall"
#   city: Stanford
#   region: CA
#   postcode: '94305'
#   country: United States

summary: Improving your ansible playbooks/roles deployment using molecule for test driven.
abstract: ""

# Talk start and end times.
#   End time can optionally be hidden by prefixing the line with `#`.
date: "2019-03-07T16:00:00Z"
date_end: "2019-03-07T17:00:00Z"
all_day: false

# Schedule page publish date (NOT talk date).
publishDate: "2019-03-07T00:00:00Z"

authors: [ luis ]
tags: [ Community, Ansible, Molecule ]

# Is this a featured talk? (true/false)
featured: true

image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com/photos/bzdhc5b3Bxs)'
  focal_point: Right

links:
- icon: twitter
  icon_pack: fab
  name: Follow
  url: https://twitter.com/k4ch0
url_code: ""
url_pdf: ""
url_slides: ""
url_video: ""

# Markdown Slides (optional).
#   Associate this talk with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: molecule-ansible

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects:
- ansible-galaxy-roles

# Enable math on this page?
math: false
---

{{% alert note %}}
Click on the **Slides** button above to view the built-in slides feature.
{{% /alert %}}

On this talk I share some best practices to perform test driven on your Ansible Roles.

I explain how workflow with molecule will help you to improve the development speed for your Ansible Roles.

I show an example of how to integrate molecule on an Ansible Role that does not have it.