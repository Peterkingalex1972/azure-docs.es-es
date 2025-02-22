---
title: Implementación y consumo
titleSuffix: Azure Machine Learning Studio
description: Puede usar Azure Machine Learning Studio para implementar flujos de trabajo y modelos de aprendizaje automático como servicios web. Estos servicios web pueden usarse después para llamar a los modelos de aprendizaje automático de las aplicaciones en Internet para que realicen predicciones en tiempo real o en un modo por lotes.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 0a29d763ab54ee716e514df23576e9c3b294d792
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60751083"
---
# <a name="azure-machine-learning-studio-web-services-deployment-and-consumption"></a>Servicios web de Azure Machine Learning Studio: Implementación y consumo

Puede usar Azure Machine Learning Studio para implementar flujos de trabajo y modelos de aprendizaje automático como servicios web. Estos servicios web pueden usarse después para llamar a los modelos de aprendizaje automático de las aplicaciones en Internet para que realicen predicciones en tiempo real o en un modo por lotes. Como estos servicios web son RESTFul, se los puede llamar desde diversos lenguajes de programación y plataformas como .NET y Java, y aplicaciones como Excel.

Las siguientes secciones proporcionan vínculos a tutoriales, código y documentación para ayudarle a empezar.

## <a name="deploy-a-web-service"></a>Implementación de un servicio web

### <a name="with-azure-machine-learning-studio"></a>Con Azure Machine Learning Studio

El portal de Studio y el de Servicios web Microsoft Azure Machine Learning le permiten implementar y administrar un servicio web sin tener que escribir código.

Los vínculos siguientes proporcionan información general sobre cómo implementar un nuevo servicio web:

* Para ver una introducción a la implementación de un nuevo servicio web basado en Azure Resource Manager, consulte [Implementación de servicios web nuevos](publish-a-machine-learning-web-service.md).
* Para ver un tutorial sobre cómo implementar un servicio web, consulte [Implementar un servicio web Azure Machine Learning](publish-a-machine-learning-web-service.md).
* Para información detallada acerca de cómo crear e implementar un servicio web, empiece con [Tutorial 1: Predicción del riesgo de crédito](tutorial-part1-credit-risk.md).
* Para obtener ejemplos específicos de la implementación de un servicio web, consulte:

  * [Tutorial 3: Implementación del modelo de riesgo crediticio](tutorial-part3-credit-risk-deploy.md)
  * [Procedimiento para implementar un servicio web en varias regiones](/azure/machine-learning/studio/publish-a-machine-learning-web-service#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Con API de proveedor de recursos de servicios web (API de Azure Resource Manager)

El proveedor de recursos de Azure Machine Learning Studio para servicios web permite la implementación y administración de servicios web mediante llamadas a la API REST. Para información más detallada, consulte la referencia [Servicio web Machine Learning (REST)](/rest/api/machinelearning/index).

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Con cmdlets de PowerShell

El proveedor de recursos de Azure Machine Learning Studio para servicios web permite la implementación y administración de servicios web mediante los cmdlets de PowerShell.

Para usar los cmdlets, primero debe iniciar sesión en su cuenta de Azure desde el entorno de PowerShell mediante el cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Si no sabe cómo llamar a los comandos de PowerShell basados en Resource Manager, consulte [Uso de Azure PowerShell con Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md).

Para exportar el experimento predictivo, use este [código de ejemplo](https://github.com/ritwik20/AzureML-WebServices). Después de crear el archivo .exe a partir del código, escriba:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

La ejecución de la aplicación permite crear una plantilla JSON del servicio web. Para usar la plantilla para implementar un servicio web, debe agregar la siguiente información:

* Nombre y clave de la cuenta de almacenamiento

    El nombre y la clave de la cuenta de almacenamiento se pueden obtener en [Azure Portal](https://portal.azure.com/).
* Identificador del plan de compromiso

    Para obtener este identificador de plan en el portal de [Servicios web Azure Machine Learning](https://services.azureml.net) , inicie sesión y haga clic en un nombre de plan.

Agréguelos a la plantilla JSON como elementos secundarios del nodo *Properties* en el mismo nivel que el nodo *MachineLearningWorkspace*.

Este es un ejemplo:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Consulte los siguientes artículos y el código de ejemplo para obtener más detalles:

* [cmdlets de Azure Machine Learning Studio](https://docs.microsoft.com/powershell/module/az.machinelearning) en MSDN
* [Tutorial](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) de ejemplo en GitHub

## <a name="consume-the-web-services"></a>Consumo de servicios web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>En la interfaz de Servicios web Azure Machine Learning (pruebas)

Puede probar el servicio web en el portal de Servicios web Azure Machine Learning. Esto incluye probar las interfaces del servicio de solicitud-respuesta (RRS) y del servicio de ejecución de lotes (BES).

* [Implementación de servicios web nuevos](publish-a-machine-learning-web-service.md)
* [Implementar un servicio web de Azure Machine Learning](publish-a-machine-learning-web-service.md)
* [Tutorial 3: Implementación del modelo de riesgo crediticio](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Desde Excel

Puede descargar una plantilla de Excel para consumir el servicio web:

* [Consumo de un servicio web Azure Machine Learning desde Excel](consuming-from-excel.md)
* [Complemento de Excel para Servicios web Azure Machine Learning](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Desde un cliente basado en REST

Los servicios web Azure Machine Learning son API de RESTful. Puede consumir estas API desde varias plataformas como. NET, Python, R, Java, etc. La página **Consumo** del servicio web en el [portal de Servicios web Microsoft Azure Machine Learning](https://services.azureml.net) incluye código de ejemplo que le ayudará a empezar. Para más información, consulte [Consumo de servicios web de Azure Machine Learning](consume-web-services.md).
