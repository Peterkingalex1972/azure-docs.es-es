---
title: Especificaciones técnicas y cumplimiento de Data Box Edge de Microsoft Azure | Microsoft Docs
description: Obtenga información sobre las especificaciones técnicas y cumplimiento para Data Box Edge de Microsoft Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 52fb32a8b34c62fe94ab35e2c051d996ab8bef10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60755141"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Especificaciones técnicas de Azure Data Box Edge

Los componentes de hardware del dispositivo Data Box Edge de Microsoft Azure se adhieren a las especificaciones técnicas y los estándares reglamentarios descritos en este artículo. Las especificaciones técnicas describen las fuentes de alimentación, la capacidad de almacenamiento, los revestimientos y los estándares medioambientales. 

## <a name="power-supply-unit-specifications"></a>Especificaciones de la fuente de alimentación

El dispositivo Data Box Edge tiene dos fuentes de alimentación de 100-240 V con ventiladores de alto rendimiento. Las dos fuentes de alimentación proporcionan una configuración de alimentación redundante. Si se produce un error en una de ellas, el dispositivo sigue funcionando con normalidad en la otra hasta que se reemplaza el módulo con error. En la tabla siguiente se enumeran las especificaciones técnicas de las fuentes de alimentación.

| Especificación           | Fuente de alimentación de 750 W                  |
|-------------------------|----------------------------|
| Potencia de salida máxima    | 750 W                     |
| Frecuencia               | 50/60 Hz                   |
| Selección del intervalo de voltaje | Intervalo automático: 100-240 V CA |
| Conectable en funcionamiento           | Sí                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>Especificaciones de almacenamiento

Los dispositivos de Data Box Edge tienen unidades de estado sólido (SSD) de NVMe de 10 X 2.5”, cada una de ellas con una capacidad de 1,6 TB. De estas unidades SSD, 2 son discos del sistema operativo y las otras 8 son discos de datos. La capacidad total utilizable para el dispositivo es de aproximadamente 12,5 TB. La tabla siguiente contiene los detalles de la capacidad de almacenamiento del dispositivo.

|     Especificación                          |     Valor             |
|--------------------------------------------|-----------------------|
|    Número de unidades de estado sólido (SSD)     |    8                  |
|    Capacidad de un solo SSD                     |    1,6 TB             |
|    Capacidad total                          |    12,8 TB            |
|    Capacidad total utilizable*                  |    ~ 12,5 TB            |

**Parte del espacio está reservado para uso interno.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Especificaciones de dimensiones y peso del revestimiento

En las tablas siguientes se enumeran las diferentes especificaciones de dimensiones y peso del revestimiento.

### <a name="enclosure-dimensions"></a>Dimensiones del revestimiento

En la tabla siguiente se enumeran las dimensiones del revestimiento en milímetros y pulgadas.

|     Revestimiento     |     Milímetros     |     Pulgadas     |
|-------------------|---------------------|----------------|
|    Alto         |    44,45            |    1,75"          |
|    Ancho          |    434,1           |    17,09"          |
|    Length          |    740,4           |    29,15"          |

En la tabla siguiente se enumeran las dimensiones del paquete de envío en milímetros y pulgadas.

|     Paquete     |     Milímetros     |     Pulgadas     |
|-------------------|---------------------|----------------|
|    Alto         |    311,2            |    12,25"          |
|    Ancho          |    642,8          |    25,31"          |
|    Length          |   1051,1          |    41,38"          |

### <a name="enclosure-weight"></a>Peso del revestimiento

El paquete del dispositivo pesa 66 libras y requiere dos personas para manejarlo. El peso del dispositivo depende de la configuración del revestimiento.

|     Revestimiento                                 |     Peso          |
|-----------------------------------------------|---------------------|
|    Peso total, incluido el empaquetado       |    61 libras          |
|    Peso del dispositivo                       |    35 libras          |

## <a name="enclosure-environment-specifications"></a>Especificaciones medioambientales del revestimiento

En esta sección se enumeran las especificaciones relacionadas con el entorno del revestimiento, como la temperatura, la humedad y la altitud.

### <a name="temperature-and-humidity"></a>Temperatura y humedad

|     Revestimiento         |     Intervalo de temperatura ambiente     |     Humedad relativa del ambiente     |     Punto de rocío máximo     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operativo        |    10 °C a 35 °C (50 °F a 86 °F)         |    10 % - 80 % sin condensación         |    29 °C (84 °F)            |
|    No operativo    |    -40 °C a 65 °C (-40 °F a 149 °F)     |    5% - 95% sin condensación          |    33 °C (91 °F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Flujo de aire, altitud, golpes, vibraciones, orientación, seguridad y CEM

|     Revestimiento                           |     Especificaciones operativas                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Flujo de aire                              |    El flujo de aire del sistema va de delante atrás. El sistema debe funcionar con una instalación de baja presión y escape trasero. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Altitud máxima (operativo)        |    3048 metros (10 000 pies) con la temperatura máxima de funcionamiento reducida determinada por las [especificaciones de reducción de la temperatura de funcionamiento](#operating-temperature-de-rating-specifications).                                                                                |
|    Altitud máxima (no operativo)    |    12 000 metros (39 370 pies)                                                                                                                                                                                         |
|    Golpes (operativo)                   |    6 G para 11 milisegundos en 6 orientaciones                                                                                                                                                                         |
|    Golpes (no operativo)               |    71 G para 2 milisegundos en 6 orientaciones                                                                                                                                                                           |
|    Vibraciones (operativo)               |    0,26 G<sub>RMS</sub> 5 Hz a 350 Hz aleatorio                                                                                                                                                                                     |
|    Vibraciones (no operativo)           |    1,88 G<sub>RMS</sub> 10 Hz a 500 Hz durante 15 minutos (los seis lados probados).                                                                                                                                                  |
|    Orientación y montaje             |    Montaje en bastidor de 19"                                                                                                                                                                                        |
|    Seguridad y homologaciones                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    CEM                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (Clase D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Sector energético             |    N.º de Reglamento de la Comisión (UE) 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Especificaciones de reducción de temperatura de funcionamiento

|     Reducción de temperatura de funcionamiento     |     Intervalo de temperatura ambiente                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Hasta 35 °C (95 °F)                       |    La temperatura máxima se reduce en 1 °C/300 m (1 °F/547 pies) por encima de los 950 m (3117 pies).    |
|    35  C a 40  C (95  F a 104  F)            |    La temperatura máxima se reduce en 1 °C/175 m (1 °F/319 pies) por encima de los 950 m (3117 pies).    |
|    40  C a 45  C (104  F a 113  F)           |    La temperatura máxima se reduce en 1 °C/125 m (1 °F/228 pies) por encima de los 950 m (3117 pies).    |


## <a name="next-steps"></a>Pasos siguientes

- [Implementación de Azure Data Box Edge](data-box-edge-deploy-prep.md)
