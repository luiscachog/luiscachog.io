---
title: 'Improve Ansible Roles with Molecule'
summary: An introduction to using Academic's Slides feature.
authors: [ luis ]
tags: [ Ansible, Molecule, Community ]
categories: [Ansible, Molecule ]
date: "2019-03-07T00:00:00Z"
slides:
  # Choose a theme from https://github.com/hakimel/reveal.js#theming
  theme: night
  # Choose a code highlighting style (if highlighting enabled in `params.toml`)
  #   Light style: github. Dark style: dracula (default).
  highlight_style: dracula
---

<!-- [revealoptions]
  transition= "convex"
  controls= true
  controlsBackArrows= "hidden"
  progress= true
  slideNumber= true
  history= false
  center= true
  showNotes= false
  width= "100%"
	height= "100%"
	margin= 0.2
	minScale= 0.2
	maxScale= 1.5 -->


## Improve Ansible Roles with Molecule 

---
## Luis Cacho

### Linux Administrator [@Rackspace](https://rackspace.com)

<br/>
[luiscachog.io](https://luiscachog.io) &bull;
[@k4ch0](https://twitter.com/k4ch0) &bull;
[github.com/k4ch0](https://github.com/k4ch0)

---

{{< slide background-image="/img/slides-molecule-ansible/Background1.jpg" >}}

---
## Agenda

- Ansible Review
  - YAML Review
- Test Automation
  - Testing options for Ansible
- Molecule
- Demo!!

---

{{< slide background-image="/img/slides-molecule-ansible/BackgroundAnsible.png" >}}

---

## Ansible

<section>

  <div style="text-align: left; float: left;">
    **Use Cases**</p>
    - Configuration Management</p>
    - Software Provisioning</p>
    - Security and Compliance</p>
    - Application Deployment</p>
    - Orchestration</p>
    - Continuous Delivery</p>
    <!-- more Elements -->
  </div>

  <div style="text-align: left; float: right;">
    **Attributes**</p>
    - Simple</p>
    - Powerful</p>
    - Agentless</p>
    - Cross Platform</p>
    - Over 450 Modules</p>
    - Big Community</p>
    <!-- more Elements -->
  </div>
</section>

Note:
Ansible es una herramienta de automatización open source escrita en python, que nos ayuda a configurar hosts remotos, aprovisionar software, e inclusive orquestar tareas mas complicadas como Continuos Deployment o Rolling Updates con Cero Downtime.
En lugar de tener scripts para cada tarea.
Ansible es:

- Simple
- Poderoso
- No necesita agente
- Multiplataforma
- Cuenta con mas de 450 Modulos
- Gran comunidad alrededor del proyecto

___

## Ansible

![Ansible Architecture](/img/slides-molecule-ansible/AnsibleArch.png)

Note:
Revisando la arquitectura de Ansible tenemos:

- Playbook: "El libro de jugadas" que contiene las tareas a ejecutar.
- Inventario:donde se definen los hosts con los cuales va a interactuar ansible, esta formato INI. Se definen grupos de hosts y algunas variables opcionales ()IP, hostname, ssh user, key file)
- Modulos son pequenos programas que ejecuta Ansible de manera remota.
- API: Aun esta en construccion pero hay plugins que ya la usan (ansible tower)
- Plugins: Agregan funcionabilidad adicional al core de Ansible (ARA)

Todo esto se se aplica a los hosts remotos o a los dispositivos de red.

___

## Ansible

<img src="/img/slides-molecule-ansible/Ansible_Playbook.png" width="70%" height="70%" >

Note:
Los componentes de un playbook son, revisando desde la unidad mas pequena son:

- Tasks, que ejecutan el modulo de Ansible
- Al conjunto de varias tasks relacionadas, se le llama Play
- Al conjunto de diferentes Plays/jugadas se le llama Playbook.

___

## Ansible

- **Playbooks** contain/connect **roles**  
- **Roles** contain **plays**
- **Plays** contain **tasks**
- **Tasks** execute a **module**
- **Tasks** run sequencially
- **Handlers** are triggered by **tasks**, runs once at the end of the **play**

Note:
Revisando desde la unidad mas grande tenemos
Role is a list of tasks reusable
Playbook conecta los roles con los hosts

---
{{< slide background-image="/img/slides-molecule-ansible/BackgroundTest.png" >}}

Note: Por que es importante la automatizacion de pruebas?

---

## Test Automation

- Reliable Code
- Quality
  - Fast feedback
- Time and cost saving
- Faster Development Cycle
  - CI/CD
- Repeatability
  - Test same change accross multiple environments (OS, Providers); multiple data sets

Note:
La automatizacion de las pruebas nos ayuda a:

- Tener un codigo mas seguro y confiable
- Mejora la calidad de nuestro codigo ya que permiten que se ejecuten más pruebas en menos tiempo, aumentando la cobertura de las pruebas, y realizando más exigentes.
- Reducimos el tiempo y el costo de un desarrollo
- Nos ayuda a tener un ciclo de desarrollo mas rapido ya que lo podemos/debemos integrar con alguna herramienta de CI/CD
- Repetibilidad, con lo cual podemos probar el mismo cambio en multples ambientes, sistemas operativos, o provides, o con diferentes data sets

___

## Test Automation
#### Testing options for Ansible

- Ansible tasks *- Test Ansible w/ Ansible*<!-- .element: class="fragment" -->
- Test Kitchen *- Test Ansible w/ Ruby*<!-- .element: class="fragment" -->
- ansible-test *- Test Ansible w/ Unmaintained Python*<!-- .element: class="fragment" -->
- Molecule *- Test Ansible w/ Python*<!-- .element: class="fragment" -->

Note:
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
- Onlys support docker provisioner on dEbian-based images
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

---

{{< slide background-image="/img/slides-molecule-ansible/BackgroundMolecule.png" >}}
___

## Molecule
##### Testing Ansible with Molecule

- Tool designed to aid in the development and testing of Ansible roles.
- Provides support for testing with multiple instances, operating systems, providers, test frameworks and testing scenarios.
- Encourages an approach that results in consistently developed roles that are well-written, easily understood and maintained.

[https://github.com/ansible/molecule](https://github.com/ansible/molecule)
</br>
[https://molecule.readthedocs.io/](https://molecule.readthedocs.io/)

Note:
De acuerdo a la propia documentacion de Molecule, se define como una herramienta disenada para ayudar en el desarrollo y pruebas de los roles de ansible.
Brinda soporte para probar con diferentes instancias, OS, Providers, scenarios
Se enfoca en generar los mejores roles posibles, tanto bien escritos como faciles de mantener

___

## Molecule
#### Testing Ansible with Molecule

| **Pros** | **Cons** |
| --- | --- |
| - Written in Python | - No Windows support |
| - Ansible-Native | - No Dinamyc Inventory support |
| - Established community |
| - Open Source |

Note:

- Escrito en python,  
- Desarrollado e integrado para ansible
- Comunidad Madura
- Open Source

___

## Molecule
#### Testing Ansible with Molecule

- Creates nodes for testing
- Run the playbook on the nodes
- Run the playbook again to test idempotence
- Lints the Ansible code with ansible-lint
- Lint the Python code with flake8
- Runs the verifier tests on the nodes to ensure the desired state

Note:

- Creamos los nodos para probar, dependiendo del provisioner puede ser Docker, Vagrant, AWS, Azure, etc
- Se ejecutaran los playbooks en los nodos creados
- Volvera a correr el playbook, para probar idempotencia, es decir, que el tarea se aplique varias veces sin cambiar el resultado de primera vez.
- Buscaran problemas de estilo en el codigo de ansible usando ansible-lint
- Buscaran problemas en el codigo de python con flake8
- El verificador realizara loas pruebas para asegurar el estado deseado

___

## Molecule
#### Testing Ansible with Molecule

<img src="/img/slides-molecule-ansible/MoleculeProcess.jpeg" width="65%" height="65%" >

Note:

___
## Molecule
#### Testing Ansible with Molecule

- What can I test?
  - Files exists and permissions
  - Service are running
  - User exists and is member of the correct groups
  - Package installed
  - Basic Software interaction (Test web server basic authentication)

---

{{< slide background-image="/img/slides-molecule-ansible/Background1.jpg" >}}

___

## Molecule Demo
- Let's try!! <!-- .element: class="fragment" -->
  - Creates 2 nodes <!-- .element: class="fragment" -->
  - Converge both nodes <!-- .element: class="fragment" -->
  - Check for idempotence <!-- .element: class="fragment" -->
  - Lint the Ansible and Python code <!-- .element: class="fragment" -->
  - Verify the role against some tests <!-- .element: class="fragment" -->
</br>
- Github Repo: <!-- .element: class="fragment" --> 
</br>*(Thanks to GaRaGeD for provide a playbook to test)*<!-- .element: class="fragment" -->
</br>[https://github.com/k4ch0/elastic_stack](https://github.com/k4ch0/elastic_stack) <!-- .element: class="fragment" -->

___

## Molecule Demo

#### Terminal time!! <!-- .element: class="fragment" -->

___

## Molecule Demo
#### TO-DO <!-- .element: class="fragment" -->

- Ansible-Vault implementation <!-- .element: class="fragment" -->
- Integrating Molecule into Travis CI, Circle CI, Jenkins, etc <!-- .element: class="fragment" -->

---

{{< slide background-image="/img/slides-molecule-ansible/BackgroundConclusion.jpg" >}}
___

## Conclusion <!-- .element: class="fragment" -->

- There are different testing solutions for Ansible, but Molecule is an Ansible-native and the robust option. <!-- .element: class="fragment" -->
- Molecule allows you to create, converge, check idempotence, lint and verify your Ansible code. <!-- .element: class="fragment" -->
- Molecule help you to create the best playbooks possible. <!-- .element: class="fragment" -->

---

## Questions? <!-- .element: class="fragment" -->

---

## Thank you! <!-- .element: class="fragment" data-fragment-index="1" -->

*Talk links, references and resources can be found at:* <!-- .element: class="fragment" data-fragment-index="2" -->
</br>
[https://luiscachog.io/talk/molecule-ansible](https://luiscachog.io/talk/molecule-ansible) <!-- .element: class="fragment" data-fragment-index="3" -->
