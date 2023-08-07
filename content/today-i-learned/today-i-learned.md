---
# An instance of the Portfolio widget.
# Documentation: https://wowchemy.com/docs/page-builder/
widget: portfolio

# This file represents a page section.
headless: true

# Order that this section appears on the page.
weight: 10

title: Today I Learned
subtitle: '🌲 🌱 Digital Garden 🌱 🌲'

content:
  # Page type to display. E.g. project.
  page_type: book

  filters:
    # Folders to display content from
    folders:
      - garden
    tags:
      - Digital-Garden

  # Field to sort by, such as Date or Title
  sort_by: 'Date'
  sort_ascending: false

  # Default filter index (e.g. 0 corresponds to the first `filter_button` instance below).
  filter_default: 0

  # Filter toolbar (optional).
  # Add or remove as many filters (`filter_button` instances) as you like.
  # To show all items, set `tag` to "*".
  # To filter by a specific tag, set `tag` to an existing tag name.
  # To remove the toolbar, delete the entire `filter_button` block.
  filter_button:
    - name: All
      tag: '*'
    - name: Cloud Native
      tag: Cloud-Native
    - name: Development
      tag: Development
    - name: Infrastructure as Code
      tag: IaC
    - name: Network
      tag: Network
    - name: Storage
      tag: Storage
    - name: SysAdmin
      tag: SysAdmin
    - name: Miscellaneous
      tag: Miscellaneous
design:
  # Choose how many columns the section has. Valid values: '1' or '2'.
  columns: '1'

  # Toggle between the various page layout types.
  #   1 = List
  #   2 = Compact
  #   3 = Card
  #   5 = Showcase
  view: 3

  # For Showcase view, flip alternate rows?
  flip_alt_rows: false
---
