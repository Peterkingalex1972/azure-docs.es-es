---
title: 'Azure Container Registry: preguntas más frecuentes'
description: Respuestas a las preguntas más frecuentes relacionadas con el servicio Azure Container Registry
services: container-registry
author: sajayantony
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: sajaya
ms.openlocfilehash: 2b835765bbd40ffbd4a5117f767a7ba163e41dda
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309284"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Preguntas más frecuentes sobre Azure Container Registry

En este artículo se abordan las preguntas más frecuentes y los problemas conocidos sobre Azure Container Registry.

## <a name="resource-management"></a>Administración de recursos

- [¿Se puede crear una instancia de Azure Container Registry con una plantilla de Resource Manager?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [¿Existe un examen de vulnerabilidades de seguridad para imágenes en ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [¿Cómo se configura Kubernetes con Azure Container Registry?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [¿Cómo se pueden obtener las credenciales de administrador para un registro de contenedor?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [¿Cómo se pueden obtener las credenciales de administrador de una plantilla de Resource Manager?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [La eliminación de la replicación produce un error con el estado Prohibido aunque la replicación se elimina mediante la CLI de Azure o Azure PowerShell.](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [Las reglas de Firewall se actualizaron correctamente, pero no entran en vigor](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>¿Se puede crear una instancia de Azure Container Registry con una plantilla de Resource Manager?

Sí. Esta es [una plantilla](https://github.com/Azure/azure-cli/blob/master/src/command_modules/azure-cli-acr/azure/cli/command_modules/acr/template.json) que puede usar para crear un registro.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>¿Existe un examen de vulnerabilidades de seguridad para imágenes en ACR?

Sí. Consulte la documentación de [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) y [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>¿Cómo se configura Kubernetes con Azure Container Registry?

Consulte la documentación de [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) y los pasos para [Azure Kubernetes Service](container-registry-auth-aks.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>¿Cómo se pueden obtener las credenciales de administrador para un registro de contenedor?

> [!IMPORTANT]
> La cuenta de usuario administrador está diseñada para que un solo usuario acceda al registro, principalmente con fines de prueba. No se recomienda compartir las credenciales de cuenta de administrador con varios usuarios. Se recomienda la identidad individual para usuarios y entidades de servicio para escenarios desatendidos. Consulte [Introducción a la autenticación](container-registry-authentication.md).

Antes de obtener las credenciales de administrador, asegúrese de que el usuario administrador del registro esté habilitado.

Para obtener las credenciales mediante la CLI de Azure:

```azurecli
az acr credential show -n myRegistry
```

Uso de Azure PowerShell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>¿Cómo se pueden obtener las credenciales de administrador de una plantilla de Resource Manager?

> [!IMPORTANT]
> La cuenta de usuario administrador está diseñada para que un solo usuario acceda al registro, principalmente con fines de prueba. No se recomienda compartir las credenciales de cuenta de administrador con varios usuarios. Se recomienda la identidad individual para usuarios y entidades de servicio para escenarios desatendidos. Consulte [Introducción a la autenticación](container-registry-authentication.md).

Antes de obtener las credenciales de administrador, asegúrese de que el usuario administrador del registro esté habilitado.

Para obtener la primera contraseña:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Para obtener la segunda contraseña:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>La eliminación de la replicación produce un error con el estado Prohibido aunque la replicación se elimina mediante la CLI de Azure o Azure PowerShell.

El error se observa cuando el usuario tiene permisos en un registro pero no tiene permisos a nivel de lector en la suscripción. Para resolver este problema, asigne al usuario los permisos de lector en la suscripción:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>Las reglas de Firewall se actualizaron correctamente, pero no entran en vigor

Lleva algún tiempo propagar los cambios en las reglas del firewall. Después de cambiar la configuración del firewall, espere unos minutos antes de comprobar este cambio.


## <a name="registry-operations"></a>Operaciones de registro

- [¿Cómo se accede a Docker Registry HTTP API V2?](#how-do-i-access-docker-registry-http-api-v2)
- [¿Cómo se pueden eliminar los manifiestos sin referencia de etiquetas en un repositorio?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [¿Por qué no se reduce el uso de cuota del registro después de eliminar las imágenes?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [¿Cómo se pueden validar los cambios de cuota de almacenamiento?](#how-do-i-validate-storage-quota-changes)
- [¿Cómo se autentica con el registro cuando se ejecuta la CLI en un contenedor?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [¿Ofrece Azure Container Registry la única configuración de TLS v1.2 y cómo habilita TLS v1.2?](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [¿Admite Azure Container Registry la confianza en el contenido?](#does-azure-container-registry-support-content-trust)
- [¿Cómo se puede conceder acceso para extraer o insertar imágenes sin permiso para administrar el recurso del registro?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [¿Cómo se habilita la cuarentena automática de imágenes para un registro?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>¿Cómo se accede a Docker Registry HTTP API V2?

ACR admite Docker Registry HTTP API V2. Se puede acceder a las API en `https://<your registry login server>/v2/`. Ejemplo: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>¿Cómo se pueden eliminar los manifiestos sin referencia de etiquetas en un repositorio?

Si se encuentra en bash:

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Para PowerShell:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Nota: Puede agregar `-y` en el comando de eliminación para omitir la confirmación.

Para más información, consulte [Eliminación de imágenes de contenedor en Azure Container Registry](container-registry-delete.md)

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>¿Por qué no se reduce el uso de cuota del registro después de eliminar las imágenes?

Esta situación puede ocurrir si otras imágenes de contenedor hacen referencia a las capas subyacentes. Si elimina una imagen sin referencias, el uso del registro se actualiza en unos minutos.

### <a name="how-do-i-validate-storage-quota-changes"></a>¿Cómo se pueden validar los cambios de cuota de almacenamiento?

Cree una imagen con una capa de 1 GB mediante el siguiente archivo de Docker. Esto asegura que la imagen tiene una capa que no comparte ninguna otra imagen en el registro.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Cree e inserte la imagen en el registro mediante la CLI de Docker.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Debería poder ver que el uso del almacenamiento ha aumentado en Azure Portal, o puede consultar el uso mediante la CLI.

```bash
az acr show-usage -n myregistry
```

Elimine la imagen con la CLI o Azure Portal y compruebe el uso actualizado en pocos minutos.

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>¿Cómo se autentica con el registro cuando se ejecuta la CLI en un contenedor?

Para ejecutar el contenedor de la CLI de Azure, monte el socket de Docker:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

En el contenedor, instale `docker`:

```bash
apk --update add docker
```

A continuación, autentíquese con el registro:

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>¿Ofrece Azure Container Registry la única configuración de TLS v1.2 y cómo habilita TLS v1.2?

Sí. Habilite TLS mediante cualquier cliente de Docker reciente (versión 18.03.0 y posterior). 

### <a name="does-azure-container-registry-support-content-trust"></a>¿Admite Azure Container Registry la confianza en el contenido?

Sí, puede usar las imágenes de confianza en Azure Container Registry, ya que [Docker Notary](https://docs.docker.com/notary/getting_started/) se ha integrado y se puede habilitar. Para más información, consulte [Confianza de contenido en Azure Container Registry](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>¿Dónde se encuentra el archivo de la huella digital?

En `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* Las claves públicas y los certificados de todas las funciones (excepto los roles de delegación) se almacenan en `root.json`.
* Las claves públicas y los certificados del rol de delegación se almacenan en el archivo JSON del rol principal (por ejemplo `targets.json` para el rol `targets/releases`).

Se recomienda comprobar esas claves públicas y certificados después de la comprobación TUF general realizada por el cliente de Docker y Notary.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>¿Cómo se puede conceder acceso para extraer o insertar imágenes sin permiso para administrar el recurso del registro?

ACR admite los [roles personalizados](container-registry-roles.md) que proporcionan diferentes niveles de permisos. Específicamente, los roles `AcrPull` y `AcrPush` permiten a los usuarios extraer o insertar imágenes sin permiso para administrar el recurso del registro en Azure.

* Azure Portal: Su registro -> Control de acceso (IAM) -> Agregar (seleccione `AcrPull` o `AcrPush` para el rol).
* CLI de Azure: Busque el identificador del recurso del registro mediante el siguiente comando:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Después, puede asignar el rol de `AcrPull` o `AcrPush` a un usuario (en el ejemplo siguiente se usa `AcrPull`):

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  O bien, asigne el rol a una entidad de servicio identificada por su identificador de la aplicación:

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

El usuario asignado es capaz entonces de autenticar y acceder a las imágenes en el registro.

* Para autenticarse en un registro:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Para enumerar los repositorios:

  ```azurecli
  az acr repository list -n myRegistry
  ```

 Para extraer una imagen:
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

Con el uso solo del rol `AcrPull` o `AcrPush`, el usuario asignado no tiene permiso para administrar el recurso del registro en Azure. Por ejemplo, `az acr list` o `az acr show -n myRegistry` no mostrarán el registro.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>¿Cómo se habilita la cuarentena automática de imágenes para un registro?

La cuarentena de imágenes actualmente es una característica de versión preliminar de ACR. Puede habilitar el modo de cuarentena de un registro para que solo las imágenes que han pasado correctamente el análisis de seguridad sean visibles para los usuarios normales. Para más información, consulte el [repositorio de GitHub para ACR](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

## <a name="diagnostics-and-health-checks"></a>Comprobaciones de mantenimiento y diagnóstico

- [Comprobación de mantenimiento con `az acr check-health`](#check-health-with-az-acr-check-health)
- [Se produce este error en el comando docker pull: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers) [net/http: la solicitud se canceló mientras se esperaba la conexión (Client.Timeout excedido mientras se esperan los encabezados)]](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [El comando docker push se ha ejecutado correctamente pero docker pull ha producido el siguiente error: unauthorized: authentication required (no autorizado: se necesita autorización)](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [Habilitación y obtención de los registros de depuración del demonio de Docker](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Los nuevos permisos de usuario pueden no ser efectivos inmediatamente después de la actualización.](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [La información de autenticación no se proporciona en el formato correcto en las llamadas a la API REST directas.](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [¿Por qué Azure Portal no muestra todos los repositorios o etiquetas?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [¿Cómo se pueden recopilar los seguimientos http en Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Comprobación de mantenimiento con `az acr check-health`

Para solucionar problemas de registro y de entorno comunes, consulte [Check the health of an Azure container registry](container-registry-check-health.md) (Comprobación del mantenimiento de un registro de contenedor de Azure).

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>Se produce este error en el comando docker pull: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers) [net/http: la solicitud se canceló mientras se esperaba la conexión (Client.Timeout excedido mientras se esperan los encabezados)]

 - Si este error es un problema transitorio, entonces el reintento se realizará correctamente.
 - Si `docker pull` produce un error continuamente, entonces podría haber un problema con el demonio de Docker. Por lo general, el problema se puede mitigar con el reinicio del demonio de Docker. 
 - Si continúa apareciendo este problema después de reiniciar el demonio de Docker, el problema podría tener que ver con algunos problemas de conectividad de red con la máquina. Para comprobar si la red general de la máquina está en buen estado, ejecute el siguiente comando para probar la conectividad de los puntos de conexión. La versión de `az acr` mínima que contiene este comando de comprobación de conectividad es 2.2.9. Actualice la CLI de Azure si está usando una versión anterior.
 
   ```azurecli
    az acr check-health -n myRegistry
    ```
 - Siempre debe tener un mecanismo de reintento en todas las operaciones del cliente de Docker.

### <a name="docker-pull-is-slow"></a>El comando docker pull es lento
Utilice [esta](http://www.azurespeed.com/Azure/Download) herramienta para probar la velocidad de descarga de la red de la máquina. Si la red de la máquina es lenta, considere usar la máquina virtual de Azure en la misma región que el registro. Esto generalmente le da una velocidad de red más rápida.

### <a name="docker-push-is-slow"></a>El comando docker push es lento
Utilice [esta](http://www.azurespeed.com/Azure/Upload) herramienta para probar la velocidad de carga de la red de la máquina. Si la red de la máquina es lenta, considere usar la máquina virtual de Azure en la misma región que el registro. Esto generalmente le da una velocidad de red más rápida.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>El comando docker push se ha ejecutado correctamente pero docker pull ha producido el siguiente error: unauthorized: authentication required (no autorizado: se necesita autorización)

Este error puede ocurrir con la versión de Red Hat del demonio de Docker, donde `--signature-verification` está habilitado de forma predeterminada. Puede comprobar las opciones del demonio de Docker para Red Hat Enterprise Linux (RHEL) o Fedora mediante el siguiente comando:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Por ejemplo, el servidor de Fedora 28 tiene las siguientes opciones de demonio de Docker:

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

Con `--signature-verification=false` que falta, `docker pull` produce un error similar al siguiente:

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Para resolver el error:
1. Agregue la opción `--signature-verification=false` al archivo de configuración del demonio de Docker `/etc/sysconfig/docker`. Por ejemplo:

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. Reinicie el servicio del demonio de Docker con el comando siguiente:

  ```bash
  sudo systemctl restart docker.service
  ```

Para encontrar los detalles de `--signature-verification`, ejecute `man dockerd`.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Habilitación y obtención de los registros de depuración del demonio de Docker  

Inicie `dockerd` con la opción `debug`. En primer lugar, cree el archivo de configuración del demonio de Docker (`/etc/docker/daemon.json`) si no existe y agregue la opción `debug`:

```json
{   
    "debug": true   
}
```

Después, reinicie el demonio. Por ejemplo, con Ubuntu 14.04:

```bash
sudo service docker restart
```

Se puede encontrar más información en la [documentación de Docker](https://docs.docker.com/engine/admin/#enable-debugging). 

 * Los registros se pueden generar en ubicaciones diferentes, dependiendo del sistema. Por ejemplo, para Ubuntu 14.04, es `/var/log/upstart/docker.log`:   
Consulte la [documentación de Docker](https://docs.docker.com/engine/admin/#read-the-logs) para obtener más información.    

 * Par Docker para Windows, los registros se generan en %LOCALAPPDATA%/docker/. Sin embargo, es posible que aún no contenga toda la información de depuración.   

   Para acceder al registro completo del demonio, es posible que necesite algunos pasos adicionales:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Ahora tiene acceso a todos los archivos de la máquina virtual que ejecutan `dockerd`. El registro está en `/var/log/docker.log`.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Los nuevos permisos de usuario pueden no ser efectivos inmediatamente después de la actualización.

Cuando concede nuevos permisos (nuevos roles) a una entidad de servicio, es posible que el cambio no tenga efecto inmediatamente. Hay dos razones posibles:

* Retraso de asignación de roles de Azure Active Directory. Normalmente es rápido, pero puede tardar algunos minutos debido a un retraso de propagación.
* Retraso de permisos en el servidor de tokens de ACR. Esto puede tardar hasta 10 minutos. Para mitigarlo, puede `docker logout` y después autenticarse de nuevo con el mismo usuario después de un minuto:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Actualmente ACR no admite la eliminación de la replicación principal por parte de los usuarios. La solución consiste en incluir la réplica principal creada en la plantilla pero omitir su creación al agregar `"condition": false` como se muestra a continuación:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>La información de autenticación no se proporciona en el formato correcto en las llamadas a la API REST directas.

Puede encontrar un error `InvalidAuthenticationInfo`, especialmente con la herramienta `curl` con la opción `-L`, `--location` (para seguir las redirecciones).
Por ejemplo, capturar el blob mediante `curl` con la opción `-L` y autenticación básica:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

puede dar lugar a la siguiente respuesta:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

La causa principal es que algunas implementaciones `curl` siguen las redirecciones con encabezados de la solicitud original.

Para resolver el problema, debe seguir las redirecciones manualmente sin los encabezados. Imprima los encabezados de respuesta con la opción `-D -` de `curl` y después extraiga el encabezado `Location`:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>¿Por qué Azure Portal no muestra todos los repositorios o etiquetas? 

Si utiliza el explorador Microsoft Edge o Internet Explorer, puede ver un máximo de 100 repositorios o etiquetas. Si el registro tiene más de 100 repositorios o etiquetas, le recomendamos que utilice el explorador Firefox o Chrome para enumerarlos todos.

### <a name="how-do-i-collect-http-traces-on-windows"></a>¿Cómo se pueden recopilar los seguimientos http en Windows?

#### <a name="prerequisites"></a>Requisitos previos

- Habilite el descifrado de https en Fiddler: <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Habilite Docker para que use un proxy mediante la interfaz de usuario de Docker: <https://docs.docker.com/docker-for-windows/#proxies>
- Asegúrese de revertirlo cuando termine.  Docker no funcionará con esto habilitado y Fiddler no se ejecutará.

#### <a name="windows-containers"></a>Contenedores de Windows

Configure el proxy de Docker en 127.0.0.1:8888.

#### <a name="linux-containers"></a>Contenedores de Linux

Busque la dirección IP de Docker del conmutador virtual de la máquina virtual de Docker:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Configure el proxy de Docker para la salida del comando anterior y el puerto 8888 (por ejemplo 10.0.75.1:8888).

## <a name="tasks"></a>Tareas

- [¿Cómo se realizan las cancelaciones por lotes?](#how-do-i-batch-cancel-runs)
- [¿Cómo se puede incluir la carpeta .git en el comando az acr build?](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>¿Cómo se realizan las cancelaciones por lotes?

Los siguientes comandos cancelan todas las tareas en ejecución en el registro especificado.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>¿Cómo se puede incluir la carpeta .git en el comando az acr build?

Si se pasa una carpeta de origen local al comando `az acr build`, la carpeta `.git` se excluye del paquete cargado de forma predeterminada. Puede crear un archivo `.dockerignore` con la siguiente configuración. Indica al comando que restaure todos los archivos bajo `.git` en el paquete cargado. 

```
!.git/**
```

Esta configuración también se aplica al comando `az acr run`.

## <a name="cicd-integration"></a>Integración de CI/CD

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [Acciones de GitHub](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Pasos siguientes

* [Más información](container-registry-intro.md) acerca de Azure Container Registry.