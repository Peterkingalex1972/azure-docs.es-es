---
title: 'Creación de clústeres de Apache Hadoop mediante la CLI de Azure: Azure HDInsight'
description: Obtenga información sobre cómo crear clústeres de HDInsight con la CLI de Azure multiplataforma.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: hrasheed
ms.openlocfilehash: 0a278cd98b0dd6c6d8f0fe9bfee81e5bafd4f543
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65597684"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Creación de clústeres de HDInsight mediante la CLI de Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Los pasos de este tutorial describen la creación de un clúster de HDInsight 3.6 mediante la CLI de Azure.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

CLI de Azure. Si no tiene instalada la CLI de Azure, consulte [Instalación de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) los pasos que debe seguir.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Creación de un clúster

1. Inicie sesión en la suscripción de Azure. Si va a usar Azure Cloud Shell, simplemente seleccione **Probar** en la esquina superior derecha del bloque de código. En caso contrario, escriba el siguiente comando:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Establezca las variables de entorno. En este artículo, el uso de variables se basa en Bash. Se necesitarán ligeras variaciones con otros entornos. Vea [az-hdinsight-create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) para obtener una lista completa de los parámetros posibles para la creación del clúster.

    |Parámetro | DESCRIPCIÓN |
    |---|---|
    |`--size`| Número de nodos de trabajo del clúster. En este artículo se usa la variable `clusterSizeInNodes` como el valor que se pasa a `--size`. |
    |`--version`| versión del clúster de HDInsight. En este artículo se usa la variable `clusterVersion` como el valor que se pasa a `--version`. Consulte también: [Versiones compatibles de HDInsight](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Tipo de clúster de HDInsight, como hadoop, interactivehive, hbase, kafka, storm, spark, rserver, mlservices.  En este artículo se usa la variable `clusterType` como el valor que se pasa a `--type`. Consulte también: [Tipos y configuración de clústeres](./hdinsight-hadoop-provision-linux-clusters.md#cluster-types).|
    |`--component-version`|Las versiones de los distintos componentes de Hadoop, en versiones separadas por espacios con el formato "componente=versión". En este artículo se usa la variable `componentVersion` como el valor que se pasa a `--component-version`. Consulte también: [Componentes de Hadoop](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    Reemplace `RESOURCEGROUPNAME`, `LOCATION`, `CLUSTERNAME`, `STORAGEACCOUNTNAME` y `PASSWORD` por los valores adecuados. Cambie los valores de las demás variables según sea necesario. Después, escriba los comandos de la CLI.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'
    
    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. [Cree el grupo de recursos](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) con el comando siguiente:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Para obtener una lista de las ubicaciones válidas, use el comando `az account list-locations` y luego una de las ubicaciones del valor `name`.

4. [Cree una cuenta de almacenamiento de Azure](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) con el comando siguiente:

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Extraiga la clave principal de la cuenta de almacenamiento de Azure](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) y almacénela en una variable con el comando siguiente:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Cree un contenedor de almacenamiento de Azure](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) con el comando siguiente:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Cree el clúster de HDInsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) con el comando siguiente:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --size $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-default-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > Los clústeres de HDInsight incluyen diversos tipos, que corresponden a la carga de trabajo o la tecnología para los que el clúster está optimizado. No hay ningún método admitido para crear un solo clúster que combine varios tipos, como Storm y HBase.

    Pueden pasar varios minutos (por lo general, unos quince) hasta que finalice el proceso de creación del clúster.

## <a name="clean-up-resources"></a>Limpieza de recursos

Después de completar el artículo, puede eliminar el clúster. Con HDInsight, los datos se almacenan en Azure Storage, por lo que puede eliminar un clúster de forma segura cuando no se esté usando. También se le cobrará por un clúster de HDInsight aunque no se esté usando. Como en muchas ocasiones los cargos por el clúster son mucho más elevados que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no se estén usando.

Escriba todos o algunos de los siguientes comandos para quitar recursos:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>Solución de problemas

Si experimenta problemas con la creación de clústeres de HDInsight, consulte los [requisitos de control de acceso](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Pasos siguientes

Una vez creado correctamente un clúster de HDInsight correctamente mediante la CLI de Azure, use los siguientes vínculos para aprender a trabajar con él:

### <a name="apache-hadoop-clusters"></a>Clústeres de Apache Hadoop

* [Uso de Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Uso de Apache Pig con HDInsight](hadoop/hdinsight-use-pig.md)
* [Uso de MapReduce con HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clústeres de Apache HBase

* [Introducción a Apache HBase en HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desarrollo de aplicaciones de Java para Apache HBase en HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clústeres de Apache Storm

* [Desarrollo de topologías de Java para Apache Storm en HDInsight](storm/apache-storm-develop-java-topology.md)
* [Uso de componentes de Python en Apache Storm en HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implementación y supervisión de topologías con Apache Storm en HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
