---
title: HDInsight의 Hadoop 클러스터에 Giraph 설치 및 사용 - Azure
description: Giraph를 사용하여 HDInsight 클러스터를 사용자 지정하는 방법 및 Giraph를 사용하는 방법을 알아봅니다.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 37e8b0a5cc89eded1890eebbeb93cc82d54c9f05
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360897"
---
# <a name="install-and-use-apache-giraph-on-windows-based-hdinsight-clusters"></a>Windows 기반 HDInsight 클러스터에서 Apache Giraph 설치 및 사용

스크립트 동작을 사용하여 Apache Giraph로 Windows 기반 HDInsight 클러스터를 사용자 지정하는 방법 및 대규모 그래프를 처리하기 위해 Giraph를 사용하는 방법을 알아봅니다. Linux 기반 클러스터와 함께 Giraph를 사용한 작업에 대한 자세한 내용은 [HDInsight Hadoop 클러스터에 Apache Giraph 설치(Linux)](hdinsight-hadoop-giraph-install-linux.md)를 참조하세요.

> [!IMPORTANT]  
> 이 문서의 단계는 Windows 기반 HDInsight 클러스터에만 적용됩니다. HDInsight는 HDInsight 3.4 이하 버전의 경우 Windows에서만 사용 가능합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요. Linux 기반 HDInsight 클러스터에 Giraph를 설치하는 방법에 대한 자세한 내용은 [HDInsight Hadoop 클러스터에 Apache Giraph 설치(Linux)](hdinsight-hadoop-giraph-install-linux.md)를 참조하세요.


*스크립트 작업*을 사용하여 Azure HDInsight에서 모든 형식의 클러스터(Hadoop, Storm, HBase, Spark)에 Giraph를 설치할 수 있습니다. HDInsight 클러스터에 Giraph를 설치하기 위한 샘플 스크립트는 [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1)의 읽기 전용 Azure Storage Blob에서 다운로드할 수 있습니다. 샘플 스크립트는 HDInsight 클러스터 버전 3.1에서만 작동합니다. HDInsight 클러스터 버전에 대한 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**관련된 문서**

