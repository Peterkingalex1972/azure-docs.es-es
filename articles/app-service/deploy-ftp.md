---
title: Implementación de contenido en Azure App Service con FTP/S | Microsoft Docs
description: Aprenda a implementar la aplicación en Azure App Service mediante FTP o FTPS.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/30/2018
ms.author: cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: f239fb4464d2e7f67b91a2d18c21b089ffd52b27
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070497"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Implementación de la aplicación en Azure App Service mediante FTP/S

En este artículo se muestra cómo usar FTP o FTPS para implementar la aplicación web, el back-end de la aplicación móvil o la aplicación de API en [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714).

El punto de conexión FTP/S de la aplicación ya está activo. No se necesita ninguna configuración para habilitar la implementación de FTP/S.

## <a name="open-ftp-dashboard"></a>Apertura del panel FTP

En [Azure Portal](https://portal.azure.com), abra la [página de recursos](../azure-resource-manager/manage-resources-portal.md#manage-resources) de la aplicación.

Para abrir el panel FTP, haga clic en **Centro de implementación** > **FTP** > **Panel**.

![Apertura del panel FTP](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Obtención de la información de conexión para FTP

En el panel FTP, haga clic en **Copiar** para copiar las credenciales de aplicación y el punto de conexión FTPS.

![Copia de información de FTP](./media/app-service-deploy-ftp/ftp-dashboard.png)

Se recomienda usar las **Credenciales de la aplicación** para implementar en su aplicación, porque son exclusivas de cada aplicación. Sin embargo, si hace clic en **Credenciales de usuario**, puede definir credenciales a nivel de usuario que puede usar para el inicio de sesión FTP/S en todas las aplicaciones de App Service de la suscripción.

> [!NOTE]
> La autenticación en un punto de conexión FTP o FTPS con credenciales de nivel de usuario requiere un nombre de usuario con el formato siguiente: 
>
>`<app-name>\<user-name>`
>
> Puesto que las credenciales de nivel de usuario están vinculadas al usuario y no a un recurso específico, el nombre de usuario debe tener este formato para dirigir la acción de inicio de sesión al punto de conexión de la aplicación adecuada.
>

## <a name="deploy-files-to-azure"></a>Implementación de archivos en Azure

1. Desde el cliente de FTP (por ejemplo, [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/) o [WinSCP](https://winscp.net/index.php)), use la información de conexión recopilada para conectarse a la aplicación.
2. Copie los archivos y la estructura de directorio correspondiente al directorio [ **/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)en Azure (o el directorio **/site/wwwroot/App_Data/Jobs/** para WebJobs).
3. Vaya a la dirección URL de la aplicación para comprobar que la aplicación se está ejecutando correctamente. 

> [!NOTE] 
> A diferencia de [las implementaciones basadas en Git](deploy-local-git.md), la implementación de FTP no es compatible con las automatizaciones de implementación siguientes: 
>
> - restauraciones de dependencias (por ejemplo, automatizaciones de NuGet, NPM, PIP y Composer)
> - compilación de archivos binarios de .NET
> - generación del archivo web.config (aquí se muestra un [ejemplo de Node.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Genere estos archivos necesarios de forma manual en la máquina local e impleméntelos luego junto con la aplicación.
>

## <a name="enforce-ftps"></a>Aplicación de FTPS

Para mejorar la seguridad, permita solo FTP a través de SSL. También puede deshabilitar FTP y FTPS si no utiliza la implementación de FTP.

En la página de recursos de la aplicación de [Azure Portal](https://portal.azure.com), seleccione **Configuración de la aplicación** en el panel de navegación izquierdo.

Para deshabilitar FTP sin cifrar, seleccione **Solo FTPS**. Para deshabilitar completamente el FTP y FTPS, seleccione **Deshabilitar**. Cuando termine, haga clic en **Guardar**. Si usa **Solo FTPS** debe exigir TLS 1.2 o posterior; para ello, navegue a la hoja **Configuración de SSL** de la aplicación web. TLS 1.0 y 1.1 no son compatibles con **Solo FTPS**.

![Deshabilitación de FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatizar con scripts

Para la implementación FTP con el uso de la [CLI de Azure](/cli/azure), vea [Creación de una aplicación web e implementación de archivos con FTP (CLI de Azure)](./scripts/cli-deploy-ftp.md).

Para la implementación FTP con [Azure PowerShell](/cli/azure), vea [Carga de archivos en una aplicación web con FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Solución de problemas de implementación de FTP

- [¿Cómo se solucionan los problemas de implementación de FTP?](#how-can-i-troubleshoot-ftp-deployment)
- [No puedo usar FTP y publicar mi código. ¿Cómo se resuelve este problema?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [¿Cómo me conecto a FTP en Azure App Service con el modo pasivo?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>¿Cómo se solucionan los problemas de implementación de FTP?

El primer paso para solucionar los problemas de implementación de FTP es aislar los de implementación de los de la aplicación en tiempo de ejecución.

Un problema de implementación suele terminar en la ausencia de archivos o en la implementación de archivos incorrectos en la aplicación. Puede solucionar el problema al investigar la implementación FTP o seleccionar una ruta de implementación alternativa (como el control de código fuente).

Los problemas de aplicación en tiempo de ejecución suelen provocar la implementación del conjunto de archivos correctos en la aplicación, pero un comportamiento incorrecto de esta. Esto se puede solucionar si nos centramos en el comportamiento del código en tiempo de ejecución e investigamos las rutas de acceso con error concretas.

Para determinar un problema de implementación o de tiempo de ejecución, consulte [Deployment vs. runtime issues](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues) (Problemas de implementación frente a los de tiempo de ejecución).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>No puedo usar FTP y publicar mi código. ¿Cómo se resuelve este problema?
Compruebe que ha escrito las [credenciales](#open-ftp-dashboard) y el nombre de host correctos. Compruebe también que los siguientes puertos FTP de la máquina no estén bloqueados por firewall:

- Puerto de conexión de control FTP: 21
- Puerto de conexión de datos de FTP: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>¿Cómo me conecto a FTP en Azure App Service con el modo pasivo?
Azure App Service permite la conexión activa y pasiva. Se recomienda el modo pasivo, ya que las máquinas de implementación suelen estar protegidas por firewall (del sistema operativo o como parte de una red particular o profesional). Consulte un [ejemplo en la documentación de WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Pasos siguientes

Para ver escenarios de implementación más avanzados, pruebe [Implementación en Azure con Git](deploy-local-git.md). La implementación basada en Git en Azure permite el control de versiones, la restauración de paquetes, MSBuild y mucho más.

## <a name="more-resources"></a>Más recursos

* [Credenciales de implementación de Azure App Service](deploy-configure-credentials.md)
