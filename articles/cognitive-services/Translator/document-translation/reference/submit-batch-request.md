---
title: 문서 번역 제출 일괄 처리 요청
titleSuffix: Azure Cognitive Services
description: 문서 번역 서비스에 문서 번역 요청을 제출 합니다.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 803a0b9f4496a3d785aa6f22833fee4ae6eca6e0
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613029"
---
# <a name="document-translation-submit-batch-request"></a>문서 번역: 일괄 처리 요청 제출

이 API를 사용 하 여 대량 (일괄 처리) 번역 요청을 문서 번역 서비스에 제출 합니다. 각 요청은 여러 문서를 포함할 수 있으며 각 문서에 대 한 원본 및 대상 컨테이너를 포함 해야 합니다.

접두사 및 접미사 필터 (제공 된 경우)를 사용 하 여 폴더를 필터링 합니다. 접두사는 컨테이너 이름 뒤의 하위 경로에 적용 됩니다.

용어집/변환 메모리는 요청에 포함 될 수 있으며 문서가 번역 될 때 서비스에서 적용 됩니다.

용어 설명에 잘못 되었거나 번역 중에 연결할 수 없는 경우 문서 상태에 오류가 표시 됩니다. 이름이 같은 파일이 대상에 이미 있으면 해당 파일을 덮어씁니다. 각 대상 언어에 대 한 targetUrl는 고유 해야 합니다.

## <a name="request-url"></a>요청 URL

다음에 `POST` 요청을 보냅니다.
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

[사용자 지정 도메인 이름을](../get-started-with-document-translation.md#find-your-custom-domain-name)찾는 방법에 대해 알아봅니다.

> [!IMPORTANT]
>
> * **문서 변환 서비스에 대 한 모든 API 요청에는 사용자 지정 도메인 끝점이 필요** 합니다.
> * 문서 변환에 대 한 HTTP 요청을 수행 하려면 Azure Portal 리소스 _키와 끝점_ 페이지 또는 전역 변환기 끝점 ()에 있는 끝점을 사용할 수 없습니다 `api.cognitive.microsofttranslator.com` .

## <a name="request-headers"></a>요청 헤더

요청 헤더는 다음과 같습니다.

|headers|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|필수 요청 헤더|

## <a name="request-body-batch-submission-request"></a>요청 본문: 일괄 처리 전송 요청

|이름|Type|설명|
|--- |--- |--- |
|입력|BatchRequest []|아래 나열 된 BatchRequest. 문서 또는 문서를 포함 하는 폴더의 입력 목록입니다. 미디어 유형: "application/json", "text/json", "application/* + json".|

### <a name="inputs"></a>입력

입력 일괄 처리 변환 요청에 대 한 정의입니다.

|이름|Type|필수|설명|
|--- |--- |--- |--- |
|source|SourceInput []|True|입력. 소스는 아래에 나열 되어 있습니다. 입력 문서의 원본입니다.|
|storageType|StorageInputType []|True|입력. storageType 아래에 나열 되어 있습니다. 입력 문서 원본 문자열의 저장소 유형입니다.|
|대상|TargetInput []|True|입력. 대상 아래에 나열 되어 있습니다. 출력의 대상 위치입니다.|

**입력. 원본**

입력 문서의 원본입니다.

|이름|Type|필수|설명|
|--- |--- |--- |--- |
|filter|DocumentFilter []|False|DocumentFilter [] 아래에 나열 되어 있습니다.|
|필터. 접두사|문자열|False|변환을 위해 소스 경로에서 문서를 필터링 하는 대/소문자를 구분 하는 접두사 문자열입니다. 예를 들어 Azure storage blob Uri를 사용 하는 경우 접두사를 사용 하 여 번역을 위해 하위 폴더를 제한 합니다.|
|필터. 접미사|문자열|False|변환을 위해 소스 경로에서 문서를 필터링 하는 대/소문자를 구분 하는 접미사 문자열입니다. 일반적으로 파일 확장명에 사용 됩니다.|
|언어|문자열|False|언어 코드 지정 하지 않을 경우 문서에서 자동 검색을 수행 합니다.|
|sourceUrl|문자열|True|문서를 포함 하는 폴더/컨테이너 또는 단일 파일의 위치입니다.|
|storag|Storag|False|아래 나열 된 StorageSource|
|storageSource AzureBlob|문자열|False||

**입력. storageType**

입력 문서 원본 문자열의 저장소 유형입니다.

|이름|Type|
|--- |--- |
|파일|문자열|
|폴더|문자열|

**입력. 대상**

완성 된 번역 된 문서에 대 한 대상입니다.

|이름|Type|필수|설명|
|--- |--- |--- |--- |
|category|문자열|False|번역 요청에 대 한 범주/사용자 지정 시스템입니다.|
|용어집|용어집 []|False|용어는 아래에 나열 되어 있습니다. 용어집 목록입니다.|
|용어집. 형식|문자열|False|형식.|
|glossaryUrl|문자열|True (용어집을 사용 하는 경우)|용어집의 위치입니다. Format 매개 변수가 제공 되지 않은 경우 파일 확장명을 사용 하 여 서식 지정을 추출 합니다. 번역 언어 쌍이 용어집에 없으면 적용 되지 않습니다.|
|용어집. storag|Storag|False|위에 나열 된 StorageSource|
|targetUrl|문자열|True|문서를 포함 하는 폴더/컨테이너의 위치입니다.|
|언어|문자열|True|두 문자 대상 언어 코드입니다. [언어 코드의 목록을](../../language-support.md)참조 하세요.|
|storag|StorageSource]|False|위에 나열 된 StorageSource]입니다.|
|버전|문자열|False|버전.|

