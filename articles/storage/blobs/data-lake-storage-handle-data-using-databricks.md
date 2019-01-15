---
title: '자습서: Azure Databricks를 사용하여 추출, 로드 및 전송 작업을 수행하는 방법 알아보기'
description: 이 자습서에서는 Azure Data Lake Storage Gen2 미리 보기에서 Azure Databricks로 데이터를 추출하고, 데이터를 변환한 다음, Azure SQL Data Warehouse에 데이터를 로드하는 방법을 알아봅니다.
services: storage
author: jamesbak
ms.service: storage
ms.author: jamesbak
ms.topic: tutorial
ms.date: 12/06/2018
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 6b2812e31174c4e5d61ae9941563e39357de9522
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107092"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>자습서: Azure Databricks를 사용하여 데이터 추출, 변환 및 로드

이 자습서에서는 Azure Databricks를 사용하여 ETL(데이터 추출, 변환 및 로드) 작업을 수행합니다. Azure Data Lake Storage Gen2가 설정된 Azure Storage 계정에서 Azure SQL Data Warehouse로 데이터를 이동합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Databricks 작업 영역 만들기
> * Azure Databricks에 Spark 클러스터 만들기
> * Azure Data Lake Storage Gen2 지원 계정 만들기
> * Azure Data Lake Storage Gen2에 데이터 업로드
> * Azure Databricks에 Notebook 만들기
> * Data Lake Storage Gen2에서 데이터 추출
> * Azure Databricks에서 데이터 변환
> * Azure SQL Data Warehouse에 데이터를 로드합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* Azure SQL Data Warehouse를 만들고, 서버 수준 방화벽 규칙을 만들고, 서버 관리자로 서버에 연결합니다. [빠른 시작: Azure SQL Data Warehouse 만들기](../../sql-data-warehouse/create-data-warehouse-portal.md) 문서의 지침을 따릅니다.
* Azure SQL Data Warehouse에 대한 데이터베이스 마스터 키를 만듭니다. [데이터베이스 마스터 키 만들기](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key) 문서의 지침을 따릅니다.
* [Azure Data Lake Storage Gen2 계정을 만듭니다](data-lake-storage-quickstart-create-account.md).
* [U-SQL 예제 및 문제 추적](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) 리포지토리에서 (**small_radio_json.json**)을 다운로드하고 파일을 저장할 경로를 적어둡니다.
* [Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-the-workspace"></a>작업 영역 만들기

이 섹션에서는 Azure Portal을 사용하여 Azure Databricks 작업 영역을 만듭니다.

1. Azure Portal에서 **리소스 만들기** > **분석** > **Azure Databricks**를 차례로 선택합니다.

    ![Azure Portal의 Databricks](./media/data-lake-storage-handle-data-using-databricks/azure-databricks-on-portal.png "Azure Portal의 Databricks")

1. **Azure Databricks 서비스** 아래에서 다음 값을 입력하여 Databricks 작업 영역을 만듭니다.

    |자산  |설명  |
    |---------|---------|
    |**작업 영역 이름**     | Databricks 작업 영역에 대한 이름을 제공합니다.        |
    |**구독**     | 드롭다운에서 Azure 구독을 선택합니다.        |
    |**리소스 그룹**     | 새 리소스 그룹을 만들지, 아니면 기존 그룹을 사용할지 여부를 지정합니다. 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)를 참조하세요. |
    |**위치**:     | **미국 서부 2**를 선택합니다.        |
    |**가격 책정 계층**     |  **표준**을 선택합니다.     |

    ![Azure Databricks 작업 영역 만들기](./media/data-lake-storage-handle-data-using-databricks/create-databricks-workspace.png "Azure Databricks 작업 영역 만들기")

1. **대시보드에 고정**을 선택한 다음, **만들기**를 선택합니다.

1. 계정 생성에는 몇 분 정도가 소요됩니다. 계정을 만드는 동안 포털의 오른쪽에 **Azure Databricks에 대한 배포 제출** 타일이 표시됩니다. 작업 상태를 모니터링하려면 맨 위에 있는 진행률 표시줄을 확인합니다.

    ![Databricks 배포 타일](./media/data-lake-storage-handle-data-using-databricks/databricks-deployment-tile.png "Databricks 배포 타일")

