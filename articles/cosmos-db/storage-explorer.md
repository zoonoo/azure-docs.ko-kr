---
title: Azure Storage 탐색기를 사용 하 여 Azure Cosmos DB 리소스 관리
description: Azure Storage Explorer를 사용하여 Azure Cosmos DB에 연결하고 리소스를 관리하는 방법을 알아봅니다.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/23/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: be37ab43db9b5b696a619cb1539981c064b4cb0e
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537799"
---
# <a name="manage-azure-cosmos-db-resources-by-using-azure-storage-explorer"></a>Azure Storage 탐색기를 사용 하 여 Azure Cosmos DB 리소스 관리

Azure Storage 탐색기를 사용 하 여 Azure Cosmos DB에 연결할 수 있습니다. Windows, macOS 또는 Linux에서 Azure 및 소 버린 클라우드에서 호스트 되는 Azure Cosmos DB 계정에 연결할 수 있습니다.

동일한 도구를 사용 하 여 여러 Azure 엔터티를 한 곳에서 관리 합니다. 저장소 blob 및 큐와 같은 다른 Azure 엔터티와 함께 Azure Cosmos DB 엔터티를 관리 하 고, 데이터를 조작 하 고, 저장 프로시저 및 트리거를 업데이트할 수 있습니다. Azure Storage 탐색기는 SQL, MongoDB, Graph 및 Table Api에 대해 구성 된 Cosmos 계정을 지원 합니다.

