---
title: Translator Text API V3.0 참조
titlesuffix: Azure Cognitive Services
description: Translator Text API V3.0에 대한 참조 문서입니다.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 243ee16f8de8add8283581c8c03a37594797864b
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430037"
---
# <a name="translator-text-api-v30"></a>Translator Text API v3.0

## <a name="whats-new"></a>새로운 기능

Translator Text API 버전 3은 최신 JSON 기반 Web API를 제공합니다. 기존 기능을 더 적은 개수의 작업으로 통합하여 유용성 및 성능을 개선하고 새로운 기능을 제공합니다.

 * 특정 언어의 텍스트를 한 스크립트에서 다른 스크립트로 음차하여 변환
 * 단일 요청에서 여러 언어로 번역
 * 단일 요청의 언어 감지, 번역, 음차
 * 용어의 대체 번역을 조회하고, 컨텍스트에서 사용된 용어를 보여 주는 예와 역방향 번역을 찾기 위한 사전
 * 추가 정보를 제공하는 언어 감지 결과

## <a name="base-urls"></a>기준 URL

Text API v3.0은 다음 클라우드에서 사용할 수 있습니다.

| 설명 | 지역 | 기준 URL                                        |
|-------------|--------|-------------------------------------------------|
| Azure       | 전역 | api.cognitive.microsofttranslator.com           |


## <a name="authentication"></a>인증

Microsoft Cognitive Services의 Translator Text API를 구독하고 구독 키(Azure Portal에서 확인할 수 있음)를 사용하여 인증합니다. 

가장 간단한 방법은 요청 헤더 `Ocp-Apim-Subscription-Key`를 사용하여 Translator 서비스에 Azure 비밀 키를 전달하는 것입니다.

또는 비밀 키를 사용하여 토큰 서비스에서 인증 토큰을 받을 수 있습니다. 그런 다음, `Authorization` 요청 헤더를 사용하여 Translator 서비스에 인증 토큰을 전달합니다. 인증 토큰을 받으려면 다음 URL에 대해 `POST` 요청을 수행합니다.

| Environment     | 인증 서비스 URL                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

다음은 비밀 키가 제공된 경우 토큰을 얻기 위한 요청 예입니다.

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'
// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

요청에 성공하면 인코드된 액세스 토큰이 응답 본문에 일반 텍스트로 반환됩니다. 유효한 토큰이 인증의 전달자 토큰으로 Translator 서비스에 전달됩니다.

```
Authorization: Bearer <Base64-access_token>
```

인증 토큰은 10분 동안 유효합니다. Translator API를 여러 번 호출할 때는 토큰을 다시 사용해야 합니다. 그러나 프로그램이 확장된 기간 동안 Translator API에 대한 요청을 수행하는 경우 프로그램은 일정한 간격(예: 8분마다)으로 새 액세스 토큰을 요청해야 합니다.

요약하면, Translator API에 대한 클라이언트 요청에는 다음 표에 있는 인증 헤더 중 하나가 포함됩니다.

<table width="100%">
  <th width="30%">헤더</th>
  <th>설명</th>
  <tr>
    <td>Ocp-Apim-Subscription-Key</td>
    <td>*비밀 키를 전달하는 경우 Cognitive Services 구독에 사용합니다*.<br/>값은 Translator Text API 구독에 대한 Azure 비밀 키입니다.</td>
  </tr>
  <tr>
    <td>권한 부여</td>
    <td>*인증 토큰을 전달하는 경우 Cognitive Services 구독에 사용합니다*.<br/>값은 전달자 토큰인 `Bearer <token>`입니다.</td>
  </tr>
</table> 

## <a name="errors"></a>오류

표준 오류 응답은 이름/값 쌍이 포함된 JSON 개체 `error`입니다. 값도 다음 속성을 가진 JSON 개체입니다.

  * `code`: 서버에서 정의된 오류 코드입니다.

  * `message`: 사용자가 읽을 수 있는 오류 표시를 제공하는 문자열입니다.

예를 들어 평가판 구독이 있는 고객은 무료 할당량이 모두 사용된 후 다음 오류를 받게 됩니다.

