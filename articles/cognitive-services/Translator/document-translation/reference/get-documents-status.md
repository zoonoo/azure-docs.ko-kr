---
title: 문서 상태 가져오기
titleSuffix: Azure Cognitive Services
description: 문서 상태 가져오기 메서드는 일괄 처리 문서 번역 요청의 모든 문서에 대한 상태를 반환합니다.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: 8476c4891cef9d9055b16c7ac574e569ecf5b3f2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864867"
---
# <a name="get-documents-status"></a>문서 상태 가져오기

문서 상태 가져오기 메서드는 일괄 처리 문서 번역 요청의 모든 문서에 대한 상태를 반환합니다.

응답에 포함된 문서는 문서 ID를 기준으로 내림차순으로 정렬됩니다. 응답의 문서 수가 페이징 제한을 초과하면 서버 측 페이징이 사용됩니다. 페이지가 매겨진 응답은 부분 결과를 나타내며 응답에 연속 토큰을 포함합니다. 연속 토큰이 없다는 것은 추가 페이지를 사용할 수 없음을 의미합니다.

$top 및 $skip 쿼리 매개 변수를 사용하여 반환할 결과 수와 컬렉션 오프셋을 지정할 수 있습니다. 서버는 클라이언트에서 지정한 값을 준수합니다. 그러나 클라이언트는 다른 페이지 크기를 포함하거나 연속 토큰을 포함하는 응답을 처리할 준비가 되어 있어야 합니다.

$top 및 $skip이 모두 포함된 경우 서버는 먼저 $skip을 적용한 다음 컬렉션에 $top을 적용해야 합니다.

> [!NOTE]
> 서버가 $top 및/또는 $skip을 준수할 수 없는 경우 서버는 쿼리 옵션을 무시하는 대신 클라이언트에게 오류를 알리는 오류를 반환해야 합니다. 이렇게 하면 반환된 데이터에 대해 클라이언트가 가정할 위험이 줄어듭니다.

## <a name="request-url"></a>요청 URL

다음에 `GET` 요청을 보냅니다.
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents
```

[사용자 지정 도메인 이름](../get-started-with-document-translation.md#find-your-custom-domain-name)을 찾는 방법에 대해 알아봅니다.

> [!IMPORTANT]
>
> * **문서 번역 서비스에 대한 모든 API 요청에는 사용자 지정 도메인 엔드포인트가 필요합니다.**
> * Azure Portal 리소스 _키 및 엔드포인트_ 페이지에 있는 엔드포인트나 전역 번역기 엔드포인트(`api.cognitive.microsofttranslator.com`)를 사용하여 문서 번역에 대한 HTTP 요청을 수행할 수 없습니다.

## <a name="request-parameters"></a>요청 매개 변수

쿼리 문자열에 전달된 요청 매개 변수는 다음과 같습니다.

|쿼리 매개 변수|필수|Description|
|--- |--- |--- |
|id|True|작업 ID입니다.|
|$skip|거짓|컬렉션에서 $skip 항목을 건너뜁니다. $top 및 $skip이 모두 제공되면 $skip이 먼저 적용됩니다.|
|$top|거짓|컬렉션에서 $top 항목을 가져옵니다. $top 및 $skip이 모두 제공되면 $skip이 먼저 적용됩니다.|

## <a name="request-headers"></a>요청 헤더

요청 헤더는 다음과 같습니다.

|headers|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|필수 요청 헤더|

## <a name="response-status-codes"></a>응답 상태 코드

요청을 반환하는 가능한 HTTP 상태 코드는 다음과 같습니다.

|상태 코드|Description|
|--- |--- |
|200|OK. 성공적으로 요청하고 문서의 상태를 반환합니다. HeadersRetry-After: integerETag: string|
|400|잘못된 요청입니다. 입력 매개 변수를 확인하세요.|
|401|권한이 없습니다. 자격 증명을 확인하세요.|
|404|리소스를 찾을 수 없습니다.|
|500|내부 서버 오류.|
|기타 상태 코드|<ul><li>너무 많은 요청</li><li>서버를 일시적으로 사용할 수 없음</li></ul>|


## <a name="get-documents-status-response"></a>문서 상태 가져오기 응답

### <a name="successful-get-documents-status-response"></a>문서 상태 가져오기 성공적인 응답

성공적인 응답에서 반환되는 정보는 다음과 같습니다.

|Name|유형|Description|
|--- |--- |--- |
|@nextLink|문자열|다음 페이지의 URL입니다. 사용 가능한 페이지가 더 이상 없으면 Null입니다.|
|값|DocumentStatusDetail []|아래 나열된 개별 문서의 세부 상태입니다.|
|value.path|문자열|문서 또는 폴더의 위치입니다.|
|value.createdDateTimeUtc|문자열|작업을 만든 날짜/시간입니다.|
|value.lastActionDateTimeUt|문자열|작업 상태가 업데이트된 날짜/시간입니다.|
|value.status|상태|작업 또는 문서의 가능한 상태 목록입니다.<ul><li>취소됨</li><li>Cancelling</li><li>실패</li><li>NotStarted</li><li>실행 중</li><li>성공</li><li>ValidationFailed</li></ul>|
|value.to|문자열|대상 언어입니다.|
|value.progress|문자열|사용 가능한 경우 번역의 진행률입니다.|
|value.id|문자열|문서 ID입니다.|
|value.characterCharged|integer|API로 청구되는 문자 수입니다.|

### <a name="error-response"></a>오류 응답

|Name|유형|설명|
|--- |--- |--- |
|code|문자열|상위 수준 오류 코드를 포함하는 열거형입니다. 가능한 값은 다음과 같습니다.<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>권한 없음</li></ul>|
|message|문자열|상위 수준 오류 메시지를 가져옵니다.|
|대상|문자열|오류의 출처입니다. 예를 들어 유효하지 않은 문서의 경우 "documents" 또는 "document id"가 됩니다.|
|innerError|InnerErrorV2|Cognitive Services API 지침을 준수하는 새로운 내부 오류 형식입니다. 여기에는 필수 속성 ErrorCode, 메시지 및 선택적 속성 대상, 세부 정보(키 값 쌍), 내부 오류(중첩될 수 있음)가 포함됩니다.|
|innerError.code|문자열|코드 오류 문자열을 가져옵니다.|
|innerError.message|문자열|상위 수준 오류 메시지를 가져옵니다.|

## <a name="examples"></a>예제

### <a name="example-successful-response"></a>성공적인 응답 예

다음은 성공적인 응답의 예입니다.

```JSON
{
  "value": [
    {
      "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
      "createdDateTimeUtc": "2020-03-26T00:00:00Z",
      "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
      "status": "Running",
      "to": "fr",
      "progress": 0.1,
      "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
      "characterCharged": 0
    }
  ],
  "@nextLink": "https://westus.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55/documents?$top=5&$skip=15"
}
```

### <a name="example-error-response"></a>오류 응답 예

다음은 오류 응답의 예입니다. 다른 오류 코드에 대한 스키마는 동일합니다.

상태 코드: 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "target": "Operation",
    "innerError": {
      "code": "InternalServerError",
      "message": "Unexpected internal server error has occurred"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

문서 번역 및 클라이언트 라이브러리를 사용하는 방법에 대해 자세히 알아보려면 빠른 시작을 따르세요.

> [!div class="nextstepaction"]
> [문서 번역 시작](../get-started-with-document-translation.md)
