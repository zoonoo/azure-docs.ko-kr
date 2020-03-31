---
title: '자습서: Azure HDInsight에서 판매 인사이트를 파생하는 엔드투엔드 ETL 파이프라인 만들기'
description: Spark 주문형 클러스터 및 Power BI를 사용하여 판매 데이터에서 인사이트를 얻기 위해 Azure HDInsight에서 ETL 파이프라인을 만들어 사용하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 03/24/2020
ms.openlocfilehash: a4df99c45b27ad662133010422cae2e30e36e584
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247268"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>자습서: Azure HDInsight에서 판매 인사이트를 파생하는 엔드투엔드 데이터 파이프라인 만들기

이 자습서에서는 ETL(추출, 변환 및 로드) 작업을 수행하는 엔드투엔드 데이터 파이프라인을 빌드합니다. 이 파이프라인은 Azure HDInsight에서 실행되는 [Apache Spark](./spark/apache-spark-overview.md) 및 Apache Hive 클러스터를 사용하여 데이터를 쿼리하고 조작합니다. 또한 데이터 스토리지에는 Data Lake Storage Gen2 및 시각화에는 Power BI와 같은 기술을 사용할 수 있습니다.

이 데이터 파이프라인은 다양한 저장소의 데이터를 결합하고, 원치 않는 데이터를 모두 제거하고, 새 데이터를 추가한 다음, 이 모두를 스토리지에 다시 로드하여 비즈니스 인사이트를 시각화합니다. [규모에 맞게 ETL(추출, 변환 및 로드)](./hadoop/apache-hadoop-etl-at-scale.md)에서 ETL 파이프라인에 대해 자세히 참조하세요.

