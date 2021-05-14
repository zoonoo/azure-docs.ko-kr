---
title: 부트스트랩을 사용하여 Azure HDInsight 클러스터 구성 사용자 지정
description: .NET, PowerShell 및 Resource Manager 템플릿을 사용하여 프로그래밍 방식으로 HDInsight 클러스터 구성을 사용자 지정하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/01/2020
ms.openlocfilehash: 55792cc1890702b904db00b7e3cb3ac3a78c63f1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866525"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정

부트스트랩 스크립트를 사용하여 프로그래밍 방식으로 Azure HDInsight의 구성 요소를 설치 및 구성할 수 있습니다.

HDInsight 클러스터를 만들 때 구성 파일 설정을 설정하는 방법에는 세 가지가 있습니다.

* Azure PowerShell 사용
* .NET SDK 사용
* Azure Resource Manager 템플릿 사용

예를 들어 해당 프로그래밍 방식을 사용하여 다음 파일의 옵션을 구성할 수 있습니다.

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* server.properties(kafka-broker 구성)

만드는 동안 HDInsight 클러스터에 추가 구성 요소를 설치하는 방법에 대한 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정(Linux)](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* PowerShell을 사용하는 경우 [Az Module](/powershell/azure/)이 필요합니다.

## <a name="use-azure-powershell"></a>Azure PowerShell 사용

다음 PowerShell 코드는 [Apache Hive](https://hive.apache.org/) 구성을 사용자 지정합니다.

> [!IMPORTANT]  
> `Spark2Defaults` 매개 변수는 [AzHDInsightConfigValue](/powershell/module/az.hdinsight/add-azhdinsightconfigvalue)와 함께 사용해야 할 수 있습니다. 아래 코드 예제에 나와 있는 것처럼 매개 변수에 빈 값을 전달할 수 있습니다.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90s" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

완전히 작동하는 PowerShell 스크립트는 [부록](#appendix-powershell-sample)에서 찾을 수 있습니다.

**변경을 확인하려면:**

1. `https://CLUSTERNAME.azurehdinsight.net/`으로 이동합니다. `CLUSTERNAME`은 클러스터의 이름입니다.
1. 왼쪽 메뉴에서 **Hive** > **구성** > **고급** 으로 이동합니다.
1. **고급 hive 사이트** 를 확장합니다.
1. **hive.metastore.client.socket.timeout** 을 찾아 값이 **90s** 인지 확인합니다.

다른 구성 파일을 사용자 지정하는 추가 샘플:

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>.NET SDK 사용

[.NET용 Azure HDInsight SDK](/dotnet/api/overview/azure/hdinsight)를 참조하세요.

## <a name="use-resource-manager-template"></a>Resource Manager 템플릿 사용

Resource Manager 템플릿에서 부트스트랩을 사용할 수 있습니다.

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

:::image type="content" source="./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png" alt-text="HDInsight Hadoop 사용자 지정 클러스터 부트스트랩 Azure Resource Manager 템플릿":::

spark2-defaults에서 구성을 전환하는 샘플 Resource Manager 템플릿 코드 조각은 스토리지에서 이벤트 로그를 정기적으로 정리합니다.  

```json
"configurations": {
    "spark2-defaults": {
        "spark.history.fs.cleaner.enabled": "true",
        "spark.history.fs.cleaner.interval": "7d",
        "spark.history.fs.cleaner.maxAge": "90d"
    }
}
```

## <a name="see-also"></a>참고 항목

* [HDInsight의 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)에서는 다른 사용자 지정 옵션을 사용하여 HDInsight 클러스터를 만드는 방법에 대한 지침을 제공합니다.
* [HDInsight용 스크립트 작업 스크립트 개발](hdinsight-hadoop-script-actions-linux.md)
* [HDInsight 클러스터에서 Apache Spark 설치 및 사용](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [HDInsight 클러스터에 Apache Giraph 설치 및 사용](./hdinsight-hadoop-hue-linux.md)

## <a name="appendix-powershell-sample"></a>부록: PowerShell 샘플

이 PowerShell 스크립트는 HDInsight 클러스터를 만들고 Hive 설정을 사용자 지정합니다. `$nameToken`, `$httpPassword`, `$sshPassword` 값을 입력해야 합니다.

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>"
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>'

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>'
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

# Note: Storage account kind BlobStorage cannot be used as primary storage.

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90s"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```