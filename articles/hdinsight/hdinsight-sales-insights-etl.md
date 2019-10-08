---
title: '자습서: 판매 인사이트를 얻기 위한 엔드투엔드 ETL(추출, 변환, 로드) 파이프라인 만들기'
description: Spark 주문형 클러스터 및 Power BI를 사용하여 판매 데이터에서 인사이트를 얻기 위해 Azure HDInsight에서 ETL 파이프라인을 만들어 사용하는 방법을 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: 52509f886ac9882c372de401ca163ccef418837f
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695708"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>자습서: 판매 인사이트를 얻기 위한 엔드투엔드 데이터 파이프라인 만들기

이 자습서에서는 추출, 변환 및 로드 작업을 수행하는 엔드투엔드 데이터 파이프라인을 작성합니다. 이 파이프라인은 Azure HDInsight에서 실행되는 Apache Spark 및 Apache Hive 클러스터를 사용하여 데이터를 쿼리하고 조작합니다. 사용되는 다른 기술로 데이터 스토리지용 Data Lake Storage Gen2 및 시각화용 Power BI가 있습니다.

이 데이터 파이프라인은 다양한 모든 저장소의 데이터를 결합하고, 원치 않는 데이터를 모두 제거하고, 새 데이터를 추가한 다음, 이를 스토리지에 다시 로드하여 비즈니스 인사이트를 시각화합니다. 파이프라인에 대한 자세한 내용은 [규모에 맞게 ETL(추출, 변환 및 로드)](./hadoop/apache-hadoop-etl-at-scale.md)을 참조하세요.

