---
title: Trabajar con el registro de Docker
intro: '{% ifversion fpt %}El registro de Docker ahora se reemplazó con el {% data variables.product.prodname_container_registry %}.{% else %}Puedes subir y extraer tus imágenes de Docker utilizando el registro de Docker del {% data variables.product.prodname_registry %}, el cual utiliza el designador de nombre `https://docker.pkg.github.com`.{% endif %}'
product: '{% data reusables.gated-features.packages %}'
redirect_from:
  - /articles/configuring-docker-for-use-with-github-package-registry
  - /github/managing-packages-with-github-package-registry/configuring-docker-for-use-with-github-package-registry
  - /github/managing-packages-with-github-packages/configuring-docker-for-use-with-github-packages
  - /packages/using-github-packages-with-your-projects-ecosystem/configuring-docker-for-use-with-github-packages
  - /packages/guides/container-guides-for-github-packages/configuring-docker-for-use-with-github-packages
  - /packages/guides/configuring-docker-for-use-with-github-packages
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
shortTitle: Registro de Docker
---

<!-- Main versioning block. Short page for dotcom -->
{% ifversion fpt %}

El registro de Docker de {% data variables.product.prodname_dotcom %} (el cual utilizó el designador de nombre `docker.pkg.github.com`) se reemplazó con el {% data variables.product.prodname_container_registry %} (el cual utiliza el designador de nombre `https://ghcr.io`). El {% data variables.product.prodname_container_registry %} ofrece beneficios tales como permisos granulares y optimización de almacenamiento para las imágenes de Docker.

Las imágenes de Docker que se almacenaron previamente en el registro de Docker se están migrando automáticamente al {% data variables.product.prodname_container_registry %}. Para obtener más información, consulta la sección "[Migrarse al {% data variables.product.prodname_container_registry %} desde el registro de Docker](/packages/working-with-a-github-packages-registry/migrating-to-the-container-registry-from-the-docker-registry)" y [Trabajar con el {% data variables.product.prodname_container_registry %}](/packages/working-with-a-github-packages-registry/working-with-the-container-registry)".

{% else %}
<!-- The remainder of this article is displayed for releases that don't support the Container registry -->

{% data reusables.package_registry.packages-ghes-release-stage %}
{% data reusables.package_registry.packages-ghae-release-stage %}

**Nota:** Cuando instalas o publicas una imagen de docker, {% data variables.product.prodname_registry %} no es compatible con capas externas, tales como imágenes de Windows.

## Acerca de la compatibilidad de Docker

Cuando instalas o publicas una imagen de Docker, el registro de Docker no es compatible actualmente con capas ajenas, tales como imágenes de Windows.

{% ifversion ghes = 2.22 %}

Antes de que puedas utilizar el registro de Docker en el {% data variables.product.prodname_registry %}, el administrador de sitio de {% data variables.product.product_location %} debe habilitar la compatibilidad de Docker y el aislamiento de subdominios para tu instancia. Para obtener más informacón, consulta la sección "[Administrar GitHub Packages para tu empresa](/enterprise/admin/packages)".

{% endif %}

## Autenticarte en {% data variables.product.prodname_registry %}

{% data reusables.package_registry.authenticate-packages %}

{% data reusables.package_registry.authenticate-packages-github-token %}

### Autenticarte con un token de acceso personal

{% data reusables.package_registry.required-scopes %}

Puedes autenticar a {% data variables.product.prodname_registry %} con Docker usando el comando de inicio de sesión `Docker`.

Para mantener tus credenciales seguras, te recomendamos que guardes tu token de acceso personal en un archivo local de tu computadora y utilices el marcador `--password-stdin` de Docker, el cual lee tu token desde un archivo local.

{% ifversion fpt %}
{% raw %}
  ```shell
  $ cat <em>~/TOKEN.txt</em> | docker login https://docker.pkg.github.com -u <em>USERNAME</em> --password-stdin
  ```
{% endraw %}
{% endif %}

