---
title: Azure Storage Explorer를 사용하여 Azure Cosmos DB 리소스 관리
description: Azure Storage Explorer를 사용하여 Azure Cosmos DB에 연결하고 리소스를 관리하는 방법을 알아봅니다.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/23/2020
ms.author: dech
ms.custom: seodec18
ms.openlocfilehash: 447ebaac46235d551236b0b354acbacb67df0d07
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122568040"
---
# <a name="manage-azure-cosmos-db-resources-by-using-azure-storage-explorer"></a>Azure Storage Explorer를 사용하여 Azure Cosmos DB 리소스 관리
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Storage Explorer를 사용하여 Azure Cosmos DB에 연결할 수 있습니다. Windows, macOS 또는 Linux에서 Azure 및 소버린 클라우드에서 호스팅되는 Azure Cosmos DB 계정에 연결할 수 있습니다.

동일한 도구를 사용하여 다양한 Azure 엔터티를 한 곳에서 관리할 수 있습니다. Azure Cosmos DB 엔터티를 관리하고, 데이터를 조작하고, 스토리지 Blob 및 큐와 같은 다른 Azure 엔터티와 함께 저장 프로시저 및 트리거를 업데이트 할 수 있습니다. Azure Storage Explorer는 SQL, MongoDB, Graph 및 Table API용으로 구성된 Cosmos 계정을 지원합니다.

