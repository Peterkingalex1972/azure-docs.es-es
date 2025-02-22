---
title: 'Azure Portal: Enmascaramiento dinámico de datos en SQL Database | Microsoft Docs'
description: Cómo empezar a usar el enmascaramiento dinámico de datos de SQL Database en Azure Portal
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
ms.date: 03/04/2018
ms.openlocfilehash: 1400a21c3fee51bb26a3271546a7553a3429b42d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568803"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Introducción al enmascaramiento dinámico de datos de SQL Database con Azure Portal

En este artículo se muestra cómo implementar el [enmascaramiento dinámico de datos](sql-database-dynamic-data-masking-get-started.md) con Azure Portal. También puede implementar el enmascaramiento dinámico de datos mediante [Cmdlets de Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) o la [API REST](https://docs.microsoft.com/rest/api/sql/).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Configuración del enmascaramiento dinámico de datos para la base de datos con Azure Portal

1. Inicie Azure Portal en [https://portal.azure.com](https://portal.azure.com).
2. Desplácese hasta la hoja de configuración de la base de datos que incluye los datos confidenciales que desea enmascarar.
3. Haga clic en el icono **Enmascaramiento dinámico de datos**, lo que inicia la hoja de configuración **Enmascaramiento dinámico de datos**.

   * Como alternativa, puede desplazarse hacia abajo hasta la sección **Operaciones** y hacer clic en **Enmascaramiento de datos dinámicos**.

     ![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. En la página de configuración de **Enmascaramiento dinámico de datos** puede ver algunas columnas de base de datos que el motor de recomendaciones ha marcado para enmascaramiento. Para aceptar las recomendaciones, solo tiene que hacer clic en **Agregar máscara** para una o varias columnas y se creará una máscara en función del tipo predeterminado para esta columna. Para cambiar la función de enmascaramiento, haga clic en la regla de enmascaramiento y edite formato de campo de enmascaramiento en el formato diferente que elija. Asegúrese de hacer clic en **Guardar** para guardar la configuración.

    ![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. Para agregar una máscara para cualquier columna de la base de datos, en la parte superior de la hoja de configuración **Enmascaramiento dinámico de datos**, haga clic en **Agregar máscara** para abrir la página de configuración **Agregar regla de enmascaramiento**.

    ![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. Seleccione los valores para **Esquema**, **Tabla** y **Columna** para definir el campo designado para enmascararse.
7. Elija un **Formato de campo de enmascaramiento** en la lista de categorías de enmascaramiento de datos confidenciales.

    ![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. Haga clic en **Guardar** en la página de regla de enmascaramiento de datos para actualizar el conjunto de reglas de enmascaramiento en la directiva de enmascaramiento dinámico de datos.
9. Escriba los usuarios SQL o las identidades AAD que deben excluirse del enmascaramiento y tengan acceso a los datos confidenciales sin máscara. Esto debe ser una lista separada por puntos y coma de usuarios. Los usuarios con privilegios de administrador siempre tienen acceso a los datos originales sin máscara.

    ![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > Para hacer que el nivel de aplicación pueda mostrar datos confidenciales para los usuarios con privilegios de la aplicación, agregue el usuario SQL o identidad AAD que la aplicación usa para consultar la base de datos. Se recomienda que esta lista incluya un número mínimo de usuarios con privilegio para minimizar la exposición de los datos confidenciales.

10. Haga clic en **Guardar** en la página de configuración de enmascaramiento de datos para guardar la directiva de enmascaramiento nueva o actualizada.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información general sobre el enmascaramiento dinámico de datos, consulte [este artículo](sql-database-dynamic-data-masking-get-started.md).
* También puede implementar el enmascaramiento dinámico de datos mediante [Cmdlets de Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) o la [API REST](https://docs.microsoft.com/rest/api/sql/).