* [HDInsight Hadoop 클러스터에 Apache Giraph 설치(Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [HDInsight에서 Apache Hadoop 클러스터 만들기](hdinsight-provision-clusters.md): HDInsight 클러스터를 만드는 방법에 대한 일반 정보입니다.
* [스크립트 동작을 사용 하 여 HDInsight 클러스터를 사용자 지정] [hdinsight-클러스터-사용자 지정]: 스크립트 작업을 사용 하 여 HDInsight 클러스터 사용자 지정에 대 한 일반 정보입니다.
* [HDInsight용 스크립트 작업 스크립트 개발](hdinsight-hadoop-script-actions.md)

## <a name="what-is-giraph"></a>Giraph 정의
<a href="https://giraph.apache.org/" target="_blank">Apache Giraph</a>를 통해 Hadoop을 사용하여 그래프 처리를 수행할 수 있으며, Azure HDInsight에서 이를 사용할 수도 있습니다. 그래프는 인터넷과 같은 대규모 네트워크의 라우터 간 연결, 소셜 네트워크(또는 소셜 그래프)상의 사람들 간 관계 등 개체 간의 관계를 모델링합니다. 그래프 처리를 통해 그래프의 개체 간 관계를 추론하여 다음을 수행할 수 있습니다.

* 현재 관계를 기반으로 하여 잠재적인 친구 파악.
* 네트워크의 두 컴퓨터 간에 가장 짧은 경로 파악.
* 웹 페이지의 페이지 순위 계산.

## <a name="install-giraph-using-portal"></a>포털을 사용하여 Giraph 설치
1. **HDInsight에서 Hadoop 클러스터 만들기**에서 설명한 대로 [사용자 지정 만들기](hdinsight-provision-clusters.md) 옵션을 사용하여 클러스터를 만들기 시작합니다.
2. 아래와 같이 마법사의 **스크립트 동작** 페이지에서 **스크립트 동작 추가**를 클릭하여 스크립트 동작에 대한 세부 정보를 제공합니다.

    ![스크립트 작업을 사용하여 클러스터 사용자 지정](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "스크립트 작업을 사용하여 클러스터 사용자 지정")

    |자산|값|  
    |---|---|  
    |이름|스크립트 작업의 이름을 지정합니다. 예: **Install Giraph**|
    |스크립트 URI|클러스터를 사용자 지정하기 위해 호출되는 스크립트에 URI(Uniform Resource Identifier)를 지정합니다. 예를 들어 *https:\//hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1*|
    |노드 유형|사용자 지정 스크립트가 실행되는 노드를 지정합니다. **모든 노드**, **헤드 노드만** 또는 **작업자 노드만**을 선택할 수 있습니다.
    |매개 변수|스크립트에 필요한 경우 매개 변수를 지정합니다. Giraph를 설치하는 스크립트는 매개 변수가 필요하지 않으므로 공백으로 둘 수 있습니다.|  

    두 개 이상의 스크립트 작업을 추가하여 클러스터에 여러 구성 요소를 설치할 수 있습니다. 스크립트를 추가한 후 확인 표시를 클릭하여 클러스터 만들기를 시작합니다.

## <a name="use-giraph"></a>Giraph 사용
SimpleShortestPathsComputation 예제를 사용하여 그래프의 개체 간 가장 짧은 경로를 찾기 위한 기본 <a href = "https://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> 구현을 보여줍니다. 다음 단계에 따라 샘플 데이터와 샘플 jar을 업로드하고, SimpleShortestPathsComputation 예제를 사용하여 작업을 실행한 후 결과를 확인합니다.

1. 샘플 데이터 파일을 Azure Blob Storage에 업로드합니다. 로컬 워크스테이션에서 **tiny_graph.txt**라는 새 파일을 만듭니다. 이 파일은 다음 줄을 포함해야 합니다.

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    tiny_graph.txt 파일을 HDInsight 클러스터의 기본 저장소에 업로드합니다. 데이터를 업로드하는 방법은 [HDInsight에서 Apache Hadoop 작업에 대한 데이터 업로드](hdinsight-upload-data.md)를 참조하세요.

    이 데이터는 [source\_id, source\_value,[[dest\_id], [edge\_value],...]]의 형식을 사용하여 방향이 지정된 그래프의 개체 간 관계를 설명합니다. 각 줄은 **source\_id** 개체와 하나 이상의 **dest\_id** 개체 간 관계를 나타냅니다. **edge\_value**(또는 가중치)는 **source_id**와 **dest\_id** 간 연결의 강도 또는 거리로 생각할 수 있습니다.

    개체 간 거리로 위의 값(또는 가중치)을 사용하여 그리면 다음과 같을 수 있습니다.

    ![원과 거리가 다른 선으로 그린 tiny_graph.txt](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)

2. SimpleShortestPathsComputation 예제를 실행합니다. 다음 Azure PowerShell cmdlet에서 tiny_graph.txt 파일을 입력으로 사용하여 예제를 실행합니다.

    > [!IMPORTANT]  
    > Azure Service Manager를 사용하여 HDInsight 리소스를 관리하는 Azure PowerShell 지원은 더 이상 **지원되지 않고** 2017년 1월 1일에 제거되었습니다. 이 문서의 단계에서는 Azure Resource Manager로 작동하는 새 HDInsight cmdlet을 사용합니다.
    >
    > [Azure PowerShell 설치 및 구성](/powershell/azureps-cmdlets-docs) 단계를 수행하여 최신 버전의 Azure PowerShell을 설치합니다. Azure Resource Manager로 작동하는 새로운 cmdlet을 사용하도록 수정해야 하는 스크립트가 있는 경우 자세한 내용은 [HDInsight 클러스터에 대한 Azure Resource Manager 기반 개발 도구에 마이그레이션](hdinsight-hadoop-development-using-azure-resource-manager.md) 을 참조하세요.

    ```powershell
    $clusterName = "clustername"
    # Giraph examples jar
    $jarFile = "wasb:///example/jars/giraph-examples.jar"
    # Arguments for this job
    $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
                    "-ca", "mapred.job.tracker=headnodehost:9010",
                    "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
                    "-vip", "wasb:///example/data/tiny_graph.txt",
                    "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
                    "-op",  "wasb:///example/output/shortestpaths",
                    "-w", "2"
    # Create the definition
    $jobDefinition = New-AzureHDInsightMapReduceJobDefinition
        -JarFile $jarFile
        -ClassName "org.apache.giraph.GiraphRunner"
        -Arguments $jobArguments

    # Run the job, write output to the Azure PowerShell window
    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureHDInsightJob -Job $job
    Write-Host "STDERR"
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
    ```

    위의 예제에서는 **clustername** 을 Giraph가 설치된 HDInsight 클러스터의 이름으로 바꿉니다.
3. 결과를 확인합니다. 작업이 완료되면 결과가 **wasb:///example/out/shotestpaths** 폴더에 있는 두 출력 파일에 저장됩니다. 파일 이름은 **part-m-00001** 및 **part-m-00002**입니다. 다음 단계에 따라 다운로드하고 출력을 확인합니다.

    ```powershell
    $subscriptionName = "<SubscriptionName>"       # Azure subscription name
    $storageAccountName = "<StorageAccountName>"   # Azure Storage account name
    $containerName = "<ContainerName>"             # Blob storage container name

    # Select the current subscription
    Select-AzureSubscription $subscriptionName

    # Create the Storage account context object
    $storageAccountKey = Get-AzStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    # Download the job output to the workstation
    Get-AzStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
    Get-AzStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force
    ```

    그러면 워크스테이션의 현재 디렉터리에 **example/output/shortestpaths** 디렉터리 구조가 만들어지고 해당 위치에 두 출력 파일이 다운로드됩니다.

    다음과 같이 **Cat** cmdlet을 사용하여 파일 내용을 표시합니다.

        Cat example/output/shortestpaths/part*

    출력은 다음과 유사합니다.

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation 예제는 개체 ID 1로 시작하도록 하드 코딩되며 다른 개체에 대한 가장 짧은 경로를 찾습니다. 따라서 출력은 `destination_id distance`이며, 여기서 distance는 가장자리를 기준으로 개체 ID 1과 대상 ID 간의 거리 값(또는 가중치)입니다.

    이를 시각화하면 ID 1과 다른 모든 개체 간의 가장 짧은 경로를 이동하여 결과를 확인할 수 있습니다. ID 1과 ID 4 간의 가장 짧은 경로는 5입니다. <span style="color:orange">ID 1과 3</span> 사이의 총 거리와 <span style="color:red">ID 3과 4</span> 사이의 총 거리를 더한 값입니다.

    ![가장 짧은 경로와 함께 원으로 그린 개체](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-azure-powershell"></a>Azure PowerShell을 사용하여 Giraph 설치
[스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.  샘플은 Azure PowerShell을 사용하여 Apache Spark를 설치하는 방법을 보여 줍니다. [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1)을 사용하도록 스크립트를 사용자 지정해야 합니다.

## <a name="install-giraph-using-net-sdk"></a>.NET SDK을 사용하여 Giraph 설치
[스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요. 샘플은 .NET SDK를 사용하여 Spark를 설치하는 방법을 보여줍니다. [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1)을 사용하도록 스크립트를 사용자 지정해야 합니다.

## <a name="see-also"></a>참고 항목
* [HDInsight Hadoop 클러스터에 Apache Giraph 설치(Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [HDInsight에서 Apache Hadoop 클러스터 만들기](hdinsight-provision-clusters.md): HDInsight 클러스터를 만드는 방법에 대한 일반 정보입니다.
* [스크립트 작업을 사용 하 여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md): 스크립트 작업을 사용 하 여 HDInsight 클러스터 사용자 지정에 대 한 일반 정보입니다.
* [HDInsight용 스크립트 작업 스크립트 개발](hdinsight-hadoop-script-actions.md)
* [HDInsight 클러스터에서 Apache Spark 설치 및 사용][hdinsight-install-spark]: Spark 설치에 대한 스크립트 작업 샘플입니다.

[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
