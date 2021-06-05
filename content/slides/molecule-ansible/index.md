---
title: Improve Ansible Roles with Molecule
summary: An introduction to using Molecule to test your Ansible Roles.
authors: [ luiscachog ]
tags: [ Ansible, Molecule, Community ]
categories: [Ansible, Molecule, Slide ]
date: "2019-03-07T00:00:00Z"
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

# Improve Ansible Roles with Molecule

---

## Luis Cacho

### Security Systems Administrator

#### [@Rackspace](https://rackspace.com)

[luiscachog.io](https://luiscachog.io) | [@luiscachog](https://twitter.com/luiscachog) | [github.com/luiscachog](https://github.com/luiscachog)

---
{{< slide background-image="/media/slides/molecule-ansible/Background1.jpg" >}}

---

## Agenda

- Ansible Review
    - YAML Review
- Test Automation
    - Testing options for Ansible
- Molecule
- Demo!!

---
{{< slide background-image="/media/slides/molecule-ansible/BackgroundAnsible.png" >}}

---
{{% section %}}

## Ansible (1/4)

|**Use Cases**| **Attributes** |
|--- | --- |
| Configuration Management | Simple |
| Software Provisioning | Powerful |
| Security and Compliance | Agentless |
| Application Deployment | Cross Platform |
| Orchestration | Over 450 Modules |
| Continuous Delivery | Big Community |

{{< speaker_note >}}

- Press `S` key to view -
Ansible es una herramienta de automatización open source escrita en python.
Nos ayuda a configurar hosts remotos, aprovisionar software, e inclusive orquestar tareas mas complicadas como Continuos Deployment o Rolling Updates con Cero Downtime.
En lugar de tener scripts para cada tarea.
Ansible es:

- Simple
- Poderoso
- No necesita agente
- Multiplataforma
- Cuenta con mas de 450 Modulos
- Gran comunidad alrededor del proyecto
{{< /speaker_note >}}

---

## Ansible (2/4)

{{< figure library="true" src="slides/molecule-ansible/AnsibleArch.png" lightbox="false" width="90%">}}

{{< speaker_note >}}

- Press `S` key to view -

Revisando la arquitectura de Ansible tenemos:

- Playbook: "El libro de jugadas" que contiene las tareas a ejecutar.
- Inventario:donde se definen los hosts con los cuales va a interactuar ansible, esta formato INI. Se definen grupos de hosts y algunas variables opcionales ()IP, hostname, ssh user, key file)
- Modulos son pequenos programas que ejecuta Ansible de manera remota.
- API: Aun esta en construccion pero hay plugins que ya la usan (ansible tower)
- Plugins: Agregan funcionabilidad adicional al core de Ansible (ARA)

Todo esto se se aplica a los hosts remotos o a los dispositivos de red.
{{< /speaker_note >}}

---

## Ansible (3/4)

{{< figure library="true" src="slides/molecule-ansible/Ansible_Playbook.png" lightbox="false" width="70%">}}

{{< speaker_note >}}

- Press `S` key to view -
Los componentes de un playbook son, revisando desde la unidad mas pequena son:

- Tasks, que ejecutan el modulo de Ansible
- Al conjunto de varias tasks relacionadas, se le llama Play
- Al conjunto de diferentes Plays/jugadas se le llama Playbook.
{{< /speaker_note >}}

---

## Ansible (4/4)

- **Playbooks** contain/connect **roles**
- **Roles** contain **plays**
- **Plays** contain **tasks**
- **Tasks** execute a **module**
- **Tasks** run sequencially
- **Handlers** are triggered by **tasks**, runs once at the end of the **play**

{{< speaker_note >}}

- Press `S` key to view -
Entonces, podemos decir que desde la unidad mas grande tenemos
Role is a list of tasks reusable
Playbook conecta los roles con los hosts
{{< /speaker_note >}}

{{% /section %}}

---
{{< slide background-image="/media/slides/molecule-ansible/BackgroundTest.png" >}}

{{< speaker_note >}}

- Press `S` key to view -
Por que es importante la automatizacion de pruebas?
{{< /speaker_note >}}

---
{{% section %}}

### Test Automation (1/2)

- Reliable Code
- Quality (Fast feedback)
- Time and cost saving
- Faster Development Cycle (CI/CD)
- Repeatability (Test same change accross multiple environments (OS, Providers); multiple data sets)

{{< speaker_note >}}

- Press `S` key to view -
La automatizacion de las pruebas nos ayuda a:

- Tener un codigo mas seguro y confiable
- Mejora la calidad de nuestro codigo ya que permiten que se ejecuten más pruebas en menos tiempo, aumentando la cobertura de las pruebas, y realizando más exigentes.
- Reducimos el tiempo y el costo de un desarrollo
- Nos ayuda a tener un ciclo de desarrollo mas rapido ya que lo podemos/debemos integrar con alguna herramienta de CI/CD
- Repetibilidad, con lo cual podemos probar el mismo cambio en multples ambientes, sistemas operativos, o provides, o con diferentes data sets
{{< /speaker_note >}}

---

### Test Automation (2/2)

#### Testing options for Ansible

<ul>
{{% fragment %}} <li><strong>Ansible tasks</strong><i> - Test Ansible w/ Ansible</i></li>{{% /fragment %}}
{{% fragment %}} <li><strong>Test Kitchen</strong><i> - Test Ansible w/ Ruby</i></li>{{% /fragment %}}
{{% fragment %}} <li><strong>ansible-test</strong><i> - Test Ansible w/ Unmaintained Python</i></li>{{% /fragment %}}
{{% fragment %}} <li><strong>Molecule</strong><i> - Test Ansible w/ Python</i></li>{{% /fragment %}}
</ul>