{% ifversion ghes or ghae %}
{% ifversion ghes > 2.22 %}
Para obtener más información acerca de cómo crear un paquete, consulta la [documentación maven.apache.org](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html).
{% endif %}
{% raw %}
 ```shell
 $ docker images

> REPOSITORY           TAG      IMAGE ID      CREATED      SIZE
> monalisa             1.0      c75bebcdd211  4 weeks ago  1.11MB

# Etiqueta la imagen con <em>OWNER/REPO/IMAGE_NAME</em>
$ docker tag c75bebcdd211 docker.pkg.github.com/octocat/octo-app/monalisa:1.0

# Sube la imagen a {% data variables.product.prodname_registry %}
$ docker push docker.pkg.github.com/octocat/octo-app/monalisa:1.0
```
{% endraw %}
{% ifversion ghes > 2.22 %}
Por ejemplo, los proyectos *OctodogApp* y *OctocatApp* publicarán en el mismo repositorio:

{% raw %}
 ```shell
 $ cat <em>~/TOKEN.txt</em> | docker login <em>HOSTNAME</em> -u <em>USERNAME</em> --password-stdin
```
{% endraw %}
{% endif %}

{% endif %}

Para utilizar este ejemplo de comando de ingreso, reemplaza `USERNAME` con tu nombre de usuario de {% data variables.product.product_name %}{% ifversion ghes or ghae %}, `HOSTNAME` con la URL de {% data variables.product.product_location %},{% endif %} y `~/TOKEN.txt` con la ruta de archivo de tu token de acceso personal para {% data variables.product.product_name %}.

Para obtener más información, consulta la sección "[Inicio de sesión de Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)."

## Publicar una imagen

{% data reusables.package_registry.docker_registry_deprecation_status %}

{% note %}

**Nota:** Los nombres de imagen deben utilizar letras en minúscula únicamente.

{% endnote %}

{% data variables.product.prodname_registry %} admite varias imágenes Docker de primer nivel por repositorio. Un repositorio puede tener cualquier cantidad de etiquetas de imagen. Puedes experimentar un servicio de menor calidad al publicar o instalar imágenes de Docker de más de 10 GB, las capas tienen un límite de 5 GB cada una. Para obtener más información, consulta "[Etiqueta Docker](https://docs.docker.com/engine/reference/commandline/tag/)" en la documentación de Docker.

{% data reusables.package_registry.viewing-packages %}

1. Determina el nombre y la ID de la imagen Docker utilizando `imágenes docker`.
  ```shell
  $ docker images
  > <&nbsp>
  > REPOSITORY        TAG        IMAGE ID       CREATED      SIZE
  > <em>IMAGE_NAME</em>        <em>VERSION</em>    <em>IMAGE_ID</em>       4 weeks ago  1.11MB
  ```
2. Utilizando la ID de la imagen de Docker, etiqueta a la imagen de docker, reemplazando *OWNER* con el nombre de la cuenta de usuario u organización a la que pertenece el repositorio, *REPOSITORY* con el nombre del repositorio que contiene tu proyecto, *IMAGE_NAME* con el nombre del paquete o imagen, {% ifversion ghes or ghae %} *HOSTNAME* con el nombre de host de {% data variables.product.product_location %}{% endif %} y *VERSION* con la versión del paquete y hora de compilción.
  {% ifversion fpt %}
  ```shell
  $ docker tag <em>IMAGE_ID</em> docker.pkg.github.com/<em>OWNER/REPOSITORY/IMAGE_NAME:VERSION</em>
  ```
  {% else %}
  {% ifversion ghes > 2.22 %}
  Para obtener más información acerca de cómo crear un paquete, consulta la [documentación maven.apache.org](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html).
  {% endif %}
  ```shell
  Puedes publicar una nueva imagen de Docker por primera vez y nombrarla <code>monalisa</code>.
  ```
