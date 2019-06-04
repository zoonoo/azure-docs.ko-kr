---
title: Cosmos DB 엔드포인트를 사용하여 Azure Databricks 구현
description: 이 자습서에서는 Cosmos DB에 대해 서비스 엔드포인트를 사용하도록 설정한 가상 네트워크에서 Azure Databricks를 구현하는 방법을 설명합니다.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: 0d5442a63680227f3a6186330502666c92dc3129
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012852"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>자습서: Cosmos DB 엔드포인트를 사용하여 Azure Databricks 구현

이 자습서에서는 Cosmos DB에 대해 서비스 엔드포인트를 사용하도록 설정한 Databricks 환경이 VNet에 삽입되도록 구현하는 방법에 대해 설명합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 가상 네트워크에 Azure Databricks 작업 영역 만들기
> * Cosmos DB 서비스 엔드포인트 만들기
> * Cosmos DB 계정 만들기 및 데이터 가져오기
> * Azure Databricks 클러스터 만들기
> * Azure Databricks Notebook에서 Cosmos DB 쿼리

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음을 수행합니다.

* 가상 네트워크에 [Azure Databricks 작업 영역](quickstart-create-databricks-workspace-vnet-injection.md)을 만듭니다.

* [Spark 커넥터](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar)를 다운로드합니다.

* [NOAA National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/)에서 샘플 데이터를 다운로드합니다. 상태 또는 영역을 선택하고 **검색**을 선택합니다. 다음 페이지에서 기본값을 그대로 수락하고 **검색**을 선택합니다. 그런 다음, 페이지의 왼쪽에서 **CSV 다운로드**를 선택하여 결과를 다운로드합니다.

