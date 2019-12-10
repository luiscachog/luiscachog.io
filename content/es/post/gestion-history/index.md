---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Gestion de History"
url: "/gestion-de-history"
subtitle: "Como mejorar el log histórico de tu shell"
summary: "Como mejorar el histórico de tu shell"
authors: [ luis ]
tags: [ Shell, Bash, Zsh, Linux ]
categories: [ Shell, Linux ]
keywords: [ Shell, Bash, Zsh, Linux, Configuration ]
date: 2019-12-03
publishDate: 2019-12-03
lastmod: 2019-12-09
featured: true
draft: false


# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

{{% toc %}}

El archivo de logs histórico (history) tiene varias opciones que podemos cambiar para tener un mejor control del mismo. Aquí vamos a ver algunas opciones para el control y gestión del fichero del log histórico (history).

## Mostrar la fecha y hora de cuando escribimos comandos

Para mostrar la fecha y hora en el formato que requieras puedes agregas las lineas siguientes:

```shell
export HISTTIMEFORMAT='- %F %T - '
```

## Control del tamaño del archivo de logs histórico

Tenemos dos variables de entorno para ello, *HISTSIZE* y *HISTFILESIZE*, que indican el tamaño del fichero, por ejemplo:

```shell
HISTSIZE=1000
HISTFILESIZE=1000
```

Con esto hacemos que el tamaño máximo del fichero de logs histórico sea de 1000 comandos o líneas.

{{% alert note %}}
Si ponemos el tamaño de la variable *HISTSIZE* a **cero** hacemos que no se guarde nada en el archivo de logs histórico
```shell
export HISTSIZE=0
```
{{% /alert %}}

## Control de duplicados en el histórico

En el log histórico se van guardando **TODOS** los comandos que se van introduciendo aunque repitamos 20 veces el mismo comando, se guardará 20 veces, lo cual es en ocasiones una perdida de espacio.
Por lo que podemos usar la variable *HISTCONTROL* para hacer 2 cosas:

- Eliminar los duplicados consecutivos con *ignoredups*.
- Eliminar los duplicados sean o no consecutivos con *erasedups*.

```shell
HISTCONTROL=ignoredups
HISTCONTROL=erasedups
```

## Path para guardar el archivo de logs histórico

Por defecto el histórico se guarda en ```~/.bash_history``` pero podemos indicar donde guardarlo con la variable *HISTFILE*.

```shell
HISTFILE=~/.bitacora.
```

Un truco muy bueno cuando en un mismo servidor entran varios administradores que se pasan a root y poder controlar y guardar que hace cada uno es guardar un archivo de logs histórico por cada uno de ellos de la siguiente forma:

```shell
HISTSIZE=5000
HISTFILESIZE=5000
HISTFILE=/root/.bash_hist-$(who am i | awk '{print $1}';exit)
```

Con esto se guardará en el home de del usuario root un archivo de logs histórico por cada uno de los usuarios que se hayan pasado a root. El tamaño se puede ampliar o reducir a gusto. También podemos poner que ignore duplicados. 

Todas estas variables debemos ponerlas en un archivo donde se activen al arranque que puede ser ```~/.bashrc```.

Espero les sea útilidad.

:smile: