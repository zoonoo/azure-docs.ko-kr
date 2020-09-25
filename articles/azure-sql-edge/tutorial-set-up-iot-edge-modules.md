---
title: Azure SQL Edge에서 IoT Edge 모듈 설정
description: 철광석 불순물 예측을 위해 세 부분으로 구성된 이번 Azure SQL Edge 자습서 중 2부에서는 IoT Edge 모듈 및 연결을 설정합니다.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sourabha, sstein
ms.date: 09/22/2020
ms.openlocfilehash: 7b2432fda70e8f9a5fa8bc64ede846d977672e9e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90886484"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>IoT Edge 모듈 및 연결 설정

Azure SQL Edge에서 철광석 불순물 예측을 위해 세 부분으로 구성된 이번 자습서 중 2부에서는 다음과 같은 IoT Edge 모듈을 설정합니다.

- Azure SQL Edge
- 데이터 생성기 IoT Edge 모듈

## <a name="specify-container-registry-credentials"></a>컨테이너 레지스트리 자격 증명 지정

모듈 이미지를 호스팅하는 컨테이너 레지스트리에 대한 자격 증명을 지정해야 합니다. 이러한 자격 증명은 리소스 그룹에서 만든 컨테이너 레지스트리에서 찾을 수 있습니다. **액세스 키** 섹션으로 이동합니다. 다음 필드를 확인합니다.

- 레지스트리 이름
- 로그인 서버
- 사용자 이름
- 암호

이제 IoT Edge 모듈에서 컨테이너 자격 증명을 지정합니다.

1. 리소스 그룹에서 만든 IoT 허브로 이동합니다.

2. **자동 디바이스 관리**에 있는 **IoT Edge** 섹션에서 **디바이스 ID**를 클릭합니다. 이 자습서에서는 ID가 `IronOrePredictionDevice`입니다.

3. **모듈 설정** 섹션을 선택합니다.

4. **컨테이너 레지스트리 자격 증명**에서 다음 값을 입력합니다.

   _필드_|_값_
   -------|-------
   Name|레지스트리 이름
   주소|로그인 서버
   사용자 이름|사용자 이름
   암호|암호
  
## <a name="deploy-the-data-generator-module"></a>데이터 생성기 모듈 배포

1. **자동 디바이스 관리**에 있는 **IoT Edge** 섹션에서 **디바이스 ID**를 클릭합니다. 이 자습서의 경우 ID는 `IronOrePredictionDevice`입니다. 그런 다음, **모듈 설정**을 클릭합니다.

2.  **디바이스에서 모듈 설정**의 **IoT Edge 모듈** 섹션 아래에서 **+추가**를 클릭하고 **IoT Edge 모듈**을 선택합니다.