* Azure Cosmos DB 데이터 마이그레이션 도구의 [미리 컴파일된 이진 파일](https://aka.ms/csdmtool)을 다운로드합니다.

## <a name="create-a-cosmos-db-service-endpoint"></a>Cosmos DB 서비스 엔드포인트 만들기

1. Azure Databricks 작업 영역이 가상 네트워크에 배포되면 [Azure Portal](https://portal.azure.com)에서 가상 네트워크로 이동합니다. Databricks 배포를 통해 만들어진 공용 및 프라이빗 서브넷을 확인합니다.

   ![가상 네트워크 서브넷](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. *public-subnet*을 선택하고, Cosmos DB 서비스 엔드포인트를 만듭니다. 그런 다음, **저장**합니다.
   
   ![Cosmos DB 서비스 엔드포인트 추가](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Cosmos DB 계정 만들기

1. Azure Portal을 엽니다. 화면의 왼쪽 위에서 **리소스 만들기 > 데이터베이스 > Azure Cosmos DB**를 차례로 선택합니다.

2. **기본 사항** 탭에서 다음 설정을 사용하여 **인스턴스 세부 정보**를 작성합니다.

   |설정|값|
   |-------|-----|
   |구독|*구독*|
   |리소스 그룹|*리소스 그룹*|
   |계정 이름|db-vnet-service-endpoint|
   |API|Core(SQL)|
   |위치|미국 서부|
   |지리적 중복|사용 안 함|
   |다중 영역 쓰기|사용|

   ![Cosmos DB 서비스 엔드포인트 추가](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. **네트워크** 탭을 선택하고 가상 네트워크를 구성합니다. 

   a. 만든 가상 네트워크를 필수 구성 요소로 선택한 다음, *public-subnet*을 선택합니다. *public-subnet*에는 *'Microsoft AzureCosmosDB' 엔드포인트가 없습니다.'* 라는 메모가 있습니다. 이는 *public-subnet*에서만 Cosmos DB 서비스 엔드포인트를 사용하도록 설정했기 때문입니다.

   b. **Azure Portal에서 액세스하도록 허용**을 사용하도록 설정되어 있는지 확인합니다. 이 설정을 사용하면 Azure Portal에서 Cosmos DB 계정에 액세스할 수 있습니다. 이 옵션을 **거부**로 설정하면 계정에 액세스하려고 할 때 오류가 발생합니다. 

   > [!NOTE]
   > 이 자습서에는 필요하지 않지만 로컬 머신에서 Cosmos DB 계정에 액세스하는 기능을 사용하려면 *내 IP에서 액세스 허용*을 사용하도록 설정할 수도 있습니다. 예를 들어 Cosmos DB SDK를 사용하여 계정에 연결하는 경우 이 설정을 사용하도록 설정해야 합니다. 사용하지 않도록 설정되는 경우 "액세스 거부" 오류가 발생합니다.

   ![Cosmos DB 계정 네트워크 설정](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. **검토 + 만들기**를 선택한 다음, **만들기**를 선택하여 가상 네트워크 내에 Cosmos DB 계정을 만듭니다.

5. Cosmos DB 계정이 만들어지면 **설정** 아래의 **키**로 이동합니다. 기본 연결 문자열을 복사하여 나중에 사용할 수 있도록 텍스트 편집기에 저장합니다.

    ![Cosmos DB 계정 키 페이지](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. **데이터 탐색기** 및 **새 컬렉션**을 선택하여 새 데이터베이스 및 컬렉션을 Cosmos DB 계정에 추가합니다.

    ![새 Cosmos DB 컬렉션](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Cosmos DB에 데이터 업로드

1. [Cosmos DB용 데이터 마이그레이션 도구](https://aka.ms/csdmtool)인 **Dtui.exe**의 그래픽 인터페이스 버전을 엽니다.

    ![Cosmos DB 데이터 마이그레이션 도구](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. **원본 정보** 탭의 **다음에서 가져오기** 드롭다운에서 **CSV 파일**을 선택합니다. 그런 다음, **파일 추가**를 선택하고, 다운로드한 storm 데이터 CSV를 필수 구성 요소로 추가합니다.

    ![Cosmos DB 데이터 마이그레이션 도구 원본 정보](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. **대상 정보** 탭에서 연결 문자열을 입력합니다. 연결 문자열 형식은 `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`입니다. AccountEndpoint 및 AccountKey는 이전 섹션에서 저장한 기본 연결 문자열에 포함됩니다. `Database=<your database name>`을 연결 문자열 끝에 추가하고 **확인**을 선택합니다. 그런 다음, 컬렉션 이름과 파티션 키를 추가합니다.

    ![Cosmos DB 데이터 마이그레이션 도구 대상 정보](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. [요약] 페이지에 도달할 때까지 **다음**을 선택합니다. 그런 다음, **가져오기**를 선택합니다.

## <a name="create-a-cluster-and-add-library"></a>클러스터 만들기 및 라이브러리 추가

1. [Azure Portal](https://portal.azure.com)에서 Azure Databricks 서비스로 이동하여 **작업 영역 시작**을 선택합니다.

   ![Databricks 작업 영역 시작](./media/service-endpoint-cosmosdb/launch-workspace.png)

2. 새 클러스터를 만듭니다. 클러스터 이름을 선택하고 나머지 기본 설정을 그대로 수락합니다.

   ![새 클러스터 설정](./media/service-endpoint-cosmosdb/create-cluster.png)

3. 클러스터가 만들어지면 클러스터 페이지로 이동하여 **라이브러리** 탭을 선택합니다. **새로 설치**를 선택하고, Spark 커넥터 jar 파일을 업로드하여 라이브러리를 설치합니다.

    ![Spark 커넥터 라이브러리 설치](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    **라이브러리** 탭에서 라이브러리가 설치되어 있는지 확인할 수 있습니다.

    ![Databricks 클러스터 라이브러리 탭](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Databricks Notebook에서 Cosmos DB 쿼리

1. Azure Databricks 작업 영역으로 이동하여 새 Python Notebook을 만듭니다.

    ![새 Databricks Notebook 만들기](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. 다음 Python 코드를 실행하여 Cosmos DB 연결 구성을 설정합니다. **Endpoint**, **Masterkey**, **Database** 및 **Collection**을 적절하게 변경합니다.

    ```python
    connectionConfig = {
      "Endpoint" : "https://<your Cosmos DB account name.documents.azure.com:443/",
      "Masterkey" : "<your Cosmos DB primary key>",
      "Database" : "<your database name>",
      "preferredRegions" : "West US 2",
      "Collection": "<your collection name>",
      "schema_samplesize" : "1000",
      "query_pagesize" : "200000",
      "query_custom" : "SELECT * FROM c"
    }
    ```

3. 다음 Python 코드를 사용하여 데이터를 로드하고 임시 보기를 만듭니다.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. 다음 매직 명령을 사용하여 데이터를 반환하는 SQL 문을 실행합니다.

    ```python
    %sql
    select * from storm
    ```

    VNet에 삽입된 Databricks 작업 영역을 서비스 엔드포인트 사용 Cosmos DB 리소스에 성공적으로 연결했습니다. Cosmos DB에 연결하는 방법에 대한 자세한 내용은 [Apache Spark용 Azure Cosmos DB 커넥터](https://github.com/Azure/azure-cosmosdb-spark)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹, Azure Databricks 작업 영역 및 모든 관련 리소스를 삭제합니다. 작업을 삭제하면 불필요한 요금 청구를 방지합니다. 나중에 Azure Databricks 작업 영역을 사용하려는 경우 클러스터를 중지하고 나중에 다시 시작할 수 있습니다. 이 Azure Databricks 작업 영역을 계속 사용하지 않으려면 다음 단계를 사용하여 이 자습서에서 만든 모든 리소스를 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음, 만든 리소스 그룹의 이름을 클릭합니다.

2. 리소스 그룹 페이지에서 **삭제**를 선택하고, 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음, **삭제**를 다시 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Databricks 작업 영역을 가상 네트워크에 배포하고, Cosmos DB Spark 커넥터를 사용하여 Databricks에서 Cosmos DB 데이터를 쿼리했습니다. 가상 네트워크에서 Azure Databricks를 사용하는 방법에 대해 자세히 알아보려면 Azure Databricks에서 SQL Server를 사용하는 방법에 대한 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [자습서: Azure Databricks Notebook에서 가상 네트워크의 SQL Server Linux Docker 컨테이너 쿼리](vnet-injection-sql-server.md)