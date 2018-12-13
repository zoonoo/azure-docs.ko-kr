---
title: 음성 번역 정보
titlesuffix: Azure Cognitive Services
description: Speech Translation의 기능 개요
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 04/28/2018
ms.author: erhopf
ms.openlocfilehash: af363a45ab113d89c7aad7a38c6e10d335bc6035
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469146"
---
# <a name="about-the-speech-translation-api"></a>Speech Translation API 정보

Speech Service API를 사용하면 음성에 대한 실시간 종단 간 다중 언어 번역을 애플리케이션, 도구 및 디바이스에 추가할 수 있습니다. 같은 API를 음성 대 음성 및 음성 대 텍스트 번역 모두에 사용될 수 있습니다.

클라이언트 응용 프로그램에서 Translator Speech API를 통해 음성 오디오를 서비스로 스트림하고 결과 스트림을 다시 받습니다. 이러한 결과에는 원본 언어의 인식된 텍스트와 대상 언어의 해당 번역이 포함되어 있습니다. 발언이 완료될 때까지 중간 번역이 제공될 수 있으며, 발언이 완료되면 최종 번역이 제공됩니다.

필요에 따라 진정한 음성 대 음성 번역이 가능해지도록 최종 번역의 합성된 오디오 버전이 준비될 수 있습니다.

Speech Translation API는 WebSocket 프로토콜을 사용하여 클라이언트 및 서버 간 전이중 통신 채널을 제공합니다. 그렇지만 WebSocket을 처리할 필요는 없습니다. Speech SDK에서 자동으로 처리하기 때문입니다.

Speech Translation API에서는 다양한 Microsoft 제품 및 서비스를 지원하는 동일한 기술을 사용합니다. 전 세계의 수천 여 기업에서 응용 프로그램과 워크플로에 이미 이 서비스를 사용하고 있습니다.

## <a name="about-the-technology"></a>기술 정보

Microsoft의 번역 엔진의 기반에는 SMT(통계적 기계 번역)와 NMT(신경망 기계 번역)의 두 방식이 있습니다. 신경망을 사용하는 인공 지능 방식인 후자가 기계 번역에 대한 좀 더 최신의 접근 방식입니다. NMT는 단지 정확도만 더 나은 것이 아니라 유연성과 자연스러운 측면에서도 더 나은 번역을 제공합니다. 이렇게 유연한 번역이 제공될 수 있는 핵심 이유는 NMT가 단어를 번역하는 데 문장의 전체 컨텍스트를 사용한다는 것입니다.

현재 Microsoft는 가장 인기 있는 언어에 대해 NMT로 마이그레이션했으며 덜 자주 사용되는 언어에 대해서만 SMT를 사용하고 있습니다. [음성 대 음성 번역에 사용할 수 있는 모든 언어](language-support.md#speech-translation)는 NMT를 통해 지원됩니다. 음성 대 텍스트 번역은 언어 쌍에 따라 SMT 또는 NMT를 사용할 수 있습니다. 대상 언어가 NMT에 의해 지원되는 경우 전체 번역은 NMT 기반입니다. 대상 언어가 NMT에 의해 지원되지 않는 경우 두 언어 사이에 영어를 “피벗”으로 사용하여 NMT 및 SMT 혼합으로 번역됩니다.

두 모델 간의 차이점은 번역 엔진 내부에 있습니다. 최종 사용자는 특히 중국어, 일본어 및 아랍어 같은 언어에 대해서만 개선된 번역 품질을 느끼고 있습니다.

> [!NOTE]
> Microsoft 번역 엔진의 기반 기술에 대해 더 자세히 알아보고 싶나요? [기계 번역](https://www.microsoft.com/en-us/translator/mt.aspx)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
* [C#에서 음성을 번역하는 방법 알아보기](how-to-translate-speech-csharp.md)
* [C++에서 음성을 번역하는 방법 알아보기](how-to-translate-speech-cpp.md)
* [Java에서 음성을 번역하는 방법 알아보기](how-to-translate-speech-java.md)