.
</code>
  {% ifversion ghes > 2.22 %}
  Por ejemplo, los proyectos *OctodogApp* y *OctocatApp* publicarán en el mismo repositorio:
  ```shell
  $ docker tag <em>IMAGE_ID</em> <em>HOSTNAME/OWNER/REPOSITORY/IMAGE_NAME:VERSION</em>
  ```
  {% endif %}
  {% endif %}
3. Si aún no creas una imagen de docker para el paquete, crea la imagen, reemplazando *OWNER* con el nombre de la cuenta de organización o usuario a la que pertenece el repositorio, *REPOSITORY* con el nombre del repositorio que contiene tu proyecto, *IMAGE_NAME* con el nombre del paquete o imagen, *VERSION* con la versión de paquete al momento de la compilación,{% ifversion ghes or ghae %}*HOSTNAME* con el nombre de host de {% data variables.product.product_location %},{% endif %} y *PATH* a la imagen en caso de que no esté en el directorio de trabajo actual.
  {% ifversion fpt %}
  ```shell
  $ docker build -t docker.pkg.github.com/<em>OWNER/REPOSITORY/IMAGE_NAME:VERSION</em> <em>PATH</em>
  ```
  {% else %}
  {% ifversion ghes > 2.22 %}
  Para obtener más información acerca de cómo crear un paquete, consulta la [documentación maven.apache.org](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html).
  {% endif %}
  ```shell
  $ docker build -t docker.<em>HOSTNAME/OWNER/REPOSITORY/IMAGE_NAME:VERSION</em> <em>PATH</em>
  ```
  {% ifversion ghes > 2.22 %}
  Por ejemplo, los proyectos *OctodogApp* y *OctocatApp* publicarán en el mismo repositorio:
  ```shell
  $ docker build -t <em>HOSTNAME/OWNER/REPOSITORY/IMAGE_NAME:VERSION</em> <em>PATH</em>
  ```
  {% endif %}
  {% endif %}
4. Publicar la imagen para {% data variables.product.prodname_registry %}.
  {% ifversion fpt %}
  ```shell
  $ docker push docker.pkg.github.com/<em>OWNER/REPOSITORY/IMAGE_NAME:VERSION</em>
  ```
  {% else %}
  {% ifversion ghes > 2.22 %}
  Para obtener más información acerca de cómo crear un paquete, consulta la [documentación maven.apache.org](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html).
  {% endif %}
  ```shell
  $ docker push docker.<em>HOSTNAME/OWNER/REPOSITORY/IMAGE_NAME:VERSION</em>
  ```
  {% ifversion ghes > 2.22 %}
  Por ejemplo, los proyectos *OctodogApp* y *OctocatApp* publicarán en el mismo repositorio:
  ```shell
  $ docker push <em>HOSTNAME/OWNER/REPOSITORY/IMAGE_NAME:VERSION</em>
  ```
  {% endif %}
  {% endif %}
  {% note %}

  **Nota:** Debes subir tu imagen usando `IMAGE_NAME: VERSION` y no utilizar `IMAGE_NAME: SHA`.

  {% endnote %}

### Ejemplo de publicación de una imagen Docker

{% ifversion ghes > 2.22 %}
Estos ejemplos asumen que tu instancia tiene habilitado el aislamiento de subdominios.
{% endif %}

Puedes publicar la versión 1.0 de la imagen `monalisa` en el repositorio `octocat/octo-app` usando una ID de imagen.

{% ifversion fpt %}
```shell
$ docker images

> REPOSITORY           TAG      IMAGE ID      CREATED      SIZE
> monalisa             1.0      c75bebcdd211  4 weeks ago  1.11MB

# Etiqueta la imagen con <em>OWNER/REPO/IMAGE_NAME</em>
$ docker tag c75bebcdd211 docker.pkg.github.com/octocat/octo-app/monalisa:1.0

# Sube la imagen a {% data variables.product.prodname_registry %}
$ docker push docker.pkg.github.com/octocat/octo-app/monalisa:1.0
```

{% else %}

