---
title: 'Acceso condicional: Azure SQL Database y Data Warehouse | Microsoft Docs'
description: Aprenda a configurar Acceso condicional para Azure SQL Database y Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 03/29/2019
ms.openlocfilehash: 1b7000138c4dfc42b774969c1b971d969064b78f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569301"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Acceso condicional (MFA) con Azure SQL Database y Data Warehouse  

Azure [SQL Database](sql-database-technical-overview.md), la [instancia administrada](sql-database-managed-instance.md) y [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) admiten el acceso condicional de Microsoft. 

> [!NOTE]
> Este tema se aplica al servidor de Azure SQL y tanto a las bases de datos de SQL Database como a SQL Data Warehouse que se crean en el servidor de Azure SQL. Para simplificar, SQL Database se utiliza cuando se hace referencia tanto a SQL Database como a SQL Data Warehouse.

Los pasos siguientes muestran cómo configurar SQL Database para aplicar una directiva de Acceso condicional.  

## <a name="prerequisites"></a>Requisitos previos  
- Debe configurar SQL Database o SQL Data Warehouse para que admitan la autenticación de Azure Active Directory. Para ver pasos concretos, consulte [Configuración y administración de la autenticación de Azure Active Directory con SQL Database o SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- Cuando se habilita la autenticación multifactor, debe conectarse con una herramienta admitida, como la versión más reciente de SSMS. Para más información, consulte [Configuración de Multi-Factor Authentication de Azure SQL Database para SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Configuración de la CA para Azure SQL DB/DW  
1. Inicie sesión en Portal, seleccione **Azure Active Directory** y, después, **Acceso condicional**. Para más información, consulte [Referencia técnica del acceso condicional de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Hoja Acceso condicional](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. En la hoja **Conditional Access-Policies** (Acceso condicional: Directivas), haga clic en **Nueva directiva**, proporcione un nombre y haga clic en **Configure rules** (Configurar reglas).  
3. En **Asignaciones**, seleccione **Usuarios y grupos**, active **Seleccionar usuarios y grupos** y seleccione el usuario o el grupo para el acceso condicional. Haga clic en **Seleccionar** y en **Listo** para aceptar la selección.  
   ![Seleccionar usuarios y grupos](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Seleccione **Aplicaciones en la nube** y haga clic en **Seleccionar aplicaciones**. Verá todas las aplicaciones disponibles para el acceso condicional. Seleccione **Azure SQL Database**, en la parte inferior, haga clic en **Seleccionar** y después en **Listo**.  
   ![Seleccionar SQL Database](./media/sql-database-conditional-access/select-sql-database.png)  
   Si no encuentra **Azure SQL Database** como aparece en la tercera captura de pantalla siguiente, realice estos pasos:   
   - Inicie sesión en la instancia de Azure SQL DB/DW mediante SSMS con una cuenta de administrador de AAD.  
   - Ejecute `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Inicie sesión en AAD y compruebe que Azure SQL Database y Data Warehouse aparezcan entre las aplicaciones en su entorno AAD.  

5. Seleccione **Controles de acceso**, seleccione **Conceder** y active la directiva que desea aplicar. Para este ejemplo, se selecciona **Requerir autenticación multifactor**.  
   ![Seleccionar Conceder acceso](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Resumen  
La aplicación seleccionada (Azure SQL Database) que permite conectarse a Azure SQL DB/DW con Azure AD Premium ahora aplica la directiva de Acceso condicional seleccionada, **Requerir autenticación multifactor**.  
Si tiene preguntas sobre Azure SQL Database y Data Warehouse con respecto a la autenticación multifactor, póngase en contacto con MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Pasos siguientes  

Para ver un tutorial, consulte [Protección de bases de datos de Azure SQL](sql-database-security-tutorial.md).
