---
title: Azure 양식 인식기에 대 한 재해 복구 지침
titleSuffix: Azure Cognitive Services
description: 모델 복사 API를 사용 하 여 양식 인식기 리소스를 백업 하는 방법을 알아봅니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 2e5b32421a04e09bd32d2bba21ff4faf920d84dd
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2020
ms.locfileid: "84221863"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>양식 인식기 모델 백업 및 복구

Azure Portal에서 양식 인식기 리소스를 만들 때는 지역을 지정 합니다. 그런 다음에는 리소스와 모든 작업이 해당 특정 Azure 서버 지역과 연결 된 상태를 유지 합니다. 전체 지역에 도달 하는 네트워크 문제가 드물게 발생 하는 것은 아닙니다. 솔루션을 항상 사용할 수 있어야 하는 경우 다른 지역으로 장애 조치 (failover) 하거나 둘 이상의 지역 간에 작업을 분할 하도록 디자인 해야 합니다. 두 방법 모두 서로 다른 지역에는 두 개 이상의 양식 인식기 리소스가 필요 하 고 여러 지역에서 [사용자 지정 모델](./quickstarts/curl-train-extract.md) 을 동기화 할 수 있어야 합니다.

복사 API를 사용 하면 하나의 양식 인식기 계정 또는 다른 사용자에 게 사용자 지정 모델을 복사 하 여 지원 되는 모든 지역에 있을 수 있습니다. 이 가이드에서는 REST API 복사를 사용 하는 방법을 보여 줍니다. Postman과 같은 HTTP 요청 서비스를 사용 하 여 요청을 실행할 수도 있습니다.

## <a name="business-scenarios"></a>비즈니스 시나리오

앱 또는 비즈니스가 양식 인식기 사용자 지정 모델 사용에 따라 달라 지는 경우 다른 지역의 다른 폼 인식기 계정으로 모델을 복사 하는 것이 좋습니다. 지역 가동 중단이 발생 하면 복사 된 지역에서 모델에 액세스할 수 있습니다.

##  <a name="prerequisites"></a>필수 구성 요소

1. 서로 다른 Azure 지역에 있는 두 가지 양식 인식기 Azure 리소스입니다. 없는 경우 Azure Portal로 이동 하 여 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" 새 양식 인식기 리소스를 만듭니다 " target="_blank"> . 새 양식 인식기 리소스를 만듭니다 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .
1. 양식 인식기 리소스의 구독 키, 끝점 URL 및 구독 ID입니다. Azure Portal의 리소스 **개요** 탭에서 이러한 값을 찾을 수 있습니다.


## <a name="copy-api-overview"></a>API 복사 개요

사용자 지정 모델을 복사 하는 프로세스는 다음 단계로 구성 됩니다.

1. 먼저 대상 리소스, 즉 복사 된 모델을 받는 리소스에 대 한 복사 권한 부여 요청을 실행 &mdash; 합니다. 새로 만든 대상 모델의 URL은 복사 된 데이터를 받게 됩니다.
1. 그런 다음 복사할 &mdash; 모델을 포함 하는 리소스를 원본 리소스로 복사 요청을 보냅니다. 작업의 진행 상황을 추적 하기 위해 쿼리할 수 있는 URL을 다시 받게 됩니다.
1. 작업이 성공할 때까지 원본 리소스 자격 증명을 사용 하 여 진행률 URL을 쿼리 합니다. 대상 리소스의 새 모델 ID를 쿼리하여 새 모델의 상태를 가져올 수도 있습니다.

## <a name="generate-copy-authorization-request"></a>복사 권한 부여 요청 생성

다음 HTTP 요청은 대상 리소스에서 복사 권한 부여를 가져옵니다. 대상 리소스의 끝점과 키를 헤더로 입력 해야 합니다.

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

`201\Created`본문에 값이 포함 된 응답을 받게 됩니다 `modelId` . 이 문자열은 새로 만든 (비어 있는) 모델의 ID입니다. 는 `accessToken` API가이 리소스에 데이터를 복사 하는 데 필요 하며, `expirationDateTimeTicks` 값은 토큰의 만료입니다. 이러한 값 중 세 가지를 모두 안전한 위치에 저장 합니다.

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","accessToken":"1855fe23-5ffc-427b-aab2-e5196641502f","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>복사 작업 시작

다음 HTTP 요청은 소스 리소스에 대 한 복사 작업을 시작 합니다. 원본 리소스의 끝점과 키를 헤더로 입력 해야 합니다. 요청 URL에는 복사 하려는 원본 모델의 모델 ID가 포함 되어 있습니다.

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

요청 본문은 다음과 같은 형식 이어야 합니다. 대상 리소스의 리소스 ID 및 지역 이름을 입력 해야 합니다. 이전 단계에서 복사한 모델 ID, 액세스 토큰 및 만료 값도 필요 합니다.

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","accessToken":"1855fe23-5ffc-427b-aab2-e5196641502f","expirationDateTimeTicks":637233481531659440}
}
```

`202\Accepted`작업 위치 헤더를 사용 하 여 응답을 받게 됩니다. 이 값은 작업의 진행률을 추적 하는 데 사용 하는 URL입니다. 다음 단계를 위해 임시 위치에 복사 합니다.

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

## <a name="track-copy-progress"></a>복사 진행률 추적

소스 리소스 끝점에 대 한 **Get Copy Model Result** API를 쿼리하여 진행률을 추적 합니다.

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

응답은 작업의 상태에 따라 달라 집니다. `"status"`JSON 본문에서 필드를 찾습니다. 스크립트에서이 API 호출을 자동화 하는 경우 1 초 마다 작업을 쿼리 하는 것이 좋습니다.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="optional-track-the-target-model-id"></a>필드 대상 모델 ID 추적 

또한 **사용자 지정 모델 가져오기** API를 사용 하 여 대상 모델을 쿼리하여 작업 상태를 추적할 수 있습니다. 첫 번째 단계에서 복사한 대상 모델 ID를 사용 하 여이 API를 호출 합니다.

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

응답 본문에 모델에 대 한 정보가 표시 됩니다. `"status"`모델 상태에 대 한 필드를 선택 합니다.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>말아 샘플 코드

다음 코드 조각에서는 넘기기를 사용 하 여 위의 단계에 설명 된 API 호출을 수행 합니다. 사용자의 리소스에 특정 한 모델 Id 및 구독 정보를 입력 해야 합니다.

### <a name="generate-copy-authorization-request"></a>복사 권한 부여 요청 생성

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>복사 작업 시작

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>복사 진행률 추적

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.0-preview/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>다음 단계

이 가이드에서는 복사 API를 사용 하 여 사용자 지정 모델을 보조 폼 인식기 리소스로 백업 하는 방법을 배웠습니다. 다음으로 API 참조 문서를 탐색 하 여 양식 인식기로 수행할 수 있는 다른 작업을 확인 합니다.
* [REST API 참조 설명서](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)