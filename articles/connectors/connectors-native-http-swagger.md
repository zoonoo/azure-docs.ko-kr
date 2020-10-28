---
title: Azure Logic Apps에서 REST 엔드포인트 연결
description: Azure Logic Apps를 사용하여 작업, 프로세스 및 워크플로 자동화
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: af98811e158b9613e41389e08e19cb36797aa272
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790596"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용하여 REST 엔드포인트 호출

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 기본 제공 HTTP + Swagger 커넥터로 Logic Apps를 빌드하여 [Swagger](https://swagger.io) 파일을 통해 정기적으로 모든 REST 엔드포인트를 호출하는 워크플로를 자동화할 수 있습니다. HTTP + Swagger 트리거 및 동작은 [HTTP 트리거 및 동작](connectors-native-http.md)과 동일하게 작동하지만 Swagger 파일로 설명하는 API 구조 및 출력을 노출하여 ogic App Designer에 더 나은 환경을 제공합니다. 폴링 트리거를 구현하려면 [Logic Apps에서 다른 API, 서비스 및 시스템을 호출하기 위한 사용자 지정 API 만들기](../logic-apps/logic-apps-create-api-app.md#polling-triggers)에서 설명하는 폴링 패턴을 따릅니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 대상 REST 엔드포인트를 설명하는 Swagger(OpenAPI 아님) 파일의 URL

  일반적으로 커넥터가 작동하려면 REST 엔드포인트가 다음 조건을 충족해야 합니다.

  * Swagger 파일은 공개적으로 액세스할 수 있는 HTTPS URL에 호스팅해야 합니다.
  
  * Swagger 파일은 `operationID` 정의의 각 작업에 대 한를 포함 해야 합니다. 그렇지 않으면 커넥터가 Swagger 파일의 마지막 작업만 표시 합니다. 

  * Swagger 파일은 [CORS(원본 간 리소스 공유)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)를 사용해야 합니다.

  호스트되지 않았거나 보안 및 원본 간 요구 사항을 충족하지 않는 Swagger 파일을 참조하려면 [Azure Storage 계정의 Blob 컨테이너에 Swagger 파일을 업로드하고](#host-swagger) 이 스토리지 계정에서 CORS를 사용하도록 설정하여 해당 파일을 참조할 수 있습니다.

  이 토픽의 예에서는 [Cognitive Services 계정 및 액세스 키](../cognitive-services/cognitive-services-apis-create-account.md)를 필요로 하는 [Cognitive Services Face API](../cognitive-services/face/overview.md)를 사용합니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

* 대상 엔드포인트를 호출할 논리 앱입니다. HTTP + Swagger 트리거를 시작하려면 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). HTTP + Swagger 동작을 사용하려면 원하는 트리거를 사용하여 논리 앱을 시작합니다. 이 예제에서는 첫 번째 단계로 HTTP + Swagger 트리거를 사용합니다.

## <a name="add-an-http--swagger-trigger"></a>HTTP + Swagger 트리거 추가

이 기본 제공 트리거는 REST API를 설명하고 해당 파일의 콘텐츠를 포함하는 응답을 반환하는 Swagger 파일의 URL에 HTTP 요청을 보냅니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 디자이너의 검색 상자에 필터로 "swagger"를 입력합니다. **트리거** 목록에서 **HTTP + Swagger** 트리거를 선택합니다.

   ![HTTP + Swagger 트리거 선택](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. **SWAGGER 엔드포인트 URL** 상자에 Swagger 파일의 URL을 입력하고 **다음** 을 선택합니다.

   이 예에서는 [Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)에 대해 미국 서부 지역에 있는 Swagger URL을 사용합니다.

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   !["H T P + Swagger" 트리거와 "Swagger 엔드포인트 U R L" 속성이 U R L 값으로 설정 된 논리 앱 디자이너를 보여 주는 스크린샷](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Swagger 파일에서 설명하는 작업이 디자이너에 표시되면 사용할 작업을 선택합니다.

   !["H T P + Swagger" 트리거와 함께 논리 앱 디자이너와 Swagger 작업을 표시 하는 목록을 보여 주는 스크린샷](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. 선택한 작업에 따라 달라지는 트리거 매개 변수에 대해 엔드포인트 호출에 포함하려는 값을 제공합니다. 대상 엔드포인트를 트리거하는 빈도에 대해 되풀이를 설정합니다.

   이 예에서는 트리거 이름을 "HTTP + Swagger trigger: Face - Detect"로 변경하여 더 설명적인 이름을 단계에 지정합니다.

   !["얼굴 감지" 작업을 표시 하는 "H T P + Swagger" 트리거를 사용 하 여 논리 앱 디자이너를 보여 주는 스크린샷](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. 다른 사용 가능한 매개 변수를 추가하려면 **새 매개 변수 추가** 목록을 열고 원하는 매개 변수를 선택합니다.

   HTTP + Swagger에 사용 가능한 인증 유형에 대한 자세한 내용은 [아웃바운드 호출에 인증 추가](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)를 참조하세요.

1. 트리거가 발생할 때 실행되는 작업을 사용하여 논리 앱의 워크플로를 계속해서 작성합니다.

1. 마치면 논리 앱을 저장해야 합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

## <a name="add-an-http--swagger-action"></a>HTTP + Swagger 동작 추가

이 기본 제공 동작은 REST API를 설명하고 해당 파일의 콘텐츠를 포함하는 응답을 반환하는 Swagger 파일의 URL에 대해 HTTP 요청을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. Logic Apps 디자이너에서 논리 앱을 엽니다.

1. HTTP + Swagger 동작을 추가하려는 단계에서 **새 단계** 를 선택합니다.

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 표시되는 더하기 기호( **+** )를 선택한 다음, **작업 추가** 를 선택합니다.

1. 디자이너의 검색 상자에 필터로 "swagger"를 입력합니다. **동작** 목록에서 **HTTP + Swagger** 작업을 선택합니다.

    ![HTTP + Swagger 동작 선택](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. **SWAGGER 엔드포인트 URL** 상자에 Swagger 파일의 URL을 입력하고 **다음** 을 선택합니다.

   이 예에서는 [Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)에 대해 미국 서부 지역에 있는 Swagger URL을 사용합니다.

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Swagger 엔드포인트에 대한 URL 입력](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Swagger 파일에서 설명하는 작업이 디자이너에 표시되면 사용할 작업을 선택합니다.

   ![Swagger 파일의 작업](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. 선택한 작업에 따라 달라지는 동작 매개 변수에 대해 엔드포인트 호출에 포함하려는 값을 제공합니다.

   이 예제에는 매개 변수가 없지만 동작의 이름을 "HTTP + Swagger 동작: Face - Identify"로 변경하여 더 설명적인 이름을 단계에 지정합니다.

   ![작업 세부 정보](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. 다른 사용 가능한 매개 변수를 추가하려면 **새 매개 변수 추가** 목록을 열고 원하는 매개 변수를 선택합니다.

   HTTP + Swagger에 사용 가능한 인증 유형에 대한 자세한 내용은 [아웃바운드 호출에 인증 추가](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)를 참조하세요.

1. 마치면 논리 앱을 저장해야 합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Azure Storage에서 Swagger 호스트

Azure Storage 계정의 Blob 컨테이너에 Swagger 파일을 업로드하고 이 스토리지 계정에서 CORS를 사용하도록 설정하여 호스트되지 않았거나 보안 및 원본 간 요구 사항을 충족하지 않는 Swagger 파일을 참조할 수 있습니다. Azure Storage에서 Swagger 파일을 만들고 설정하고 저장하려면 다음 단계를 수행합니다.

1. [Azure Storage 계정 만들기](../storage/common/storage-account-create.md)

1. 이제 Blob에 CORS를 사용하도록 설정합니다. 스토리지 계정 메뉴에서 **CORS** 를 선택합니다. **Blob service** 탭에서 이러한 값을 지정한 다음, **저장** 을 선택합니다.

   | 속성 | 값 |
   |----------|-------|
   | **허용된 원본** | `*` |
   | **허용된 메서드** | `GET`, `HEAD`, `PUT` |
   | **허용된 헤더** | `*` |
   | **노출된 헤더** | `*` |
   | **최대 기간** (초) | `200` |
   |||

   이 예제에는 [Azure Portal](https://portal.azure.com)을 사용하지만 [Azure Storage Explorer](https://storageexplorer.com/) 등의 도구를 사용하거나, 이 샘플 [PowerShell 스크립트](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)를 사용하여 이 설정을 자동으로 구성할 수 있습니다.

1. [Blob 컨테이너를 만듭니다](../storage/blobs/storage-quickstart-blobs-portal.md). 컨테이너의 **개요** 창에서 **액세스 수준 변경** 을 선택합니다. **공용 액세스 수준** 목록에서 **Blob(Blob에 대해서만 익명 읽기 액세스)** 을 선택하고 **확인** 을 선택합니다.

1. [[Azure Portal](https://portal.azure.com) 또는 [Azure Storage Explorer](https://storageexplorer.com/)를 통해 Swagger 파일을 ](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)Blob 컨테이너에 업로드합니다.

1. Blob 컨테이너에서 파일을 참조하려면 Azure Storage Explorer에서 대/소문자를 구분하는 다음 형식의 HTTPS URL을 가져옵니다.

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<complete-swagger-file-name>?<query-parameters>`

## <a name="connector-reference"></a>커넥터 참조

HTTP + Swagger 트리거 또는 동작의 출력에 대한 자세한 내용은 다음과 같습니다. HTTP + Swagger 호출은 다음 정보를 반환합니다.

| 속성 이름 | Type | Description |
|---------------|------|-------------|
| headers | object | 요청의 헤더 |
| 본문 | object | JSON 개체 | 요청의 본문 콘텐츠가 포함된 개체 |
| 상태 코드 | int | 요청의 상태 코드 |
|||

| 상태 코드 | Description |
|-------------|-------------|
| 200 | 확인 |
| 202 | 수락됨 |
| 400 | 잘못된 요청 |
| 401 | 권한 없음 |
| 403 | 사용할 수 없음 |
| 404 | 찾을 수 없음 |
| 500 | 내부 서버 오류. 알 수 없는 오류 발생. |
|||

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