{{< speaker_note >}}

- Press `S` key to view -
Algunas herramientas que revise, despues de ver la conferencia de Elana Hashman en en Ansible Fest 2017 son:

Testing Ansible with Ansible Tasks -> Test ansible with Ansible tasks
Benefits:

- As flexible and powerful as you need it
Issues:
- High development cost
- Ansible can't detect ansible bugs
- Need to write your own provisioner

Testing Ansible with ansible-test ->
Benefits:

- Written in Python
- Solves the provisioning problem in "Test ansible with Ansible"
- Simple tool with small codebase
Issues:
- Onlys support docker provisioner on debian-based images
- Does not apear to be actively maintained

Testing Ansible with Test Kitchen ->
Benefits:

- Large community
- Supports Ansible
- Supportw windows testing for Ansible
Issues:
- Written in Ruby
- Verifiers are Ruby or bash based
- Installs Ansible on the target host and runs it locally

{{< /speaker_note >}}

{{% /section %}}

---
{{< slide background-image="/media/slides/molecule-ansible/BackgroundMolecule.png" >}}

---
{{% section %}}

### Molecule (1/5)

#### Testing Ansible with Molecule

- Tool designed to aid in the development and testing of Ansible roles.
- Provides support for testing with multiple instances, operating systems, providers, test frameworks and testing scenarios.
- Encourages an approach that results in consistently developed roles that are well-written, easily understood and maintained.

[github.com/ansible/molecule](https://github.com/ansible/molecule) | [molecule.readthedocs.io](https://molecule.readthedocs.io/)

{{< speaker_note >}}

- Press `S` key to view -

De acuerdo a la propia documentacion de Molecule, se define como una herramienta disenada para ayudar en el desarrollo y pruebas de los roles de ansible.
Brinda soporte para probar con diferentes instancias, OS, Providers, scenarios
Se enfoca en generar los mejores roles posibles, tanto bien escritos como faciles de mantener

{{< /speaker_note >}}

---

### Molecule (2/5)

#### Testing Ansible with Molecule

| **Pros** | **Cons** |
| --- | --- |
| - Written in Python | - No Windows support |
| - Ansible-Native | - No Dinamyc Inventory support |
| - Established community |
| - Open Source |

{{< speaker_note >}}

- Press `S` key to view -
- Escrito en python,
- Desarrollado e integrado para ansible
- Comunidad Madura
- Open Source

{{< /speaker_note >}}

---

### Molecule (3/5)

#### Testing Ansible with Molecule

- Creates nodes for testing
- Run the playbook on the nodes
- Run the playbook again to test idempotence
- Lints the Ansible code with ansible-lint
- Lint the Python code with flake8
- Runs the verifier tests on the nodes to ensure the desired state

{{< speaker_note >}}

- Press `S` key to view -
A grandes rasgos las actividades que hace molecule son:
- Creamos los nodos para probar, dependiendo del provisioner puede ser Docker, Vagrant, AWS, Azure, etc
- Se ejecutaran los playbooks en los nodos creados
- Volvera a correr el playbook, para probar idempotencia, es decir, que el tarea se aplique varias veces sin cambiar el resultado de primera vez.
- Buscaran problemas de estilo en el codigo de ansible usando ansible-lint
- Buscaran problemas en el codigo de python con flake8
- El verificador realizara loas pruebas para asegurar el estado deseado
{{< /speaker_note >}}

---

### Molecule (4/5)

#### Testing Ansible with Molecule

{{< figure library="true" src="slides/molecule-ansible/MoleculeProcess.jpeg" lightbox="false" width="50%">}}

---

### Molecule (5/5)

#### Testing Ansible with Molecule

- What can I test?
    - Files exists and permissions
    - Service are running
    - User exists and is member of the correct groups
    - Package installed
    - Basic Software interaction (Test web server basic authentication)
{{% /section %}}

---
{{< slide background-image="/media/slides/molecule-ansible/Background1.jpg" >}}

---
{{% section %}}

### Molecule Demo (1/3)

- Let's try it!!
    - Creates 2 nodes
    - Converge both nodes
    - Check for idempotence
    - Lint the Ansible and Python code
    - Verify the role against some tests
- Github Repo: [github.com/luiscachog/elastic_stack](https://github.com/luiscachog/elastic_stack) {{% fragment %}} *(Thanks to GaRaGeD for provide a playbook to test)* {{% /fragment %}}

---

### Molecule Demo (2/3)

{{% fragment %}}##### Terminal time!! {{% /fragment %}}

---

### Molecule Demo (3/3)

#### TO-DO

- Ansible-Vault implementation
- Integrating Molecule into Travis CI, Circle CI, Jenkins, etc
{{% /section %}}

---
{{< slide background-image="/media/slides/molecule-ansible/BackgroundConclusion.jpg" >}}

---
{{% section %}}

## Conclusion

- There are different testing solutions for Ansible, but Molecule is an Ansible-native and the robust option.
- Molecule allows you to create, converge, check idempotence, lint and verify your Ansible code.
- Molecule help you to create the best playbooks possible.

---

## Questions?

*Talk links, references and resources can be found at:* [luiscachog.io/talk/molecule-ansible](https://luiscachog.io/talk/molecule-ansible)

---

## Thank you

*You can find me on:*

[luiscachog [at] gmail.com](mailto:luiscachog@gmail.com) | [luiscachog.io](https://luiscachog.io) | [github.com/luiscachog](https://github.com/luiscachog) | [@luiscachog](https://twitter.com/luiscachog)

{{% /section %}}
