---
title: Translator Speech 서비스란?
titleSuffix: Azure Cognitive Services
description: Translator Speech API를 사용하여 음성을 음성으로 및 음성을 텍스트로 번역을 애플리케이션에 추가합니다.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: overview
ms.date: 3/5/2018
ms.author: v-jansko
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 24014bb06a779c214f18f966dfb1d26d61adee8d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "60827524"
---
# <a name="what-is-translator-speech-api"></a>Translator Speech API란?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Translator Speech API는 대상 OS 또는 개발 언어와 관계없이 다국어 음성 번역이 필요한 애플리케이션, 도구 또는 솔루션에 엔드투엔드 실시간 음성 번역을 추가하는 데 사용할 수 있습니다. API는 음성 대 음성 및 음성 대 텍스트 번역 모두에 사용될 수 있습니다.

Translator Text API는 사용자 개발 프로젝트에서 쉽게 사용할 수 있는 클라우드의 AI 알고리즘 및 기계 학습의 [Azure Cognitive Services API 컬렉션](https://docs.microsoft.com/azure/)에 속한 Azure 서비스입니다.

Translator Speech API를 사용하여 클라이언트 애플리케이션은 음성 오디오를 서비스로 스트리밍하고 텍스트 및 오디오 기반의 결과를 돌려받습니다. 여기에는 인식된 텍스트를 소스 언어로, 해당 번역을 대상 언어로 포함합니다. 텍스트 결과는 심층 신경망에 의해 작동하는 ASR(Automatic Speech Recognition)을 들어오는 오디오 스트리밍에 적용하여 생성합니다. 원시 ASR 출력은 TrueText라는 새 기술로 더욱 향상되어 사용자 의도를 더 밀접하게 반영합니다. 예를 들어 TrueText는 말더듬(으흠 및 콜록) 및 반복되는 단어를 제거하고, 적절한 구두점 및 대문자 사용을 복원합니다. 비속어를 제외하거나 마스킹하는 기능도 포함됩니다. 인식 및 번역 엔진은 특히 대화형 음성 처리를 학습합니다.

Translator Speech 서비스는 무음 감지 기능을 사용하여 발화의 마지막을 확인합니다. 음성 활동을 잠시 멈춘 뒤 서비스는 완성된 발언에 대한 최종 결과를 다시 스트리밍합니다. 서비스는 부분 결과를 돌려보내 진행 중인 발언에 대한 중간 인식과 번역을 제공할 수도 있습니다.

음성 대 음성 번역의 경우 서비스는 대상 언어로 구어체 텍스트에서 음성을 합성하는 기능(텍스트를 음성으로 변환)을 제공합니다. 텍스트를 음성으로 변환하는 오디오는 클라이언트에서 지정한 형식으로 생성됩니다. WAV 및 MP3 형식을 사용할 수 있습니다.

Translator Speech API는 WebSocket 프로토콜을 사용하여 클라이언트 및 서버 간 전이중 통신 채널을 제공합니다.

## <a name="about-microsoft-translator"></a>Microsoft Translator 정보
Microsoft Translator는 클라우드 기반 기계 번역 서비스입니다. 이 서비스의 핵심에는 다양한 Microsoft 제품 및 서비스를 제공하고 전세계 수천 개 비즈니스가 해당 애플리케이션 및 워크플로에서 사용하면서 해당 콘텐츠가 전세계 고객에게 도달하게 하는 Translator Text API 및 [Translator Speech API](https://www.microsoft.com/en-us/translator/translatorapi.aspx)가 있습니다.

[Microsoft Translator 서비스](https://www.microsoft.com/en-us/translator/home.aspx)를 자세히 알아보기

## <a name="microsoft-translator-neural-machine-translation-nmt"></a>Microsoft Translator NMT(신경망 기계 번역)
Translator Speech API는 레거시 통계 기계 번역(SMT) 및 최신 신경망 기계 번역(NMT) 둘 다 사용하여 번역을 제공합니다.

통계 기계 번역은 성능 개선 면에서 안정기에 접어들었습니다. 번역 품질은 SMT를 사용한 제네릭 시스템에 대해 더 이상 유의미하게 개선되지 않습니다. 새로운 AI 기반 번역 기술이 신경망(NN)에 기반해 탄력이 붙고 있습니다.

NMT는 원시 번역 품질 채점 관점에서뿐만 아니라 SMT보다 더 유창하고 더 인간적인 사운드에서도 더 나은 번역을 제공합니다.
이 유동성의 핵심 이유는 NMT가 단어를 번역하는 데 문장의 전체 컨텍스트를 사용한다는 것입니다. SMT는 각 단어 앞뒤의 몇 개 단어로 된 직접 컨텍스트만 사용합니다.

NMT 모델은 API의 중심에 있으며 최종 사용자에게는 보이지 않습니다. 두드러진 유일한 차이는 다음과 같습니다.
* 특히 중국어, 일본어 및 아랍어 같은 언어에 대해 개선된 번역 품질
* (Microsoft Translator Text API용) 기존 Hub 사용자 지정 기능과 비호환성

지원되는 모든 음성 번역 언어는 NMT에서 제공합니다. 따라서 모든 음성 대 음성 번역은 NMT를 사용합니다.

음성 대 텍스트 번역은 언어 쌍에 따라 NMT 및 SMT 조합을 사용할 수 있습니다. 대상 언어가 NMT에 의해 지원되는 경우 전체 번역은 NMT 기반입니다. 대상 언어가 NMT에 의해 지원되지 않는 경우 두 언어 사이에 영어를 “피벗”으로 사용하여 NMT 및 SMT 혼합으로 번역됩니다.

[Microsoft.com](https://www.microsoft.com/en-us/translator/languages.aspx)에서 지원되는 언어를 볼 수 있습니다.

[NMT 작동 방법](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)에 대해 자세히 알아보기

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [등록](translator-speech-how-to-signup.md)

> [!div class="nextstepaction"]
> [코딩 시작](quickstarts/csharp.md)

## <a name="see-also"></a>참고 항목
- [Cognitive Services 설명서 페이지](https://docs.microsoft.com/azure/)
- [Cognitive Services 제품 페이지](https://azure.microsoft.com/services/cognitive-services/)
- [솔루션 및 가격 책정 정보](https://www.microsoft.com/en-us/translator/home.aspx)
