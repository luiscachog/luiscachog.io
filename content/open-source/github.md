---
widget: blank
headless: true
active: true
weight: 15
title: Contributions
subtitle: to OpenSource projects
design:
  columns: '2'
  background:
    text_color_light: false
  spacing:
    padding:
      - 20px
      - '0'
      - 20px
      - '0'
advanced:
  css_style: ''
  css_class: ''

---

<!-- Include the library. -->
<script
  src="https://unpkg.com/github-calendar@latest/dist/github-calendar.min.js">
</script>

<!-- Optionally, include the theme (if you don't want to struggle to write the CSS) -->
<link
  rel="stylesheet"
  href="https://unpkg.com/github-calendar@latest/dist/github-calendar-responsive.css"
/>

<!-- Prepare a container for your calendar. -->
<div class="calendar">
    <!-- Loading stuff -->
    Loading github data...
</div>

<script>
    // or enable responsive functionality:
    GitHubCalendar(".calendar", "luiscachog", { responsive: true });
</script>
