---
title: 'CLI de Azure Service Fabric: sfctl mesh secret | Microsoft Docs'
description: Se describen los comandos de sfctl mesh secret de la CLI de Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 5e58e6a401cc166c176dc465d58ba9e8a8ed83b0
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035957"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
Obtiene y elimina recursos de mesh secret.

## <a name="commands"></a>Comandos:

|Get-Help|DESCRIPCIÓN|
| --- | --- |
| delete | Elimina el recurso de secreto. |
| list | Enumera todos los recursos de secreto. |
| show | Obtiene el secreto de recurso con el nombre especificado. |

## <a name="sfctl-mesh-secret-delete"></a>sfctl mesh secret delete
Elimina el recurso de secreto.

Elimina el recurso de secreto especificado y todos sus valores con nombre.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --name -n [Obligatorio] | Nombre del secreto. |

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración. |
| --help -h | Muestra este mensaje de ayuda y sale. |
| --output -o | Formato de salida.  Valores permitidos\: json, jsonc, table y tsv.  Valor predeterminado\: json. |
| --query | Cadena de consulta de JMESPath. Consulte http\://jmespath.org/ para obtener más información y ejemplos. |
| --verbose | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos. |

## <a name="sfctl-mesh-secret-list"></a>sfctl mesh secret list
Enumera todos los recursos de secreto.

Obtiene la información sobre todos los recursos de secreto de un grupo de recursos determinado. La información incluye la descripción y otras propiedades del secreto.

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración. |
| --help -h | Muestra este mensaje de ayuda y sale. |
| --output -o | Formato de salida.  Valores permitidos\: json, jsonc, table y tsv.  Valor predeterminado\: json. |
| --query | Cadena de consulta de JMESPath. Consulte http\://jmespath.org/ para obtener más información y ejemplos. |
| --verbose | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos. |

## <a name="sfctl-mesh-secret-show"></a>sfctl mesh secret show
Obtiene el secreto de recurso con el nombre especificado.

Obtiene la información sobre el recurso de secreto con el nombre especificado. La información incluye la descripción y otras propiedades del secreto.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --name -n [Obligatorio] | Nombre del secreto. |

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración. |
| --help -h | Muestra este mensaje de ayuda y sale. |
| --output -o | Formato de salida.  Valores permitidos\: json, jsonc, table y tsv.  Valor predeterminado\: json. |
| --query | Cadena de consulta de JMESPath. Consulte http\://jmespath.org/ para obtener más información y ejemplos. |
| --verbose | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos. |


## <a name="next-steps"></a>Pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).