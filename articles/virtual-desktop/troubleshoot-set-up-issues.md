---
title: Creación de los grupos de inquilinos y de host de Windows Virtual Desktop - Azure
description: Cómo solucionar problemas de grupos de inquilinos y de host durante la instalación de un entorno de inquilinos de Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: helohr
ms.openlocfilehash: 93f111eb4439b89ac367ad7e7b951e26cecb86e4
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981090"
---
# <a name="tenant-and-host-pool-creation"></a>Creación de los grupos de inquilinos y de host

En este artículo se tratan problemas que se producen durante la instalación inicial del inquilino de Windows Virtual Desktop y la infraestructura del grupo de host de la sesión relacionada.

## <a name="provide-feedback"></a>Envío de comentarios

En este momento no se aceptan casos de soporte técnico mientras Windows Virtual Desktop se encuentre en versión preliminar. Visite la [Comunidad técnica de Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para hablar sobre Windows Virtual Desktop con el equipo de producto y los miembros activos de la comunidad.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Adquisición de la imagen de sesión múltiple de Windows 10 Enterprise

Para usar la imagen de sesión múltiple de Windows 10 Enterprise, vaya a Azure Marketplace, seleccione **Comenzar** > **Microsoft Windows 10** > y [Windows 10 Enterprise para Versión preliminar de escritorios virtuales, versión 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![Captura de pantalla de selección de Windows 10 Enterprise para Versión preliminar de escritorios virtuales, versión 1809.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Crear un inquilino de Windows Virtual Desktop

En esta sección se tratan los posibles problemas al crear el inquilino de Windows Virtual Desktop.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Error: El usuario no está autorizado para consultar el servicio de administración

![Captura de pantalla de una ventana de PowerShell en que un usuario no está autorizado para consultar el servicio de administración.](media/UserNotAuthorizedNewTenant.png)

Ejemplo de error no procesado:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**Causa:** No han asignado al usuario que ha iniciado sesión el rol TenantCreator en su Azure Active Directory.

**Corrección:** Siga las instrucciones de [Asignar el rol de aplicación TenantCreator a un usuario en el inquilino de Azure Active Directory](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role). Después de seguir las instrucciones, tendrá un usuario asignado al rol TenantCreator.

![Captura de pantalla del rol TenantCreator asignado.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Creación de máquinas virtuales de host de sesión de Windows Virtual Desktop

Las máquinas virtuales de host de sesión se pueden crear de varias maneras, pero los equipos de Servicios de Escritorio remoto/Windows Virtual Desktop solo admiten problemas de aprovisionamiento de máquinas virtuales relacionados con la plantilla de Azure Resource Manager. La plantilla de Azure Resource Manager está disponible en [Azure Marketplace](https://azuremarketplace.microsoft.com/) y [GitHub](https://github.com/).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problemas con Windows Virtual Desktop: aprovisionar una oferta de Azure Marketplace de grupo de host

Windows Virtual Desktop: en Azure Marketplace está disponible una plantilla de grupo de host.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Error: El mensaje "Crear una cuenta gratuita" se muestra al utilizar el vínculo de GitHub

![Captura de pantalla para crear una cuenta gratuita.](media/be615904ace9832754f0669de28abd94.png)

**Causa 1:** No existen suscripciones activas en la cuenta utilizada para iniciar sesión en Azure, o bien la cuenta utilizada no tiene permisos para ver las suscripciones.

**Corrección 1:** Inicie sesión con una cuenta que tenga acceso de colaborador (como mínimo) en la suscripción en que se van a implementar las máquinas virtuales del host de sesión.

**Causa 2:** La suscripción que se utiliza es parte de un inquilino del proveedor de servicios en la nube (CSP) de Microsoft.

**Corrección 2:** Vaya a la ubicación de GitHub, consulte **Creación y aprovisionamiento de un grupo de host nuevo de Windows Virtual Desktop** y siga estas instrucciones:

1. Haga clic con el botón derecho en **Implementar en Azure** y seleccione **Copiar dirección de vínculo**.
2. Abra el **Bloc de notas** y pegue el vínculo.
3. Antes del carácter #, inserte el nombre del inquilino del cliente final del CSP.
4. Abra el vínculo nuevo en un explorador y la plantilla se cargará en el portal de Azure.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Errores de la plantilla de Azure Resource Manager y de la Configuración de estado deseado (DSC) de PowerShell

Siga estas instrucciones para solucionar problemas de implementaciones incorrectas de DSC de PowerShell y plantillas de Azure Resource Manager.

1. Revise los errores en la implementación mediante [Ver operaciones de implementación con Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
2. Si no hay ningún error en la implementación, revise los errores en el registro de actividad mediante [Ver registros de actividad para auditar las acciones sobre los recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
3. Una vez identificado el error, utilice el mensaje de error y los recursos de [Solucionar errores comunes de implementación de Azure con Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) para solucionar el problema.
4. Elimine los recursos creados durante la implementación anterior y vuelva a intentar implementar la plantilla.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Error: Error al realizar la implementación...\<hostname>/joindomain

![Captura de pantalla de error de implementación.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Ejemplo de error no procesado:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa 1:** Las credenciales proporcionadas para unir las máquinas virtuales al dominio son incorrectas.

**Corrección 1:** Vea el error "Credenciales incorrectas" cuando las máquinas virtuales no están unidas al dominio en [Configuración de máquina virtual del host de sesión](troubleshoot-vm-configuration.md).

**Causa 2:** No se resuelve el nombre de dominio.

**Corrección 2:** Vea el error "No se resuelve el nombre de dominio" cuando las máquinas virtuales no están unidas al dominio en [Configuración de máquina virtual del host de sesión](troubleshoot-vm-configuration.md).


### <a name="error-your-deployment-failedunauthorized"></a>Error: Error al realizar la implementación...\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Causa:** La suscripción que está usando es de un tipo que no puede acceder a las características necesarias en la región donde el cliente está intentando realizar la implementación. Por ejemplo, las suscripciones a MSDN, Free o Education pueden mostrar este error.

**Solución:** Cambie el tipo de suscripción o la región a uno que pueda acceder a las características necesarias.

### <a name="error-vmextensionprovisioningerror"></a>Error: VMExtensionProvisioningError

![Captura de pantalla de error de implementación con error de estado de aprovisionamiento terminal.](media/7aaf15615309c18a984673be73ac969a.png)

**Causa 1:** Error transitorio del entorno de Windows Virtual Desktop.

**Causa 2:** Error transitorio de conexión.

**Corrección:** Inicie sesión con PowerShell para confirmar que el entorno de Windows Virtual Desktop es correcto. Finalice manualmente el registro de la máquina virtual en [Creación de un grupo de host con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Error: El nombre de usuario de administrador especificado no está permitido

![Captura de pantalla de error de implementación en que un administrador especificado no está permitido.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Ejemplo de error no procesado:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Causa:** La contraseña proporcionada contiene subcadenas prohibidas (admin, administrador, raíz).

**Corrección:** Actualice el nombre de usuario o utilice distintos usuarios.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Error: La máquina virtual ha indicado un error al procesar la extensión

![Captura de pantalla de la operación de recursos completa con un estado de aprovisionamiento terminal de Error de implementación.](media/49c4a1836a55d91cd65125cf227f411f.png)

Ejemplo de error no procesado:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-hostpool4-preview-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few: 
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource": 
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft. 
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**Causa:** La extensión DSC de PowerShell no ha podido obtener acceso de administrador a la máquina virtual.

**Corrección:** Confirme que el nombre de usuario y la contraseña tienen acceso administrativo a la máquina virtual y vuelva a ejecutar la plantilla de Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Error: DeploymentFailed: la configuración de DSC de PowerShell "FirstSessionHost" se ha completado con errores

![Captura de pantalla de error de implementación con la configuración de DSC de PowerShell "FirstSessionHost" completada con errores.](media/64870370bcbe1286906f34cf0a8646ab.png)

Ejemplo de error no procesado:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list 
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Causa:** La extensión DSC de PowerShell no ha podido obtener acceso de administrador a la máquina virtual.

**Corrección:** Confirme que el nombre de usuario y la contraseña proporcionados tienen acceso administrativo a la máquina virtual y vuelva a ejecutar la plantilla de Azure Resource Manager.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Error: DeploymentFailed: InvalidResourceReference

Ejemplo de error no procesado:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa:** Parte del nombre del grupo de recursos se usa para determinados recursos que se crean mediante la plantilla. Debido a los recursos existentes con coincidencia de nombres, la plantilla puede seleccionar un recurso existente de un grupo diferente.

**Corrección:** Al ejecutar la plantilla de Azure Resource Manager para implementar las máquinas virtuales del host de sesión, asegúrese de los dos primeros caracteres sean únicos para el nombre del grupo de recursos de suscripción.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Error: DeploymentFailed: InvalidResourceReference

Ejemplo de error no procesado:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Causa:** Este error se debe a que la NIC creada con la plantilla de Azure Resource Manager tiene el mismo nombre que otra NIC de la red virtual.

**Corrección:** Utilice un prefijo de host diferente.

### <a name="error-deploymentfailed--error-downloading"></a>Error: DeploymentFailed: error al descargar

Ejemplo de error no procesado:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-hostpool-3-preview-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-private-preview-
1/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Causa:** Este error se debe a que una ruta estática, una regla de firewall o un NSG bloquean la descarga del archivo zip vinculado a la plantilla de Azure Resource Manager.

**Corrección:** Suprima la ruta estática de bloqueo, la regla de firewall o el NSG. De manera opcional, abra el archivo json de plantilla de Azure Resource Manager en un editor de texto, comprima el vínculo en un archivo zip y descargue el recurso en una ubicación permitida.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Error: El usuario no está autorizado para consultar el servicio de administración

Ejemplo de error no procesado:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00", 
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing 
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s). 
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n 
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**Causa:** El administrador de inquilinos de Windows Virtual Desktop especificado no tiene una asignación de roles válida.

**Corrección:** El usuario que ha creado el inquilino de Windows Virtual Desktop debe iniciar sesión en PowerShell para Windows Virtual Desktop y asignar un rol al usuario que lo ha intentado. Si está ejecutando los parámetros de la plantilla de GitHub Azure Resource Manager, siga estas instrucciones mediante los comandos de PowerShell:

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Error: El usuario requiere autenticación multifactor (MFA) de Azure

![Captura de pantalla de error de implementación debido a la falta de autenticación multifactor (MFA)](media/MFARequiredError.png)

Ejemplo de error no procesado:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Causa:** El administrador de inquilinos de Windows Virtual Desktop especificado requiere autenticación multifactor (MFA) de Azure para iniciar sesión.

**Corrección:** Cree una entidad de servicio y asígnele un rol para el inquilino de Windows Virtual Desktop mediante los pasos descritos en [Tutorial: Creación de entidades de servicio y asignaciones de roles con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell). Después de comprobar que puede iniciar sesión en Windows Virtual Desktop con la entidad de servicio, vuelva a ejecutar la oferta de Azure Marketplace o la plantilla de GitHub Azure Resource Manager, según el método que utilice. Siga las instrucciones siguientes para introducir los parámetros correctos para el método.

Si está ejecutando la oferta de Azure Marketplace, proporcione valores para los parámetros siguientes a fin de autenticarse correctamente en Windows Virtual Desktop:

- Propietario de RDS del inquilino de Windows Virtual Desktop: Entidad de servicio
- Identificador de aplicación: La identificación de la aplicación de la nueva entidad de servicio que ha creado
- Contraseña/Confirmar contraseña: El secreto de contraseña generado para la entidad de servicio
- Identificador de inquilino de Azure AD: El identificador de inquilino de Azure AD de la entidad de servicio que ha creado

Si está ejecutando la plantilla de GitHub Azure Resource Manager, proporcione valores para los parámetros siguientes a fin de autenticarse correctamente en Windows Virtual Desktop:

- Nombre principal de usuario de administrador de inquilinos (UPN) o identificador de aplicación: La identificación de la aplicación de la nueva entidad de servicio que ha creado
- Contraseña del administrador de inquilinos: El secreto de contraseña generado para la entidad de servicio
- IsServicePrincipal: **true**
- AadTenantId: El identificador de inquilino de Azure AD de la entidad de servicio que ha creado

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general sobre cómo solucionar problemas de Windows Virtual Desktop y las pistas de escalación, consulte [Introducción, comentarios y soporte técnico para solucionar problemas](troubleshoot-set-up-overview.md).
- Para solucionar problemas al configurar una máquina virtual (VM) en Windows Virtual Desktop, consulte [Configuración de la máquina virtual del host de sesión](troubleshoot-vm-configuration.md).
- Para solucionar problemas con conexiones de cliente de Windows Virtual Desktop, consulte [Conexiones de cliente de Escritorio remoto](troubleshoot-client-connection.md).
- Para solucionar problemas al usar PowerShell con Windows Virtual Desktop, consulte [PowerShell para Windows Virtual Desktop](troubleshoot-powershell.md).
- Para obtener más información acerca del servicio en versión preliminar, consulte [Entorno de versión preliminar de Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Para realizar un tutorial de solución de problemas, consulte [Tutorial: Solución de problemas de las implementaciones de plantillas de Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Para más información sobre las acciones de auditoría, consulte [Operaciones de auditoría con Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Si desea conocer más detalles sobre las acciones que permiten determinar los errores durante la implementación, consulte [Visualización de operaciones de implementación con el Portal de Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