![ETL 아키텍처](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>필수 조건

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

이 자습서의 끝 부분에서 비즈니스 인사이트를 시각화하려면 [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331)을 다운로드합니다.

## <a name="create-resources"></a>리소스 만들기

### <a name="clone-the-repository-with-scripts-and-data"></a>스크립트 및 데이터를 사용하여 리포지토리 복제

1. [Azure 포털](https://portal.azure.com)
1. 위쪽 메뉴 모음에서 Cloud Shell을 엽니다. Azure Cloud Shell에서 메시지가 표시되면 파일 공유를 만드는 데 사용할 구독을 선택합니다.

    ![Azure Cloud Shell 열기](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. "환경 선택" 드롭다운 메뉴에서 "Bash"를 선택합니다.
1. Azure 계정에 로그인하고 구독을 설정합니다. 
1. 프로젝트의 리소스 그룹을 설정합니다.
    1. 고유한 리소스 그룹 이름을 선택합니다.
    1. Azure Cloud Shell에서 아래의 코드 조각을 실행하여 이후 단계에서 사용할 변수를 설정합니다.

        ```azurecli-interactive 
        resourceGroup="<RESOURCE GROUP NAME>"
        subscriptionID="<SUBSCRIPTION ID>"
        
        az account set --subscription $subscriptionID
        az group create --name $resourceGroup --location westus
        ```

1. Cloud Shell에서 다음 명령을 입력하여 [HDInsight 판매 인사이트 ETL 리포지토리](https://github.com/Azure-Samples/hdinsight-sales-insights-etl)에서 이 자습서에서 사용할 데이터와 스크립트를 다운로드합니다.

        ```azurecli-interactive 
        git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
        cd hdinsight-sales-insights-etl
        ```

1. 셸 프롬프트에서 `ls`를 입력하여 다음 파일 및 디렉터리가 만들어졌는지 확인합니다.

    ```output
    /salesdata/
    /scripts/
    /templates/
    ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>파이프라인에 필요한 Azure 리소스 배포 

1. `chmod +x scripts/*.sh` 스크립트에 대한 실행 권한을 추가합니다.
1. `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>` 명령을 사용하여 Azure에서 다음 리소스를 배포하는 스크립트를 실행합니다.

    1. Azure Blob Storage 계정 - 이 계정은 회사 판매 데이터를 보관합니다.
    2. Azure Data Lake Storage Gen2 계정 - 이 계정은 두 HDInsight 클러스터 모두의 스토리지 계정으로 사용됩니다. HDInsight 및 Data Lake Storage Gen2에 대해서는 [Data Lake Storage Gen2와 Azure HDInsight 연결](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)에서 자세히 알아보세요.
    3. 사용자가 할당한 관리 ID - 이 계정은 Data Lake Storage Gen2 계정에 대한 액세스 권한을 HDInsight 클러스터에 제공합니다.
    4. Apache Spark 클러스터 - 이 클러스터는 원시 데이터를 정리하고 변환하는 데 사용됩니다.
    5. Apache Hive 대화형 쿼리 클러스터 - 이 클러스터를 사용하면 Power BI를 통해 시각화된 판매 데이터를 쿼리할 수 있습니다.
    6. NSG(네트워크 보안 그룹) 규칙으로 지원되는 Azure 가상 네트워크 - 이 가상 네트워크를 사용하면 클러스터에서 통신하고 해당 통신을 보호할 수도 있습니다. 

클러스터를 만드는 데 약 20분 정도 걸릴 수 있습니다.

`resources.sh` 스크립트에는 Resource Manager 템플릿(`resourcestemplate.json`)을 사용하여 원하는 구성으로 지정된 리소스를 만드는 다음 명령이 포함되어 있습니다.

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

또한 `resources.sh` 스크립트는 다음 명령을 사용하여 판매 데이터 csv 파일을 새로 만든 Blob 스토리지 계정에 업로드합니다.

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

클러스터에 대한 ssh 액세스에 사용되는 기본 암호는 `Thisisapassword1`입니다. 암호를 변경하려면 `resourcesparameters.json` 파일로 이동하여 `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` 및 `llapsshPassword` 매개 변수의 암호를 변경합니다.

### <a name="verify-deployment-and-collect-resource-information"></a>배포 확인 및 리소스 정보 수집

1. 배포 상태를 확인하려면 Azure Portal에서 리소스 그룹으로 이동합니다. **설정** 아래에서 **배포**를 클릭합니다. `ResourcesDeployment`라는 배포 이름을 클릭합니다. 여기서는 성공적으로 배포된 리소스와 아직 진행 중인 리소스를 확인할 수 있습니다.
1. 배포가 완료되면 Azure Portal > **리소스 그룹** > <RESOURCE_GROUP_NAME>으로 차례로 이동합니다.
1. 판매 파일을 저장하기 위해 만든 새 Azure 스토리지 계정을 찾습니다. 스토리지 계정의 이름은 `blob`으로 시작하고, 임의의 문자열을 포함합니다. 
    1. 나중에 사용할 수 있도록 스토리지 계정 이름을 적어 둡니다.
    1. Blob 스토리지 계정의 이름을 클릭합니다.
    1. 포털 왼쪽의 **설정** 아래에서 **액세스 키**를 클릭합니다.
    1. **Key1** 상자에서 문자열을 복사하여 나중에 사용할 수 있도록 저장합니다.
1. HDInsight 클러스터용 스토리지로 만든 Data Lake Storage Gen2 계정을 찾습니다. 이 계정은 Blob 스토리지 계정과 동일한 리소스 그룹에 있지만 `adlsgen2`로 시작합니다.
    1. Data Lake Storage Gen2 계정의 이름을 적어 둡니다.
    1. Data Lake Storage Gen2 계정의 이름을 클릭합니다.
    1. 포털 왼쪽의 **설정** 아래에서 **액세스 키**를 클릭합니다.
    1. **Key1** 상자에서 문자열을 복사하여 나중에 사용할 수 있도록 저장합니다.

> [!Note]
> 스토리지 계정의 이름을 알고 있으면 Azure Cloud Shell 프롬프트에서 다음 명령을 사용하여 계정 키를 가져올 수도 있습니다.
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-an-azure-data-factory"></a>Azure Data Factory 만들기

Azure Data Factory는 Azure Pipelines를 자동화하는 데 도움이 되는 도구입니다. 이러한 작업을 수행할 수 있는 유일한 방법은 아니지만 이러한 프로세스를 자동화하는 좋은 방법입니다. Azure Data Factory에 대한 자세한 내용은 [Azure Data Factory 설명서](https://azure.microsoft.com/services/data-factory/)를 참조하세요. 

이 Azure Data Factory에는 다음 두 가지 활동이 있는 하나의 파이프라인이 있습니다. 

1. 첫 번째 활동은 데이터를 Azure Blob Storage에서 Data Lake Storage Gen 2 스토리지 계정으로 복사하여 데이터 수집을 모방합니다.
2. 두 번째 활동은 Spark 클러스터의 데이터를 변환합니다. 이 스크립트는 원치 않는 열을 제거하고 단일 트랜잭션에서 생성된 수익을 계산하는 새 열을 추가하여 데이터를 변환합니다.

Azure Data Factory 파이프라인을 설정하려면 `adf.sh` 스크립트를 실행합니다.

1. `chmod +x adf.sh`를 사용하여 파일에 대한 실행 권한 추가
1. `./adf.sh`를 사용하여 스크립트 실행 

이 스크립트에서 수행하는 작업은 다음과 같습니다.

1. Data Lake Storage Gen2 스토리지 계정에 대한 `Storage Blob Data Contributor` 권한이 있는 서비스 주체를 만듭니다.
1. [Data Lake Storage Gen2 FileSystem REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create)에 대한 POST 요청에 권한을 부여하기 위한 인증 토큰을 가져옵니다.
1. Data Lake Storage Gen2 스토리지 계정의 실제 이름을 `sparktransform.py` 및 `query.hql` 파일에 채웁니다.
1. Data Lake Storage Gen2 및 Blob Storage 계정에 대한 스토리지 키를 가져옵니다.
1. 연결된 서비스 및 활동을 사용하여 Azure Data Factory 파이프라인을 만드는 다른 리소스 배포를 만듭니다. 연결된 서비스에서 스토리지 계정에 올바르게 액세스할 수 있도록 스토리지 키를 매개 변수로 템플릿 파일에 전달합니다.

ADF 파이프라인은 다음 명령을 사용하여 배포됩니다.

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>데이터 파이프라인 실행

### <a name="trigger-the-adf-activities"></a>ADF 활동 트리거

만든 ADF 파이프라인의 첫 번째 활동은 데이터를 Blob 스토리지에서 Data Lake Storage Gen2로 이동합니다. 두 번째 활동에서는 Spark 변환을 데이터에 적용하고, 변환된 csv 파일을 새 위치에 저장합니다. 전체 파이프라인을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

파이프라인을 트리거하려면 다음 중 하나를 수행할 수 있습니다.

1. 다음 명령을 실행하여 PowerShell에서 ADF 파이프라인을 트리거합니다. 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

1. Data Factory를 열고, [작성자 및 모니터]를 선택하고, 복사 파이프라인을 트리거한 다음, 포털에서 Spark 파이프라인을 트리거할 수도 있습니다. 포털을 통해 파이프라인을 트리거하는 방법에 대한 내용은 [Azure Data Factory를 사용하여 HDInsight에서 주문형 Apache Hadoop 클러스터 만들기](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-create-linux-clusters-adf#trigger-a-pipeline)를 참조하세요.

파이프라인이 실행되었는지 확인하려면 다음 단계 중 하나를 수행할 수 있습니다.

1. 포털을 통해 Azure Data Factory의 **모니터** 섹션으로 이동합니다.
2. Data Lake Storage Gen 2 스토리지 계정 스토리지 탐색기, `files` 파일 시스템, `transformed` 폴더로 차례로 이동한 다음, 해당 내용을 확인하여 파이프라인이 성공했는지 확인합니다.

HDInsight를 사용하여 데이터를 변환하는 다른 방법은 [Jupyter Notebook](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-load-data-run-query) 사용에 대한 이 문서를 확인하세요.

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>테이블을 대화형 쿼리 클러스터에 만들어 Power BI에서 데이터 보기

1. SCP를 사용하여 query.hql 파일을 LLAP 클러스터에 복사합니다.

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. 다음 명령을 사용하여 SSH를 통해 LLAP 클러스터에 연결한 다음, 암호를 입력합니다. `resourcesparameters.json` 파일을 변경하지 않은 경우 암호는 `Thisisapassword1`입니다.

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. 다음 명령을 실행하여 스크립트를 실행합니다.

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

이 스크립트는 Power BI에서 액세스할 수 있는 대화형 쿼리 클러스터에 관리형 테이블을 만듭니다. 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>판매 데이터에서 Power BI 대시보드 만들기

1. Power BI Desktop을 엽니다.
1. **데이터 가져오기**를 선택합니다.
1. **HDInsight 대화형 쿼리 클러스터**를 검색합니다.
1. 클러스터에 대한 URI를 여기에 붙여넣습니다. `https://<LLAP CLUSTER NAME>.azurehdinsight.net` 형식이어야 합니다. 데이터베이스에 대해 `default`를 입력합니다.
1. 클러스터에 액세스하는 데 사용하는 사용자 이름과 암호를 입력합니다.

데이터가 로드되면 만들려는 대시보드로 시험해 볼 수 있습니다. Power BI 대시보드를 시작하려면 다음 링크를 참조하세요.

* [Power BI 디자이너용 대시보드 소개](https://docs.microsoft.com/power-bi/service-dashboards)
* [자습서: Power BI 서비스 시작](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않을 경우 요금이 청구되지 않도록 다음 단계에 따라 모든 리소스를 삭제합니다.

```azurecli-interactive 
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [규모에 맞게 ETL(추출, 변환 및 로드)](./hadoop/apache-hadoop-etl-at-scale.md)