> [!NOTE]
> Storage 탐색기와 Azure Cosmos DB 통합은 더 이상 사용 되지 않습니다. 이 릴리스에서는 1 년 이상 기존 기능이 제거 되지 않습니다. 대신 [Azure portal](https://portal.azure.com/), [azure portal 데스크톱 앱](https://portal.azure.com/App/Download) 또는 독립 실행형 [azure Cosmos 탐색기](data-explorer.md) 를 사용 해야 합니다. 대체 옵션에는 Storage 탐색기에서 현재 지원 되지 않는 여러 가지 새로운 기능이 포함 되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

MongoDB 용 Azure Cosmos DB API 또는 SQL API를 사용 하는 Cosmos 계정 계정이 없으면 Azure Portal에서 계정을 만들 수 있습니다. 자세한 내용은 [Azure Cosmos DB: .net을 사용 하 여 SQL API 웹 앱 빌드 및 Azure Portal](create-sql-api-dotnet.md) 를 참조 하세요.

## <a name="installation"></a>설치

최신 Azure Storage 탐색기 비트를 설치 하려면 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 참조 하세요. Windows, Linux 및 macOS 버전을 지원 합니다.

## <a name="connect-to-an-azure-subscription"></a>Azure 구독에 연결

1. **Azure Storage 탐색기** 를 설치한 후 왼쪽 창에서 **플러그** 인 아이콘을 선택 합니다.

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

1. **Azure 계정 추가** 를 선택한 다음, **로그인** 을 선택합니다.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

1. **Azure 로그인** 대화 상자에서 **로그인** 을 선택 하 고 Azure 자격 증명을 입력 합니다.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

1. 목록에서 구독을 선택한 다음, **적용** 을 선택합니다.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

    탐색기 창이 업데이트 되 고 선택한 구독의 계정이 표시 됩니다.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

    **Cosmos DB 계정은** Azure 구독에 연결 되어 있습니다.

## <a name="use-a-connection-string-to-connect-to-azure-cosmos-db"></a>연결 문자열을 사용 하 여 Azure Cosmos DB에 연결

연결 문자열을 사용 하 여 Azure Cosmos DB에 연결할 수 있습니다. 이 메서드는 SQL 및 Table Api만 지원 합니다. 연결 문자열을 사용 하 여 연결 하려면 다음 단계를 따르세요.

1. 왼쪽 트리에서 **로컬 및 연결** 을 찾아 **Cosmos DB 계정** 을 마우스 오른쪽 단추로 클릭 한 다음 **Cosmos DB에 연결** 을 선택 합니다.

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

2. Cosmos DB에 **연결** 창에서 다음을 수행 합니다.
   1. 드롭다운 메뉴에서 API를 선택 합니다.
   1. **연결 문자열 상자에** 연결 문자열을 붙여 넣습니다. 기본 연결 문자열을 검색 하는 방법은 [연결 문자열 가져오기](manage-with-powershell.md#list-keys)를 참조 하세요.
   1. **계정 레이블을** 입력 하 고 **다음** 을 선택 하 여 요약을 확인 합니다.
   1. **연결** 을 선택 하 여 Azure Cosmos DB 계정을 연결 합니다.

      :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

## <a name="use-a-local-emulator-to-connect-to-azure-cosmos-db"></a>로컬 에뮬레이터를 사용 하 여 Azure Cosmos DB에 연결

에뮬레이터를 사용 하 여 Azure Cosmos DB에 연결 하려면 다음 단계를 수행 합니다. 이 메서드는 SQL 계정만 지원 합니다.

1. Cosmos DB 에뮬레이터를 설치 하 고 엽니다. 에뮬레이터를 설치 하는 방법은 [Cosmos DB 에뮬레이터](./local-emulator.md)를 참조 하세요.

1. 왼쪽 트리에서 **로컬 및 연결** 을 찾아 **Cosmos DB 계정** 을 마우스 오른쪽 단추로 클릭 한 다음 **Cosmos DB 에뮬레이터에 연결** 을 선택 합니다.

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

1. Cosmos DB에 **연결** 창에서 다음을 수행 합니다.
   1. **연결 문자열 상자에** 연결 문자열을 붙여 넣습니다. 기본 연결 문자열을 검색 하는 방법에 대 한 자세한 내용은 [연결 문자열 가져오기](manage-with-powershell.md#list-keys)를 참조 하세요.
   1. **계정 레이블을** 입력 하 고 **다음** 을 선택 하 여 요약을 확인 합니다.
   1. **연결** 을 선택 하 여 Azure Cosmos DB 계정을 연결 합니다.

      :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB 리소스 관리

다음 작업을 사용 하 여 Azure Cosmos DB 계정을 관리할 수 있습니다.

* Azure Portal에서 계정을 엽니다.
* 빠른 액세스 목록에 리소스를 추가 합니다.
* 리소스를 검색 하 고 새로 고칩니다.
* 데이터베이스를 만들고 삭제 합니다.
* 컬렉션을 만들고 삭제 합니다.
* 문서를 만들고, 편집 하 고, 삭제 하 고, 필터링 합니다.
* 저장 프로시저, 트리거 및 사용자 정의 함수를 관리 합니다.

### <a name="quick-access-tasks"></a>빠른 액세스 작업

탐색기 창에서 구독을 마우스 오른쪽 단추로 클릭 하 여 여러 빠른 작업 작업을 수행할 수 있습니다. 예를 들면 다음과 같습니다.

* Azure Cosmos DB 계정 또는 데이터베이스를 마우스 오른쪽 단추로 클릭 한 다음 **포털에서 열기** 를 선택 하 여 Azure Portal 브라우저에서 리소스를 관리 합니다.

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

* Azure Cosmos DB 계정, 데이터베이스 또는 컬렉션을 마우스 오른쪽 단추로 클릭 한 다음 빠른 액세스 **에 추가** 를 선택 하 여 빠른 실행 메뉴에 추가 합니다.

* 선택한 경로 아래 **에서 검색** 을 선택 하 여 키워드 검색을 사용 하도록 설정 합니다.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

### <a name="database-and-collection-management"></a>데이터베이스 및 컬렉션 관리

#### <a name="create-a-database"></a>데이터베이스 만들기

1. Azure Cosmos DB 계정을 마우스 오른쪽 단추로 클릭 한 다음 **데이터베이스 만들기** 를 선택 합니다.

   :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

1. 데이터베이스 이름을 입력 한 다음 **enter** 키를 눌러 완료 합니다.

#### <a name="delete-a-database"></a>데이터베이스 삭제

1. 데이터베이스를 마우스 오른쪽 단추로 클릭 한 다음 **데이터베이스 삭제** 를 선택 합니다. 

   :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

1. 팝업 창에서 **예** 를 선택 합니다. 데이터베이스 노드가 삭제되고, Azure Cosmos DB 계정이 자동으로 새로 고쳐집니다.

   :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

#### <a name="create-a-collection"></a>컬렉션 만들기

1. 데이터베이스를 마우스 오른쪽 단추로 클릭 한 다음 **컬렉션 만들기** 를 선택 합니다.

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

1. 컬렉션 만들기 창에서 **컬렉션 ID** , **저장소 용량** 등과 같은 요청 된 정보를 입력 합니다. **확인** 을 선택하여 작업을 마칩니다.

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

1. 파티션 키를 지정할 수 있도록 **무제한** 을 선택 하 고 **확인** 을 선택 하 여 완료 합니다.

   > [!NOTE]
   > 컬렉션을 만들 때 파티션 키를 사용 하는 경우 생성이 완료 되 면 컬렉션의 파티션 키 값을 변경할 수 없습니다.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

#### <a name="delete-a-collection"></a>컬렉션 삭제

- 컬렉션을 마우스 오른쪽 단추로 클릭 하 고 **컬렉션 삭제** 를 선택한 다음 팝업 창에서 **예** 를 선택 합니다.

    컬렉션 노드가 삭제되고, 데이터베이스가 자동으로 새로 고쳐집니다.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

### <a name="document-management"></a>문서 관리

#### <a name="create-and-modify-documents"></a>문서 만들기 및 수정

- 왼쪽 창에서 **문서** 를 열고 **새 문서** 를 선택한 다음 오른쪽 창에서 콘텐츠를 편집 하 고 **저장** 을 선택 합니다.
- 기존 문서를 업데이트 한 후 **저장** 을 선택할 수도 있습니다. 변경 내용을 취소 하려면 **취소** 를 선택 합니다.

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

#### <a name="delete-a-document"></a>문서 삭제

* **삭제** 단추를 선택 하 여 선택한 문서를 삭제 합니다.

#### <a name="query-for-documents"></a>문서에 대한 쿼리

* 문서 필터를 편집 하려면 [SQL 쿼리](./sql-query-getting-started.md)를 입력 한 다음 **적용** 을 선택 합니다.

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

### <a name="graph-management"></a>그래프 관리

#### <a name="create-and-modify-a-vertex"></a>꼭 짓 점 만들기 및 수정

* 새 꼭 짓 점을 만들려면 왼쪽 창에서 **그래프** 를 열고 **새 꼭 짓 점** 을 선택 하 고 콘텐츠를 편집한 다음 **확인** 을 선택 합니다.
* 기존 꼭 짓 점을 수정 하려면 오른쪽 창에서 펜 아이콘을 선택 합니다.

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

#### <a name="delete-a-graph"></a>그래프 삭제

* 꼭 짓 점을 삭제 하려면 꼭 짓 점 이름 옆에 있는 휴지통 아이콘을 선택 합니다.

#### <a name="filter-for-graph"></a>그래프에 대한 필터

* 그래프 필터를 편집 하려면 [gremlin 쿼리](gremlin-support.md)를 입력 한 다음 **필터 적용** 을 선택 합니다.

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

### <a name="table-management"></a>테이블 관리

#### <a name="create-and-modify-a-table"></a>테이블 만들기 및 수정

* 새 테이블을 만들려면 다음을 수행 합니다.
   1. 왼쪽 창에서 **엔터티** 를 열고 **추가** 를 선택 합니다.
   1. **엔터티 추가** 대화 상자에서 콘텐츠를 편집 합니다.
   1. 속성 **추가** 단추를 선택 하 여 속성을 추가 합니다.
   1. **삽입** 을 선택합니다.

      :::image type="content" source="./media/storage-explorer/table.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

* 테이블을 수정 하려면 **편집** 을 선택 하 고 콘텐츠를 수정한 다음 **업데이트** 를 선택 합니다.

   

#### <a name="import-and-export-table"></a>테이블 가져오기 및 내보내기

* 가져오려면 **가져오기** 단추를 선택한 다음 기존 테이블을 선택 합니다.
* 내보내려면 **내보내기** 단추를 선택한 다음 대상을 선택 합니다.

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

#### <a name="delete-entities"></a>엔터티 삭제

* 엔터티를 선택 하 고 **삭제** 단추를 선택 합니다.

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

#### <a name="query-a-table"></a>테이블 쿼리

- **쿼리 단추를** 선택 하 고 쿼리 조건을 입력 한 다음 **쿼리 실행** 단추를 선택 합니다. 쿼리 창을 닫으려면 **쿼리 닫기** 단추를 선택 합니다.

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>저장 프로시저, 트리거 및 UDF 관리

* 저장 프로시저를 만들려면 다음을 수행 합니다.
  1. 왼쪽 트리에서 **저장 프로시저** 를 마우스 오른쪽 단추로 클릭 한 다음 **저장 프로시저 만들기** 를 선택 합니다.
  
     :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::
  
  1. 왼쪽에 이름을 입력 하 고 오른쪽 창에 저장 프로시저 스크립트를 입력 한 다음 **만들기** 를 선택 합니다.
  
* 기존 저장 프로시저를 편집 하려면 해당 프로시저를 두 번 클릭 하 고 업데이트 한 다음 **업데이트** 를 선택 하 여 저장 합니다. **취소** 를 선택 하 여 변경을 취소할 수도 있습니다.

* **트리거** 및 **UDF** 에 대 한 작업은 **저장 프로시저** 와 비슷합니다.

## <a name="troubleshooting"></a>문제 해결

다음은 Storage 탐색기에서 Azure Cosmos DB를 사용할 때 발생 하는 일반적인 문제에 대 한 해결 방법입니다.

### <a name="sign-in-issues"></a>로그인 문제

먼저 응용 프로그램을 다시 시작 하 여 문제가 해결 되는지 확인 합니다. 문제가 지속 되 면 문제 해결을 계속 합니다.

#### <a name="self-signed-certificate-in-certificate-chain"></a>인증서 체인의 자체 서명된 인증서

이 오류가 표시 될 수 있는 몇 가지 이유는 다음과 같습니다. 가장 일반적인 두 가지는 다음과 같습니다.

* *투명 프록시* 뒤에 있습니다. IT 부서와 같은 누군가가 HTTPS 트래픽을 가로채 고 암호를 해독 한 다음 자체 서명 된 인증서를 사용 하 여 암호화 합니다.

* 바이러스 백신 소프트웨어와 같은 소프트웨어를 실행 하 고 있습니다. 소프트웨어는 사용자가 받은 HTTPS 메시지에 자체 서명 된 TLS/SSL 인증서를 삽입 합니다.

Storage 탐색기 자체 서명 된 인증서를 찾으면 수신 하는 HTTPS 메시지가 변조 되었는지 여부를 알 수 없습니다. 자체 서명 된 인증서의 복사본이 있는 경우 해당 인증서를 신뢰 하도록 Storage 탐색기에 지시할 수 있습니다. 인증서를 삽입 한 사람을 잘 모를 경우 다음 단계를 수행 하 여 찾을 수 있습니다.

1. OpenSSL 설치:

     - [Windows](https://slproweb.com/products/Win32OpenSSL.html): 모든 광원 버전이 양호 합니다.
     - macOS 및 Linux: 운영 체제에 포함 되어야 합니다.

1. OpenSSL 실행:
    * Windows: 설치 디렉터리로 이동한 다음 **///** 를 클릭 하 고 **openssl.exe** 를 두 번 클릭 합니다.
    * Mac 및 Linux: 터미널에서 **openssl** 를 실행 합니다.
1. `s_client -showcerts -connect microsoft.com:443`을 실행합니다.
1. 자체 서명된 인증서를 찾습니다. 자체 서명 된 것이 확실 하지 않은 경우 제목 ("s:")과 발급자 ("i:")가 동일한 위치에서 찾을 수 있습니다.
1. 자체 서명 된 인증서가 있는 경우 **-----BEGIN certificate-----** 를 포함 하 여 모든 것을 복사 하 여 새에 **-----끝 인증서-----** 에 붙여넣습니다. 각 항목에 대 한 CER 파일.
1. Storage 탐색기를 연 후 **Edit**  >  **SSL 인증서** 편집  >  **인증서 가져오기** 로 이동 합니다. 파일 선택기를 사용 하 여를 찾고 선택 하 고 엽니다. 만든 CER 파일.

자체 서명 된 인증서를 찾을 수 없는 경우 자세한 도움말은 사용자 의견을 보낼 수 있습니다.

#### <a name="unable-to-retrieve-subscriptions"></a>구독을 검색할 수 없음

로그인 한 후 구독을 검색할 수 없는 경우 다음 제안을 시도 합니다.

* 계정에 구독에 대 한 액세스 권한이 있는지 확인 합니다. 이렇게 하려면 [Azure Portal](https://portal.azure.com/)에 로그인 합니다.
* 올바른 환경에 로그인 했는지 확인 합니다.
  * [Azure](https://portal.azure.com/)
  * [Azure 중국](https://portal.azure.cn/)
  * [Azure 독일](https://portal.microsoftazure.de/)
  * [Azure 미국 정부](https://portal.azure.us/)
  * 사용자 지정 환경/Azure Stack
* 프록시 뒤에 있는 경우 Storage 탐색기 프록시가 제대로 구성 되어 있는지 확인 합니다.
* 계정을 제거한 다음 다시 추가 합니다.
* 홈 디렉터리 (예: C:\Users\ContosoUser)에서 다음 파일을 삭제 한 다음 계정을 다시 추가 합니다.
  * .adalcache
  * .devaccounts
  * .extaccounts
* F12 키를 눌러 개발자 콘솔을 엽니다. 로그인 할 때 콘솔에서 오류 메시지를 시청 하세요.

   :::image type="content" source="./media/storage-explorer/console.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

#### <a name="unable-to-see-the-authentication-page"></a>인증 페이지를 볼 수 없음

인증 페이지를 볼 수 없습니다.

* 연결 속도에 따라 로그인 페이지가 로드 되는 데 시간이 걸릴 수 있습니다. 인증 대화 상자를 닫기 전에 1 분 이상 기다립니다.
* 프록시 뒤에 있는 경우 Storage 탐색기 프록시가 제대로 구성 되어 있는지 확인 합니다.
* F12 (개발자 도구 콘솔)에서 응답을 시청 하 여 인증이 작동 하지 않는 이유에 대 한 단서를 찾을 수 있는지 확인 합니다.

#### <a name="cant-remove-an-account"></a>계정을 제거할 수 없음

계정을 제거할 수 없거나 재인증 링크가 아무것도 수행 하지 않는 경우:

* 홈 디렉터리에서 다음 파일을 삭제 한 다음 계정을 다시 추가 합니다.
  * .adalcache
  * .devaccounts
  * .extaccounts

* SAS 연결 Storage 리소스를 제거하려면 다음을 삭제합니다.
  * Windows - %AppData%/StorageExplorer 폴더
  * /Users/<your_name>/Library/application support 지원/macOS 용 StorageExplorer
  * Linux - ~/.config/StorageExplorer
  
  > [!NOTE]
  > 이러한 파일을 삭제 하는 경우 **모든 자격 증명을 다시 입력 해야** 합니다.

### <a name="httphttps-proxy-issue"></a>HTTP/HTTPS 프록시 문제

ASE에서 HTTP/HTTPS 프록시를 구성 하는 경우 왼쪽 트리에 Azure Cosmos DB 노드를 나열할 수 없습니다. Azure Portal에서 Azure Cosmos DB 데이터 탐색기를 사용 하 여 해결할 수 있습니다.

### <a name="development-node-under-local-and-attached-node-issue"></a>“로컬 및 연결” 노드 중 “개발” 노드 문제

왼쪽 트리의 **로컬 및 연결** 된 노드 아래에서 **개발** 노드를 선택 하 고 나면 응답이 없습니다. 이는 정상적인 동작입니다.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

### <a name="attach-an-azure-cosmos-db-account-in-the-local-and-attached-node-error"></a>**로컬 및 연결 된** 노드 오류에 Azure Cosmos DB 계정 연결

**로컬 및 연결 된** 노드에서 Azure Cosmos DB 계정을 연결한 후 다음 오류가 표시 되 면 올바른 연결 문자열을 사용 하 고 있는지 확인 합니다.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

### <a name="expand-azure-cosmos-db-node-error"></a>Azure Cosmos DB 노드 확장 오류

왼쪽 트리에서 노드를 확장 하려고 하면 다음과 같은 오류가 표시 될 수 있습니다.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="왼쪽 창의 플러그 인 아이콘을 보여 주는 스크린샷":::

다음 제안 해 보세요.

* Azure Cosmos DB 계정이 프로 비전 진행 중인지 확인 합니다. 계정이 성공적으로 만들어지면 다시 시도 하세요.
* 계정이 **빠른 액세스** 또는 **로컬 및 연결** 된 노드에 있는 경우 계정이 삭제 되었는지 확인 합니다. 이 경우 노드를 수동으로 제거 해야 합니다.

## <a name="next-steps"></a>다음 단계

* Azure Storage 탐색기에서 Azure Cosmos DB를 사용 하는 방법을 보려면이 비디오를 시청 하세요. [Azure Storage 탐색기에서 Azure Cosmos DB를 사용](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)합니다.
* [Storage Explorer 시작](../vs-azure-tools-storage-manage-with-storage-explorer.md)에서 Storage Explorer에 대해 자세히 알아보고 더 많은 서비스에 연결합니다.