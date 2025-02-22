---
title: 'Tutorial: Administración de recursos de proceso con Azure Functions en Azure SQL Data Warehouse| Microsoft Docs'
description: Cómo usar las funciones de Azure para administrar el proceso del almacenamiento de datos.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/27/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: b94e4c6f178119d6205c302cf35a9effaf2aa885
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "61083944"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-sql-data-warehouse"></a>Uso de Azure Functions para administrar recursos de proceso en Azure SQL Data Warehouse

Este tutorial usa Azure Functions para administrar recursos de proceso de un almacenamiento de datos en Azure SQL Data Warehouse.

Para poder usar Azure Function App con SQL Data Warehouse, debe crear un [cuenta de la entidad de servicio](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) con acceso de colaborador en la misma suscripción que la instancia de almacenamiento de datos. 

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Implementación de la escala basada en el temporizador con una plantilla de Azure Resource Manager

Para implementar la plantilla, necesitará la siguiente información:

- Nombre del grupo de recursos en el que se encuentra su instancia de SQL DW
- Nombre del servidor lógico en el que se encuentra su instancia de SQL DW
- Nombre de la instancia de su SQL DW
- Id. del inquilino (identificación de directorio) de Azure Active Directory
- Id. de suscripción 
- Id. de aplicación de la entidad de servicio
- Clave secreta de entidad de servicio

Una vez que tenga la información anterior, implemente esta plantilla:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Una vez implementada la plantilla, encontrará tres nuevos recursos: un plan gratuito de Azure App Service, un consumo basado en el plan de Function App y una cuenta de almacenamiento que controlará el registro y la cola de operaciones. Seguir leyendo las demás secciones para saber cómo modificar las funciones implementadas para que se ajusten a sus necesidades.

## <a name="change-the-compute-level"></a>Cambio del nivel de proceso

1. Vaya a Function App Service. Si ha implementado la plantilla con los valores predeterminados, este servicio debe denominarse *DWOperations*. Una vez se abre Function App, observará cinco funciones implementadas para el servicio de Function App. 

   ![Funciones que se implementan con la plantilla](media/manage-compute-with-azure-functions/five-functions.png)

2. Seleccione *DWScaleDownTrigger* o *DWScaleUpTrigger* dependiendo de si desea cambiar el tiempo para reducir verticalmente o escalar horizontalmente. En el menú desplegable, seleccione Integrar.

   ![Seleccione Integrar por función](media/manage-compute-with-azure-functions/select-integrate.png)

3. En este momento, el valor mostrado debería indicar *%ScaleDownTime%* o *%ScaleUpTime%* . Estos valores indican que la programación se basa en los valores definidos en la [Configuración de la aplicación][Application Settings]. Por ahora, puede omitir este valor y cambiar la programación para el momento que prefiera en función de los siguientes pasos.

4. En el área de programación, agregue el tiempo de la expresión CRON que prefiera para reflejar la frecuencia con la que desea que SQL Data Warehouse se reduzca verticalmente. 

   ![Cambiar la programación de la función](media/manage-compute-with-azure-functions/change-schedule.png)

   El valor de `schedule` es una [expresión CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression) que incluye estos seis campos: 
   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Por ejemplo, *"0 30 9 * * 1-5"* reflejaría un desencadenador a las 9:30 a. m. de cada día de la semana. Para más información, visite los [ejemplos de programación][schedule examples] de Azure Functions.


## <a name="change-the-time-of-the-scale-operation"></a>Cambio de la hora de la operación de escalado

1. Vaya a Function App Service. Si ha implementado la plantilla con los valores predeterminados, este servicio debe denominarse *DWOperations*. Una vez se abre Function App, observará cinco funciones implementadas para Function App Service. 

2. Seleccione *DWScaleDownTrigger* o *DWScaleUpTrigger* dependiendo de si desea cambiar el valor del proceso para reducir verticalmente o escalar horizontalmente. Al seleccionar las funciones, el panel debe mostrar el archivo *index.js*.

   ![Cambiar el nivel de proceso del desencadenador de función](media/manage-compute-with-azure-functions/index-js.png)

