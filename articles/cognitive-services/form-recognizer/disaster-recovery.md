---
title: Azure Form Recognizer에 대한 재해 복구 지침
titleSuffix: Azure Cognitive Services
description: 모델 복사 API를 사용하여 Form Recognizer 리소스를 백업하는 방법을 알아봅니다.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: b5eb776a7807f48ae6c1a0e3c5879da1f6823830
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466918"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>Form Recognizer 모델 백업 및 복구

Azure Portal에서 Form Recognizer 리소스를 만들 때는 지역을 지정합니다. 그때부터 리소스와 모든 작업이 해당 특정 Azure 서버 지역과 연결된 상태를 유지합니다. 전체 지역에 적용되는 네트워크 문제가 발생하는 것은 드물지만 불가능한 것도 아닙니다. 솔루션을 항상 사용할 수 있어야 하는 경우, 다른 지역으로 장애 조치(failover)하거나 둘 이상의 지역 간에 워크로드를 분할하도록 설계해야 합니다. 두 방법을 사용하려면 서로 다른 지역에 두 개 이상의 Form Recognizer 리소스가 필요하고 여러 지역에서 사용자 지정 모델을 동기화할 수 있어야 합니다.

복사 API를 사용하면 한 Form Recognizer 계정의 사용자 지정 모델을 지원되는 모든 지역에 있는 다른 계정에 복사할 수 있으므로 이러한 시나리오가 가능합니다. 이 가이드에서는 cURL과 함께 복사 REST API를 사용하는 방법을 보여 줍니다. Postman과 같은 HTTP 요청 서비스를 사용하여 요청을 실행할 수도 있습니다.

## <a name="business-scenarios"></a>비즈니스 시나리오

앱 또는 비즈니스가 Form Recognizer 사용자 지정 모델 사용에 따라 달라지는 경우, 다른 지역의 다른 Form Recognizer 계정으로 모델을 복사하는 것이 좋습니다. 지역 가동 중단이 발생하는 경우 모델이 복사된 지역에 있는 모델에 액세스할 수 있습니다.

##  <a name="prerequisites"></a>사전 요구 사항

1. 서로 다른 Azure 지역에 있는 두 가지 Form Recognizer Azure 리소스. 없다면 Azure Portal로 이동하여 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="새 Form Recognizer 리소스 만들기" target="_blank">새 Form Recognizer 리소스 만들기</a>를 수행합니다.
1. Form Recognizer 리소스의 구독 키, 엔드포인트 URL 및 구독 ID. Azure Portal의 리소스 **개요** 탭에서 이러한 값을 찾을 수 있습니다.


## <a name="copy-api-overview"></a>복사 API 개요

사용자 지정 모델을 복사하는 프로세스는 다음 단계로 구성됩니다.

1. 먼저 대상 리소스, 즉 복사된 모델을 받을 리소스에 대한 복사 권한 부여 요청을 실행합니다. 복사된 데이터를 받을, 새로 만든 대상 모델의 URL이 반환됩니다.
1. 다음으로, 원본 리소스, 즉 복사할 모델을 포함하는 리소스로 복사 요청을 보냅니다. 작업의 진행률을 추적하기 위해 쿼리할 수 있는 URL이 반환됩니다.
1. 작업이 성공할 때까지 원본 리소스 자격 증명을 사용하여 진행률 URL을 쿼리합니다. 또한 대상 리소스의 새 모델 ID를 쿼리하여 새 모델의 상태를 가져올 수도 있습니다.

## <a name="generate-copy-authorization-request"></a>복사 권한 부여 요청 생성

다음 HTTP 요청은 대상 리소스에서 복사 권한 부여를 가져옵니다. 대상 리소스의 엔드포인트와 키를 헤더로 입력해야 합니다.

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

본문에 `modelId` 값이 포함된 `201\Created` 응답을 받게 됩니다. 이 문자열은 새로 만든(비어 있는) 모델의 ID입니다. API가 이 리소스에 데이터를 복사하려면 `accessToken`이 필요하며, `expirationDateTimeTicks` 값은 토큰의 만료일입니다. 이러한 값 세 가지를 모두 안전한 위치에 저장합니다.

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>복사 작업 시작

다음 HTTP 요청은 원본 리소스에 대한 복사 작업을 시작합니다. 원본 리소스의 엔드포인트와 키를 헤더로 입력해야 합니다. 요청 URL에는 복사하려는 원본 모델의 모델 ID가 포함되어 있습니다.

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/<your model ID>/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