```shell
$ docker images

> REPOSITORY           TAG      IMAGE ID      CREATED      SIZE
> monalisa             1.0      c75bebcdd211  4 weeks ago  1.11MB

# Tag the image with <em>OWNER/REPO/IMAGE_NAME</em>
$ docker tag c75bebcdd211 docker.<em>HOSTNAME</em>/octocat/octo-app/monalisa:1.0

# Push the image to {% data variables.product.prodname_registry %}
$ docker push docker.<em>HOSTNAME</em>/octocat/octo-app/monalisa:1.0
```

{% endif %}

Puedes publicar una nueva imagen de Docker por primera vez y nombrarla `monalisa`.

{% ifversion fpt %}
```shell
# Construye la imagen con docker.pkg.github.com/<em>OWNER/REPOSITORY/IMAGE_NAME:VERSION</em>
# Asume que Dockerfile reside en el directorio de trabajo actual (.)
$ docker build -t docker.pkg.github.com/octocat/octo-app/monalisa:1.0 .
$ docker build -t docker.pkg.github.com/octocat/octo-app/monalisa:1.0 .

# Sube la imagen a {% data variables.product.prodname_registry %}
$ docker push docker.pkg.github.com/octocat/octo-app/monalisa:1.0
```

{% else %}
```shell
# Build the image with docker.<em>HOSTNAME/OWNER/REPOSITORY/IMAGE_NAME:VERSION</em>
# Assumes Dockerfile resides in the current working directory (.)
$ docker build -t docker.<em>HOSTNAME</em>/octocat/octo-app/monalisa:1.0 .

# Push the image to {% data variables.product.prodname_registry %}
$ docker push docker.<em>HOSTNAME</em>/octocat/octo-app/monalisa:1.0
```
{% endif %}

## Descargar una imagen

{% data reusables.package_registry.docker_registry_deprecation_status %}

Puedes utilizar el comando `docker pull` para instalar una imagen de docker desde el {% data variables.product.prodname_registry %}, reemplazando *OWNER* con el nombre de la cuenta de usuario u organización a la que pertenece el repositorio, *REPOSITORY* con el nombre de repositorio que contiene tu proyecto, *IMAGE_NAME* con el nombre del paquete o imagen,{% ifversion ghes or ghae %} *HOSTNAME* con el nombre del host de {% data variables.product.product_location %}, {% endif %} y *TAG_NAME* con la etiqueta de la imagen que quieres instalar.

{% ifversion fpt %}
```shell
$ docker pull docker.pkg.github.com/<em>OWNER/REPOSITORY/IMAGE_NAME:TAG_NAME</em>
```
{% else %}
<!--Versioning out this "subdomain isolation enabled" line because it's the only option for GHES 2.22 so it can be misleading.-->
{% ifversion ghes > 2.22 %}
Para obtener más información acerca de cómo crear un paquete, consulta la [documentación maven.apache.org](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html).
{% endif %}
```shell
$ docker pull docker.<em>HOSTNAME/OWNER/REPOSITORY/IMAGE_NAME:TAG_NAME</em>
```
{% ifversion ghes > 2.22 %}
Por ejemplo, los proyectos *OctodogApp* y *OctocatApp* publicarán en el mismo repositorio:
```shell
$ docker pull <em>HOSTNAME/OWNER/REPOSITORY/IMAGE_NAME:TAG_NAME</em>
```
{% endif %}
{% endif %}

{% note %}

**Nota:** Debes extraer la imagen utilizando `IMAGE_NAME:VERSION` y no así, utilizando `IMAGE_NAME:SHA`.

{% endnote %}

## Leer más

- "{% ifversion fpt or ghes > 3.0 %}[Borrar y restaurar un paquete](/packages/learn-github-packages/deleting-and-restoring-a-package){% elsif ghes < 3.1 or ghae %}[Borrar un paquete](/packages/learn-github-packages/deleting-a-package){% endif %}"

{% endif %}  <!-- End of main versioning block -->
