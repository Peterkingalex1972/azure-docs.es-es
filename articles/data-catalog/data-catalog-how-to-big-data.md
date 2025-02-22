---
title: Trabajo con orígenes de "macrodatos" en Azure Data Catalog
description: Artículo de procedimientos que resalta los patrones necesarios para usar Azure Data Catalog con orígenes de "macrodatos", incluidos Azure Blob Storage, Azure Data Lake y Hadoop HDFS.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 00b9d6ab6ca8d9b4154e0fba491081597dc08402
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882536"
---
# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>Trabajo con orígenes de macrodatos en Azure Data Catalog

## <a name="introduction"></a>Introducción

**Microsoft Azure Data Catalog** es un servicio en la nube totalmente administrado que actúa como sistema de registro y de detección de orígenes de datos empresariales. Ayuda a las personas a detectar, conocer y usar orígenes de datos, y a las organizaciones a obtener un mayor valor de los orígenes de datos existentes, incluyendo los macrodatos.

**Azure Data Catalog** admite el registro de blobs y directorios de blobs de Azure Blob Storage, así como directorios y archivos de Hadoop HDFS. La naturaleza semiestructurada de estos orígenes de datos proporciona gran flexibilidad. Sin embargo, para obtener el máximo partido de su registro en **Azure Data Catalog**, los usuarios deben tener en cuenta cómo se organizan los orígenes de datos.

## <a name="directories-as-logical-data-sets"></a>Directorios como conjuntos de datos lógicos

Un patrón común para organizar los orígenes de macrodatos es tratar a los directorios como conjuntos de datos lógicos. Los directorios de nivel superior se usan para definir un conjunto de datos, mientras que las subcarpetas para definir las particiones y los archivos que contienen almacenarán los propios datos.

Un ejemplo de este patrón puede ser:

```text
    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...
```

En este ejemplo, vehicle_maintenance_events y location_tracking_events representan conjuntos de datos lógicos. Todas estas carpetas contienen archivos de datos que se organizan por año y mes en subcarpetas. Potencialmente, cada una de estas carpetas puede contener cientos o miles de archivos.

En este patrón, el registro de archivos individuales en **Azure Data Catalog** probablemente no tenga sentido. En su lugar, registre los directorios que representan los conjuntos de datos que sean significativos para los usuarios que trabajan con los datos.

## <a name="reference-data-files"></a>Archivos de datos de referencia

Un patrón complementario es almacenar conjuntos de datos de referencia como archivos individuales. Estos conjuntos de datos puede considerarse como el lado "pequeño" de los macrodatos y a menudo son similares a las dimensiones de un modelo de datos analítico. Los archivos de datos de referencia contienen los registros que se usan para proporcionar contexto para la mayoría de los archivos de datos almacenados en otro lugar en el almacén de macrodatos.

Un ejemplo de este patrón puede ser:

```text
    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv
```

Cuando un analista o un científico de datos trabaja con los datos que se encuentran en estructuras de directorio grandes, los datos de estos archivos de referencia se pueden usar para proporcionar información más detallada a las entidades a las que se hace referencia solo por nombre o Id. en el conjunto de datos mayor.

En este patrón, tiene sentido registrar los archivos de datos de referencia individuales en **Azure Data Catalog**. Cada archivo representa un conjunto de datos y cada uno de ellos se puede anotar y detectar individualmente.

## <a name="alternate-patterns"></a>Patrones alternativos

Los patrones que se han descritos en la sección anterior son solo dos formas de organizar un almacén de macrodatos, pero cada implementación es diferente. Independientemente de cómo se estructuren los orígenes de datos, al registrar los orígenes de macrodatos en **Azure Data Catalog**, es preciso centrarse en el registro de los archivos y directorios que representen los conjuntos de datos que sean útiles para otras personas de la organización. Si se registran todos los archivos y directorios, se puede provocar un desorden en el catálogo, lo que dificulta la búsqueda a los usuarios.

## <a name="summary"></a>Resumen

El registro de orígenes de datos en **Azure Data Catalog** facilita su detección y comprensión. Mediante el registro y anotación de los archivos y directorios de macrodatos que representan conjuntos de datos lógicos, puede facilitar a los usuarios la búsqueda y el uso de los orígenes de macrodatos que necesitan.
