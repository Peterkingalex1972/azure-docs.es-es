---
title: 'Tutorial: Inicio del Lector inmersivo mediante Python'
titleSuffix: Azure Cognitive Services
description: En este tutorial, creará una aplicación de Python que inicia el Lector inmersivo.
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 08/02/2019
ms.author: dylankil
ms.openlocfilehash: 5e33108c9fc674abaf980a1272cca31aa21cffff
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991132"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Tutorial: Inicio del Lector inmersivo con el proyecto de ejemplo de Python

En la [introducción](./overview.md), se indica lo que es el Lector inmersivo y cómo implementa técnicas demostradas para mejorar la comprensión lectora de nuevos lectores, alumnos de idiomas y alumnos con diferencias de aprendizaje. En este tutorial se explica cómo crear una aplicación web de Python que inicia el Lector inmersivo. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una aplicación web de Python con Pip, Flask, Jinja y virtualenv mediante un proyecto de ejemplo
> * Adquisición de un token de acceso
> * Iniciar el Lector inmersivo con contenido de muestra

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

* Un recurso de Lector inmersivo configurado para la autenticación de Azure Active Directory (Azure AD). Siga [estas instrucciones](./azure-active-directory-authentication.md) para realizar la configuración. Necesitará algunos de los valores que se crean aquí cuando configure las propiedades del entorno. Guarde la salida de la sesión en un archivo de texto para futuras referencias.
* [Git](https://git-scm.com/)
* [SDK del Lector inmersivo](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) y [pip](https://docs.python.org/3/installing/index.html). A partir de Python 3.4, pip se incluye de forma predeterminada con los instaladores binarios de Python.
* [Flask](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) y [virtualenvwrapper-win para Windows](https://pypi.org/project/virtualenvwrapper-win/) o [virtualenvwrapper para OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [Módulo requests](https://pypi.org/project/requests/2.7.0/)
* Un IDE como [Visual Studio Code](https://code.visualstudio.com/)

## <a name="acquire-an-azure-ad-authentication-token"></a>Adquisición de un token de autenticación de Azure AD

Escriba una API de back-end para recuperar un token de autenticación de Azure AD.

Necesita algunos valores del paso del requisito previo de configuración de la autenticación de Azure AD para esta parte. Vuelva a consultar el archivo de texto que ha guardado de esa sesión.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Una vez que tenga estos valores, cree un nuevo archivo llamado _.env_ y pegue el siguiente código en él, proporcionando sus valores de propiedad personalizados desde arriba. Reemplace el archivo _.env_ de la aplicación de ejemplo por el archivo recién creado.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Asegúrese de no confirmar este archivo en el control de código fuente, ya que contiene secretos que no deben hacerse públicos.

El punto de conexión de la API **getimmersivereadertoken** debe estar protegido mediante algún tipo de autenticación (por ejemplo, [OAuth](https://oauth.net/2/)) para evitar que usuarios no autorizados obtengan tokens para utilizarlos contra el servicio Lector inmersivo y su facturación; este trabajo está fuera del ámbito de este tutorial.

## <a name="create-a-python-web-app-on-windows"></a>Creación de una aplicación web de Python en Windows

Cree una aplicación web de Python con `flask` en Windows.

Instale [Git](https://git-scm.com/).

Una vez instalado Git, abra un símbolo del sistema y clone el repositorio Git del SDK del Lector inmersivo en una carpeta del equipo.

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Instale [Python](https://www.python.org/downloads/).

Active la casilla Add Python to PATH (Agregar Python a PATH).

![Cuadro de diálogo de instalación de Python en Windows, paso 1](./media/pythoninstallone.jpg)

Agregue características opcionales activando las casillas correspondientes y haga clic en el botón 'Next' (Siguiente).

![Cuadro de diálogo de instalación de Python en Windows, paso 2](./media/pythoninstalltwo.jpg)

Elija 'Custom installation’ (Instalación personalizada) y establezca la ruta de acceso de instalación como carpeta raíz (por ejemplo, `C:\Python37-32\`) y haga clic en el botón 'Install’ (Instalar).

![Cuadro de diálogo de instalación de Python en Windows, paso 3](./media/pythoninstallthree.jpg)

Una vez finalizada la instalación de Python, abra un símbolo del sistema y vaya con el comando `cd` a la carpeta Scripts de Python.

```cmd
cd C:\Python37-32\Scripts
```

Instale Flask.

```cmd
pip install flask
```

Instale Jinja2. Se trata de un motor de plantillas completo para Python.

```cmd
pip install jinja2
```

Instale virtualenv. Se trata de una herramienta para crear entornos de Python aislados.

```cmd
pip install pip install virtualenv
```

Instale virtualenvwrapper-win. Con virtualenvwrapper se quiere facilitar el uso de virtualenv.

```cmd
pip install virtualenvwrapper-win
```

Instale el módulo requests. Requests es una biblioteca HTTP con licencia de Apache2, escrita en Python.

```cmd
pip install requests
```

Cree un entorno virtual.

```cmd
mkvirtualenv advanced-python
```

Vaya con el comando `cd` a la carpeta raíz del proyecto de ejemplo.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Conecte el proyecto de ejemplo con el entorno. De este modo se asigna el entorno virtual recién creado a la carpeta raíz del proyecto de ejemplo.

```cmd
setprojectdir .
```

Active el entorno virtual.

```cmd
activate
```

Ahora el proyecto debe estar activo; verá algo parecido a `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` en el símbolo del sistema.

Desactive el entorno.

```cmd
deactivate
```

Ahora debe desaparecer el prefijo `(advanced-python)`, ya que el entorno ha quedado desactivado.

Para volver a activar el entorno, ejecute `workon advanced-python` desde la carpeta raíz del proyecto de ejemplo.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>Iniciar el Lector inmersivo con contenido de muestra

Cuando el entorno esté activo, ejecute el proyecto de ejemplo escribiendo `flask run` en la carpeta raíz del proyecto de ejemplo.

```cmd
flask run
```

Abra el explorador web y vaya a _http://localhost:5000_ .

## <a name="create-a-python-web-app-on-osx"></a>Creación de una aplicación web de Python en OSX

Cree una aplicación web de Python con `flask` en OSX.

Instale [Git](https://git-scm.com/).

Una vez instalado Git, abra el terminal y clone el repositorio Git del SDK del Lector inmersivo en una carpeta del equipo.

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Instale [Python](https://www.python.org/downloads/).

La carpeta raíz de Python, `Python37-32` por ejemplo, debe estar ahora en la carpeta de aplicaciones.

Una vez finalizada la instalación de Python, abra el terminal y vaya con el comando `cd` a la carpeta Scripts de Python.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Instale PIP.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

A continuación, ejecute los siguientes comandos para instalar pip para el usuario que tiene actualmente iniciada la sesión, con el fin de evitar problemas de permisos.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Escriba la contraseña cuando se le solicite.
- Agregue la ruta de acceso de la instalación de pip a la variable PATH.
- Vaya al final del archivo y escriba, como último elemento de la lista, la ruta de acceso que desea agregar, por ejemplo, `PATH=$PATH:/usr/local/bin`.
- Presione Control-X para salir.
- Escriba `Y` para guardar el búfer modificado.
- Eso es todo. Para probarlo, escriba `echo $PATH` en una nueva ventana de terminal.

Instale Flask.

```bash
pip install flask --user
```

Instale Jinja2. Se trata de un motor de plantillas completo para Python.

```bash
pip install Jinja2 --user
```

Instale virtualenv. Se trata de una herramienta para crear entornos de Python aislados.

```bash
pip install virtualenv --user
```

Instale virtualenvwrapper. Con virtualenvwrapper se quiere facilitar el uso de virtualenv.

```bash
pip install virtualenvwrapper --user
```

Instale el módulo requests. Requests es una biblioteca HTTP con licencia de Apache2, escrita en Python.

```bash
pip install requests --user
```

Elija la carpeta donde desea conservar los entornos virtuales y ejecute este comando.

```bash
mkdir ~/.virtualenvs
```

Vaya con el comando `cd` a la carpeta de aplicación de ejemplo Python del SDK de Lector inmersivo.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Cree un entorno virtual.

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Conecte el proyecto de ejemplo con el entorno. De este modo se asigna el entorno virtual recién creado a la carpeta raíz del proyecto de ejemplo.

```bash
setprojectdir .
```

Active el entorno virtual.

```bash
activate
```

Ahora el proyecto debe estar activo; verá algo parecido a `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` en el símbolo del sistema.

Desactive el entorno.

```bash
deactivate
```

Ahora debe desaparecer el prefijo `(advanced-python)`, ya que el entorno ha quedado desactivado.

Para volver a activar el entorno, ejecute `workon advanced-python` desde la carpeta raíz del proyecto de ejemplo.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Iniciar el Lector inmersivo con contenido de muestra

Cuando el entorno esté activo, ejecute el proyecto de ejemplo escribiendo `flask run` en la carpeta raíz del proyecto de ejemplo.

```bash
flask run
```

Abra el explorador web y vaya a _http://localhost:5000_ .

## <a name="next-steps"></a>Pasos siguientes

* Explorar el [SDK del Lector inmersivo](https://github.com/microsoft/immersive-reader-sdk) y agregar la [Referencia del SDK del Lector inmersivo](./reference.md)
* Ver muestras de código en [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