## <a name="example-request"></a>요청 예

일괄 처리 요청의 예는 다음과 같습니다.

**컨테이너의 모든 문서 변환**

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**컨테이너의 모든 문서 번역 용어집 적용**

특정 blob/문서 (컨테이너가 아닌)에 대해 SAS 토큰 & 용어집 URL을 만들었는지 확인 합니다.

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
     "glossaries": [
                        {
                            "glossaryUrl": https://my.blob.core.windows.net/glossaries/en-fr.xlf?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=BsciG3NWoOoRjOYesTaUmxlXzyjsX4AgVkt2AsxJ9to%3D,
                            "format": "xliff",
                            "version": "1.2"
                        }
                    ]

                }
            ]
        }
    ]
}
```

**컨테이너의 특정 폴더 변환**

SAS 토큰이 아직 컨테이너에 대 한 것 이지만 폴더 이름 (대 소문자 구분)을 필터의 접두사로 지정 했는지 확인 합니다.

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D,
                "filter": {
                    "prefix": "MyFolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**컨테이너의 특정 문서 변환**

* "StorageType": "File"을 지정 했는지 확인 합니다.
* 특정 blob/문서에 대 한 원본 URL & SAS 토큰을 생성 했는지 확인 합니다 (컨테이너가 아님).
* 대상 파일 이름을 대상 URL의 일부로 지정 했는지 확인 합니다. SAS 토큰은 아직 컨테이너에 대 한 것입니다.
* 아래 샘플 요청에서는 두 개의 대상 언어로 번역 된 단일 문서를 보여 줍니다.

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "es"
                },
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "de"
                }
            ]
        }
    ]
}
```

## <a name="response-status-codes"></a>응답 상태 코드

요청을 반환하는 가능한 HTTP 상태 코드는 다음과 같습니다.

|상태 코드|Description|
|--- |--- |
|202|수락됨 성공적인 요청 및 일괄 처리 요청은 서비스에 의해 생성 됩니다. 헤더 Operation-Location는 HeadersOperation-Location: string 작업을 사용 하 여 상태 url을 표시 합니다.|
|400|잘못된 요청. 잘못된 요청입니다. 입력 매개 변수를 확인 하십시오.|
|401|권한이 없습니다. 자격 증명을 확인 하세요.|
|429|요청 빈도가 너무 높습니다.|
|500|내부 서버 오류.|
|503|서비스를 현재 사용할 수 없습니다.  나중에 다시 시도하세요.|
|기타 상태 코드|<ul><li>너무 많은 요청</li><li>서버 임시 사용할 수 없음</li></ul>|

## <a name="error-response"></a>오류 응답

|이름|Type|설명|
|--- |--- |--- |
|code|문자열|상위 수준 오류 코드를 포함 하는 열거형입니다. 가능한 값은 다음과 같습니다.<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>권한 없음</li></ul>|
|message|문자열|상위 수준 오류 메시지를 가져옵니다.|
|innerError|InnerErrorV2|Cognitive Services API 지침을 준수 하는 새로운 내부 오류 형식입니다. 필수 속성인 ErrorCode, message 및 optional 속성 target, details (키 값 쌍), 내부 오류 (중첩 될 수 있음)가 포함 되어 있습니다.|
|내부. Errorcode|문자열|코드 오류 문자열을 가져옵니다.|
|innerError|문자열|상위 수준 오류 메시지를 가져옵니다.|

## <a name="examples"></a>예제

### <a name="example-successful-response"></a>성공적인 응답 예제

성공적인 응답에서 반환 되는 정보는 다음과 같습니다.

POST 메서드의 응답 헤더 Operation-Location URL 값에서 작업 ID를 찾을 수 있습니다. URL의 마지막 매개 변수는 작업의 작업 ID ("/operation/" 다음에 나오는 문자열)입니다.

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>예제 오류 응답

```JSON
{
  "error": {
    "code": "ServiceUnavailable",
    "message": "Service is temporary unavailable",
    "innerError": {
      "code": "ServiceTemporaryUnavailable",
      "message": "Service is currently unavailable.  Please try again later"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

문서 번역 및 클라이언트 라이브러리를 사용 하는 방법에 대 한 자세한 내용은 빠른 시작을 따르세요.

> [!div class="nextstepaction"]
> [문서 번역 시작](../get-started-with-document-translation.md)
