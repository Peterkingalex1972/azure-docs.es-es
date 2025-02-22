---
title: Control de versiones de una aplicación de cadena de bloques en Azure Blockchain Workbench
description: Cómo utilizar las versiones de una aplicación en Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 04/15/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 63f18e3ee316b9791bb62bfcd20c07a30cbebb5e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60896884"
---
# <a name="azure-blockchain-workbench-application-versioning"></a>Control de versiones de una aplicación de Azure Blockchain Workbench

Puede crear y usar varias versiones de una aplicación de Azure Blockchain Workbench. Si se cargan varias versiones de la misma aplicación, hay un historial de versiones disponible y los usuarios pueden elegir qué versión desean usar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Requisitos previos

* Una implementación de Blockchain Workbench. Para más información sobre la implementación, consulte [Implementación de Azure Blockchain Workbench](deploy.md).
* Una aplicación de cadena de bloques implementada en Blockchain Workbench. Consulte [Creación de una aplicación de cadena de bloques en Azure Blockchain Workbench](create-app.md)

## <a name="add-an-app-version"></a>Adición de una versión de aplicación

Para agregar una nueva versión, cargue la nueva configuración y los archivos de contrato inteligente a Blockchain Workbench.

1. En un explorador web, vaya a la dirección web de Blockchain Workbench. Por ejemplo, `https://{workbench URL}.azurewebsites.net/` Para más información sobre cómo buscar la dirección web de Blockchain Workbench, consulte [Dirección URL web de Blockchain Workbench](deploy.md#blockchain-workbench-web-url).
2. Inicie sesión como [administrador de Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).
3. Seleccione la aplicación de cadena de bloques que desea actualizar con otra versión.
4. Seleccione **Agregar versión**. Aparece el panel **Agregar versión**.
5. Elija la nueva configuración de contrato de la versión y los archivos de código de contrato para cargar. El archivo de configuración se valida automáticamente. Corrija los errores de validación antes de implementar la aplicación.
6. Seleccione **Agregar versión** para agregar la nueva versión de la aplicación de cadena de bloques.

    ![Agregar una nueva versión](media/version-app/add-version.png)

La implementación de la aplicación de cadena de bloques puede tardar unos minutos. Cuando haya finalizado la implementación, actualice la página de la aplicación. Al elegir la aplicación y seleccionar el botón **Historial de versiones**, se muestra el historial de versiones de la aplicación.

> [!IMPORTANT]
> Las versiones anteriores de la aplicación están deshabilitadas. Puede volver a habilitar individualmente las versiones anteriores.
>
> Es posible que deba volver a agregar miembros a los roles de la aplicación si se realizaron cambios en los roles de aplicación de la nueva versión.

## <a name="using-app-versions"></a>Uso de versiones de la aplicación

De forma predeterminada, se usa la última versión de la aplicación habilitada en Blockchain Workbench. Si desea usar una versión anterior de una aplicación, deberá elegir antes la versión de la página de la aplicación.

1. En la sección de aplicaciones de Blockchain Workbench, active la casilla la aplicación que contiene el contrato que desea utilizar. Si se habilitan las versiones anteriores, el botón del historial de versiones está disponible.
2. Seleccione el botón **Historial de versiones**.
3. En el panel del historial de versiones, elija la versión de la aplicación seleccionando el vínculo en la columna *Fecha de modificación*.

    ![Selección de una versión anterior](media/version-app/use-version.png)

    Puede crear nuevos contratos o realizar acciones en contratos de una versión anterior. La versión de la aplicación se muestra después del nombre de la aplicación y aparece una advertencia acerca de la versión anterior.

## <a name="next-steps"></a>Pasos siguientes

* [Solución de problemas de Azure Blockchain Workbench](troubleshooting.md)
