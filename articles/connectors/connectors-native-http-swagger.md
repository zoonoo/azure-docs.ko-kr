---
title: Azure 논리 앱에서 REST 끝점에 연결
description: Azure Logic Apps를 사용하여 자동화된 작업, 프로세스 및 워크플로에서 REST 엔드포인트를 모니터링합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: b34fdc36bd0b1ce294a92b2ae8fa5da01568e5a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787372"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Azure 논리 앱을 사용하여 REST 끝점 호출

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 기본 제공 HTTP + Swagger 커넥터를 사용하면 논리 앱을 빌드하여 [Swagger 파일을](https://swagger.io) 통해 REST 끝점을 정기적으로 호출하는 워크플로를 자동화할 수 있습니다. HTTP + Swagger 트리거 및 작업은 [HTTP 트리거 및 동작과](connectors-native-http.md) 동일하게 작동하지만 Swagger 파일에 설명된 API 구조 와 출력을 노출하여 논리 앱 디자이너에서 더 나은 환경을 제공합니다. 폴링 트리거를 구현하려면 사용자 지정 API 만들기에 설명된 폴링 패턴을 따라 [논리 앱에서 다른 API, 서비스 및 시스템을 호출합니다.](../logic-apps/logic-apps-create-api-app.md#polling-triggers)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 대상 REST 끝점을 설명하는 Swagger(OpenAPI가 아님) 파일의 URL

  일반적으로 REST 끝점은 커넥터가 작동하려면 이 조건을 충족해야 합니다.

  * Swagger 파일은 공개적으로 액세스할 수 있는 HTTPS URL에서 호스팅되어야 합니다.

  * Swagger 파일에는 [CORS(원본 간 리소스 공유)가](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) 활성화되어 있어야 합니다.

  호스팅되지 않거나 보안 및 원본 간 요구 사항을 충족하지 않는 Swagger 파일을 참조하려면 [Azure 저장소 계정의 Blob 컨테이너에 Swagger 파일을 업로드하고](#host-swagger)해당 저장소 계정에서 CORS를 사용하도록 설정하여 파일을 참조할 수 있습니다.

  이 항목의 예제에서는 [코그너티브 서비스](https://docs.microsoft.com/azure/cognitive-services/face/overview) [계정 및 액세스 키가](../cognitive-services/cognitive-services-apis-create-account.md)필요한 인지 서비스 Face API를 사용합니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식 로직 앱을 새로 접하는 경우 [Azure 논리 앱이란 무엇입니까?](../logic-apps/logic-apps-overview.md)

* 대상 끝점을 호출할 논리 앱입니다. HTTP + Swagger 트리거로 시작하려면 [빈 논리 앱을 만듭니다.](../logic-apps/quickstart-create-first-logic-app-workflow.md) HTTP + Swagger 작업을 사용하려면 원하는 트리거로 논리 앱을 시작합니다. 이 예제에서는 HTTP + Swagger 트리거를 첫 번째 단계로 사용합니다.

## <a name="add-an-http--swagger-trigger"></a>HTTP + 스웨거 트리거 추가

이 기본 제공 트리거는 REST API를 설명하고 해당 파일의 내용을 포함하는 응답을 반환하는 Swagger 파일의 URL로 HTTP 요청을 보냅니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다. 논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 디자이너에서 검색 상자에 필터로 "swagger"를 입력합니다. 트리거 목록에서 HTTP + **Swagger** **트리거를 선택합니다.**

   ![HTTP + 스웨거 트리거 선택](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. **SWAGGER 끝점 URL** 상자에 Swagger 파일에 대 한 URL을 입력 하 고 **다음을**선택 합니다.

   이 예제에서는 [인지 서비스 Face API에](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)대 한 미국 서부 지역에 있는 Swagger URL을 사용 합니다.

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![스웨거 엔드포인트에 대한 URL 입력](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. 디자이너가 Swagger 파일에 설명된 작업을 표시하면 사용할 작업을 선택합니다.

   ![스웨거 파일의 작업](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. 끝점 호출에 포함할 선택한 작업에 따라 달라지는 트리거 매개 변수에 대한 값을 제공합니다. 트리거가 끝점을 호출할 빈도에 대한 되풀이를 설정합니다.

   이 예제는 트리거의 이름을 "HTTP + Swagger 트리거: Face - Detect"로 변경하여 단계에 더 설명적인 이름이 있도록 합니다.

   ![작업 세부 정보](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. 사용 가능한 다른 매개 변수를 추가하려면 새 매개 변수 목록 **추가를** 열고 원하는 매개 변수를 선택합니다.

   HTTP + Swagger에서 사용할 수 있는 인증 유형에 대한 자세한 내용은 [아웃바운드 호출에 대한 인증 추가를](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)참조하십시오.

1. 트리거가 발생할 때 실행되는 작업을 사용하여 논리 앱의 워크플로를 계속해서 작성합니다.

1. 작업이 완료되면 논리 앱을 저장해야 합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

## <a name="add-an-http--swagger-action"></a>HTTP + 스웨거 액션 추가

이 기본 제공 작업은 REST API를 설명하는 Swagger 파일의 URL에 대한 HTTP 요청을 하고 해당 파일의 내용을 포함하는 응답을 반환합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다. Logic Apps 디자이너에서 논리 앱을 엽니다.

1. HTTP + Swagger 작업을 추가하려는 단계에서 새 **단계를**선택합니다.

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 표시되는 더하기**+** 기호 ()를 선택한 다음 **작업 추가를**선택합니다.

1. 디자이너에서 검색 상자에 필터로 "swagger"를 입력합니다. **작업** 목록에서 **HTTP + Swagger** 작업을 선택합니다.

    ![HTTP + Swagger 동작 선택](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. **SWAGGER 끝점 URL** 상자에 Swagger 파일에 대 한 URL을 입력 하 고 **다음을**선택 합니다.

   이 예제에서는 [인지 서비스 Face API에](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)대 한 미국 서부 지역에 있는 Swagger URL을 사용 합니다.

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![스웨거 엔드포인트에 대한 URL 입력](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. 디자이너가 Swagger 파일에 설명된 작업을 표시하면 사용할 작업을 선택합니다.

   ![스웨거 파일의 작업](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. 끝점 호출에 포함할 작업(선택한 작업에 따라 달라지는 작업 매개 변수)에 대한 값을 제공합니다.

   이 예제에는 매개 변수가 없지만 단계의 설명이 더 많은 이름을 가지도록 "HTTP + Swagger 작업: Face - Identify"로 작업의 이름을 바꿉니다.

   ![작업 세부 정보](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. 사용 가능한 다른 매개 변수를 추가하려면 새 매개 변수 목록 **추가를** 열고 원하는 매개 변수를 선택합니다.

   HTTP + Swagger에서 사용할 수 있는 인증 유형에 대한 자세한 내용은 [아웃바운드 호출에 대한 인증 추가를](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)참조하십시오.

1. 작업이 완료되면 논리 앱을 저장해야 합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Azure 저장소의 호스트 스웨거

Azure 저장소 계정의 Blob 컨테이너에 해당 파일을 업로드하고 해당 저장소 계정에 CORS를 사용하도록 설정하여 호스팅되지 않거나 보안 및 원본 간 요구 사항을 충족하지 않는 Swagger 파일을 참조할 수 있습니다. Azure 저장소에 Swagger 파일을 만들고 설정 및 저장하려면 다음 단계를 따르십시오.

1. [Azure 저장소 계정을 만듭니다.](../storage/common/storage-create-storage-account.md)

1. 이제 Blob에 대한 CORS를 사용하도록 설정합니다. 저장소 계정의 메뉴에서 **CORS를**선택합니다. **Blob 서비스** 탭에서 이러한 값을 지정한 다음 **저장을**선택합니다.

   | 속성 | 값 |
   |----------|-------|
   | **허용된 원본** | `*` |
   | **허용된 메서드** | `GET`, `HEAD`, `PUT` |
   | **허용된 헤더** | `*` |
   | **노출된 헤더** | `*` |
   | **최대** 연령(초) | `200` |
   |||

   이 예제에서는 [Azure 포털을](https://portal.azure.com)사용하지만 Azure [저장소 탐색기와](https://storageexplorer.com/)같은 도구를 사용하거나 이 샘플 [PowerShell 스크립트를](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)사용하여 이 설정을 자동으로 구성할 수 있습니다.

1. [Blob 컨테이너를 만듭니다.](../storage/blobs/storage-quickstart-blobs-portal.md) 컨테이너의 **개요** 창에서 **액세스 수준 변경을**선택합니다. 공용 **액세스 수준** 목록에서 **Blob(Blob에 대해서만 익명 읽기 액세스)을**선택하고 **확인을**선택합니다.

1. [Azure 포털](https://portal.azure.com) 또는 [Azure 저장소 탐색기를](https://storageexplorer.com/)통해 [Swagger 파일을 Blob 컨테이너에 업로드합니다.](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)

1. Blob 컨테이너에서 파일을 참조하려면 대/소문자를 구분하는 이 형식 다음에 오는 HTTPS 링크를 사용합니다.

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>커넥터 참조

다음은 HTTP + Swagger 트리거 또는 동작의 출력에 대한 자세한 정보입니다. HTTP + Swagger 호출은 이 정보를 반환합니다.

| 속성 이름 | Type | Description |
|---------------|------|-------------|
| headers | object | 요청의 헤더 |
| 본문 | object | JSON 개체 | 요청의 본문 콘텐츠가 있는 개체 |
| 상태 코드 | int | 요청의 상태 코드 |
|||

| 상태 코드 | 설명 |
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