3. Cambie el valor de *ServiceLevelObjective* al nivel que desee y guarde. Este valor es el nivel de proceso al que la instancia de almacenamiento de datos escalará según la programación definida en la sección Integrar.

## <a name="use-pause-or-resume-instead-of-scale"></a>Use la pausa o la reanudación en lugar de la escala 

Actualmente, las funciones predeterminadas son *DWScaleDownTrigger* y *DWScaleUpTrigger*. Si desea que usar las funcionalidades de pausa o reanudación en su lugar, puede habilitar *DWPauseTrigger* o *DWResumeTrigger*.

1. Navegue hasta el panel Funciones.

   ![Panel Funciones](media/manage-compute-with-azure-functions/functions-pane.png)



2. Haga clic en el control deslizante de alternancia para habilitar los desencadenadores que desee.

3. Navegue hasta las pestañas *Integrar* pestañas para cambiar la programación de los respectivos desencadenadores.

   > [!NOTE]
   > La diferencia funcional entre los desencadenadores de escalado y los desencadenadores de pausar y reanudar es el mensaje que se envía a la cola. Para obtener más información, vea [Añadir una nueva función de desencadenador][Add a new trigger function].


## <a name="add-a-new-trigger-function"></a>Añadir una nueva función de desencadenador

En este momento, hay solo dos funciones de escalado incluidas en la plantilla. Con estas funciones, en el transcurso de un día, solo puede reducir verticalmente y escalar horizontalmente una sola vez. Para un control más granular, como escalar horizontalmente varias veces al día o tener un comportamiento de escalado diferente durante los fines de semana, tendrá que agregar otro desencadenador.

1. Creación de una nueva función en blanco. Seleccione el botón *+* situado cerca de la ubicación Funciones para que aparezca el panel de plantillas de función.

   ![Crear una nueva función](media/manage-compute-with-azure-functions/create-new-function.png)

2. En Idioma, seleccione *Javascript*, a continuación, seleccione *TimerTrigger*.

   ![Crear una nueva función](media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Nombre la función y establezca la programación. La imagen muestra cómo se puede desencadenar la función cada sábado a medianoche (viernes por la noche).

   ![Reducir verticalmente el sábado](media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Copiar el contenido del *index.js* de una de las demás funciones del desencadenador.

   ![Copiar index.js](media/manage-compute-with-azure-functions/index-js.png)

5. Establecer la variable de operación para el comportamiento deseado como se indica a continuación:

   ```javascript
   // Resume the data warehouse instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the data warehouse instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the data warehouse instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```


## <a name="complex-scheduling"></a>Programación compleja

En esta sección se muestra brevemente qué es necesario para obtener una programación más compleja de la pausa, la reanudación y las funcionalidades del escalado.

### <a name="example-1"></a>Ejemplo 1:

Escalar horizontalmente todos los días a las 8 a.m. a DW600 y reducir verticalmente a las 8 p.m. a DW200.

| Función  | Schedule     | Operación                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW600"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>Ejemplo 2: 

Escalar horizontalmente todos los días a las 8 a. m. a DW1000, reducir verticalmente una vez a DW600 a las 4 p.m. y reducir verticalmente a las 10 p. m. a DW200.

| Función  | Schedule     | Operación                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-3"></a>Ejemplo 3: 

Escalar horizontalmente a las 8 a.m. a DW1000 y reducir verticalmente una vez a DW600 a las 4 p.m. los días laborables. Se pausa el viernes a las 11 p.m., se reanuda a las 7 a.m. del lunes.

| Función  | Schedule       | Operación                                |
| :-------- | :------------- | :--------------------------------------- |
| Function1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Function4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |



## <a name="next-steps"></a>Pasos siguientes

Más información sobre funciones de Azure [desencadenadas mediante temporizador](../azure-functions/functions-create-scheduled-function.md).

Restauración del [repositorio de ejemplos](https://github.com/Microsoft/sql-data-warehouse-samples) de SQL Data Warehouse.



[schedule examples]: ../azure-functions/functions-bindings-timer.md#example

[Application Settings]: ../azure-functions/functions-how-to-use-azure-function-app-settings.md
[Add a new trigger function]: manage-compute-with-azure-functions.md#add-a-new-trigger-function