3. IoT Edge 모듈의 올바른 이름과 이미지 URI를 제공합니다.
   이미지 URI는 이 자습서의 1부에서 만든 리소스 그룹의 컨테이너 레지스트리에서 찾을 수 있습니다. **서비스**에서 **리포지토리** 섹션을 선택합니다. 이 자습서에서는 `silicaprediction`이라는 리포지토리를 선택합니다. 적절한 태그를 선택합니다. 이미지 URI는 다음과 같은 형식입니다.

   *컨테이너 레지스트리의 로그인 서버*/*리포지토리 이름*:*태그 이름*

   다음은 그 예입니다.

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

4. *다시 시작 정책* 및 *원하는 상태* 필드를 그대로 둡니다.

5. **추가**를 클릭합니다.


## <a name="deploy-the-azure-sql-edge-module"></a>Azure SQL Edge 모듈 배포

1. **+ 추가**를 클릭한 다음, **Marketplace 모듈**을 클릭하여 Azure SQL Edge 모듈을 배포합니다. 

2. **IoT Edge Module Marketplace** 블레이드에서 *Azure SQL Edge*를 검색하고 *Azure SQL Edge Developer*를 선택합니다. 

3. **IoT Edge 모듈**에서 새로 추가된 *Azure SQL Edge* 모듈을 클릭하여 Azure SQL Edge 모듈을 구성합니다. 구성 옵션에 대한 자세한 내용은 [Azure SQL Edge 배포](https://docs.microsoft.com/azure/azure-sql-edge/deploy-portal)를 참조하세요.

4. *Azure SQL Edge* 모듈 배포에 `MSSQL_PACKAGE` 환경 변수를 추가하고, 이 자습서의 [1부](tutorial-deploy-azure-resources.md)의 8단계에서 만든 데이터베이스 dacpac 파일의 SAS URL을 지정합니다.

5. **Update**(업데이트)를 클릭합니다.

6. **디바이스에서 모듈 설정** 페이지에서 **다음: 경로 >** 를 클릭합니다.

7. **디바이스에서 모듈 설정** 페이지의 경로 창에서 아래 설명된 대로 모듈의 경로를 IoT Edge 허브 통신으로 지정합니다. 아래의 경로 정의에 있는 모듈 이름을 업데이트해야 합니다.

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/IronOreMeasures INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/IronOreMeasures")
   ```

   다음은 그 예입니다. 

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/IronOreMeasures")
   ```


7. **디바이스에서 모듈 설정** 페이지에서 **다음: 검토 + 만들기 >** 를 클릭합니다.

8. **디바이스에서 모듈 설정** 페이지에서 **만들기**를 클릭합니다.

## <a name="create-and-start-the-t-sql-streaming-job-in-azure-sql-edge"></a>Azure SQL Edge에서 T-SQL 스트리밍 작업을 만들고 시작합니다.

1. Azure Data Studio를 엽니다.

2. **시작** 탭에서 다음 정보를 사용하여 새 연결을 시작합니다.

   |_필드_|_값_|
   |-------|-------|
   |연결 형식| Microsoft SQL Server|
   |서버|이 데모에 대해 생성된 VM에서 언급된 공용 IP 주소|
   |사용자 이름|sa|
   |암호|Azure SQL Edge 인스턴스를 만드는 동안 사용된 강력한 암호입니다.|
   |데이터베이스|기본값|
   |서버 그룹|기본값|
   |이름(선택 사항)|필요에 따라 이름을 입력합니다.|

3. **연결**

4. **파일** 메뉴 탭에서 새 Notebook을 열거나 키보드 바로 가기 키 Ctrl + N을 사용합니다.

5. 새 쿼리 창에서 아래 스크립트를 실행하여 T-SQL 스트리밍 작업을 만듭니다. 스크립트를 실행하기 전에 다음 변수를 변경해야 합니다. 
   - *SQL_SA_Password:* Azure SQL Edge 모듈을 배포하는 동안 지정된 MSSQL_SA_PASSWORD 값입니다. 
   
   ```sql
   Use IronOreSilicaPrediction
   Go

   Declare @SQL_SA_Password varchar(200) = '<SQL_SA_Password>'
   declare @query varchar(max) 

   /*
   Create Objects Required for Streaming
   */

   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'MyStr0ng3stP@ssw0rd';

   If NOT Exists (select name from sys.external_file_formats where name = 'JSONFormat')
   Begin
      CREATE EXTERNAL FILE FORMAT [JSONFormat]  
      WITH ( FORMAT_TYPE = JSON)
   End 


   If NOT Exists (select name from sys.external_data_sources where name = 'EdgeHub')
   Begin
      Create EXTERNAL DATA SOURCE [EdgeHub] 
      With(
         LOCATION = N'edgehub://'
      )
   End 

   If NOT Exists (select name from sys.external_streams where name = 'IronOreInput')
   Begin
      CREATE EXTERNAL STREAM IronOreInput WITH 
      (
         DATA_SOURCE = EdgeHub,
         FILE_FORMAT = JSONFormat,
         LOCATION = N'IronOreMeasures'
       )
   End


   If NOT Exists (select name from sys.database_scoped_credentials where name = 'SQLCredential')
   Begin
       set @query = 'CREATE DATABASE SCOPED CREDENTIAL SQLCredential
                 WITH IDENTITY = ''sa'', SECRET = ''' + @SQL_SA_Password + ''''
       Execute(@query)
   End 

   If NOT Exists (select name from sys.external_data_sources where name = 'LocalSQLOutput')
   Begin
      CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
      LOCATION = 'sqlserver://tcp:.,1433',CREDENTIAL = SQLCredential)
   End

   If NOT Exists (select name from sys.external_streams where name = 'IronOreOutput')
   Begin
      CREATE EXTERNAL STREAM IronOreOutput WITH 
      (
         DATA_SOURCE = LocalSQLOutput,
         LOCATION = N'IronOreSilicaPrediction.dbo.IronOreMeasurements'
      )
   End

   EXEC sys.sp_create_streaming_job @name=N'IronOreData',
   @statement= N'Select * INTO IronOreOutput from IronOreInput'

   exec sys.sp_start_streaming_job @name=N'IronOreData'
   ```

6. 다음 쿼리를 사용하여 데이터 생성 모듈의 데이터가 데이터베이스로 스트리밍되는지 확인합니다. 

   ```sql
   Select Top 10 * from dbo.IronOreMeasurements
   order by timestamp desc
   ```


이 자습서에서는 데이터 생성기 모듈과 SQL Edge 모듈을 배포했습니다. 그런 다음, 데이터 생성 모듈에 의해 생성된 데이터를 SQL로 스트리밍하는 스트리밍 작업을 만들었습니다. 

## <a name="next-steps"></a>다음 단계

- [ONNX를 사용하여 Azure SQL Edge에 ML 모델 배포](tutorial-run-ml-model-on-sql-edge.md)
