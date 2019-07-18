---
title: Azure Logic Apps에서 REST 끝점에 연결
description: Azure Logic Apps를 사용 하 여 자동화 된 작업, 프로세스 및 워크플로 REST 끝점을 모니터링
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: f0410ed7a98e4838e41407868cf26b5254811ae3
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541673"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용 하 여 REST 끝점 호출

사용 하 여 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 기본 제공 HTTP + Swagger 커넥터를 통해 REST 끝점을 정기적으로 호출 하는 워크플로 자동화할 수 있습니다를 [Swagger 파일](https://swagger.io) 논리 앱을 빌드하여 합니다. HTTP + Swagger 트리거 및 동작은 동일 하 게 작동 합니다 [HTTP 트리거 및 작업](connectors-native-http.md) 의 API 구조 및 Swagger 파일에서 설명 하는 출력을 노출 하 여 논리 앱 디자이너에서 향상 된 환경을 제공 합니다. 폴링 트리거를 구현 하려면에 설명 된 폴링 패턴을 따릅니다 [논리 앱에서 다른 Api, 서비스 및 시스템 호출을 사용자 지정 Api 만들기](../logic-apps/logic-apps-create-api-app.md#polling-triggers)합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 대상 REST 끝점을 설명 하는 Swagger 파일에 대 한 URL

  일반적으로 REST 끝점에는 커넥터가 작동 하려면이 조건을 충족 해야 합니다.

  * Swagger 파일을 공개적으로 액세스할 수 있는 HTTPS URL에서 호스트 되어야 합니다.

  * Swagger 파일 있어야 [크로스-원본 자원 공유 (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) 사용 하도록 설정 합니다.

  호스트 되지 않는 또는 보안 및 원본 간 요구 사항을 충족 하지 않는 Swagger 파일을 참조 하려면 [Azure storage 계정에서 blob 컨테이너에 Swagger 파일을 업로드](#host-swagger), 따라서 해당 저장소 계정에서 CORS를 사용 하 고 파일을 참조할 수 있습니다.

  이 항목에서는 사용 예제는 [Cognitive Services Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview), 필요는 [Cognitive Services 계정과 액세스 키](../cognitive-services/cognitive-services-apis-create-account.md)합니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

* 대상 끝점을 호출 하려는 논리 앱. 시작 하려면 HTTP + Swagger 트리거 [빈 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)합니다. 사용 하 여 HTTP + Swagger 동작을 원하는 모든 트리거를 사용 하 여 논리 앱을 시작 합니다. 이 예제에서는 HTTP + Swagger 트리거를 첫 번째 단계입니다.

## <a name="add-an-http--swagger-trigger"></a>추가 HTTP + Swagger 트리거

기본 제공이 트리거는 REST API를 설명 하 고 해당 파일의 콘텐츠를 포함 하는 응답을 반환 하는 Swagger 파일에 대 한 URL로 HTTP 요청을 보냅니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 검색 상자에서 디자이너에서 필터로 "swagger"를 입력 합니다. **트리거** 목록에서 선택 합니다 **HTTP + Swagger** 트리거.

   ![선택한 HTTP + Swagger 트리거](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. 에 **SWAGGER 끝점 URL** 상자 Swagger 파일에 대 한 URL을 입력 하 고 선택 **다음**합니다.

   이 예제에서는 미국 서 부 지역에 있는 Swagger URL을 사용 합니다 [Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Swagger 끝점에 대 한 URL을 입력 합니다.](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. 디자이너에서 Swagger 파일에 설명 된 작업 표시를 하는 경우 사용 하려는 작업을 선택 합니다.

   ![Swagger 파일에서 작업](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. 끝점 호출에 포함 하려는 선택된 된 작업에 따라 달라 지는 트리거 매개 변수의 값을 제공 합니다. 되풀이 트리거 빈도 대 한 끝점을 호출 하도록 설정 합니다.

   이 예제에서는 트리거를 "HTTP + Swagger 트리거: 얼굴 감지-"단계 보다 알기 쉬운 이름에 포함 되도록 합니다.

   ![작업 세부 정보](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. 다른 사용 가능한 매개 변수를 추가 하려면 열을 **새 매개 변수 추가** 목록 및 매개 변수를 선택 합니다.

   HTTP + Swagger에 대 한 인증 유형 사용할 수 있는 방법에 대 한 자세한 내용은 참조 [인증 HTTP 트리거 및 작업](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)합니다.

1. 트리거가 발생할 때 실행되는 작업을 사용하여 논리 앱의 워크플로를 계속해서 작성합니다.

1. 완료 하면 논리 앱을 저장 해야 합니다. 디자이너 도구 모음에서 선택 **저장할**합니다.

## <a name="add-an-http--swagger-action"></a>추가 HTTP + Swagger 동작

이 기본 제공 작업 REST API를 설명 하 고 해당 파일의 콘텐츠를 포함 하는 응답을 반환 하는 Swagger 파일에 대 한 URL로 HTTP 요청을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. Logic Apps 디자이너에서 논리 앱을 엽니다.

1. HTTP + Swagger를 추가 하려는 단계 아래 작업을 선택 **새 단계**합니다.

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 더하기 기호 ( **+** ) 표시 하 고 선택한 **작업 추가**합니다.

1. 검색 상자에서 디자이너에서 필터로 "swagger"를 입력 합니다. **작업** 목록에서 선택 합니다 **HTTP + Swagger** 작업 합니다.

    ![HTTP + Swagger 동작 선택](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. 에 **SWAGGER 끝점 URL** 상자 Swagger 파일에 대 한 URL을 입력 하 고 선택 **다음**합니다.

   이 예제에서는 미국 서 부 지역에 있는 Swagger URL을 사용 합니다 [Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Swagger 끝점에 대 한 URL을 입력 합니다.](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. 디자이너에서 Swagger 파일에 설명 된 작업 표시를 하는 경우 사용 하려는 작업을 선택 합니다.

   ![Swagger 파일에서 작업](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. 끝점 호출에 포함 하려는 선택된 된 작업에 따라 달라 지는 작업 매개 변수의 값을 제공 합니다.

   이 예제에서는 매개 변수가 있지만 작업의 이름을 바꿉니다 "HTTP + Swagger 동작: 얼굴-식별"단계 보다 알기 쉬운 이름에 포함 되도록 합니다.

   ![작업 세부 정보](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. 다른 사용 가능한 매개 변수를 추가 하려면 열을 **새 매개 변수 추가** 목록 및 매개 변수를 선택 합니다.

   HTTP + Swagger에 대 한 인증 유형 사용할 수 있는 방법에 대 한 자세한 내용은 참조 [인증 HTTP 트리거 및 작업](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)합니다.

1. 완료 하면 논리 앱을 저장 해야 합니다. 디자이너 도구 모음에서 선택 **저장할**합니다.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Azure Storage에서 Swagger 호스트

호스트 되지 않는 또는 Azure storage 계정에서 blob 컨테이너에 해당 파일을 업로드 하 고 해당 저장소 계정에서 CORS를 사용 하 여 보안 및 원본 간 요구 사항을 충족 하지 않는 Swagger 파일을 참조할 수 있습니다. 만들기, 설정 및 Azure Storage에서 Swagger 파일을 저장, 하려면 다음이 단계를 수행 합니다.

1. [Azure Storage 계정 만들기](../storage/common/storage-create-storage-account.md)

1. 이제 CORS를 사용 하도록 설정 된 blob에 대 한 합니다. 저장소 계정 메뉴에서 선택 **CORS**합니다. 에 **Blob service** 탭, 이러한 값을 지정 하 고 선택한 **저장**합니다.

   | 자산 | 값 |
   |----------|-------|
   | **허용 되는 원본** | `*` |
   | **허용 되는 메서드** | `GET`, `HEAD`, `PUT` |
   | **허용 되는 헤더** | `*` |
   | **노출 된 헤더** | `*` |
   | **최대 기간** 초 단위로 | `200` |
   |||

   이 예제에서는 합니다 [Azure portal](https://portal.azure.com)와 같은 도구를 사용할 수 있습니다 [Azure Storage 탐색기](https://storageexplorer.com/), 자동으로이 샘플을 사용 하 여이 설정을 구성 하거나 [PowerShell스크립트](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Blob 컨테이너 만들기](../storage/blobs/storage-quickstart-blobs-portal.md)합니다. 컨테이너의 온 **개요** 창 **액세스 수준 변경**합니다. **공용 액세스 수준을** 목록에서 **Blob (blob 전용 익명 읽기 액세스)** 를 선택한 **확인**합니다.

1. [Swagger 파일을 blob 컨테이너에 업로드](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)를 통해 합니다 [Azure portal](https://portal.azure.com) 또는 [Azure Storage 탐색기](https://storageexplorer.com/)합니다.

1. Blob 컨테이너에 파일을 참조 하려면를 대/소문자 구분이 형식에 따라 HTTPS 링크를 사용 합니다.

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>커넥터 참조

HTTP + Swagger에서 출력에 대 한 자세한 내용은 여기에 트리거 또는 동작 합니다. HTTP + Swagger 호출이이 정보를 반환 합니다.

| 속성 이름 | 형식 | 설명 |
|---------------|------|-------------|
| headers | object | 요청에서 헤더 |
| body | object | JSON 개체 | 요청 본문 내용 사용 하 여 개체 |
| status code | int | 요청에서 상태 코드 |
|||

| status code | 설명 |
|-------------|-------------|
| 200 | 확인 |
| 202 | 동의함 |
| 400 | 잘못된 요청 |
| 401 | 권한 없음 |
| 403 | 사용할 수 없음 |
| 404 | 찾을 수 없음 |
| 500 | 내부 서버 오류. 알 수 없는 오류 발생. |
|||

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.