## <a name="create-the-spark-cluster"></a>Spark 클러스터 만들기

이 자습서의 작업을 수행하려면 Spark 클러스터가 필요합니다. 다음 단계에 따라 Spark 클러스터를 만듭니다.

1. Azure Portal에서 본인이 만든 Databricks 작업 영역으로 이동한 다음, **작업 영역 시작**을 선택합니다.

1. Azure Databricks 포털로 리디렉션됩니다. 포털에서 **클러스터**를 선택합니다.

    ![Azure의 Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-on-azure.png "Azure의 Databricks")

1. **새 클러스터** 페이지에서 값을 제공하여 클러스터를 만듭니다.

    ![Azure에서 Databricks Spark 클러스터 만들기](./media/data-lake-storage-handle-data-using-databricks/create-databricks-spark-cluster.png "Azure에서 Databricks Spark 클러스터 만들기")

1. 다음 필드에 대한 값을 입력하고, 다른 필드에는 기본값을 그대로 적용합니다.

    * 클러스터의 이름을 입력합니다.
    * 이 문서에서는 **5.1** 런타임을 사용하여 클러스터를 만듭니다.
    * **비활성 \_\_분 후 종료** 확인란을 선택합니다. 클러스터가 사용되지 않는 경우 클러스터를 종료할 시간(분)을 입력합니다.

1. **클러스터 만들기**를 선택합니다.

클러스터가 실행되면 Notebook을 클러스터에 연결하고 Spark 작업을 실행할 수 있습니다.

## <a name="create-a-file-system"></a>파일 시스템 만들기

Data Lake Storage Gen2 스토리지 계정에 데이터를 저장하려면 파일 시스템을 만들어야 합니다.

먼저 Azure Databricks 작업 영역에서 노트북을 만든 다음, 코드 조각을 실행하여 스토리지 계정에서 파일 시스템을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 본인이 만든 Azure Databricks 작업 영역으로 이동한 다음, **작업 영역 시작**을 선택합니다.

1. 왼쪽 창에서 **작업 영역**을 선택합니다. **작업 영역** 드롭다운에서 **만들기** > **Notebook**을 차례로 선택합니다.

    ![Databricks에서 Notebook 만들기](./media/data-lake-storage-handle-data-using-databricks/databricks-create-notebook.png "Databricks에서 Notebook 만들기")

1. **노트북 만들기** 대화 상자에서 노트북 이름을 입력합니다. 언어로 **Scala**를 선택한 다음, 앞에서 만든 Spark 클러스터를 선택합니다.

    ![Databricks에서 Notebook에 대한 세부 정보 입력](./media/data-lake-storage-handle-data-using-databricks/databricks-notebook-details.png "Databricks에서 Notebook에 대한 세부 정보 입력")

    **만들기**를 선택합니다.

1. 첫 번째 Notebook 셀에 다음 코드를 입력하고 코드를 실행합니다. 샘플의 대괄호 안에 표시된 자리 표시자를 사용자 고유의 값으로 바꿉니다.

    ```scala
    %python%
    configs = {"fs.azure.account.auth.type": "OAuth",
        "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
        "fs.azure.account.oauth2.client.id": "<service-client-id>",
        "fs.azure.account.oauth2.client.secret": "<service-credentials>",
        "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token"}
     
    dbutils.fs.mount(
        source = "abfss://<file-system-name>@<account-name>.dfs.core.windows.net/[<directory-name>]",
        mount_point = "/mnt/<mount-name>",
        extra_configs = configs)
    ```

1. Shift+Enter 키를 선택하여 코드를 실행합니다.

이제 저장소 계정에 대한 파일 시스템이 만들어집니다.

## <a name="upload-the-sample-data"></a>샘플 파일 업로드

다음 단계는 나중에 Azure Databricks에서 변환할 샘플 데이터 파일을 스토리지 계정에 업로드하는 것입니다.

다운로드한 샘플 데이터를 스토리지 계정에 업로드합니다. 스토리지 계정에 데이터를 업로드하는 방법은 계층 구조 네임스페이스 사용 여부에 따라 달라집니다.

