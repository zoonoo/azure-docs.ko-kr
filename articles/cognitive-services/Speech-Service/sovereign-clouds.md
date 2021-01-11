---
title: 소 버린 클라우드-음성 서비스
titleSuffix: Azure Cognitive Services
description: 소 버린 클라우드를 사용 하는 방법 알아보기
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.custom: references_regions
ms.date: 01/07/2021
ms.author: alexeyo
ms.openlocfilehash: f30b1f0f14bba54b8b4fcd7c5190f3c533f199a6
ms.sourcegitcommit: 63caac7871df9d999ca5a5f1b8c036ae7014231c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98061755"
---
# <a name="speech-services-in-sovereign-clouds"></a>소 버린 클라우드의 음성 서비스

## <a name="azure-government-united-states"></a>Azure Government (미국)

미국 정부 기관 및 파트너 에게만 사용할 수 있습니다. [여기 및](../../azure-government/documentation-government-welcome.md) 여기 Azure Government에 대 한 자세한 내용을 참조 하세요 [.](../../azure-government/compare-azure-government-global-azure.md)

- **Azure Portal:**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **영역만**
  - US Gov 애리조나
  - US Gov 버지니아
- **사용 가능한 가격 책정 계층:**
  - Free (F0) 및 Standard (S0). 자세한 내용은 [여기](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) 를 참조 하세요.
- **지원 되는 기능:**
  - 음성 텍스트 변환
    - 사용자 지정 음성 (AM (음향 모델) 및 LM (언어 모델) 적응)
      - [Speech Studio](https://speech.azure.us/)
  - 텍스트 음성 변환
    - 표준 음성
    - 신경망
  - 음성 변환기
- **지원되지 않는 기능:**
  - Custom Voice
- **지원 되는 언어:**
  - [여기](language-support.md) 에서 지원 되는 언어 목록을 참조 하세요.

### <a name="endpoint-information"></a>엔드포인트 정보

이 섹션에는 speech [SDK](speech-sdk.md)사용, 음성 [텍스트 REST API](rest-speech-to-text.md)및 [텍스트 음성 변환 REST API](rest-text-to-speech.md)에 대 한 음성 서비스 끝점 정보가 포함 되어 있습니다.

#### <a name="speech-services-rest-api"></a>Speech Services REST API

음성 서비스 REST API 끝점 Azure Government의 형식은 다음과 같습니다.

|  REST API 형식/작업 | 끝점 형식 |
|--|--|
| 액세스 토큰 | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [음성 텍스트 REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [짧은 오디오에 대 한 음성 텍스트 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [Text-to-Speech REST API](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

`<REGION_IDENTIFIER>`이 테이블에서 구독의 지역과 일치 하는 식별자로 대체 합니다.

|                     | 영역 식별자 |
|--|--|
| **US Gov 애리조나**  | `usgovarizona` |
| **US Gov 버지니아** | `usgovvirginia` |

#### <a name="speech-sdk"></a>Speech SDK

소 버린 클라우드의 Speech SDK의 경우 `SpeechConfig` `--host` [음성 CLI](spx-overview.md)의 클래스 또는 옵션의 "호스트에서" 인스턴스화를 사용 해야 합니다. ("끝점에서" 인스턴스화 `--endpoint` 를 사용할 수도 있습니다. Speech CLI 옵션).

`SpeechConfig` 클래스는 다음과 같이 인스턴스화해야 합니다.
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
다음과 같이 음성 CLI를 사용 해야 합니다 (옵션에 유의 `--host` ).
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
`subscriptionKey`음성 리소스 키로 대체 합니다. `usGovHost`이 테이블에서 필요한 서비스 제공 및 구독의 지역과 일치 하는 식으로 대체 합니다.

|  지역/서비스 제공 | 호스트 식 |
|--|--|
| **US Gov 애리조나** | |
| 음성 텍스트 변환 | `wss://usgovarizona.stt.speech.azure.us` |
| 텍스트 음성 변환 | `https://usgovarizona.tts.speech.azure.us` |
| **US Gov 버지니아** | |
| 음성 텍스트 변환 | `wss://usgovvirginia.stt.speech.azure.us` |
| 텍스트 음성 변환 | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure 중국

중국의 비즈니스 존재를 가진 조직에서 사용할 수 있습니다. Azure 중국에 대 한 자세한 내용은 [여기를](/azure/china/overview-operations) 참조 하세요. 


- **Azure Portal:**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **영역만**
  - 중국 동부 2
- **사용 가능한 가격 책정 계층:**
  - Free (F0) 및 Standard (S0). 자세한 내용은 [여기](https://www.azure.cn/pricing/details/cognitive-services/index.html) 를 참조 하세요.
- **지원 되는 기능:**
  - 음성 텍스트 변환
    - 사용자 지정 음성 (AM (음향 모델) 및 LM (언어 모델) 적응)
      - [Speech Studio](https://speech.azure.cn/)
  - 텍스트 음성 변환
    - 표준 음성
    - 신경망
  - 음성 변환기
- **지원되지 않는 기능:**
  - Custom Voice
- **지원 되는 언어:**
  - [여기](language-support.md) 에서 지원 되는 언어 목록을 참조 하세요.

### <a name="endpoint-information"></a>엔드포인트 정보

이 섹션에는 speech [SDK](speech-sdk.md)사용, 음성 [텍스트 REST API](rest-speech-to-text.md)및 [텍스트 음성 변환 REST API](rest-text-to-speech.md)에 대 한 음성 서비스 끝점 정보가 포함 되어 있습니다.

#### <a name="speech-services-rest-api"></a>Speech Services REST API

Azure 중국의 음성 서비스 REST API 끝점은 다음과 같은 형식입니다.

|  REST API 형식/작업 | 끝점 형식 |
|--|--|
| 액세스 토큰 | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [음성 텍스트 REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [짧은 오디오에 대 한 음성 텍스트 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [Text-to-Speech REST API](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

`<REGION_IDENTIFIER>`이 테이블에서 구독의 지역과 일치 하는 식별자로 대체 합니다.

|                     | 영역 식별자 |
|--|--|
| **중국 동부 2**  | `chinaeast2` |

#### <a name="speech-sdk"></a>Speech SDK

소 버린 클라우드의 Speech SDK의 경우 `SpeechConfig` `--host` [음성 CLI](spx-overview.md)의 클래스 또는 옵션의 "호스트에서" 인스턴스화를 사용 해야 합니다. ("끝점에서" 인스턴스화 `--endpoint` 를 사용할 수도 있습니다. Speech CLI 옵션).

`SpeechConfig` 클래스는 다음과 같이 인스턴스화해야 합니다.
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
다음과 같이 음성 CLI를 사용 해야 합니다 (옵션에 유의 `--host` ).
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
`subscriptionKey`음성 리소스 키로 대체 합니다. `azCnHost`이 테이블에서 필요한 서비스 제공 및 구독의 지역과 일치 하는 식으로 대체 합니다.

|  지역/서비스 제공 | 호스트 식 |
|--|--|
| **중국 동부 2** | |
| 음성 텍스트 변환 | `wss://chinaeast2.stt.speech.azure.cn` |
| 텍스트 음성 변환 | `https://chinaeast2.tts.speech.azure.cn` |