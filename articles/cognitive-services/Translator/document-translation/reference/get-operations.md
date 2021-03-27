---
title: 문서 변환 가져오기 작업
titleSuffix: Azure Cognitive Services
description: Get 작업 메서드는 제출 된 일괄 처리 요청 목록과 각 요청에 대 한 상태를 반환 합니다.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: c42f3081a831c267c7bc605267b99e2a916ea3d8
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613023"
---
# <a name="document-translation-get-operations"></a>문서 번역: get 작업

Get 작업 메서드는 제출 된 일괄 처리 요청 목록과 각 요청에 대 한 상태를 반환 합니다. 이 목록에는 사용자가 제출한 일괄 처리 요청 (구독 기반)만 포함 됩니다. 각 요청에 대 한 상태는 id를 기준으로 정렬 됩니다.

요청 수가 페이징 한도를 초과 하면 서버측 페이징이 사용 됩니다. 페이지가 매겨진 응답은 부분 결과를 나타내며 응답에 연속 토큰을 포함 합니다. 연속 토큰이 없으면 추가 페이지를 사용할 수 없음을 의미 합니다.

$top 및 $skip 쿼리 매개 변수를 사용 하 여 반환할 결과 수와 컬렉션의 오프셋을 지정할 수 있습니다.

서버는 클라이언트에서 지정 된 값을 준수 합니다. 그러나 클라이언트는 다른 페이지 크기를 포함 하거나 연속 토큰을 포함 하는 응답을 처리할 수 있도록 준비 해야 합니다.

$Top와 $skip 모두 포함 된 경우 서버는 먼저 $skip를 적용 한 다음 컬렉션에서 $top 합니다. 

> [!NOTE]
> 서버에서 $top 및/또는 $skip를 처리할 수 없는 경우 서버는 쿼리 옵션을 무시 하는 대신 클라이언트에 게 오류를 알리는 오류를 반환 해야 합니다. 이렇게 하면 클라이언트에서 반환 되는 데이터에 대 한 가정을 줄일 가능성이 줄어듭니다.

## <a name="request-url"></a>요청 URL

다음에 `GET` 요청을 보냅니다.
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

[사용자 지정 도메인 이름을](../get-started-with-document-translation.md#find-your-custom-domain-name)찾는 방법에 대해 알아봅니다.

> [!IMPORTANT]
>
> * **문서 변환 서비스에 대 한 모든 API 요청에는 사용자 지정 도메인 끝점이 필요** 합니다.
> * 문서 변환에 대 한 HTTP 요청을 수행 하려면 Azure Portal 리소스 _키와 끝점_ 페이지 또는 전역 변환기 끝점 ()에 있는 끝점을 사용할 수 없습니다 `api.cognitive.microsofttranslator.com` .

## <a name="request-parameters"></a>요청 매개 변수

쿼리 문자열에 전달된 요청 매개 변수는 다음과 같습니다.

|쿼리 매개 변수|필수|설명|
|--- |--- |--- |
|$skip|False|컬렉션에서 $skip 항목을 건너뜁니다. $Top와 $skip를 모두 제공 하는 경우 $skip 먼저 적용 됩니다.|
|$top|False|컬렉션에서 $top 항목을 가져옵니다. $Top와 $skip를 모두 제공 하는 경우 $skip 먼저 적용 됩니다.|

## <a name="request-headers"></a>요청 헤더

요청 헤더는 다음과 같습니다.

|headers|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|필수 요청 헤더|

## <a name="response-status-codes"></a>응답 상태 코드

요청을 반환하는 가능한 HTTP 상태 코드는 다음과 같습니다.

|상태 코드|설명|
|--- |--- |
|200|OK. 성공적인 요청 및 모든 작업의 상태를 반환 합니다. HeadersRetry: integerETag: string|
|400|잘못된 요청. 잘못된 요청입니다. 입력 매개 변수를 확인 하십시오.|
|401|권한이 없습니다. 자격 증명을 확인 합니다.|
|500|내부 서버 오류.|
|기타 상태 코드|<ul><li>너무 많은 요청</li><li>서버 임시 사용할 수 없음</li></ul>|

## <a name="get-operations-response"></a>작업 응답 가져오기

### <a name="successful-get-operations-response"></a>성공한 작업 가져오기 응답

성공적인 응답에서 반환 되는 정보는 다음과 같습니다.

|이름|Type|Description|
|--- |--- |--- |
|id|문자열|작업의 ID입니다.|
|createdDateTimeUtc|문자열|작업을 만든 날짜/시간입니다.|
|lastActionDateTimeUtc|문자열|작업 상태가 업데이트 된 날짜 시간입니다.|
|상태|String|작업 또는 문서에 대 한 가능한 상태 목록: <ul><li>취소됨</li><li>Cancelling</li><li>실패</li><li>NotStarted</li><li>실행 중</li><li>성공</li><li>ValidationFailed</li></ul>|
|요약|StatusSummary []|아래 나열 된 세부 정보를 포함 하는 요약입니다.|
|요약. total|정수|총 문서 수입니다.|
|요약. 실패|정수|실패 한 문서 수입니다.|
|요약. 성공|정수|성공적으로 번역 된 문서 수입니다.|
|요약. inProgress|정수|진행 중인 문서 수입니다.|
|요약. notYetStarted|정수|아직 처리를 시작 하지 않은 문서 수입니다.|
|요약. 취소 됨|정수|취소 된 문서 수입니다.|
|totalCharacterCharged|정수|청구 되는 총 문자 수입니다.|

###<a name="error-response"></a>오류 응답

|이름|Type|설명|
|--- |--- |--- |
|code|문자열|상위 수준 오류 코드를 포함 하는 열거형입니다. 가능한 값은 다음과 같습니다.<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>권한 없음</li></ul>|
|message|문자열|상위 수준 오류 메시지를 가져옵니다.|
|대상|문자열|오류의 소스를 가져옵니다. 예를 들어 잘못 된 문서의 경우 "문서" 또는 "문서 id"가 될 수 있습니다.|
|innerError|InnerErrorV2|Cognitive Services API 지침을 준수 하는 새로운 내부 오류 형식입니다. 필수 속성인 ErrorCode, message 및 optional 속성 target, details (키 값 쌍), 내부 오류 (중첩 될 수 있음)가 포함 되어 있습니다.|
|innerError|문자열|코드 오류 문자열을 가져옵니다.|
|innerError|문자열|상위 수준 오류 메시지를 가져옵니다.|

## <a name="examples"></a>예제

### <a name="example-successful-response"></a>성공적인 응답 예제

성공적인 응답의 예는 다음과 같습니다.

```JSON
{
  "value": [
    {
      "id": "727bf148-f327-47a0-9481-abae6362f11e",
      "createdDateTimeUtc": "2020-03-26T00:00:00Z",
      "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
      "status": "Succeeded",
      "summary": {
        "total": 10,
        "failed": 1,
        "success": 9,
        "inProgress": 0,
        "notYetStarted": 0,
        "cancelled": 0,
        "totalCharacterCharged": 0
      }
    }
  ]
}
```

### <a name="example-error-response"></a>예제 오류 응답

다음은 오류 응답의 예입니다. 다른 오류 코드에 대 한 스키마는 동일 합니다.

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

문서 번역 및 클라이언트 라이브러리를 사용 하는 방법에 대 한 자세한 내용은 빠른 시작을 따르세요.

> [!div class="nextstepaction"]
> [문서 번역 시작](../get-started-with-document-translation.md)