> [!NOTE]
> Storage Explorer와의 Azure Cosmos DB 통합은 더 이상 사용되지 않습니다. 이 릴리스에서는 최소 1년 동안 기존 기능이 제거되지 않습니다. 대신 [Azure Portal](https://portal.azure.com/), [Azure Portal 데스크톱 앱](https://portal.azure.com/App/Download) 또는 독립 실행형 [Azure Cosmos DB Explorer](data-explorer.md)를 사용해야 합니다. 이 같은 대체 옵션에는 Storage Explorer에서 현재 지원되지 않는 여러 가지 새로운 기능이 포함되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

SQL API 또는 MongoDB 용 Azure Cosmos DB API를 사용하는 Cosmos 계정입니다. 계정이 없는 경우 Azure Portal에서 계정을 만들 수 있습니다. 자세한 내용은 [Azure Cosmos DB: .NET 및 Azure Portal을 사용하여 SQL API 웹앱 빌드](create-sql-api-dotnet.md)를 참조하세요.

## <a name="installation"></a>설치

[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)에서 최신 Azure Storage Explorer 비트를 설치합니다. Windows, Linux 및 macOS 버전이 지원됩니다.

## <a name="connect-to-an-azure-subscription"></a>Azure 구독에 연결

1. **Azure Storage Explorer** 를 설치한 후 왼쪽 창에서 **플러그인** 아이콘을 선택합니다.

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="왼쪽 창의 플러그인 아이콘을 보여주는 스크린샷입니다.":::

1. **Azure 계정 추가** 를 선택한 다음, **로그인** 을 선택합니다.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Azure 계정 추가 라디오 단추가 선택된 Azure Storage에 연결 창의 스크린샷과 Azure 환경 드롭 다운 메뉴입니다.":::

1. **Azure 로그인** 대화 상자에서 **로그인** 을 선택한 후에 Azure 자격 증명을 입력합니다.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Azure 구독에 대한 자격 증명을 입력해야 하는 위치를 보여주는 로그인 창의 스크린샷입니다.":::

1. 목록에서 구독을 선택한 다음, **적용** 을 선택합니다.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="구독 목록과 적용 단추가 표시된 계정 관리 창의 스크린샷입니다.":::

    탐색기 창이 업데이트되고, 선택한 구독에서 계정이 표시됩니다.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="선택한 구독에서 계정이 표시되도록 업데이트된 탐색기 창의 스크린샷입니다.":::

    사용자의 **Cosmos DB 계정** 은 Azure 구독에 연결되어 있습니다.

## <a name="use-a-connection-string-to-connect-to-azure-cosmos-db"></a>연결 문자열을 사용하여 Azure Cosmos DB에 연결

연결 문자열을 사용하여 Azure Cosmos DB에 연결할 수 있습니다. 이 메서드는 SQL 및 Table API만 지원합니다. 연결 문자열을 사용하여 연결하려면 다음 단계를 따릅니다:

1. 왼쪽 트리에서 **로컬 및 연결** 을 찾아 **Cosmos DB 계정** 을 마우스 오른쪽 단추로 클릭한 후 **Cosmos DB에 연결** 을 선택합니다.

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="마우스 오른쪽 버튼을 클릭한 후 Azure Cosmos D B에 연결이 강조 표시된 드롭다운 메뉴를 보여주는 스크린샷입니다.":::

2. **Cosmos DB에 연결** 창:
   1. 드롭다운 메뉴에서 API를 선택합니다.
   1. **연결 문자열** 상자에서 사용자의 연결 문자열을 붙여 넣습니다. 기본 연결 문자열을 검색하는 방법은 [연결 문자열 가져오기](manage-with-powershell.md#list-keys)를 참조하세요.
   1. **계정 레이블** 을 입력하고 **다음** 을 선택하여 요약을 확인합니다.
   1. **연결** 을 선택하여 Azure Cosmos DB 계정에 연결합니다.

      :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="API 드롭다운 메뉴, 연결 문자열 상자 및 계정 레이블 상자를 표시하는 Cosmos D B에 연결 창의 스크린샷입니다.":::

> [!NOTE]
> Azure Storage Explorer에 Azure Cosmos DB 연결 문자열이 잘못된 형식이라고 표시되면 연결 문자열 끝에 세미콜론(`;`)이 있는지 확인합니다. 올바른 Azure Cosmos DB 연결 문자열의 예는 다음과 같습니다: `AccountEndpoint=https://accountname.documents.azure.com:443;AccountKey=accountkey==;`

## <a name="use-a-local-emulator-to-connect-to-azure-cosmos-db"></a>로컬 에뮬레이터를 사용하여 Azure Cosmos DB에 연결

에뮬레이터를 사용하여 Azure Cosmos DB에 연결하려면 다음 단계를 수행합니다. 이 메서드는 SQL 계정만 지원합니다.

1. Cosmos DB 에뮬레이터를 설치하고 엽니다. 에뮬레이터를 설치하는 방법은 [Cosmos DB 에뮬레이터](./local-emulator.md)를 참조하세요.

1. 왼쪽 트리에서 **로컬 및 연결** 을 찾아 **Cosmos DB 계정** 을 마우스 오른쪽 단추로 클릭한 후 **Cosmos DB 에뮬레이터에 연결** 을 선택합니다.

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="마우스 오른쪽 단추로 클릭한 후 표시되는 메뉴를 보여주는 스크린샷으로, Azure Cosmos D B 에뮬레이터에 연결이 강조 표시되어 있습니다.":::

1. **Cosmos DB에 연결** 창:
   1. **연결 문자열** 상자에서 사용자의 연결 문자열을 붙여 넣습니다. 기본 연결 문자열 검색에 대한 자세한 내용은 [연결 문자열 가져오기](manage-with-powershell.md#list-keys)를 참조하세요.
   1. **계정 레이블** 을 입력하고 **다음** 을 선택하여 요약을 확인합니다.
   1. **연결** 을 선택하여 Azure Cosmos DB 계정에 연결합니다.

      :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="연결 문자열 상자 및 계정 레이블 상자를 표시하는 Cosmos D B에 연결 창의 스크린샷입니다.":::

## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB 리소스 관리

다음 작업을 사용하여 Azure Cosmos DB 계정을 관리할 수 있습니다:

* Azure Portal에서 계정 열기.
* 빠른 액세스 목록에 리소스 추가.
* 리소스 검색 및 새로 고침.
* 데이터베이스 만들기 및 삭제.
* 컬렉션 만들기 및 삭제.
* 문서 만들기, 편집, 삭제 및 필터링.
* 저장 프로시저, 트리거 및 사용자 정의 함수 관리.

### <a name="quick-access-tasks"></a>빠른 액세스 작업

탐색기 창에서 구독을 마우스 오른쪽 단추로 클릭하여 다양한 빠른 작업을 수행할 수 있습니다. 예를 들면 다음과 같습니다:

* Azure Cosmos DB 계정 또는 데이터베이스를 마우스 오른쪽 단추로 클릭하여 **Portal에서 열기** 를 선택하면 Azure Portal을 통해 브라우저에서 리소스를 관리할 수 있습니다.

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="마우스 오른쪽 단추를 클릭한 후 표시되는 메뉴를 보여주는 스크린샷으로, Portal에서 열기가 강조 표시되어 있습니다.":::

* Azure Cosmos DB 계정, 데이터베이스 또는 컬렉션을 마우스 오른쪽 단추로 클릭한 다음 **빠른 실행에 추가** 를 선택하여 빠른 실행 메뉴에 추가합니다.

* **여기에서 검색** 을 선택하면 선택한 경로에서 키워드 검색이 가능합니다.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="강조 표시된 검색 상자를 보여주는 스크린샷입니다.":::

### <a name="database-and-collection-management"></a>데이터베이스 및 컬렉션 관리

#### <a name="create-a-database"></a>데이터베이스 만들기

1. Azure Cosmos DB 계정을 마우스 오른쪽 단추로 클릭한 다음, **데이터베이스 만들기** 를 선택합니다.

   :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="마우스 오른쪽 단추를 클릭한 후 표시되는 메뉴를 보여주는 스크린샷으로, 데이터베이스 만들기가 강조 표시되어 있습니다.":::

1. 데이터베이스 이름을 입력한 다음 **Enter** 키를 눌러 완료합니다.

#### <a name="delete-a-database"></a>데이터베이스 삭제

1. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **데이터베이스 삭제** 를 선택합니다. 

   :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="마우스 오른쪽 단추를 클릭한 후 표시되는 메뉴를 보여주는 스크린샷으로, 데이터베이스 삭제가 강조 표시되어 있습니다.":::

1. 팝업 창에서 **예** 를 선택합니다. 데이터베이스 노드가 삭제되고, Azure Cosmos DB 계정이 자동으로 새로 고쳐집니다.

   :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="예 단추가 강조 표시된 확인 창의 스크린샷입니다.":::

#### <a name="create-a-collection"></a>컬렉션 만들기

1. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **컬렉션 만들기** 를 선택합니다.

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="마우스 오른쪽 단추를 클릭한 후 표시되는 메뉴를 보여주는 스크린샷으로, 컬렉션 만들기가 강조 표시되어 있습니다.":::

1. 컬렉션 만들기 창에서 **컬렉션 ID** , **스토리지 용량** 등과 같은 요청된 정보를 입력합니다. **확인** 을 선택하여 작업을 마칩니다.

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="컬렉션 ID 상자와 스토리지 용량 단추를 표시하는 컬렉션 만들기 창의 스크린샷입니다.":::

1. 파티션 키를 지정할 수 있도록 **무제한** 을 선택하고 **확인** 을 선택하여 완료합니다.

   > [!NOTE]
   > 컬렉션을 만들 때 파티션 키가 사용된 경우, 컬렉션을 만들고 나면 컬렉션에서 파티션 키 값을 변경할 수 없습니다.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="컬렉션 만들기 창의 스크린샷으로 스토리지 용량에 무제한이 선택되어 있고 파티션 키 상자가 강조 표시되어 있습니다.":::

#### <a name="delete-a-collection"></a>컬렉션 삭제

- 컬렉션을 마우스 오른쪽 단추로 클릭하고 **컬렉션 삭제** 를 클릭한 후 팝업 창에서 **예** 를 클릭합니다.

    컬렉션 노드가 삭제되고, 데이터베이스가 자동으로 새로 고쳐집니다.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="마우스 오른쪽 단추를 클릭한 후 표시되는 메뉴를 보여주는 스크린샷으로, 컬렉션 삭제가 강조 표시되어 있습니다.":::

### <a name="document-management"></a>문서 관리

#### <a name="create-and-modify-documents"></a>문서 만들기 및 수정

- 왼쪽 창에서 **문서** 를 열고 **새 문서** 를 선택한 다음 오른쪽 창에서 콘텐츠를 편집하고 **저장** 을 선택합니다.
- 기존 문서를 업데이트한 후 **저장** 을 선택할 수도 있습니다. 변경 내용을 취소하려면 **취소** 를 선택합니다.

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="왼쪽 창에 강조 표시된 문서를 보여주는 스크린샷입니다. 오른쪽 창에는 새 문서, 저장 및 취소가 강조 표시되어 있습니다.":::

#### <a name="delete-a-document"></a>문서 삭제

* 선택한 문서를 삭제하려면 **삭제** 단추를 클릭합니다.

#### <a name="query-for-documents"></a>문서에 대한 쿼리

* 문서 필터를 편집하려면 [SQL 쿼리](./sql-query-getting-started.md)를 입력한 다음 **적용** 을 선택합니다.

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="필터 및 적용 단추, ID 번호 및 강조 표시된 쿼리 상자를 보여주는 오른쪽 창의 스크린샷입니다.":::

### <a name="graph-management"></a>그래프 관리

#### <a name="create-and-modify-a-vertex"></a>꼭짓점 만들기 및 수정

* 새 꼭짓점을 만들려면 왼쪽 창에서 **그래프** 를 열고 **새 꼭짓점** 을 선택한 다음 콘텐츠를 편집하고 **확인** 을 선택합니다.
* 기존 꼭짓점을 수정하려면 오른쪽 창에서 펜 아이콘을 클릭합니다.

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="왼쪽 창에서 선택한 그래프와 오른쪽 창에서 강조 표시된 새 꼭짓점 및 펜 아이콘을 보여주는 스크린샷입니다.":::

#### <a name="delete-a-graph"></a>그래프 삭제

* 꼭짓점을 삭제하려면 꼭짓점 이름 옆의 휴지통 아이콘을 클릭합니다.

#### <a name="filter-for-graph"></a>그래프에 대한 필터

* 그래프 필터를 편집하려면 [Gremlin 쿼리](gremlin-support.md)를 입력한 다음 **필터 적용** 을 선택합니다.

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="왼쪽 창에서 선택한 그래프와 오른쪽 창에서 필터 적용 및 쿼리 상자가 강조 표시된 스크린샷입니다.":::

### <a name="table-management"></a>테이블 관리

#### <a name="create-and-modify-a-table"></a>테이블 만들기 및 수정

* 새 테이블을 만들려면:
   1. 왼쪽 창에서 **엔터티** 를 열고 **추가** 를 선택합니다.
   1. **엔터티 추가** 대화 상자에서 콘텐츠를 편집합니다.
   1. **속성 추가** 단추를 선택하여 속성을 추가합니다.
   1. **삽입** 을 선택합니다.

      :::image type="content" source="./media/storage-explorer/table.png" alt-text="왼쪽 창에 강조 표시된 엔티티를 표시하고 오른쪽 창에서 강조 표시된 추가, 편집, 속성 추가 및 삽입을 보여주는 스크린샷입니다.":::

* 테이블을 수정하려면 **편집** 을 선택하고 콘텐츠를 수정한 다음 **업데이트** 를 선택합니다.

   

#### <a name="import-and-export-table"></a>테이블 가져오기 및 내보내기

* 가져오려면 **가져오기** 단추를 선택하고 기존 테이블을 선택합니다.
* 내보내려면 **내보내기** 단추를 선택하고 대상을 선택합니다.

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="오른쪽 창에 강조 표시된 가져오기 및 내보내기 단추가 표시된 스크린샷입니다.":::

#### <a name="delete-entities"></a>엔터티 삭제

* 엔터티를 선택하고 **삭제** 단추를 선택합니다.

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="오른쪽 창에 강조 표시된 삭제 버튼과 예가 강조 표시된 확인 팝업 창이 표시된 스크린샷입니다.":::

#### <a name="query-a-table"></a>테이블 쿼리

- **쿼리** 단추를 선택하고 쿼리 조건을 입력한 다음 **쿼리 실행** 단추를 선택합니다. 쿼리 창을 닫으려면 **쿼리 닫기** 단추를 선택합니다.

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="쿼리 실행 단추와 쿼리 닫기 단추가 강조 표시된 오른쪽 창의 스크린샷입니다.":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>저장 프로시저, 트리거 및 UDF 관리

* 저장 프로시저를 만들려면:
  1. 왼쪽 트리에서 **저장 프로시저** 를 마우스 오른쪽 단추로 클릭한 다음 **저장 프로시저 만들기** 를 선택합니다.
  
     :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="마우스 오른쪽 단추를 클릭한 후 표시되는 메뉴를 보여주는 왼쪽 창의 스크린샷으로 저장 프로시저 만들기가 강조 표시되어 있습니다.":::
  
  1. 왼쪽에 이름을 입력하고 오른쪽 창에 저장 프로시저 스크립트를 입력한 다음 **만들기** 를 선택합니다.
  
* 기존 저장 프로시저를 편집하려면 해당 프로시저를 두 번 클릭하고 업데이트한 다음 **업데이트** 를 선택하여 저장합니다. **취소** 를 선택하여 변경을 취소할 수도 있습니다.

* **트리거** 및 **UDF** 에 대한 작업은 **저장 프로시저** 작업과 유사합니다.

## <a name="troubleshooting"></a>문제 해결

다음은 Storage Explorer에서 Azure Cosmos DB를 사용할 때 발생하는 일반적인 문제의 해결 방법입니다.

### <a name="sign-in-issues"></a>로그인 문제

먼저 애플리케이션을 다시 시작하여 문제가 해결되는지 확인합니다. 만약 문제가 지속되면 문제 해결을 계속합니다.

#### <a name="self-signed-certificate-in-certificate-chain"></a>인증서 체인의 자체 서명된 인증서

이 오류가 표시되는 원인에는 몇 가지가 있는데, 가장 일반적인 두 가지는 다음과 같습니다:

* *투명 프록시* 뒤에 있습니다. IT 부서와 같은 누군가가 HTTPS 트래픽을 가로채서 해독한 다음 자체 서명된 인증서를 사용하여 암호화합니다.

* 바이러스 백신 소프트웨어와 같은 소프트웨어를 실행하고 있습니다. 소프트웨어는 사용자가 수신한 HTTPS 메시지에 자체 서명된 TLS/SSL 인증서를 삽입합니다.

Storage Explorer가 자체 서명된 인증서를 찾으면 수신한 HTTPS 메시지가 변조되었는지 여부를 알 수 없습니다. 자체 서명된 인증서의 복사본을 갖고 있는 경우 Storage Explorer에 해당 인증서를 신뢰하도록 지시할 수 있습니다. 인증서를 삽입한 사람을 모를 경우 다음 단계를 수행하여 찾을 수 있습니다:

1. OpenSSL 설치:

     - [Windows](https://slproweb.com/products/Win32OpenSSL.html): 라이트 버전이면 괜찮습니다.
     - macOS 및 Linux: 운영 체제에 포함되어야 합니다.

1. OpenSSL 실행:
    * Windows: 설치 디렉토리로 이동한 다음 **/bin/** 으로 이동하여 **openssl.exe** 를 두 번 클릭합니다.
    * Mac 및 Linux: 터미널에서 **openssl** 을 실행합니다.
1. `s_client -showcerts -connect microsoft.com:443`을 실행합니다.
1. 자체 서명된 인증서를 찾습니다. 어떤 인증서가 자체 서명된 것인지 확실하지 않은 경우 제목(“s:”)과 발급자(“i:”)가 동일한 위치를 찾습니다.
1. 자체 서명된 인증서를 찾았으면 각 인증서에 대해 **-----BEGIN CERTIFICATE-----** 부터 **-----END CERTIFICATE-----** 까지 모든 항목을 복사하여 새 .CER 파일에 붙여 넣습니다.
1. Storage Explorer를 연 다음, **편집** > **SSL 인증서** > **인증서 가져오기** 로 이동합니다. 그런 다음 파일 선택기를 사용하여 생성한 .CER 파일을 찾아 선택하고 엽니다.

자체 서명된 인증서를 찾을 수 없는 경우 피드백을 보내 추가 도움을 받을 수 있습니다.

#### <a name="unable-to-retrieve-subscriptions"></a>구독을 검색할 수 없음

로그인 후 구독을 검색할 수 없는 경우 다음의 제안을 시도해봅니다:

* 계정에 구독에 대한 액세스 권한이 있는지 확인합니다. 이렇게 하려면 먼저 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
* 올바른 환경에 로그인했는지 확인합니다:
  * [Azure](https://portal.azure.com/)
  * [Azure 중국](https://portal.azure.cn/)
  * [Azure 독일](https://portal.microsoftazure.de/)
  * [Azure 미국 정부](https://portal.azure.us/)
  * 사용자 지정 환경/Azure Stack
* 프록시 뒤에 있는 경우 Storage Explorer 프록시가 올바르게 구성되었는지 확인합니다.
* 계정을 제거한 다음, 다시 추가합니다.
* 홈 디렉터리(예: C:\Users\ContosoUser)에서 다음 파일을 삭제한 다음, 계정을 다시 추가합니다:
  * .adalcache
  * .devaccounts
  * .extaccounts
* F12 키를 눌러 개발자 콘솔을 엽니다. 로그인 할 때 콘솔에서 오류 메시지를 확인합니다.

   :::image type="content" source="./media/storage-explorer/console.png" alt-text="콘솔이 강조 표시된 개발자 도구 콘솔의 스크린샷입니다.":::

#### <a name="unable-to-see-the-authentication-page"></a>인증 페이지를 볼 수 없음

인증 페이지를 볼 수 없습니다.

* 연결 속도에 따라 로그인 페이지가 로드되는 데 시간이 걸릴 수 있습니다. 인증 대화 상자를 닫기 전에 1분 이상 기다립니다.
* 프록시 뒤에 있는 경우 Storage Explorer 프록시가 올바르게 구성되었는지 확인합니다.
* 개발자 도구 콘솔(F12)에서 응답을 보고 인증이 작동하지 않는 이유에 대한 단서를 찾을 수 있는지 확인합니다.

#### <a name="cant-remove-an-account"></a>계정을 제거할 수 없음

계정을 제거할 수 없거나 재 인증 링크가 작동하지 않는 경우:

* 홈 디렉터리에서 다음 파일을 삭제한 다음, 계정을 다시 추가합니다:
  * .adalcache
  * .devaccounts
  * .extaccounts

* SAS 연결 Storage 리소스를 제거하려면 다음을 삭제합니다.
  * Windows - %AppData%/StorageExplorer 폴더
  * macOS - /Users/<your_name>/Library/Application SUpport/StorageExplorer
  * Linux - ~/.config/StorageExplorer
  
  > [!NOTE]
  > 해당 파일을 삭제하는 경우 **모든 자격 증명을 다시 입력해야 합니다**.

### <a name="httphttps-proxy-issue"></a>HTTP/HTTPS 프록시 문제

ASE에서 HTTP/HTTPS 프록시를 구성할 때 왼쪽 트리에 Azure Cosmos DB 노드를 나열할 수 없습니다. Azure Portal에서 Azure Cosmos DB 데이터 탐색기를 사용하여 해결할 수 있습니다.

### <a name="development-node-under-local-and-attached-node-issue"></a>“로컬 및 연결” 노드 중 “개발” 노드 문제

왼쪽 트리의 **로컬 및 연결** 노드에서 **개발** 노드를 선택하면 응답이 없습니다. 이는 정상적인 동작입니다.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="선택한 개발 노드를 보여주는 스크린샷입니다.":::

### <a name="attach-an-azure-cosmos-db-account-in-the-local-and-attached-node-error"></a>**로컬 및 연결** 노드의 Azure Cosmos DB 계정 연결 오류

**로컬 및 연결** 노드에서 Azure Cosmos DB 계정을 연결한 후 다음 오류가 표시되면 올바른 연결 문자열을 사용하고 있는지 확인합니다.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="Getaddrinfo ENOTFOUND를 나타내는 자식 리소스를 검색할 수 없음 오류 팝업 창의 스크린샷입니다.":::

### <a name="expand-azure-cosmos-db-node-error"></a>Azure Cosmos DB 노드 확장 오류

왼쪽 트리에서 노드를 확장하려고 하면 다음과 같은 오류가 표시될 수 있습니다.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="이 Cosmos D B 계정에 연결할 수 없음을 나타내는 자식 리소스를 검색할 수 없음 오류 팝업 창의 스크린 샷입니다.":::

다음을 시도해봅니다:

* Azure Cosmos DB 계정이 프로비전 진행 중인지 확인합니다. 계정이 성공적으로 만들어지면 다시 시도합니다.
* 계정이 **빠른 액세스** 또는 **로컬 및 연결** 노드에 있는 경우 계정이 삭제되었는지 확인합니다. 이 경우 노드를 수동으로 제거해야 합니다.

## <a name="next-steps"></a>다음 단계

* Azure Storage Explorer에서 Azure Cosmos DB를 사용하는 방법에 대해 설명하는 비디오를 시청합니다: [Azure Storage Explorer에서 Azure Cosmos DB 사용](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* [Storage Explorer 시작](../vs-azure-tools-storage-manage-with-storage-explorer.md)에서 Storage Explorer에 대해 자세히 알아보고 더 많은 서비스에 연결합니다.