![ETL 아키텍처](./media/hdinsight-sales-insights-etl/architecture.png)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure CLI. [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

* [Azure 기본 제공 역할 - 소유자](../role-based-access-control/built-in-roles.md)의 멤버

* 이 자습서의 끝부분에서 비즈니스 인사이트를 시각화하는 [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331)

## <a name="create-resources"></a>리소스 만들기

### <a name="clone-the-repository-with-scripts-and-data"></a>스크립트 및 데이터를 사용하여 리포지토리 복제

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 위쪽 메뉴 모음에서 Azure Cloud Shell을 엽니다. Cloud Shell에서 메시지가 표시되면 파일 공유를 만드는 데 사용할 구독을 선택합니다.

   ![Azure Cloud Shell 열기](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)

1. **환경 선택** 드롭다운 메뉴에서 **Bash**를 선택합니다.

1. Azure 역할 [소유자](../role-based-access-control/built-in-roles.md)의 멤버인지 확인합니다. `user@contoso.com`을 사용자 계정으로 바꾸고, 다음 명령을 입력합니다.

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    레코드가 반환되지 않으면 멤버가 아니므로 이 자습서를 완료할 수 없습니다.

1. 다음 명령을 입력하여 구독을 나열합니다.

    ```azurecli
    az account list --output table
    ```

    이 프로젝트에 사용할 구독 ID를 적어둡니다.

1. 이 프로젝트에 사용할 구독을 설정합니다. `SUBSCRIPTIONID`를 실제 값으로 바꾸고, 다음 명령을 입력합니다.

    ```azurecli
    subscriptionID="SUBSCRIPTIONID"
    az account set --subscription $subscriptionID
    ```

1. 프로젝트에 대한 새 리소스 그룹을 만듭니다. `RESOURCEGROUP`을 원하는 이름으로 바꾸고, 명령을 입력합니다.

    ```azurecli
    resourceGroup="RESOURCEGROUP"
    az group create --name $resourceGroup --location westus
    ```

1. [HDInsight 판매 인사이트 ETL 리포지토리](https://github.com/Azure-Samples/hdinsight-sales-insights-etl)에서 이 자습서에 사용할 데이터와 스크립트를 다운로드합니다.  다음 명령을 입력합니다.

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. `salesdata scripts templates`가 만들어졌는지 확인합니다. 다음 명령을 사용하여 확인합니다.

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>파이프라인에 필요한 Azure 리소스 배포

1. 다음을 입력하여 모든 스크립트에 대한 실행 권한을 추가합니다.

    ```bash
    chmod +x scripts/*.sh
    ````

1. 스크립트를 실행합니다. `RESOURCE_GROUP_NAME` 및 `LOCATION`을 관련 값으로 바꾸고, 다음 명령을 입력합니다.

    ```bash
    ./scripts/resources.sh RESOURCE_GROUP_NAME LOCATION
    ```

    이 명령을 통해 배포하는 리소스는 다음과 같습니다.

    * Azure Blob 스토리지 계정. 이 계정은 회사 판매 데이터를 보관합니다.
    * Azure Data Lake Storage Gen2 계정. 이 계정은 두 HDInsight 클러스터 모두의 스토리지 계정으로 사용됩니다. HDInsight 및 Data Lake Storage Gen2에 대해서는 [Data Lake Storage Gen2와 Azure HDInsight 연결](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)에서 자세히 알아보세요.
    * 사용자가 할당한 관리 ID. 이 계정은 Data Lake Storage Gen2 계정에 대한 액세스 권한을 HDInsight 클러스터에 제공합니다.
    * Apache Spark 클러스터. 이 클러스터는 원시 데이터를 정리하고 변환하는 데 사용됩니다.
    * Apache Hive [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) 클러스터. 이 클러스터를 사용하면 Power BI를 통해 시각화된 판매 데이터를 쿼리할 수 있습니다.
    * NSG(네트워크 보안 그룹) 규칙에서 지원하는 Azure 가상 네트워크. 이 가상 네트워크를 사용하면 클러스터에서 통신하고 해당 통신을 보호할 수 있습니다.

클러스터를 만드는 데 약 20분 정도 걸릴 수 있습니다.

`resources.sh` 스크립트에는 다음 명령이 포함되어 있습니다. 이전 단계에서 스크립트를 이미 실행한 경우에는 이러한 명령을 실행할 필요가 없습니다.

* `az group deployment create` - 이 명령은 Azure Resource Manager 템플릿(`resourcestemplate.json`)을 사용하여 지정된 리소스를 원하는 구성으로 만듭니다.

    ```azurecli
    az group deployment create --name ResourcesDeployment \
        --resource-group $resourceGroup \
        --template-file resourcestemplate.json \
        --parameters "@resourceparameters.json"
    ```

* `az storage blob upload-batch` - 이 명령은 다음 명령을 사용하여 판매 데이터 .csv 파일을 새로 만든 Blob 스토리지 계정에 업로드합니다.

    ```azurecli
    az storage blob upload-batch -d rawdata \
        --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
    ```

클러스터에 대한 SSH 액세스의 기본 암호는 `Thisisapassword1`입니다. 암호를 변경하려면 `resourcesparameters.json` 파일로 이동하여 `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` 및 `llapsshPassword` 매개 변수의 암호를 변경합니다.

### <a name="verify-deployment-and-collect-resource-information"></a>배포 확인 및 리소스 정보 수집

1. 배포 상태를 확인하려면 Azure Portal에서 리소스 그룹으로 이동합니다. **설정** 아래에서 **배포**를 선택합니다. `ResourcesDeployment`라는 배포 이름을 선택합니다. 여기서는 성공적으로 배포된 리소스와 아직 진행 중인 리소스를 확인할 수 있습니다.

1. 클러스터 이름을 보려면 다음 명령을 입력합니다.

    ```azurecli
    sparkCluster=$(az hdinsight list \
        --resource-group $resourceGroup \
        --query "[?contains(name,'spark')].{clusterName:name}" -o tsv)

    llapCluster=$(az hdinsight list \
        --resource-group $resourceGroup \
        --query "[?contains(name,'llap')].{clusterName:name}" -o tsv)

    echo $sparkCluster
    echo $llapCluster
    ```

1. Azure 스토리지 계정 및 액세스 키를 보려면 다음 명령을 입력합니다.

    ```azurecli
    blobStorageName=$(cat resourcesoutputs.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Data Lake Storage Gen2 계정 및 액세스 키를 보려면 다음 명령을 입력합니다.

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>데이터 팩터리 만들기

Azure Data Factory는 Azure Pipelines를 자동화하는 데 도움이 되는 도구입니다. 이러한 작업을 수행할 수 있는 유일한 방법은 아니지만 프로세스를 자동화하는 좋은 방법입니다. Azure Data Factory에 대한 자세한 내용은 [Azure Data Factory 설명서](https://azure.microsoft.com/services/data-factory/)를 참조하세요.

이 데이터 팩터리에는 다음 두 가지 활동이 있는 하나의 파이프라인이 있습니다.

* 첫 번째 활동은 데이터를 Azure Blob 스토리지에서 Data Lake Storage Gen 2 스토리지 계정으로 복사하여 데이터 수집을 모방합니다.
* 두 번째 활동은 Spark 클러스터의 데이터를 변환합니다. 스크립트에서 필요하지 않은 열을 제거하여 데이터를 변환합니다. 또한 단일 트랜잭션에서 생성하는 수익을 계산하는 새 열을 추가합니다.

Azure Data Factory 파이프라인을 설정하려면 다음 명령을 실행합니다.

```bash
./scripts/adf.sh
```

이 스크립트에서 수행하는 작업은 다음과 같습니다.

1. Data Lake Storage Gen2 스토리지 계정에 대한 `Storage Blob Data Contributor` 권한이 있는 서비스 주체를 만듭니다.
1. [Data Lake Storage Gen2 파일 시스템 REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create)에 대한 POST 요청에 권한을 부여하기 위한 인증 토큰을 가져옵니다.
1. Data Lake Storage Gen2 스토리지 계정의 실제 이름을 `sparktransform.py` 및 `query.hql` 파일에 채웁니다.
1. Data Lake Storage Gen2 및 Blob 스토리지 계정에 대한 스토리지 키를 가져옵니다.
1. 연결된 서비스 및 활동을 사용하여 Azure Data Factory 파이프라인을 만드는 다른 리소스 배포를 만듭니다. 연결된 서비스에서 스토리지 계정에 올바르게 액세스할 수 있도록 스토리지 키를 매개 변수로 템플릿 파일에 전달합니다.

Data Factory 파이프라인은 다음 명령을 통해 배포됩니다.

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>데이터 파이프라인 실행

### <a name="trigger-the-data-factory-activities"></a>Data Factory 활동 트리거

만든 Data Factory 파이프라인의 첫 번째 활동은 데이터를 Blob 스토리지에서 Data Lake Storage Gen2로 이동합니다. 두 번째 활동은 Spark 변환을 데이터에 적용하고, 변환된 .csv 파일을 새 위치에 저장합니다. 전체 파이프라인을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

파이프라인을 트리거하려면 다음 중 하나를 수행할 수 있습니다.

* PowerShell에서 Data Factory 파이프라인을 트리거합니다. `DataFactoryName`을 실제 Data Factory 이름으로 바꾸고, 다음 명령을 실행합니다.

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory DataFactoryName -PipelineName "CopyPipeline_k8z"
    Invoke-AzDataFactoryV2Pipeline -DataFactory DataFactoryName -PipelineName "sparkTransformPipeline"
    ```

    또는

* 데이터 팩터리를 열고, **작성자 및 모니터링**을 선택합니다. 포털에서 복사 파이프라인, Spark 파이프라인을 차례로 트리거합니다. 포털을 통해 파이프라인을 트리거하는 방법에 대한 내용은 [Azure Data Factory를 사용하여 HDInsight에서 주문형 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline)를 참조하세요.

파이프라인이 실행되었는지 확인하려면 다음 단계 중 하나를 수행할 수 있습니다.

* 포털을 통해 데이터 팩터리의 **모니터** 섹션으로 이동합니다.
* Azure Storage Explorer에서 Data Lake Storage Gen 2 스토리지 계정으로 이동합니다. `files` 파일 시스템, `transformed` 폴더로 차례로 이동하고, 해당 내용을 확인하여 파이프라인이 성공했는지 확인합니다.

HDInsight를 사용하여 데이터를 변환하는 다른 방법은 [Jupyter Notebook 사용에 대한 이 문서](/azure/hdinsight/spark/apache-spark-load-data-run-query)를 확인하세요.

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>테이블을 대화형 쿼리 클러스터에 만들어 Power BI에서 데이터 보기

1. SCP를 사용하여 `query.hql` 파일을 LLAP 클러스터에 복사합니다. `LLAPCLUSTERNAME`을 실제 이름으로 바꾸고, 다음 명령을 입력합니다.

    ```bash
    scp scripts/query.hql sshuser@LLAPCLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. SSH를 사용하여 LLAP 클러스터에 액세스합니다. `LLAPCLUSTERNAME`을 실제 이름으로 바꾸고, 다음 명령을 입력합니다. `resourcesparameters.json` 파일을 변경하지 않은 경우 암호는 `Thisisapassword1`입니다.

    ```bash
    ssh sshuser@LLAPCLUSTERNAME-ssh.azurehdinsight.net
    ```

3. 다음 명령을 사용하여 스크립트를 실행합니다.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

이 스크립트는 Power BI에서 액세스할 수 있는 대화형 쿼리 클러스터에 관리형 테이블을 만듭니다.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>판매 데이터에서 Power BI 대시보드 만들기

1. Power BI Desktop을 엽니다.
1. **데이터 가져오기**를 선택합니다.
1. **HDInsight 대화형 쿼리 클러스터**를 검색합니다.
1. 클러스터에 대한 URI를 여기에 붙여넣습니다. `https://LLAPCLUSTERNAME.azurehdinsight.net` 형식이어야 합니다.

   데이터베이스에 대해 `default`를 입력합니다.
1. 클러스터에 액세스하는 데 사용하는 사용자 이름과 암호를 입력합니다.

데이터가 로드되면 만들려는 대시보드를 사용하여 실험할 수 있습니다. Power BI 대시보드를 시작하려면 다음 링크를 참조하세요.

* [Power BI 디자이너용 대시보드 소개](https://docs.microsoft.com/power-bi/service-dashboards)
* [자습서: Power BI 서비스 시작](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않을 경우 요금이 청구되지 않도록 다음 명령을 사용하여 모든 리소스를 삭제합니다.

```azurecli-interactive
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [규모에 맞게 ETL(추출, 변환 및 로드)](./hadoop/apache-hadoop-etl-at-scale.md)
