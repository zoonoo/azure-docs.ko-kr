---
title: 가상 음성 기술의 책임이 있는 배포에 대 한 지침
titleSuffix: Azure Cognitive Services
description: Microsoft의 일반적인 설계 지침에 따라 가상 음성 기술을 사용 합니다. 이러한 내용은 Microsoft가 voice 인재, 소비자를 통해 수행 하는 연구에서 개발 되었으며, 음성 들 있는 개인은 가상 음성의 책임 개발을 안내 합니다.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "73836775"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>가상 음성 기술의 책임이 있는 배포에 대 한 지침
다음은 가상 음성 기술 사용에 대 한 Microsoft의 일반적인 디자인 지침입니다. 이러한 내용은 Microsoft가 speech 인재, 소비자를 사용 하는 것 뿐만 아니라 speech 들를 사용 하는 개인 뿐만 아니라 가상 음성의 책임 개발을 안내 하기 위해 개발 되었습니다.

## <a name="general-considerations"></a>일반적인 고려 사항
가상 음성 기술의 배포에 대 한 다음 지침은 대부분의 시나리오에서 적용 됩니다.

### <a name="disclose-when-the-voice-is-synthetic"></a>음성이 가상 인 경우 공개
음성이 컴퓨터에 생성 됨을 공개 하는 것은 사기에서 유해한 결과가 발생 하는 위험을 최소화 하는 것이 아니라 음성을 배달 하는 조직의 신뢰도 높아집니다. [을 공개 하는 방법](concepts-disclosure-guidelines.md)에 대해 자세히 알아보세요.

### <a name="select-appropriate-voice-types-for-your-scenario"></a>시나리오에 적합 한 음성 유형 선택
사용 컨텍스트와 가상 음성 사용과 관련 된 잠재적인 피해를 신중 하 게 고려해 야 합니다. 예를 들어 개인 메시징, 금융 거래 또는 인간 적응성 또는 공감가 필요한 복잡 한 상황에 대 한 고성능의 높은 시나리오에서는 높은 성능의 가상 음성이 적합 하지 않을 수 있습니다. 사용자가 음성 형식에 대해 다른 예상치를 가질 수도 있습니다. 예를 들어, 가상 음성에서 중요 한 소식을 읽을 수 있는 경우 일부 사용자는 더 empathetic 하 고 사용자와 유사한 뉴스 읽기를 선호 하는 반면, 다른 사용자는 더 monotone, 비편향 음성을 선호 합니다. 사용자 기본 설정을 보다 잘 이해할 수 있도록 응용 프로그램을 테스트 하는 것이 좋습니다.

### <a name="be-transparent-about-capabilities-and-limitations"></a>기능 및 제한 사항에 대해 투명 하 게
사용자는 고화질 가상 음성 에이전트와 상호 작용할 때 더 높은 기대치를 가질 가능성이 높습니다. 따라서 시스템 기능이 이러한 기대를 충족 하지 못하는 경우에는 신뢰가 저하 될 수 있으며, 불쾌 또는 유해한 환경도 발생할 수 있습니다.

### <a name="provide-optional-human-support"></a>선택적 사용자 지원 제공
모호한 트랜잭션 시나리오 (예: call support center)에서는 사용자가 항상 컴퓨터 에이전트를 신뢰 하 여 요청에 적절 하 게 응답 하지 않습니다. 이러한 상황에는 시스템의 사실적인 품질에 관계 없이 인간 지원이 필요할 수 있습니다.

## <a name="considerations-for-voice-talent"></a>음성 인재 고려 사항
음성 행위자와 같은 음성 인재 작업할 때 가상 음성을 만들려면 아래 지침이 적용 됩니다.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>음성 인재에서 의미 있는 동의 얻기
음성 인재 음성 글꼴 (사용 방법 및 위치)을 제어 하 고 사용할 때마다 보정 될 것으로 간주 합니다. 따라서 시스템 소유자는 음성 인재에서 명시적으로 작성 된 권한을 얻고 사용 사례, 사용 기간, 보상 등에 대해 명확한 계약 사양을 사용 해야 합니다. 일부 음성 인재는 악성 기술의 잠재적 악성 사용을 모르고 시스템 소유자가 기술의 기능에 대해 교육을 받아야 합니다. 음성 인재 및 동의에 대 한 자세한 내용은 [Voice 인재에 대 한 공개](https://aka.ms/disclosure-voice-talent)정보를 참조 하세요.


## <a name="considerations-for-those-with-speech-disorders"></a>Speech 들를 사용 하는 경우의 고려 사항
음성 들를 사용 하는 개인으로 작업 하는 경우, 다음 지침이 적용 됩니다.

### <a name="provide-guidelines-to-establish-contracts"></a>계약 설정 지침 제공
말하는 도움을 받기 위해 종합 음성을 사용 하는 개인에 게 계약을 설정 하기 위한 지침을 제공 합니다. 계약은 음성, 사용 기간, 소유권 이전 조건, 음성 글꼴 삭제 절차, 무단 액세스를 방지 하는 방법 등을 소유한 파티를 지정 하는 것을 고려해 야 합니다. 또한 해당 사용자에 게 권한이 부여 된 경우 가족 구성원에 게 음성으로 전환 된 후 음성 글꼴 소유권의 계약 전송을 사용 하도록 설정 합니다.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>음성 패턴의 불일치에 대 한 계정
자신의 음성 글꼴을 기록 하는 음성 들 개인의 경우 음성 패턴의 불일치 (slurring 또는 특정 단어를 발음 하지 않음)가 기록 프로세스를 복잡 하 게 할 수 있습니다. 이러한 경우에는 가상 음성 기술 및 기록 세션이이를 수용 해야 합니다. 즉, 중단 및 추가 기록 세션 수를 제공 해야 합니다.

### <a name="allow-modification-over-time"></a>시간에 따른 수정 허용
Speech 들가 있는 개인은 에이징 (예: puberty에 도달 하는 경우)을 반영 하기 위해 가상 음성을 업데이트 해야 합니다. 개인에 게 시간이 지남에 따라 변경 되는 스타일 기본 설정이 있을 수 있으며 피치, 악센트 또는 기타 음성 특성을 변경할 수도 있습니다.


## <a name="reference-docs"></a>참조 문서

* [음성 인재 공개](https://aka.ms/disclosure-voice-talent)
* [제어 개요](concepts-gating-overview.md)
* [공개 방법](concepts-disclosure-guidelines.md)
* [공개 디자인 패턴](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>다음 단계

* [음성 인재 공개](https://aka.ms/disclosure-voice-talent)
* [공개 방법](concepts-disclosure-guidelines.md)
* [공개 디자인 패턴](concepts-disclosure-patterns.md)
