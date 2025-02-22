---
title: Marcos de aprendizaje profundo y AI
titleSuffix: Azure Data Science Virtual Machines
description: Herramientas y marcos de aprendizaje profundo en Azure Data Science Virtual Machine, como TensorFlow, PyTorch, Keras, Caffe, MXNet, Horovod, Theano, Chainer, etc.
keywords: herramientas de ciencia de datos, máquina virtual de ciencia de datos, herramientas para la ciencia de datos, ciencia de datos de linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2019
ms.author: vijetaj
ms.openlocfilehash: 3c9510b99bbdc7db292ecdc0bf59aadc305cf0c8
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035838"
---
# <a name="deep-learning-and-ai-frameworks"></a>Marcos de aprendizaje profundo y de AI
[Data Science Virtual Machine](https://aka.ms/dsvm) (DSVM) admite varios marcos de aprendizaje profundo para compilar aplicaciones de inteligencia artificial (AI) con funcionalidades de análisis predictivo y cognitivas, como la comprensión de imágenes y lenguajes.

Los marcos de aprendizaje profundo disponibles en DSVM incluyen:

+ TensorFlow
+ PyTorch
+ Keras
+ Caffe
+ Caffe2
+ Torch
+ MXNet Model Server
+ MXNet
+ Horovod
+ Theano
+ Chainer
+ Deep Water
+ NVIDIA DIGITS
+ nvidia-smi
+ TensorFlow Serving
+ TensorRT
+ Microsoft Cognitive Toolkit

|&nbsp;Herramientas de DL&nbsp;en&nbsp;DSVM|Windows|Linux|&nbsp;Notas de uso|
|---------|:-------------------:|:------------------:|-----|
|[TensorFlow](https://www.tensorflow.org/) | Sí (Windows 2016) | Sí |Se instala en Python 3.5 en [Linux y Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), y en Python 3.6 en [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). En DSVM se incluyen cuadernos de Jupyter de ejemplo.<br/><br/>**Para ejecutarlo**:<br/>* Terminal: active el entorno correcto y ejecute Python. <br/> *Jupyter: conéctese a [Jupyter](provision-vm.md#tools) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) y abra el directorio TensorFlow para acceder a los ejemplos.  |
|[PyTorch](https://pytorch.org/)| Sin | Sí |Se instala en [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Se incluyen cuadernos de Jupyter de ejemplo, así como ejemplos en /dsvm/samples/pytorch.    <br/><br/>**Para ejecutarlo**<br/>* Terminal: active el entorno correcto y ejecute Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux): conéctese y abra el directorio de PyTorch para obtener ejemplos.  |
|[Keras](https://keras.io/)| Sí | Sí |Se instala en Python 3.5 en [Linux y Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), y en Python 3.6 en [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). [Muestras](https://github.com/fchollet/keras/tree/master/examples)<br/><br/>**Para ejecutarlo**:<br/>* Terminal: active el entorno correcto y ejecute Python. <br/> *Jupyter: descargue los ejemplos desde la ubicación de GitHub, conéctese a [Jupyter](provision-vm.md#tools) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) y abra el directorio de ejemplos. |
|[Caffe](https://github.com/caffe2/caffe2) | Sin |Sí (Ubuntu)|Caffe se instala en `/opt/caffe`.   Se pueden encontrar ejemplos en `/opt/caffe/examples`. <br/><br/>**Para ejecutarlo**, use X2Go para iniciar sesión en la máquina virtual, inicie un terminal nuevo y escriba:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Se abre una nueva ventana del explorador con blocs de notas de ejemplo. Los binarios se instalan en /opt/caffe/build/install/bin.<br/><br/>La versión instalada de Caffe requiere Python 2.7 y no funcionará con Python 3.5 activado de forma predeterminada. Para cambiar a Python 2.7, ejecute `source activate root` para cambiar el entorno de Anaconda.|
|[Caffe2](https://github.com/caffe2/caffe2) | Sin |Sí (Ubuntu)|Caffe2 se instala en el [entorno de Conda de Python 2.7 (root)](dsvm-languages.md#python-linux-and-windows-server-2012-edition). El origen está en `/opt/caffe2`.<br/>Se incluyen blocs de notas de ejemplo en JupyterHub.<br/><br/>**Para ejecutarlo**:<br/>* En el terminal: active el [entorno de Python root](dsvm-languages.md#python-linux-and-windows-server-2012-edition), inicie Python e importe caffe2. <br/> * En JupyterHub: [conéctese a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) y desplácese al directorio Caffe2 para buscar blocs de notas de ejemplo. Algunos blocs de notas requieren que se establezca la raíz de Caffe2 en el código de Python. Escriba /opt/caffe2. |
|[Torch](http://torch.ch/) | Sin |Sí (Ubuntu)|Torch se instala en `/dsvm/tools/torch`. PyTorch se instala en el entorno de Python 2.7 (_root_), así como en el entorno de Python 3.5 (_py35_). Los ejemplos de Torch se encuentran en `/dsvm/samples/torch` y los ejemplos de PyTorch están en `/dsvm/samples/pytorch`. |
|[MXNet](https://mxnet.io/) | Sí (Windows 2016) | Sí|MXNet se instala en `C:\dsvm\tools\mxnet` en Windows y en `/dsvm/tools/mxnet` en Linux. Los enlaces de Python se instalan en Python 3.5 en [Linux y Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), y en Python 3.6 en [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Los enlaces de R también se instalan en Ubuntu.<br/><br/>Se incluyen blocs de notas de Jupyter de ejemplo. <br/><br/>**Para ejecutarlo**:<br/>* Terminal: active el entorno correcto y ejecute Python. <br/> *Jupyter: conéctese a [Jupyter](provision-vm.md#tools) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) y abra el directorio mxnet para acceder a los ejemplos.|
|[Servidor de modelo de MXNet](https://github.com/awslabs/mxnet-model-server) | Sin | Sí |Un servidor para crear puntos de conexión HTTP para los modelos MXNet y ONNX. _mxnet-model-server_ está disponible en el terminal. Se pueden encontrar ejemplos en la [página del servidor de modelo de MXNet](https://github.com/awslabs/mxnet-model-server).|
|[Horovod](https://github.com/uber/horovod) | Sin | Sí (Ubuntu) |Marco de aprendizaje profundo distribuido para TensorFlow. Horovod se instala en Python 3.5 en [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  [Ver ejemplos](https://github.com/uber/horovod/tree/master/examples)<br/><br/>**Para ejecutarlo**, active el entorno correcto en el terminal y, luego, ejecute Python. |
|[Theano](https://github.com/Theano/Theano) | Sin | Sí (Ubuntu) |Theano se instala en el entorno de Python 2.7 (_root_), así como en el entorno de Python 3.5 (_py35_).<br/><br/>**Para ejecutarlo**: <br/>* Terminal: active la versión de Python que quiera (root o py35), ejecute Python e importe Theano.<br/>*Jupyter: seleccione el kernel de Python 2.7 o 3.5 e importe Theano.  <br/>Para solucionar de forma alternativa un error de MKL reciente, primero debe establecer la capa de subprocesos MKL:<br/><br/>_export MKL_THREADING_LAYER=GNU_|
|[Chainer](https://chainer.org/) |Sin | Sí |Chainer se instala en [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). También se instalan ChainerRL y ChainerCV. <br/><br/>Se incluyen blocs de notas de ejemplo en JupyterHub.<br/><br/>**Para ejecutarlo**: <br/>* Terminal: active el entorno de [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition), ejecute _python_ y luego importe Chainer. <br/> * JupyterHub: [conéctese a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) y desplácese al directorio Chainer para buscar cuadernos de ejemplo.|
|[NVidia Digits](https://github.com/NVIDIA/DIGITS) | Sin | Sí (Ubuntu) |Sistema de aprendizaje profundo de NVIDIA para entrenar modelos de aprendizaje profundo de forma rápida. DIGITS se instala en `/dsvm/tools/DIGITS` y hay disponible un servicio denominado _digits_.  <br/><br/>**Para ejecutarlo**: <br/>Inicie sesión en la máquina virtual con X2Go. En un terminal, inicie el servicio ```sudo systemctl start digits```. <br/><br/>El servicio tarda cerca de un minuto en iniciarse. Abra un explorador web y vaya a `http://localhost:5000`. Tenga en cuenta que DIGITS no ofrece un inicio de sesión seguro y no tiene que exponerse fuera de la VM.|
|[CUDA, cuDNN, NVIDIA Driver](https://developer.nvidia.com/cuda-toolkit) |Sí | Sí | |
|nvidia-smi|Sí | Sí |Herramienta NVIDIA para consultar la actividad de GPU. _nvidia-smi_ está disponible en la ruta de acceso del sistema. <br/><br/>Inicie un símbolo del sistema (en Windows) o un terminal (en Linux) y ejecute _nvidia-smi_.|
|[TensorFlow Serving](https://www.tensorflow.org/serving/) | Sin | Sí |Un servidor para inferencias en un modelo de TensorFlow. _tensorflow_model_server_ está disponible en el terminal. Otros ejemplos están disponibles [en línea](https://www.tensorflow.org/serving/).|
|[TensorRT](https://developer.nvidia.com/tensorrt) |  Sin | Sí (Ubuntu) |Un servidor de inferencia de aprendizaje profundo de NVIDIA. TensorRT se instala como un paquete _apt_. Otros ejemplos están disponibles [en línea](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|Sí | Sí | Se instala en Python 3.5 en [Linux y Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition), y en Python 3.6 en [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). En DSVM se incluyen cuadernos de Jupyter de ejemplo. <br/><br/>**Para ejecutarlo**: <br/>Terminal: active el entorno correcto y ejecute Python. <br/>Jupyter: conéctese a [Jupyter](provision-vm.md#tools) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) y abra el directorio CNTK para acceder a los ejemplos. |
|Deep Water|Sin | Sí (Ubuntu) |El marco de aprendizaje profundo para H2O Deep Water se instala en [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) y también está disponible en `/dsvm/tools/deep_water`. Se incluyen blocs de notas de ejemplo en JupyterHub. Deep Water requiere CUDA 8 con cuDNN 5.1. No está en la ruta de acceso de la biblioteca de forma predeterminada, ya que otros marcos de aprendizaje profundo utilizan CUDA 9 y cuDNN 7. Para usar CUDA 8 + cuDNN 5.1 para Deep Water:<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>Para utilizar Deep Water:<br/>* Terminal: active el entorno de [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) y luego ejecute _python_. <br/>* JupyterHub: [conéctese a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) y desplácese al directorio deep_water para buscar cuadernos de ejemplo.|
