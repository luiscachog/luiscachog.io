---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Differences between Kubernetes and Openshift"
summary: "An introduction to know the differences between a kubernetes vanilla implementation an Openshift Container Platform"
authors: [ luiscachog ]
tags: [ Kubernetes, Openshift, DevOps, SRE, Open Source, ]
categories: [ Cloud Native, DevOps, Kubernetes, Openshift, Open Source]
date: 2021-03-25T09:10:47-05:00
slides:
  # Choose a theme from https://github.com/hakimel/reveal.js#theming
  theme: night
  # Choose a code highlighting style (if highlighting enabled in `params.toml`)
  #   Light style: github. Dark style: dracula (default).
  highlight_style: dracula

  diagram: true
  diagram_options:
    # Mermaid diagram themes include: default,base,dark,neutral,forest
    theme: base

  # RevealJS slide options.
  # Options are named using the snake case equivalent of those in the RevealJS docs.
  reveal_options:
    controls: true
    progress: false
    slide_number: c/t  # true | false | h.v | h/v | c | c/t
    center: true
    rtl: false
    mouse_wheel: true
    transition: fade  # none/fade/slide/convex/concave/zoom
    transitionSpeed: default  # default/fast/slow
    background_transition: slide  # none/fade/slide/convex/concave/zoom
    touch: true
    loop: false
    menu_enabled: true
---
<!-- [revealoptions]
  controlsBackArrows= "hidden"
  history= false
  center= true
  showNotes= false
  width= "100%"
  height= "100%"
  margin= 0.2
  minScale= 0.2
  maxScale= 1.5 -->

#

## Differences between Kubernetes and Openshift

---

### Luis Cacho

#### Senior Container Infrastructure Consultant

##### [@Red Hat](https://redhat.com)

---

{{% section %}}

### Kubernetes

#### Some assembly required

{{< figure library="true" src="slides/k8s-vs-ocp/k8s-lego.jpeg" lightbox="false" width="50%" heigth="50%">}}

---

<!-- markdownlint-capture -->
<!-- markdownlint-disable -->

### Kubernetes
<!-- markdownlint-restore -->

#### Some assembly required

{{< figure library="true" src="slides/k8s-vs-ocp/k8s-lack.png" lightbox="false" width="100%">}}

{{< speaker_note >}}

- Press `S` key to view -

Kubernetes does not provide (out-of-the-box):

  1. Operative System
  1. Developer tooling and UX
  1. Container runtime (CRI-O, Containerd, Docker, etc).
  1. Image registry
  1. Software-defined networking
  1. Load‐balancer and routing
  1. Log management
  1. Container metrics and monitoring
  1. DNS
  1. Ingress
  1. RBAC
  1. Storage
  1. Management
  1. Service Catalog (Operators)

{{< /speaker_note >}}

---
<!-- markdownlint-capture -->
<!-- markdownlint-disable -->

### Kubernetes
<!-- markdownlint-restore -->

#### Some assembly required

{{< figure library="true" src="slides/k8s-vs-ocp/k8s-landscape.png" lightbox="false" width="100%">}}

{{% /section %}}

---
{{% section %}}

### Openshift

#### Value Added

{{< figure library="true" src="slides/k8s-vs-ocp/ocp-logo.png" lightbox="false" width="70%">}}

---
<!-- markdownlint-capture -->
<!-- markdownlint-disable -->

### Openshift
<!-- markdownlint-restore -->

#### Value Added

{{< figure library="true" src="slides/k8s-vs-ocp/ocp-landscape.png" lightbox="false" width="100%">}}

---
<!-- markdownlint-capture -->
<!-- markdownlint-disable -->

### Openshift
<!-- markdownlint-restore -->
#### Value Added

{{< figure library="true" src="slides/k8s-vs-ocp/ocp-arch.webp" lightbox="false" width="100%">}}

{{% /section %}}

---

{{% section %}}

### Openshift contains Kubernetes

{{< figure library="true" src="slides/k8s-vs-ocp/ocp-contains-k8s.png" lightbox="false" width="90%">}}

{{< speaker_note >}}

- Press `S` key to view -

{{< /speaker_note >}}

---
<!-- markdownlint-capture -->
<!-- markdownlint-disable -->
### Openshift contains Kubernetes
<!-- markdownlint-restore -->

{{< figure library="true" src="slides/k8s-vs-ocp/ocp-is-k8s.png" lightbox="false" width="90%">}}

{{% /section %}}

---

## Conclusion

### Openshit IS a Kubernetes flavor

---

## Thank You