업로드에 Azure Data Factory, distp 또는 AzCopy(버전 10)를 사용할 수 있습니다. AzCopy 버전 10은 현재 미리 보기로만 제공됩니다. AzCopy를 사용하려면 다음 코드를 명령 창에 붙여넣습니다.

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_PATH>\small_radio_json.json" https://<ACCOUNT_NAME>.dfs.core.windows.net/data --recursive 
```

## <a name="extract-the-data"></a>데이터 추출

Databricks에서 샘플 데이터를 사용하려면 스토리지 계정에서 데이터를 추출해야 합니다.

Databricks Notebook으로 돌아가서 Notebook의 새 셀에 다음 코드를 입력합니다.

빈 코드 셀에 다음 코드 조각을 추가합니다. 대괄호 안에 표시된 자리 표시자를 앞에서 스토리지 계정에 저장한 값으로 바꿉니다.

```scala
dbutils.widgets.text("storage_account_name", "STORAGE_ACCOUNT_NAME", "<YOUR_STORAGE_ACCOUNT_NAME>")
dbutils.widgets.text("storage_account_access_key", "YOUR_ACCESS_KEY", "<YOUR_STORAGE_ACCOUNT_SHARED_KEY>")
```

Shift+Enter 키를 선택하여 코드를 실행합니다.

이제 Azure Databricks에서 샘플 json 파일을 데이터 프레임으로 로드할 수 있습니다. 다음 코드를 새 셀에 붙여넣습니다. 대괄호 안에 표시된 자리 표시자를 사용자 고유의 값으로 바꿉니다.

```scala
val df = spark.read.json("abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/data/small_radio_json.json")
```

Shift+Enter 키를 선택하여 코드를 실행합니다.

데이터 프레임의 콘텐츠를 보려면 다음 코드를 실행합니다.

```scala
df.show()
```

다음 코드 조각과 유사한 결과가 표시됩니다.

```bash
+---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
|               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
+---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
| El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
| Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
| Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
...
...
```

이제 Azure Data Lake Storage Gen2에서 Azure Databricks로 데이터를 추출했습니다.

## <a name="transform-the-data"></a>데이터 변환

원시 샘플 데이터 **small_radio_json.json** 파일은 라디오 방송국의 대상을 캡처하며, 다양한 열을 갖고 있습니다. 이 섹션에서는 데이터 세트의 특정 열만 검색하도록 데이터를 변환합니다.

먼저 앞에서 만든 데이터 프레임에서 **이름**, **성**, **성별**, **위치** 및 **수준** 열만 검색합니다.

```scala
val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
```

다음 코드 조각과 같은 출력이 수신됩니다.

```bash
+---------+----------+------+--------------------+-----+
|firstname|  lastname|gender|            location|level|
+---------+----------+------+--------------------+-----+
| Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|     Tess|  Townsend|     F|Nashville-Davidso...| free|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
|   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Tess|  Townsend|     F|Nashville-Davidso...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
+---------+----------+------+--------------------+-----+
```

열 **수준**을 **subscription_type**으로 지정하도록 이 데이터를 추가로 변환할 수 있습니다.

```scala
val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
renamedColumnsDF.show()
```

다음 코드 조각과 같은 출력이 수신됩니다.

```bash
+---------+----------+------+--------------------+-----------------+
|firstname|  lastname|gender|            location|subscription_type|
+---------+----------+------+--------------------+-----------------+
| Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|     Tess|  Townsend|     F|Nashville-Davidso...|             free|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
|   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Tess|  Townsend|     F|Nashville-Davidso...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
+---------+----------+------+--------------------+-----------------+
```

## <a name="load-the-data"></a>데이터 로드

이 섹션에서는 변환된 데이터를 Azure SQL Data Warehouse로 업로드합니다. Azure Databricks용 Azure SQL Data Warehouse 커넥터를 사용하여 데이터 프레임을 SQL 데이터 웨어하우스의 테이블로 직접 업로드합니다.

SQL Data Warehouse 커넥터는 Azure Blob Storage를 임시 스토리지로 사용하여 Azure Databricks와 Azure SQL Data Warehouse 간의 데이터를 업로드합니다. 따라서 저장소 계정에 연결하는 구성을 먼저 제공해야 합니다. 이 문서의 필수 구성 요소로 이미 계정을 만들어 두셨을 것입니다.

Azure Databricks에서 Azure Storage 계정에 액세스하기 위한 구성을 입력합니다.

```scala
val storageURI = "<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net"
val fileSystemName = "<FILE_SYSTEM_NAME>"
val accessKey =  "<ACCESS_KEY>"
```

Azure Databricks와 Azure SQL Data Warehouse 간에 데이터를 이동할 때 사용할 임시 폴더를 지정합니다.

```scala
val tempDir = "abfs://" + fileSystemName + "@" + storageURI +"/tempDirs"
```

다음 코드 조각을 실행하여 Azure Blob Storage 액세스 키를 구성에 저장합니다. 이 작업을 수행하면 액세스 키를 노트북에서 일반 텍스트로 유지할 필요가 없습니다.

```scala
val acntInfo = "fs.azure.account.key."+ storageURI
sc.hadoopConfiguration.set(acntInfo, accessKey)
```

Azure SQL Data Warehouse 인스턴스에 연결하기 위한 값을 입력합니다. 필수 구성 요소의 일부로 SQL 데이터 웨어하우스를 이미 만들어 두셨을 것입니다.

```scala
//SQL Data Warehouse related settings
val dwDatabase = "<DATABASE NAME>"
val dwServer = "<DATABASE SERVER NAME>" 
val dwUser = "<USER NAME>"
val dwPass = "<PASSWORD>"
val dwJdbcPort =  "1433"
val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
```

다음 코드 조각을 실행하여 변환된 데이터 프레임 **renamedColumnsDF**를 SQL 데이터 웨어하우스에 테이블로 로드합니다. 이 코드 조각은 SQL 데이터베이스에 **SampleTable**이라는 테이블을 만듭니다.

```scala
spark.conf.set(
    "spark.sql.parquet.writeLegacyFormat",
    "true")
    
