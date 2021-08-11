---
title: 포함 파일
description: 포함 파일
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 12/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 9ae69c2a119fc29d3ba7dcd78e4238f7712ca170
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414101"
---
:::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/overview.png" alt-text="애플리케이션 개요":::

이 다이어그램은 이 자습서에서 빌드하는 솔루션의 워크플로를 보여줍니다. 

1. Azure 이벤트 허브로 전송된 데이터는 Azure BLOB 스토리지에 캡처됩니다.
2. 데이터 캡처가 완료되면 이벤트가 생성되어 Azure Event Grid로 전송됩니다. 
3. Event Grid는 이 이벤트 데이터를 Azure 함수 앱에 전달합니다.
4. 함수 앱은 이벤트 데이터의 BLOB URL을 사용하여 스토리지에서 BLOB을 검색합니다. 
5. 함수 앱은 Blob 데이터를 Azure Synapse Analytics로 마이그레이션합니다. 

이 문서에서는 다음 단계를 수행합니다.

> [!div class="checklist"]
> - 자습서에 필요한 인프라 배포
> - Functions 앱에 코드 게시
> - Event Grid 구독 만들기 
> - Event Hubs로 샘플 데이터 스트리밍
> - Azure Synapse Analytics에 캡처된 데이터 확인

## <a name="prerequisites"></a>필수 구성 요소
이 자습서를 완료하려면 다음 항목이 필요합니다.

- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- .NET 데스크톱 개발, Azure 개발, ASP.NET 및 웹 개발, Node.js 개발, Python 개발용 작업이 포함된 [Visual Studio 2019](https://www.visualstudio.com/vs/)
- 컴퓨터에 [EventHubsCaptureEventGridDemo 샘플 프로젝트](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)를 다운로드합니다.
    - WindTurbineDataGenerator – 풍력 발전용 터빈 데이터 샘플을 캡처 지원 이벤트 허브로 보내는 간단한 게시자입니다.
    - FunctionDWDumper – Avro 파일이 Azure Storage Blob에 캡처될 때 Event Grid 알림을 받는 Azure Function입니다. Blob의 URI 경로를 받고, 해당 콘텐츠를 읽고, 이 데이터를 Azure Synapse Analytics(전용 SQL 풀)로 푸시합니다.

## <a name="deploy-the-infrastructure"></a>인프라 배포
이 단계에서는 [Resource Manager 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json)을 사용하여 필요한 인프라를 배포합니다. 템플릿을 배포하면 다음 리소스가 생성됩니다.

* 캡처 기능을 사용하도록 설정된 이벤트 허브
* 캡처된 파일에 대한 스토리지 계정 
* 함수 앱 호스팅용 앱 서비스 플랜
* 이벤트 처리용 함수 앱
* 데이터 웨어하우스 호스팅용 SQL Server
* 마이그레이션된 데이터를 저장하기 위한 Azure Synapse Analytics(전용 SQL 풀)

### <a name="use-azure-cli-to-deploy-the-infrastructure"></a>Azure CLI를 사용하여 인프라 배포

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 맨 위에서 **Cloud Shell** 단추를 선택합니다.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/azure-portal.png" alt-text="Azure Portal":::
3. 브라우저 맨 아래에 Cloud Shell이 열려 있는 것이 보입니다.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/launch-cloud-shell.png" alt-text="Cloud Shell":::
4. Cloud Shell에서 **Bash** 와 **PowerShell** 중에 선택하는 옵션이 표시되면 **Bash** 를 선택합니다. 
5. Cloud Shell을 처음으로 사용하는 경우 **스토리지 만들기** 를 선택하여 스토리지 계정을 만듭니다. Azure Cloud Shell은 Azure 스토리지 계정에서 일부 파일을 저장해야 합니다. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/create-storage-cloud-shell.png" alt-text="Cloud Shell용 스토리지 만들기":::
6. Cloud Shell이 초기화될 때까지 기다립니다. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/cloud-shell-initialized.png" alt-text="초기화된 Cloud Shell":::
1. 다음 CLI 명령을 실행하여 Azure 리소스 그룹을 만듭니다. 
    1. 다음 명령을 복사하여 Cloud Shell 창에 붙여넣습니다. 원하는 경우 리소스 그룹 이름 및 위치를 변경합니다.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. **Enter** 키를 누릅니다. 

        다음은 예제입니다.
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n rgDataMigration
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/rgDataMigration",
          "location": "eastus",
          "managedBy": null,
          "name": "rgDataMigration",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. 다음 CLI 명령을 실행하여 이전 섹션에서 언급한 모든 리소스(이벤트 허브, 스토리지 계정, 함수 앱, Azure Synapse Analytics)를 배포합니다. 
    1. 명령을 복사하여 Cloud Shell 창에 붙여넣습니다. 또는 원하는 편집기에 복사/붙여넣고, 값을 설정하고, Cloud Shell에 명령을 복사합니다. 

        > [!IMPORTANT]
        > 명령을 실행하기 전에 다음 엔터티에 대한 값을 지정하세요. 
        > - 앞에서 만든 리소스 그룹 이름
        > - 이벤트 허브 네임스페이스 이름 
        > - 이벤트 허브 이름. 현재 값(hubdatamigration)을 그대로 유지해도 됩니다.
        > - SQL 서버 이름
        > - SQL 사용자 이름 및 암호 
        > - 데이터베이스 이름
        > - 스토리지 계정 이름 
        > - 함수 앱 이름 


        ```azurecli
        az deployment group create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    3.  Cloud Shell 창에서 **ENTER** 를 눌러 명령을 실행합니다. 여러 리소스를 만들기 때문에 이 프로세스에 다소 시간이 걸릴 수 있습니다. 명령 결과에 오류가 없는지 확인합니다. 
1. 포털에서 **Cloud Shell** 단추를 선택하거나 Cloud Shell 창의 오른쪽 위 모서리에서 **X** 단추를 선택하여 Cloud Shell을 닫습니다. 

### <a name="verify-that-the-resources-are-created"></a>리소스가 만들어지는지 확인합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다. 
2. 검색 상자에 리소스 그룹의 이름을 입력하여 리소스 그룹 목록을 필터링합니다. 
3. 목록에서 해당 리소스 그룹을 선택합니다.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-resource-group.png" alt-text="리소스 그룹 선택":::
4. 리소스 그룹에 다음 리소스가 표시되는지 확인합니다.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png" alt-text="리소스 그룹의 리소스" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png":::

### <a name="create-a-table-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 테이블 만들기
[CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) 스크립트를 실행하여 데이터 웨어하우스에 테이블을 만듭니다. 스크립트를 실행하려면 Visual Studio 또는 포털의 쿼리 편집기를 사용합니다. 다음 단계는 쿼리 편집기 사용 방법을 보여줍니다. 

1. 리소스 그룹의 리소스 목록에서 **전용 SQL 풀** 을 선택합니다. 
2. **전용 SQL 풀** 페이지의 왼쪽 메뉴에 있는 **일반 작업** 섹션에서 **쿼리 편집기(미리 보기)** 를 선택합니다. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/sql-data-warehouse-page.png" alt-text="Azure Synapse Analytics 페이지":::
2. SQL 서버의 **사용자** 이름 및 **암호** 를 입력하고, **확인** 을 선택합니다. 클라이언트에서 SQL 서버에 액세스하도록 허용하는 방법에 대한 메시지가 표시되면 다음 단계를 수행합니다.
    1. **서버 방화벽 설정** 링크를 선택합니다. 
    2. **방화벽 설정** 페이지의 도구 모음에서 **클라이언트 IP 추가** 를 선택한 다음, 도구 모음에서 **저장** 을 선택합니다. 
    3. 성공 메시지에서 **확인** 을 선택합니다.
    4. **전용 SQL 풀** 페이지로 다시 이동하여 왼쪽 메뉴에서 **쿼리 편집기(미리 보기)** 를 선택합니다. 
    5. **사용자** 및 **암호** 를 입력한 다음, **확인** 을 선택합니다. 
1. 쿼리 창에서 다음 SQL 스크립트를 복사하여 실행합니다. 

    ```sql
    CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
        [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
        [MeasureTime] datetime NULL, 
        [GeneratedPower] float NULL, 
        [WindSpeed] float NULL, 
        [TurbineSpeed] float NULL
    )
    WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    ```

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/run-sql-query.png" alt-text="SQL 쿼리 실행":::
5. 자습서가 끝나면 데이터가 생성되었는지는 확인할 수 있도록 이 탭 또는 창을 계속 열어 둡니다. 

### <a name="update-the-function-runtime-version"></a>함수 런타임 버전 업데이트

1. 웹 브라우저에서 다른 탭을 열고, [Azure Portal](https://portal.azure.com)로 이동합니다.
1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
1. 함수 앱이 있는 리소스 그룹을 선택합니다. 
1. 리소스 그룹의 리소스 목록에서 **함수 앱** 을 선택합니다.
1. 왼쪽 메뉴의 **설정** 에서 **구성** 을 선택합니다. 
1. 오른쪽 창에서 **함수 런타임 설정** 탭으로 전환합니다. 
1. **런타임 버전** 을 **~3** 으로 업데이트합니다. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-runtime-version.png" alt-text="함수 런타임 버전 업데이트":::
6. 도구 모음에서 **저장** 을 선택합니다. 
1. **변경 내용 저장** 확인 팝업에서 **계속** 을 선택합니다. 

## <a name="publish-the-azure-functions-app"></a>Azure Functions 앱 게시

1. Visual Studio를 시작합니다.
2. [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)에서 필수 구성 요소의 일부로 다운로드한 **EventHubsCaptureEventGridDemo.sln** 솔루션을 엽니다. 이는 `/samples/e2e/EventHubsCaptureEventGridDemo` 폴더에서 찾을 수 있습니다. 
3. 솔루션 탐색기에서 마우스 오른쪽 단추로 **FunctionEGDWDumper** 프로젝트를 클릭하고, **게시** 를 선택합니다.
4. 다음 화면이 보이면 **시작** 을 선택합니다. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/start-publish-button.png" alt-text="게시 섹션의 시작 단추":::
5. **게시** 대화 상자에서 **대상** 에 대해 **Azure** 를 선택하고 **다음** 을 선택합니다. 
6. **Azure 함수 앱(Windows)** 를 선택하고 **다음** 을 선택합니다.
7. **함수 인스턴스** 탭에서 Azure 구독을 선택하고 리소스 그룹을 확장한 다음 함수 앱을 선택하고 **마침** 을 선택합니다. 아직 수행하지 않은 경우 Azure 계정에 로그인을 합니다. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-select-function-app.png" alt-text="함수 앱 선택":::
8. **게시** 페이지의 **서비스 종속성** 섹션에서 **스토리지** 에 대해 **구성** 을 선택합니다. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-storage-configure-link.png" alt-text="스토리지 서비스 종속성에 대한 링크 구성 선택":::
1. **종속성 구성** 페이지에서 다음 단계를 수행합니다. 
    1. 이전에 만든 **스토리지 계정** 을 선택하고, **다음** 을 선택합니다. 

        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-dependency-storage.png" alt-text="스토리지 계정 선택":::
    10. **연결 문자열 이름** 을 지정하고, **연결 문자열 저장** 옵션에 대해 **없음** 을 선택하고, **다음** 을 선택합니다. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-connection-string.png" alt-text="연결 문자열 이름 지정":::      
    1. **C# 코드 파일** 및 **비밀 저장소** 옵션의 선택을 취소한 다음, **마침** 을 선택합니다.  
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-changes-summary.png" alt-text="변경 내용 요약 검토":::
1. Visual Studio에서 프로필이 구성되었으면 **게시** 를 선택합니다.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-publish.png" alt-text="게시 선택":::
2. **Azure 함수** 페이지가 열려 있는 탭의 왼쪽 메뉴에서 **함수** 를 선택합니다. **EventGridTriggerMigrateData** 함수가 목록에 표시되는지 확인합니다. 표시되지 않으면 Visual Studio에서 다시 게시한 다음, 포털에서 해당 페이지를 새로 고칩니다. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-function-creation.png" alt-text="함수 만들기 확인":::    

함수를 게시한 후에는 이벤트를 구독할 수 있습니다.

## <a name="subscribe-to-the-event"></a>이벤트 구독

1. 웹 브라우저의 새 탭 또는 새 창에서 [Azure Portal](https://portal.azure.com)로 이동합니다.
2. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다. 
3. 검색 상자에 리소스 그룹의 이름을 입력하여 리소스 그룹 목록을 필터링합니다. 
4. 목록에서 해당 리소스 그룹을 선택합니다.
1. 리소스 목록에서 **Event Hubs 네임스페이스** 를 선택합니다.
1. **Event Hubs 네임스페이스** 페이지의 왼쪽 메뉴에서 **이벤트** 를 선택한 다음, 도구 모음에서 **+ 이벤트 구독** 을 선택합니다. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-add-subscription-link.png" alt-text="Event Hubs 네임스페이스에 대한 이벤트 페이지의 이벤트 구독 추가 링크":::
1. **이벤트 구독 만들기** 페이지에서 다음 단계를 수행합니다.
    1. **이벤트 구독** 이름을 입력합니다. 
    1. 시스템 항목의 **이름** 을 입력합니다. 시스템 항목은 보낸 사람이 이벤트를 보낼 수 있는 엔드포인트를 제공합니다. 자세한 내용은 [시스템 항목](../system-topics.md)을 참조하세요.
    1. **엔드포인트 유형** 에 대해 **Azure 함수** 를 선택합니다.
    1. **엔드포인트** 에 대해 링크를 선택합니다.
    1. **Azure 함수 선택** 페이지에서 자동으로 채워지지 않으면 다음 단계를 수행합니다.
        1. Azure 함수가 있는 Azure 구독을 선택합니다. 
        1. 함수에 대한 리소스 그룹을 선택합니다. 
        1. 함수 앱을 선택합니다.
        1. 배포 슬롯을 선택합니다. 
        1. **EventGridTriggerMigrateData** 함수를 선택합니다. 
    1. **Azure 함수 선택** 페이지에서 **선택 확인** 을 선택합니다.
    1. 그런 다음, **이벤트 구독 만들기** 페이지로 돌아가서 **만들기** 를 선택합니다. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png" alt-text="함수를 사용하여 이벤트 구독 만들기" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png":::
1. 이벤트 구독이 만들어졌는지 확인합니다. Event Hubs 네임스페이스에 대한 **이벤트** 페이지에서 **이벤트 구독** 탭으로 전환합니다. 
    
    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png" alt-text="이벤트 구독 확인" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png":::
1. 리소스 그룹의 리소스 목록에서(App Service가 아니라) App Service 계획을 선택합니다. 

## <a name="run-the-app-to-generate-data"></a>앱을 실행하여 데이터 생성
이벤트 허브, 전용 SQL 풀(이전의 SQL Data Warehouse), Azure 함수 앱 및 이벤트 구독의 설정을 완료했습니다. 이벤트 허브에 대한 데이터를 생성하는 애플리케이션을 실행하기 전에 몇 가지 값을 구성해야 합니다.

1. Azure Portal에서 이전에 만든 리소스 그룹으로 이동합니다. 
2. Event Hubs 네임스페이스를 선택합니다.
3. **Event Hubs 네임스페이스** 페이지의 왼쪽 메뉴에서 **공유 액세스 정책** 을 선택합니다.
4. 정책 목록에서 **RootManageSharedAccessKey** 를 선택합니다. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-namespace-shared-access-policies.png" alt-text="Event Hubs 네임스페이스에 대한 공유 액세스 정책 페이지":::    
1. **연결 문자열 기본 키** 텍스트 상자 옆에 있는 복사 단추를 선택합니다. 
1. Visual Studio 솔루션으로 돌아갑니다. 
1. 마우스 오른쪽 단추로 **WindTurbineDataGenerator** 프로젝트를 클릭하고, **시작 프로젝트로 설정** 을 선택합니다. 
1. WindTurbineDataGenerator 프로젝트에서 **program.cs** 를 엽니다.
1. `<EVENT HUBS NAMESPACE CONNECTION STRING>`을 포털에서 복사한 연결 문자열로 바꿉니다. 
1. `<EVENT HUB NAME>`을 이벤트 허브 이름으로 바꿉니다. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```
6. 솔루션을 빌드합니다. **WindTurbineGenerator.exe** 애플리케이션을 실행합니다. 
7. 몇 분 후 쿼리 창이 열려 있는 다른 브라우저 탭에서 데이터 웨어하우스의 테이블에서 마이그레이션된 데이터를 쿼리합니다.

    ```sql
    select * from [dbo].[Fact_WindTurbineMetrics]    
    ```

    ![쿼리 결과](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

## <a name="monitor-the-solution"></a>솔루션 모니터링
이 섹션은 솔루션을 모니터링하거나 문제를 해결하는 데 도움이 됩니다. 

### <a name="view-captured-data-in-the-storage-account"></a>스토리지 계정에서 캡처된 데이터 보기
1. 리소스 그룹으로 이동하여 이벤트 데이터를 캡처하는 데 사용되는 스토리지 계정을 선택합니다. 
1. **스토리지 계정** 페이지의 왼쪽 메뉴에서 **Storage Explorer(미리 보기)** 를 선택합니다.
1. **Blob 컨테이너** 를 펼치고, **windturbinecapture** 를 선택합니다. 
1. 오른쪽 창에서 **Event Hubs 네임스페이스** 와 동일한 이름의 폴더를 엽니다. 
1. 이벤트 허브(**hubdatamigration**)와 동일한 이름의 폴더를 엽니다. 
1. 폴더를 드릴스루합니다. 그러면 AVRO 파일이 표시됩니다. 예를 들면 다음과 같습니다.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png" alt-text="스토리지에서 캡처된 파일" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png":::
    

### <a name="verify-that-the-event-grid-trigger-invoked-the-function"></a>Event Grid 트리거에서 함수를 호출했는지 확인
1. 리소스 그룹으로 이동하여 함수 앱을 선택합니다. 
1. 왼쪽 메뉴에서 **함수** 를 선택합니다.
1. 목록에서 **EventGridTriggerMigrateData** 함수를 선택합니다. 
1. **함수** 페이지의 왼쪽 메뉴에서 **모니터** 를 선택합니다. 
1. **구성** 을 선택하여 호출 로그를 캡처하도록 Application Insights를 구성합니다. 
1. 새 **Application Insights** 리소스를 만들거나 기존 리소스를 사용합니다. 
1. 함수에 대한 **모니터** 페이지로 다시 이동합니다. 
1. 이벤트를 보내는 클라이언트 애플리케이션(**WindTurbineDataGenerator**)이 아직 실행되고 있는지 확인합니다. 그렇지 않으면 앱을 실행합니다. 
1. 몇 분(5분 이상) 정도 기다린 다음, **새로 고침** 단추를 선택하여 함수 호출을 확인합니다.    

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-invocations.png" alt-text="함수 호출":::
1. 호출을 선택하여 세부 정보를 확인합니다.

    Event Grid는 구독자에게 이벤트 데이터를 배포합니다. 다음 예제는 이벤트 허브를 통한 데이터 스트리밍을 BLOB에 캡처할 때 생성되는 이벤트 데이터를 보여줍니다. 특히 `data` 개체의 `fileUrl` 속성은 스토리지의 BLOB을 가리킵니다. 함수 앱은 이 URL을 사용하여 캡처된 데이터가 포함된 BLOB 파일을 검색합니다.

    ```json
    {
        "topic": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourcegroups/rgDataMigration/providers/Microsoft.EventHub/namespaces/spehubns1207",
        "subject": "hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "id": "4538f1a5-02d8-4b40-9f20-36301ac976ba",
        "data": {
            "fileUrl": "https://spehubstorage1207.blob.core.windows.net/windturbinecapture/spehubns1207/hubdatamigration/0/2020/12/07/21/49/12.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "0",
            "sizeInBytes": 473444,
            "eventCount": 2800,
            "firstSequenceNumber": 55500,
            "lastSequenceNumber": 58299,
            "firstEnqueueTime": "2020-12-07T21:49:12.556Z",
            "lastEnqueueTime": "2020-12-07T21:50:11.534Z"
        },
        "dataVersion": "1",
        "metadataVersion": "1",
        "eventTime": "2020-12-07T21:50:12.7065524Z"
    }
    ```

### <a name="verify-that-the-data-is-stored-in-the-dedicated-sql-pool"></a>데이터가 전용 SQL 풀에 저장되어 있는지 확인
쿼리 창이 열려 있는 브라우저 탭에 있는 전용 SQL 풀의 테이블에서 마이그레이션된 데이터를 쿼리합니다.

![쿼리 결과](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