```
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
오류 코드는 오류를 더 범주화하도록 뒤에 3자리 숫자가 오는 3자리 HTTP 상태 코드로 결합된 6자리 숫자입니다. 일반적인 오류 코드는 다음과 같습니다.

| 코드 | 설명 |
|:----|:-----|
| 400000| 요청 입력 중 하나가 올바르지 않습니다.|
| 400001| "scope" 매개 변수가 올바르지 않습니다.|
| 400002| "category" 매개 변수가 올바르지 않습니다.|
| 400003| 언어 지정자 누락되었거나 올바르지 않습니다.|
| 400004| 대상 스크립트 지정자("To script")가 누락되었거나 올바르지 않습니다.|
| 400005| 입력 텍스트가 누락되었거나 올바르지 않습니다.|
| 400006| 언어 및 스크립트의 조합이 올바르지 않습니다.|
| 400018| 원본 스크립트 지정자("From script")가 누락되었거나 올바르지 않습니다.|
| 400019| 지정된 언어 중 하나가 지원되지 않습니다.|
| 400020| 입력 텍스트의 배열에서 요소 중 하나가 올바르지 않습니다.|
| 400021| API 버전 매개 변수가 누락되었거나 올바르지 않습니다.|
| 400023| 지정된 언어 쌍 중 하나가 올바르지 않습니다.|
| 400035| 원본 언어("From" 필드)가 올바르지 않습니다.|
| 400036| 대상 언어("To" 필드)가 누락되었거나 올바르지 않습니다.|
| 400042| 지정된 옵션("Options" 필드) 중 하나가 올바르지 않습니다.|
| 400043| 클라이언트 추적 ID(ClientTraceId 필드 또는 X-ClientTranceId 헤더)가 누락되었거나 올바르지 않습니다.|
| 400050| 입력 텍스트가 너무 깁니다.|
| 400064| "translation" 매개 변수가 누락되었거나 올바르지 않습니다.|
| 400070| 대상 스크립트(ToScript 매개 변수)의 수가 대상 언어(To parameter)의 수와 일치하지 않습니다.|
| 400071| 값이 TextType에 적합하지 않습니다.|
| 400072| 입력 텍스트의 배열에 요소가 너무 많습니다.|
| 400073| 스크립트 매개 변수가 올바르지 않습니다.|
| 400074| 요청 분문이 유효한 JSON이 아닙니다.|
| 400075| 언어 쌍 및 범주 조합이 올바르지 않습니다.|
| 400077| 최대 요청 크기를 초과했습니다.|
| 400079| from과 to 언어 간의 변환에 대해 요청된 사용자 지정 시스템이 존재하지 않습니다.|
| 401000| 자격 증명이 누락되었거나 올바르지 않으므로 요청에 권한이 없습니다.|
| 401015| "제공된 자격 증명은 Speech API에 대한 것입니다. 이 요청에 Text API에 대한 자격 증명이 필요합니다. Translator Text API에 대한 구독을 사용하세요."|
| 403000| 작업이 허용되지 않습니다.|
| 403001| 구독이 무료 할당량을 초과했기 때문에 작업이 허용되지 않습니다.|
| 405000| 요청 메서드가 요청된 리소스에 대해 지원되지 않습니다.|
| 415000| Content-Type 헤더가 누락되었거나 올바르지 않습니다.|
| 429000, 429001, 429002| 클라이언트가 너무 많은 요청을 보냈기 때문에 서버에서 요청을 거부했습니다. 제한을 방지하도록 요청의 빈도를 줄입니다.|
| 500000| 예기치 않은 오류가 발생했습니다. 이 오류가 계속 발생하는 경우 오류의 날짜/시간, 응답 헤더 X-RequestId의 요청 식별자 및 요청 헤더 X-ClientTraceId의 클라이언트 식별자를 사용하여 보고합니다.|
| 503000| 서비스를 일시적으로 사용할 수 없습니다. 다시 시도하세요. 이 오류가 계속 발생하는 경우 오류의 날짜/시간, 응답 헤더 X-RequestId의 요청 식별자 및 요청 헤더 X-ClientTraceId의 클라이언트 식별자를 사용하여 보고합니다.|

