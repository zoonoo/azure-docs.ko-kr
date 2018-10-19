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
ms.openlocfilehash: 9282d8af30cbfb3346394bcd71510faf8d8c8a21
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129389"
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
    "code":403000,
    "message":"The subscription has exceeded its free quota."
    }
}
```
