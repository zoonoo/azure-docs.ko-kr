---
title: 합성 음성 기술의 책임있는 배포를 위한 가이드라인
titleSuffix: Azure Cognitive Services
description: 합성 음성 기술을 사용하기 위한 Microsoft의 일반적인 디자인 지침입니다. 이들은 Microsoft가 음성 재능, 소비자 뿐만 아니라 음성 장애가있는 개인과 함께 수행 한 연구에서 개발되어 합성 음성의 책임있는 개발을 안내합니다.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836775"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>합성 음성 기술의 책임있는 배포를 위한 가이드라인
합성 음성 기술을 사용하기 위한 Microsoft의 일반적인 디자인 지침은 다음과 같습니다. 이들은 Microsoft가 음성 재능, 소비자 및 음성 장애가있는 개인과 함께 수행 한 연구에서 개발되어 합성 음성의 책임있는 개발을 안내합니다.

## <a name="general-considerations"></a>일반적인 고려 사항
합성 음성 기술을 배포하는 경우 대부분의 시나리오에 다음 지침이 적용됩니다.

### <a name="disclose-when-the-voice-is-synthetic"></a>음성이 합성인 경우 공개
음성이 생성된 음성을 공개하면 기만으로 인한 유해한 결과의 위험을 최소화할 뿐만 아니라 음성을 전달하는 조직에 대한 신뢰도 높아진다. [을 공개하는 방법에](concepts-disclosure-guidelines.md)대해 자세히 알아봅니다.

### <a name="select-appropriate-voice-types-for-your-scenario"></a>시나리오에 적합한 음성 유형 선택
사용의 맥락과 합성 음성 사용과 관련된 잠재적 인 해를 신중하게 고려하십시오. 예를 들어, 고충실도 합성 음성은 개인 메시징, 금융 거래 또는 인간의 적응성 이나 공감이 필요한 복잡한 상황과 같은 고위험 시나리오에서 적절하지 않을 수 있습니다. 사용자는 음성 유형에 대해 서로 다른 기대치를 가질 수도 있습니다. 예를 들어, 합성 음성으로 읽는 민감한 뉴스를 들을 때 일부 사용자는 뉴스를 더 공감하고 인간과 같은 읽기를 선호하는 반면, 다른 사용자는 더 모노톤, 편견없는 음성을 선호합니다. 사용자 기본 설정을 더 잘 이해하기 위해 응용 프로그램을 테스트하는 것이 좋습니다.

### <a name="be-transparent-about-capabilities-and-limitations"></a>기능 및 제한 사항에 대해 투명하게
사용자는 고충실도 합성 음성 에이전트와 상호 작용할 때 더 높은 기대치를 가질 가능성이 높습니다. 따라서 시스템 기능이 이러한 기대치를 충족하지 못하면 신뢰가 저하될 수 있으며 불쾌한 경험이나 유해한 경험이 발생할 수 있습니다.

### <a name="provide-optional-human-support"></a>선택적 인간 지원 제공
모호한 트랜잭션 시나리오(예: 콜 지원 센터)에서 사용자는 항상 컴퓨터 에이전트가 자신의 요청에 적절하게 응답하도록 신뢰하지는 않습니다. 이러한 상황에서는 시스템의 음성 또는 기능의 현실적인 품질에 관계없이 인간의 지원이 필요할 수 있습니다.

## <a name="considerations-for-voice-talent"></a>음성 재능에 대한 고려 사항
성우와 같은 성우와 협력하여 합성 음성을 만들 때 아래 지침이 적용됩니다.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>음성 재능으로부터 의미 있는 동의를 얻음
음성 재능은 음성 글꼴(사용 방법 및 위치)을 제어하고 언제든지 보상을 받을 수 있기를 기대합니다. 따라서 시스템 소유자는 음성 재능으로부터 명시적인 서면 허가를 받아야 하며 사용 사례, 사용 기간, 보상 등과 같은 명확한 계약 사양을 가져야 합니다. 일부 음성 재능은 기술의 잠재적인 악의적인 사용을 인식하지 못하며 시스템 소유자가 기술의 기능에 대해 교육해야 합니다. 음성 재능과 동의에 대한 자세한 내용은 [음성 재능에 대한 공개를](https://aka.ms/disclosure-voice-talent)참조하십시오.


## <a name="considerations-for-those-with-speech-disorders"></a>음성 장애가 있는 사람들을 위한 고려 사항
음성 장애가 있는 개인과 협력하여 합성 음성 기술을 만들거나 배포하는 경우 다음 지침이 적용됩니다.

### <a name="provide-guidelines-to-establish-contracts"></a>계약 수립을 위한 가이드라인 제공
말하기에 도움을 위해 합성 음성을 사용하는 개인과 계약을 수립하기 위한 지침을 제공합니다. 계약은 음성, 사용 기간, 소유권 이전 기준, 음성 글꼴 삭제 절차 및 무단 액세스를 방지하는 방법을 소유한 당사자를 지정하는 것을 고려해야 합니다. 또한, 그 사람이 허락을 받은 경우 사망 후 음성 글꼴 소유권을 가족 구성원에게 계약상 양도할 수 있습니다.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>음성 패턴의 불일치 를 고려
음성 글꼴을 녹음하는 음성 장애가 있는 개인의 경우 음성 패턴의 불일치(특정 단어를 발음하는 슬러링 또는 발음 불능)가 기록 프로세스를 복잡하게 만들 수 있습니다. 이러한 경우 합성 음성 기술 및 녹음 세션이 이를 수용해야 합니다(즉, 휴식 시간 및 추가 녹음 세션 수 제공).

### <a name="allow-modification-over-time"></a>시간에 따라 수정 허용
음성 장애를 가진 개인은 노화를 반영하기 위해 자신의 합성 음성에 대한 업데이트를 만들기 위해 욕망 (예를 들어, 사춘기에 도달 아이). 개인은 또한 시간이 지남에 따라 변경 하는 문체 환경 설정을 할 수 있습니다., 피치에 변경 하려는 수 있습니다., 악센트, 또는 다른 음성 특성.


## <a name="reference-docs"></a>참조 문서

* [음성 재능에 대한 공개](https://aka.ms/disclosure-voice-talent)
* [게이팅 개요](concepts-gating-overview.md)
* [공개 방법](concepts-disclosure-guidelines.md)
* [공개 설계 패턴](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>다음 단계

* [음성 재능에 대한 공개](https://aka.ms/disclosure-voice-talent)
* [공개 방법](concepts-disclosure-guidelines.md)
* [공개 설계 패턴](concepts-disclosure-patterns.md)
