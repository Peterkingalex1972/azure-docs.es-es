---
title: Incorporación de una herramienta de valoración o migración por primera vez en Azure Migrate | Microsoft Docs
description: En este artículo se describe cómo crear un proyecto de Azure Migrate y agregar una herramienta de valoración o migración.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b226f7c5879673b573133cde45db78d8d1f2fffa
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810291"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Incorporación de una herramienta de evaluación y migración por primera vez

En este artículo se describe cómo agregar una herramienta de valoración o migración por primera vez a un proyecto de [Azure Migrate](migrate-overview.md).  
Azure Migrate proporciona un centro de conectividad para realizar el seguimiento de la detección, la valoración y la migración a Azure de aplicaciones y cargas de trabajo locales, así como de máquinas virtuales en la nube privadas o públicas. El centro proporciona herramientas de Azure Migrate para la valoración y la migración, así como [ofertas](migrate-services-overview.md#isv-integration) de fabricantes de software independientes (ISV). 

## <a name="create-a-project-and-add-a-tool"></a>Creación de un proyecto e incorporación de una herramienta

Configure un nuevo proyecto de Azure Migrate en una suscripción de Azure y agregue una herramienta.

- El proyecto de Azure Migrate se usa para almacenar los metadatos de detección, valoración y migración recopilados del entorno que se va a evaluar o migrar. 
- En un proyecto, puede realizar un seguimiento de los recursos detectados y coordinar la valoración y la migración.

1. En Azure Portal > **Todos los servicios**, busque **Azure Migrate**.
2. En **Servicios**, seleccione **Azure Migrate**.

    ![Configuración de Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. En **Información general**, haga clic en **Evaluar y migrar servidores**.
4. En **Detectar, evaluar y migrar servidores**, haga clic en **Evaluar y migrar servidores**.

    ![Detección y evaluación de servidores](./media/how-to-add-tool-first-time/assess-migrate.png)

1. En **Detectar, evaluar y migrar servidores**, haga clic en **Agregar herramientas**.
2. En **Migrar proyecto**, seleccione la suscripción a Azure y cree un grupo de recursos, en caso de que no lo tenga.
3. En **Detalles del proyecto**, especifique el nombre del proyecto y la geografía en la que quiere crearlo. 

    ![Crear un proyecto de Azure Migrate](./media/how-to-add-tool-first-time/migrate-project.png)

    Puede crear un proyecto de Azure Migrate en cualquiera de estas zonas geográficas.

    **Geografía** | **Región de ubicación del almacenamiento**
    --- | ---
    Asia | Sudeste Asiático o Asia Oriental
    Europa | Sur de Europa u Oeste de Europa
    Reino Unido | Sur de Reino Unido u Oeste de Reino Unido
    Estados Unidos | Centro de EE. UU. u Oeste de EE. UU. 2

    La ubicación geográfica especificada para el proyecto solo se utiliza para almacenar los metadatos que se recopilan a partir de máquinas virtuales locales. Puede seleccionar cualquier región de destino para la migración real.

4. Haga clic en **Siguiente** y agregue una herramienta de valoración o migración.

    > [!NOTE]
    > Cuando cree un proyecto, deberá agregar al menos una herramienta de valoración o migración.

5. En **Seleccione una herramienta de evaluación**, agregue una herramienta. Si no necesita una, seleccione **Omitir por ahora la adición de una herramienta de evaluación** > **Siguiente**. 
2. En **Seleccione una herramienta de migración**, agregue una herramienta según sea necesario. Si todavía no necesita una, seleccione **Omitir por ahora la adición de una herramienta de migración** > **Siguiente**.
3. En **Review + add tools** (Revisar y agregar herramientas), revise la configuración y haga clic en **Agregar herramientas**.

Después de crear el proyecto, puede seleccionar herramientas adicionales para la valoración y la migración de servidores, cargas de trabajo, bases de datos y aplicaciones web.

## <a name="create-additional-projects"></a>Creación de proyectos adicionales

En algunas circunstancias, puede que necesite crear otros proyectos de Azure Migrate. Por ejemplo, si tiene centros de datos en diversas ubicaciones geográficas o si necesita almacenar metadatos en una geografía diferente. Cree un proyecto adicional como se indica a continuación:

1. En el proyecto actual de Azure Migrate, haga clic en **Servidores** o en **Bases de datos**.
2. En la esquina superior derecha, haga clic en **Cambiar** junto al nombre del proyecto actual.
3. En **Configuración**, seleccione **Click here to create a new project** (Haga clic aquí para crear un proyecto).
4. Cree un proyecto y agregue una nueva herramienta como se describe en el procedimiento anterior.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a agregar más herramientas de [valoración](how-to-assess.md) y [migración](how-to-migrate.md). 