요청 본문은 다음과 같은 형식이어야 합니다. 대상 리소스의 리소스 ID 및 지역 이름을 입력해야 합니다. Azure Portal의 리소스 **속성** 탭에서 리소스 ID를 확인할 수 있으며, **키 및 엔드포인트** 탭에서 지역 이름을 확인할 수 있습니다. 이전 단계에서 복사한 모델 ID, 액세스 토큰 및 만료 값도 필요합니다.

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
}
```

> [!NOTE]
> 복사 API는 [AEK/CMK](https://msazure.visualstudio.com/Cognitive%20Services/_wiki/wikis/Cognitive%20Services.wiki/52146/Customer-Managed-Keys) 기능을 투명하게 지원합니다. 이를 위해 특별한 처리가 필요하지는 않지만 암호화되지 않은 리소스를 암호화된 리소스로 복사하는 경우 요청 헤더 `x-ms-forms-copy-degrade: true`를 포함해야 합니다. 이 헤더가 포함되지 않은 경우 복사 작업이 실패하고 `DataProtectionTransformServiceError`를 반환합니다.

Operation-Location 헤더를 포함하는 `202\Accepted` 응답을 받게 됩니다. 이 값은 작업의 진행률을 추적하는 데 사용할 URL입니다. 다음 단계를 위해 임시 위치에 복사합니다.

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

### <a name="common-errors"></a>일반 오류

|Error|해결 방법|
|:--|:--|
| 400 / 잘못된 요청(`"code:" "1002"`) | 유효성 검사 오류 또는 잘못된 형식의 복사 요청을 나타냅니다. 일반적인 문제는 다음과 같습니다. a) 잘못되었거나 수정된 `copyAuthorization` 페이로드. b) `expirationDateTimeTicks` 토큰의 만료된 값(`copyAuhtorization` 페이로드는 24시간 동안 유효). c) 잘못되었거나 지원되지 않는 `targetResourceRegion`. d) 잘못되었거나 형식이 잘못된 `targetResourceId` 문자열.
|

## <a name="track-copy-progress"></a>복사 진행률 추적

소스 리소스 엔드포인트에 대해 **복사 모델 결과 가져오기** API를 쿼리하여 진행률을 추적합니다.

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

응답은 작업의 상태에 따라 달라집니다. JSON 본문에서 `"status"` 필드를 찾습니다. 스크립트에서 이 API 호출을 자동화하는 경우 1초마다 작업을 쿼리하는 것이 좋습니다.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="common-errors"></a>일반 오류

|Error|해결 방법|
|:--|:--|
|"errors":[{"code":"AuthorizationError",<br>"message":"Authorization failure due to <br>missing or invalid authorization claims."}]   | `copyAuthorization`API에서 반환한 페이로드 또는 콘텐츠가 구성된 경우`copyAuthorization` 발생합니다. 페이로드가 이전 `copyAuthorization` 호출에서 반환된 것과 똑같은 내용인지 확인합니다.|
|"errors":[{"code":"AuthorizationError",<br>"message":"Could not retrieve authorization <br>metadata. If this issue persists use a different <br>target model to copy into."}] | `copyAuthorization` 페이로드가 복사 요청에 재사용되고 있음을 나타냅니다. 성공한 복사 요청은 동일한 `copyAuthorization` 페이로드를 사용하는 추가 요청을 허용하지 않습니다. 아래에 나와 있는 것 같은 별도의 오류를 발생시키고 이후에 동일한 권한 부여 페이로드를 사용하여 복사본을 다시 시도하면 이 오류가 발생합니다. 해결 방법은 새 `copyAuthorization` 페이로드를 생성한 다음, 복사 요청을 다시 실행하는 것입니다.|
|"errors":[{"code":"DataProtectionTransformServiceError",<br>"message":"Data transfer request is not allowed <br>as it downgrades to a less secure data protection scheme. Refer documentation or contact your service administrator <br>for details."}]    | `AEK`가 설정된 리소스와 `AEK`가 설정되지 않은 리소스 간에 복사하는 경우에 발생합니다. 암호화된 모델을 암호화되지 않은 모델로 대상에 복사하는 것을 허용하려면 복사 요청이 있는 `x-ms-forms-copy-degrade: true` 헤더를 지정합니다.|
|"errors":[{"code":"ResourceResolverError",<br>"message":"Could not fetch information for Cognitive resource with Id '...'. Ensure the resource is valid and exists in the specified region 'westus2'.."}] | `targetResourceId`로 지정된 Azure 리소스가 올바른 Cognitive 리소스인지, 아니면 존재하지 않는지를 나타냅니다. 이 문제를 해결하려면 복사 요청을 확인하고 다시 실행하세요.|


### <a name="optional-track-the-target-model-id"></a>[선택 사항] 대상 모델 ID 추적 

또한 **사용자 지정 모델 가져오기** API를 통해 대상 모델을 쿼리하여 작업 상태를 추적할 수 있습니다. 첫 번째 단계에서 복사한 대상 모델 ID를 사용하여 이 API를 호출합니다.

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

응답 본문에 모델에 대한 정보가 표시됩니다. `"status"` 필드에서 모델 상태를 확인합니다.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>cURL 샘플 코드

다음 코드 조각에서는 cURL을 사용하여 위의 단계에 설명된 API 호출을 수행합니다. 고유한 리소스와 관련된 모델 ID 및 구독 정보는 여전히 입력해야 합니다.

### <a name="generate-copy-authorization-request"></a>복사 권한 부여 요청 생성

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>복사 작업 시작

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>복사 진행률 추적

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.0/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>다음 단계

이 가이드에서는 복사 API를 사용하여 사용자 지정 모델을 보조 Form Recognizer 리소스에 백업하는 방법을 배웠습니다. 다음으로, API 참조 문서를 탐색하여 Form Recognizer로 할 수 있는 다른 작업을 살펴봅니다.
* [REST API 참조 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
