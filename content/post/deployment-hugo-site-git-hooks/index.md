---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Deployment de un sitio estatico con Hugo y Git Hooks"
url: "/deployment-hugo-site-git-hooks"
subtitle: "Como realizar un deployment de un sitio con Hugo automaticamente usando Git Hooks"
summary: "Como realizar un deployment de un sitio con Hugo automaticamente usando Git Hooks"
authors: [ luiscachog ]
tags: [ Hugo, Git, Open-Source, Spanish ]
categories: [ Linux, SysAdmin, DevOps, Open-Source, Spanish]
keywords: [ Hugo, DevOps, Open Source ]
date: 2018-03-05
publishDate: 2018-03-05
lastmod: 2021-04-30
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

## Motivación

Estoy intentando escribir un poco más en mi blog, ya que noté que muchas veces no lo hacia muy a menudo
por que al llegar a la consola de administración de Wordpress, habia que dar bastantes clicks para llegar al menu de "Posts",
además de que cada vez que entraba había un plugin diferente que actualizar, y verificar que nada se rompiera con
las nuevas actualizaciónes, en pocas palabras hay que darle bastante mantenimiento a un sitio con Wordpress,
y además de eso había que dedicarse a escribir el post.

Otra razón por lo que opté hacer el cambio de plataforma, es que al estar tratando de convertirme en DevOps, es necesario,
desde mi punto de vista; tratar automatizar/scriptear la mayoria de tus tareas que realizas día a día, y con [Hugo](https://gohugo.io/ "Hugo") considero que se puede realizar este objetivo también.

### Consideraciones

Una vez que decidí migrarme de Wordpress, el siguiente paso era decidir a que plataforma mudarme.
De entrada la plataforma que queria probar era un [Static Site Generator](https://en.wikipedia.org/wiki/Static_web_page),
aqui otro [link](https://cloudcannon.com/blog/what-is-a-static-website/) de por que usar un Static Site Generator.

Partiendo de lo anterior, las opciones que me parecieron interesantes fueron:

- [Hugo](https://gohugo.io)
- [Jekyll](https://jekyllrb.com)
- [Octopress](http://octopress.org/)
- [Hexo](https://hexo.io/)

Cada una de las opciones tiene diferentes caracteristicas, que no vamos a discutir en este post,
sin embargo las carteristicas que me convencieron de usar [Hugo](https://gohugo.io/ "Hugo") por encima de las otras alternativas fueron:

- Consta solamente de un binario, que comparado con las otras posibilidades hay que instalar todo un ambiente de desarrollo/producción.
- Es bastante rápido.
- Es Multi-plataforma
- Tiene diversos [temas](https://themes.gohugo.io/)

## Instruciones

### Consideraciones técnicas

El ambiente consta de:

- 1 servidor productivo donde esta instalado hugo, git y un servidor web (apache o nginx) , haremos todos los deployments usando el usuario admin, ojo que no es el usuario root.
- 1 servidor/equipo de desarrollo, de igual forma que cuenta con hugo y git, en mi caso, es mi computadora personal y mi usuario es luiscachog.
- 1 cuenta de [github.com](https://github.com)

### Autenticación mediante llaves SSH

El primer paso es realizar el intercambio de llaves SSH entre el equipo de desarrollo y el equipo productivo. Para ello seguimos los siguientes pasos:

1. Generar la llave SSH, tendrás que contestar algunas preguntas, entre las cuales está si quieres ponerle un password, a lo cual deberas dejarlo en blanco para que no te pida contraseña.

    ```shell
    luiscachog@dev-server:~$ ssh-keygen
    ```

1. Copiar la llave SSH hacia el equipo productivo:

    ```shell
    luiscachog@dev-server:~$ ssh-copy-id admin@IP_servidor_productivo
    ```

1. Verificar que te puedas conectar desde tu servidor de desarrollo, con tu usuario al servidor productivo, con el usuario que realizará los deployments.

    ```shell
    luiscachog@dev-server:~$ ssh admin@162.125.2.30 hostname
    ```

    En este caso, debera de mostrarte el hostname del servidor productivo sin pedirte el password.

### Configuración sitio con Hugo

El siguiente paso es configurar nuestro ambiente de desarrollo con Hugo y Git.

1. Para instalar ambos en Ubuntu o derivados debes de ejecutar:

    ```shell
    luiscachog@dev-server:~$ sudo apt install hugo git
    ```

    Para tener la version más actualizada de hugo puedes seguir los pasos descritos en este [link](https://gohugo.io/getting-started/installing/)

1. Vamos a crear un directorio de trabajo para nuestro sitio estatico

    ```shell
    luiscachog@dev-server:~$ mkdir ~/sites
    luiscachog@dev-server:~$ cd ~/sites
    ```

1. Crearemos un nuevo sitio usando el comando hugo

    ```shell
    luiscachog@dev-server:~$ hugo new site luiscachog.io
    Congratulations! Your new Hugo site is created in /home/luiscachog/sites/luiscachog.io.

    Just a few more steps and you're ready to go:

    1.- Download a theme into the same-named folder.
        Choose a theme from https://themes.gohugo.io/, or
        create your own with the "hugo new theme <THEMENAME>" command.
    2.- Perhaps you want to add some content. You can add single files
        with "hugo new <SECTIONNAME>/<FILENAME>.<FORMAT>".
    3.- Start the built-in live server via "hugo server".

    Visit https://gohugo.io/ for quickstart guide and full documentation.
    ```

1. Cuando termine de correr el comando se podra apreciar los siguientes directorios y archivos

    ```shell
    luiscachog@dev-server:~$ cd luiscachog.io
    luiscachog@dev-server:~$ ls
    archetypes  config.toml  content  data  layouts  static  themes
    luiscachog@dev-server:~$ tree
    .
    ├── archetypes
    │   └── default.md
    ├── config.toml
    ├── content
    ├── data
    ├── layouts
    ├── static
    └── themes

    6 directories, 2 files
    ```

1. El siguiente paso es agregar un tema, puedes encontrar uno que te guste en [https://themes.gohugo.io/](https://themes.gohugo.io/)

    ```shell
    git init
    git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke

    # Edit your config.toml configuration file
    # and add the new theme.

    echo 'theme = "ananke"' >> config.toml
    ```

    Como recomendación adicional en este paso, puedes realizar un fork del tema que te guste en github para poder realizar modificaciones y proponer cambios al mismo,
    contribuyendo de esa forma a su desarrollo, para hacerlo, sigue los pasos:

    1. Realizar un fork del tema, sigue esta [guia](https://docs.github.com/en/get-started/quickstart/contributing-to-projects) para hacerlo.
    1. Al realizar el fork, tendras en tus repositorios de github el tema que quieras, por lo que tendras que ejecutar los mismos comandos del punto anterior,
        pero el repositorio del tema apuntara a tu usario en github

    ```shell
    git init
    git submodule add https://github.com/luiscachog/gohugo-theme-ananke.git themes/ananke
    # Edit your config.toml configuration file
    # and add the new theme.

    echo 'theme = "ananke"' >> config.toml
    ```

1. Vamos a crear un post de prueba para verificar que todo esta funcionando correctamente

    ```shell
    hugo new posts/my-first-post.md
    echo "Hola Mundo" >> content/posts/my-first-post.md
    ```

    El comando anterior creara un archivo en la ruta content/posts/my-first-post.md, y el contenido será:

    ```yaml
    ---
    title: "My First Post"
    date: 2018-02-28T12:02:38-06:00
    draft: true
    ---
    Hola Mundo!!!

    ```

1. Finalmente, probaremos que nuestro sitio estatico con nuestro post se muestren de manera local, en nuestro servidor de desarrollo.
    Cabe mencionar, que por defecto el comando 'hugo server' no mostrará los posts que tengan la opción 'draft: true', por ello se agrega la bandera -D

    ```shell
    luiscachog@dev-server:~$ hugo server -D
    ```

### Configuración del repositorio Git en el servidor de desarrollo 1ra parte

En el paso pasado, realizamos la inicialización del repositorio dentro del directorio del sitio estatico:

```shell
luiscachog@dev-server:~$ pwd
/home/luiscachog/sites/luiscachog.io
luiscachog@dev-server:~$ git status
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

    new file:   .gitmodules
    new file:   themes/ananke

Untracked files:
  (use "git add <file>..." to include in what will be committed)

    archetypes/
    config.toml
    content/
    themes/ananke/
```

Ahora, para tener el repositorio publico, tenemos que [crear](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-new-repository) el repositorio
en [github.com](https://github.com) y configurarlo como un repositorio remoto

```shell
luiscachog@dev-server:~$ git add *
luiscachog@dev-server:~$ git commit -m "First commit"
luiscachog@dev-server:~$ git remote add origin https://github.com/luiscachog/luiscachog.io
luiscachog@dev-server:~$ git push -u origin master
```

### Configuración del repositorio Git en el servidor productivo

Para poder ocupar los hooks de git es necesario hacer una primera copia inicial del repositorio en el que vamos a trabajar, con la particularidad de que el repositorio clonado debe ser del tipo [bare](http://www.saintsjd.com/2011/01/what-is-a-bare-git-repository/).

En nuestro servidor productivo haremos:

```shell
admin@prod-server:~$ mkdir sites
admin@prod-server:~$ cd sites
admin@prod-server:~$ git clone --bare https://github.com/luiscachog/luiscachog.io  luiscachog.io.git
```

#### Configuración del hook

1. Ya que tenemos nuestro repositorio tipo bare en el servidor productivo vamos a crear el script que mandará a llamar el hook de git.

    ```shell
    admin@prod-server:~$ cd sites/luiscachog.io.git/hooks
    admin@prod-server:~$ vim post-update
    ```

1. Y agregamos algo asi:

    ```shell
    #!/bin/bash

    GIT_REPO=$HOME/luiscachog.io.git
    WORKING_DIRECTORY=/var/www/vhosts/luiscachog.io/working_hugo
    PUBLIC_WWW=/var/www/vhosts/luiscachog.io/public_html
    BACKUP_WWW=/var/www/vhosts/luiscachog.io/backup_html
    MY_DOMAIN=luiscachog.io

    set -e

    rm -rf $WORKING_DIRECTORY
    rsync -aqz $PUBLIC_WWW/ $BACKUP_WWW
    trap "echo 'A problem occurred.  Reverting to backup.'; rsync -aqz --del $BACKUP_WWW/ $PUBLIC_WWW; rm -rf $WORKING_DIRECTORY" EXIT

    git clone $GIT_REPO $WORKING_DIRECTORY
    mkdir -p $WORKING_DIRECTORY/themes
    rm -rf $PUBLIC_WWW/*
    /home/admin/bin/hugo -v -s $WORKING_DIRECTORY -d $PUBLIC_WWW -b "http://${MY_DOMAIN}"
    trap - EXIT
    ```

1. Damos permisos de ejecución al script

    ```shell
    admin@prod-server:~$ chmod +x post-update
    ```

1. Probamos que nuestro script funcione adecuadamente:

    ```shell
    admin@prod-server:~$ ~/sites/luiscachog.io.git/hooks/post-update

    Cloning into '/var/www/vhosts/luiscachog.io/working_hugo'...
    done.
    0 draft content
    0 future content
    4 pages created
    0 paginator pages created
    0 tags created
    1 categories created
    in 26 ms
    ```

1. Podras verificar tu nuevo post en la URL de su sitio:

    ```shell
    http://production_domain_or_IP
    ```

### Configuración del repositorio Git en el servidor de desarrollo 2da parte

Una vez tenemos configurado nuestro repositorio en el servidor de producción, procedemos a agregarlo como repositorio remoto en nuestro servidor de desarrollo

```shell
luiscachog@dev-server:~$ cd /home/luiscachog/sites/luiscachog.io
luiscachog@dev-server:~$ git remote add prod admin@IP_servidor_productivo:luiscachog.io
luiscachog@dev-server:~$ git ls-remote prod
d1b0b73528ab3117170ef74e133d0194dd2bc88a    HEAD
d1b0b73528ab3117170ef74e133d0194dd2bc88a    refs/heads/master
```

Puedes verificar los repositorios remotos con el comando:

```shell
luiscachog@dev-server:~$ git remote -v
origin  git@github.com:luiscachog/luiscachog.io.git (fetch)
origin  git@github.com:luiscachog/luiscachog.io.git (push)
prod    admin@IP_servidor_productivo:luiscachog.io.git (fetch)
prod    admin@IP_servidor_productivo:luiscachog.io.git (push)
```

Ahora cada vez que realizemos un push hacia el remote llamado 'prod' se llamara la función del hook.

```shell
luiscachog@dev-server:~$ cd /home/luiscachog/sites/luiscachog.io
luiscachog@dev-server:~$ hugo new posts/Testing-Deployment.md
luiscachog@dev-server:~$ echo "Deployment Test" >> content/posts/Testing-Deployment.md
luiscachog@dev-server:~$ git add *
luiscachog@dev-server:~$ git commit -m 'Deployment test with git hooks'
```

Este es el comando que hace la magia:

```shell
luiscachog@dev-server:~$ git push prod master
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 310 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Cloning into '/var/www/vhosts/luiscachog.io/working_hugo'...
remote: done.
remote: Cloning into '/var/www/vhosts/luiscachog.io/working_hugo/themes/hugo-future-imperfect'...
remote: INFO 2018/03/01 03:12:34 Using config file: /var/www/vhosts/luiscachog.io/working_hugo/config.toml
remote: Building sites … INFO 2018/03/01 03:12:34 syncing static files to /var/www/vhosts/luiscachog.io/public_html/
remote:
remote:                    | EN
remote: +------------------+----+
remote:   Pages            | 10
remote:   Paginator pages  |  0
remote:   Non-page files   |  0
remote:   Static files     |  3
remote:   Processed images |  0
remote:   Aliases          |  1
remote:   Sitemaps         |  1
remote:   Cleaned          |  0
remote:
remote: Total in 44 ms
To admin@IP_servidor_productivo:luiscachog.io.git
   d5b0671..cvc4dee  master -> master
```

Listo ya podemos probar nuestro sitio

```shell
http://luiscachog.io
```

Con esto el siguiente paso que realizare es hacer el deployment de mi servidor para el blog usando Ansible.

Nos Vemos!!!

**References:**
Digital Ocean blog post [^1]

[^1]: [Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-hugo-site-to-production-with-git-hooks-on-ubuntu-14-04)
