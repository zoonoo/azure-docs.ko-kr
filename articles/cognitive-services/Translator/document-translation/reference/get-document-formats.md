---
title: 문서 번역 가져오기 문서 형식 메서드
titleSuffix: Azure Cognitive Services
description: 문서 형식 가져오기 메서드는 지원 되는 문서 형식 목록을 반환 합니다.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 3cd74f70416d2049a0fb969371507c292dd47fdf
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613156"
---
# <a name="document-translation-get-document-formats"></a>문서 번역: 문서 형식 가져오기

문서 형식 가져오기 메서드는 문서 번역 서비스에서 지 원하는 문서 형식 목록을 반환 합니다. 이 목록에는 공용 파일 확장명 및 업로드 API를 사용 하는 경우 콘텐츠 형식이 포함 되어 있습니다.

## <a name="request-url"></a>요청 URL

다음에 `GET` 요청을 보냅니다.
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/documents/formats
```

[사용자 지정 도메인 이름을](../get-started-with-document-translation.md#find-your-custom-domain-name)찾는 방법에 대해 알아봅니다.

> [!IMPORTANT]
>
> * **문서 변환 서비스에 대 한 모든 API 요청에는 사용자 지정 도메인 끝점이 필요** 합니다.
> * 문서 변환에 대 한 HTTP 요청을 수행 하려면 Azure Portal 리소스 _키와 끝점_ 페이지 또는 전역 변환기 끝점 ()에 있는 끝점을 사용할 수 없습니다 `api.cognitive.microsofttranslator.com` .

## <a name="request-headers"></a>요청 헤더

요청 헤더는 다음과 같습니다.

|headers|Description|
|-----|-----|
|Ocp-Apim-Subscription-Key|필수 요청 헤더|

## <a name="response-status-codes"></a>응답 상태 코드

요청을 반환하는 가능한 HTTP 상태 코드는 다음과 같습니다.

|상태 코드|설명|
|-----|-----|
|200|OK. 지원 되는 문서 파일 형식 목록을 반환 합니다.|
|500|내부 서버 오류.|
|기타 상태 코드|<ul><li>너무 많은 요청</li><li>서버 임시 사용할 수 없음</li></ul>|

## <a name="file-format-response"></a>파일 형식 응답

### <a name="successful-fileformatlistresult-response"></a>FileFormatListResult 응답 성공

성공적인 응답에서 반환 되는 정보는 다음과 같습니다.

|이름|Type|Description|
|--- |--- |--- |
|값|FileFormat []|FileFormat []에는 아래 나열 된 세부 정보가 포함 되어 있습니다.|
|값. 형식|string[]|이 형식에 대해 지원 되는 콘텐츠 형식입니다.|
|fileExtensions|string[]|이 형식에 대해 지원 되는 파일 확장명입니다.|
|값 contentTypes|string[]|형식의 이름입니다.|
|값. 버전|String[]|지원 되는 버전|

### <a name="error-response"></a>오류 응답

|이름|Type|설명|
|--- |--- |--- |
 |code|문자열|상위 수준 오류 코드를 포함 하는 열거형입니다. 가능한 값은 다음과 같습니다.<ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>권한 없음</li></ul>|
|message|문자열|상위 수준 오류 메시지를 가져옵니다.|
|innerError|InnerErrorV2|Cognitive Services API 지침을 준수 하는 새로운 내부 오류 형식입니다. 필수 속성인 ErrorCode, message 및 optional 속성 target, details (키 값 쌍), 내부 오류 (중첩 될 수 있음)가 포함 되어 있습니다.|
|innerError|문자열|코드 오류 문자열을 가져옵니다.|
|innerError|문자열|상위 수준 오류 메시지를 가져옵니다.|

## <a name="examples"></a>예제

### <a name="example-successful-response"></a>성공적인 응답 예제
성공적인 응답의 예는 다음과 같습니다.

상태 코드: 200

```JSON
{
  "value": [
    {
      "format": "PlainText",
      "fileExtensions": [
        ".txt"
      ],
      "contentTypes": [
        "text/plain"
      ],
      "versions": []
    },
    {
      "format": "PortableDocumentFormat",
      "fileExtensions": [
        ".pdf"
      ],
      "contentTypes": [
        "application/pdf"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlPresentation",
      "fileExtensions": [
        ".pptx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.presentationml.presentation"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlSpreadsheet",
      "fileExtensions": [
        ".xlsx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
      ],
      "versions": []
    },
    {
      "format": "OutlookMailMessage",
      "fileExtensions": [
        ".msg"
      ],
      "contentTypes": [
        "application/vnd.ms-outlook"
      ],
      "versions": []
    },
    {
      "format": "HtmlFile",
      "fileExtensions": [
        ".html"
      ],
      "contentTypes": [
        "text/html"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlWord",
      "fileExtensions": [
        ".docx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.wordprocessingml.document"
      ],
      "versions": []
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