renamedColumnsDF.write
    .format("com.databricks.spark.sqldw")
    .option("url", sqlDwUrlSmall) 
    .option("dbtable", "SampleTable")
    .option( "forward_spark_azure_storage_credentials","True")
    .option("tempdir", tempDir)
    .mode("overwrite")
    .save()
```

SQL 데이터베이스에 연결하여 **SampleTable**이라는 데이터베이스가 있는지 확인합니다.

![샘플 테이블 확인](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table.png "샘플 테이블 확인")

select 쿼리를 실행하여 테이블의 콘텐츠를 확인합니다. 이 테이블에 **renamedColumnsDF** 데이터 프레임과 똑같은 데이터가 있어야 합니다.

![샘플 테이블 콘텐츠 확인](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table-content.png "샘플 테이블 콘텐츠 확인")

## <a name="clean-up-resources"></a>리소스 정리

자습서를 마친 후에는 클러스터를 종료해도 됩니다. Azure Databricks 작업 영역의 왼쪽에서 **클러스터**를 선택합니다. 클러스터를 종료하려면 **작업** 아래에서 줄임표(...)를 가리키고 **종료** 아이콘을 선택합니다.

![Databricks 클러스터 중지](./media/data-lake-storage-handle-data-using-databricks/terminate-databricks-cluster.png "Databricks 클러스터 중지")

클러스터를 만들 때 **비활성 \_\_분 후 종료** 확인란을 선택한 경우 클러스터를 수동으로 종료하지 않으면 클러스터가 자동으로 중지됩니다. 이 경우 지정한 시간 동안 클러스터가 비활성 상태이면 클러스터가 자동으로 중지됩니다.

## <a name="next-steps"></a>다음 단계

다음 자습서로 넘어가서 Azure Event Hubs를 사용하여 Azure Databricks로 실시간 데이터를 스트리밍하는 방법을 알아보세요.

> [!div class="nextstepaction"]
>[Event Hubs를 사용하여 Azure Databricks로 데이터 스트리밍](../../azure-databricks/databricks-stream-from-eventhubs.md)
