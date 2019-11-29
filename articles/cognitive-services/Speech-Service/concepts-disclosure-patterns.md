---
title: 공개 디자인 패턴
titleSuffix: Azure Cognitive Services
description: 공개에 대 한 디자인 패턴 및 모범 사례입니다.
services: cognitive-services
author: angle
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: angle
ms.openlocfilehash: e0cb73f224f53441ad4f23298a035f95d59b9f7c
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558999"
---
# <a name="disclosure-design-patterns"></a>디자인 패턴 공개
이제 가상 음성 환경을 위한 올바른 [공개 수준을](concepts-disclosure-guidelines.md#disclosure-assessment) 결정 했으므로 잠재적 디자인 패턴을 살펴보는 것이&#39;좋습니다.&#39;
## <a name="overview"></a>개요
가상 음성 환경에 적용할 수 있는 공개 디자인 패턴의 스펙트럼이 있습니다. 공개 평가 결과가 ' 높은 공개 ' 인 경우 [**명시적 공개**](#explicit-disclosure)를 사용 하는 것이 좋습니다 .이는 가상 음성의 원본을 완전히 전달 하는 것을 의미 합니다. [**암시적 공개**](#implicit-disclosure) 에는 필요한 공개 수준이 높음 또는 낮음 인지 여부에 관계 없이 음성 환경을 활용 하는 큐 및 상호 작용 패턴이 포함 됩니다.
공개 패턴의 ![스펙트럼](media/responsible-ai/disclosure-patterns/affordances.png)






| 명시적 공개 패턴                                                                                                                                                                                    | 암시적 공개 패턴                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[투명 소개](#transparent-introduction)<br> [구두 투명 소개](#verbal-transparent-introduction)<br>  [명시적 저자명](#explicit-byline)<br>  [사용자 지정 및 보정](#customization-and-calibration)<br> [보호자 공개](#parental-disclosure)<br> [음성에 대 한 자세한 정보를 제공 하는 기회 제공](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [기능 공개](#capability-disclosure)<br>[암시적 큐 및 피드백](#implicit-cues--feedback)<br> [대화형 투명도](#conversational-transparency) |



다음 차트를 사용 하 여 가상 음성에 적용 되는 패턴을 직접 참조할 수 있습니다. 이 차트의 다른 조건 중 일부는 시나리오에도 적용 될 수 있습니다.<br/>



| 가상 음성 환경을 구현 하는 경우 ... | 권장 사항 | 디자인 패턴 |
| --- | --- | --- |
| 높은 공개 필요  | 사용자가 연결을 빌드하는 데 도움이 되도록 하나 이상의 명시적 패턴 및 암시적 큐를 사용 합니다. |[명시적 공개](#explicit-disclosure)<br>[암시적 노출](#implicit-disclosure)  |
| 낮은 공개 필요 | 공개는 최소 또는 불필요 하지만 일부 암시적 패턴의 이점을 누릴 수 있습니다. | [기능 공개](#capability-disclosure)<br>[대화형 투명도](#conversational-transparency)  |
| 높은 수준의 참여 | 장기적으로 빌드하고 사용자 경험을 따라 여러 진입점을 제공 합니다. 온 보 딩 환경을 구현 하는 것이 좋습니다. | [투명 소개](#transparent-introduction)<br>[사용자 지정 및 보정](#customization-and-calibration)<br>[기능 공개](#capability-disclosure) |
| 기본 대상 사용자로 자식을 포함 합니다. | 부모를 기본 공개 대상으로 지정할 수 있으며,이를 통해 자식에 게 효과적으로 공개할 수 있습니다.  | [보호자 공개](#parental-disclosure)<br>[구두 투명 소개](#verbal-transparent-introduction)<br> [암시적 노출](#implicit-disclosure)<br> [대화형 투명도](#conversational-transparency)  |
| 사용자를 대상으로 하는 시각 장애가 있는 시각 장애가 있는 사용자를 포함 합니다.  | 모든 사용자를 포함 하 고 시각적 개체의 모든 형태에 대체 텍스트 또는 음향 효과가 연결 되어 있는지 확인 합니다. 명암비와 표시 크기에 대 한 액세스 가능성 표준을 준수 합니다. 청각 신호를 사용 하 여 공개를 전달 합니다.  | [구두 투명 소개](#verbal-transparent-introduction) <br>[청각 신호](#implicit-cues--feedback)<br>[햅 큐](#implicit-cues--feedback)<br>[대화형 투명도](#conversational-transparency)<br>[내게 필요한 옵션 표준](https://www.microsoft.com/accessibility) |
| 는 화면이 적고 장치를 사용 하지 않거나 기본 또는 유일한 상호 작용 모드로 음성을 사용 합니다. | 청각 신호를 사용 하 여 공개를 전달 합니다. | [구두 투명 소개](#verbal-transparent-introduction) <br> [청각 신호](#implicit-cues--feedback)  |
| 잠재적으로 여러 사용자/수신기를 포함할 수 있습니다 (예: 여러 가족의 개인 길잡이).  | 다양 한 사용자 컨텍스트 및 이해 수준을 염두에 둘 수 있으며, 사용자 경험에 공개 하기 위한 여러 기회를 제공 합니다.  | [투명 소개 (사용자 반환)](#transparent-introduction)<br> [음성에 대 한 자세한 정보를 제공 하는 기회 제공](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [대화형 투명도](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>명시적 공개
가상 음성 환경에 높은 노출이 필요한 경우 다음 명시적 패턴 중 하나 이상을 사용 하 여 가상 특성을 명확 하 게 명시 하는 것이 가장 좋습니다.
### <a name="transparent-introduction"></a>투명 소개

음성 환경이 시작 되기 전에 디지털 길잡이를 음성 및 해당 기능의 출처에 대해 완전히 투명 하 게 소개 합니다. 이 패턴을 사용 하는 가장 좋은 순간은 새 사용자를 온 보 딩 하거나 반환 하는 사용자에 게 새 기능을 도입할 때입니다. 소개 중 암시적 큐를 구현 하는 것은 사용자가 디지털 에이전트의 가상 특성에 대 한 멘 탈 모델을 형성 하는 데 도움이 됩니다.

#### <a name="first-time-user-experience"></a>첫 번째 사용자 환경

처음 실행 하는 동안 투명 한 소개를 ![](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*새 사용자를 온 보 딩 하는 동안 가상 음성이 도입 됩니다.*

권장 사항
- 음성이 인공 임을 설명 합니다 (예: 디지털&quot;&quot;).
- 에이전트가 수행할 수 있는 작업을 설명 합니다.
- 명시적으로 음성&#39;s 원본에 대 한 상태
- 가상 음성에 대해 자세히 알아볼 수 있는 진입점을 제공 합니다.

#### <a name="returning-user-experience"></a>사용자 환경 반환

사용자가 온 보 딩 환경을 건너뛰면 사용자가 처음으로 음성을 트리거할 때까지 투명 소개 환경에 대 한 진입점을 계속 제공 합니다.
<br/>

사용자 환경을 반환 하는 동안 투명 한 소개를 ![](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*가상 음성 환경에 일관 된 진입점을 제공 합니다. 사용자가 언제 든 지 사용자 경험의 특정 시점에서 음성을 트리거할 때 온 보 딩 환경으로 돌아갈 수 있습니다.*


### <a name="verbal-transparent-introduction"></a>구두 투명 소개

디지털 길잡이&#39;음성의 출처를 알려 주는 음성 메시지가 표시 됩니다. 이 패턴은 음성이 유일한 상호 작용 모드 인 고급 시나리오에 적합 합니다.
<br/>

![구두로의 음성 투명 소개](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*사용자 환경에 사용자의 음성을 이미 도입 하거나 특성&#39;을 이미 도입할 수 있는 경우 투명 소개를 사용 합니다.*


![구두로의 음성 투명 소개](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*추가적인 투명성을 위해 음성 행위자는 첫 번째 사용자의 가상 음성 원본을 공개할 수 있습니다.*

### <a name="explicit-byline"></a>명시적 저자명

사용자가 음성을 트리거하기 위해 오디오 플레이어 또는 대화형 구성 요소와 상호 작용 하는 경우이 패턴을 사용 합니다.


뉴스 미디어 시나리오에서 명시적 저자명을 ![](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*명시적 저자명는 음성이 제공 되는 위치의 특성입니다.*

권장 사항

- 합성 음성에 대 한 자세한 정보를 제공 하는 입력 지점 제공

### <a name="customization-and-calibration"></a>사용자 지정 및 보정

사용자에 게 디지털 길잡이가 응답 하는 방법 (즉, 음성 소리)을 제어할 수 있습니다.  사용자가 고유한 용어를 사용 하 여 시스템을 조작 하 고 특정 목표를 염두에 두면, 정의에 따라 실제 사용자가 아닌 것&#39;을 이미 파악 한 것입니다.

#### <a name="user-control"></a>사용자 정의 컨트롤

가상 음성 환경에 의미 있고 눈에 띄는 영향을 주는 선택 항목을 제공 합니다.

사용자 기본 설정 ![](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*사용자 기본 설정을 통해 사용자는 환경을 사용자 지정 하 고 개선할 수 있습니다.*

권장 사항

- 사용자가 음성 (예: 언어 및 음성 유형 선택)을 사용자 지정할 수 있습니다.
- 사용자에 게 고유한 음성 (예: 음성 보정, 사용자 지정 명령)에 대응할 수 있도록 시스템을 교육 하는 방법을 제공 합니다.
- 사용자 생성 또는 상황에 맞는 상호 작용을 위한 최적화 (예: 미리 알림)

#### <a name="persona-customization"></a>가상 사용자 지정

디지털 길잡이&#39;를 음성으로 사용자 지정 하는 방법을 제공 합니다. 음성이 유명인 하거나 널리 인식할 수 있는 사람을 기반으로 하는 경우 사용자가 음성을 미리 볼 때 시각적 개체와 음성 소개를 모두 사용 하는 것이 좋습니다.

음성 사용자 지정 ![](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*음성 집합에서 선택 하는 기능을 제공 하면 인공 특성을 전달 하는 데 도움이 됩니다.*

권장 사항
- 사용자가 각 음성의 소리를 미리 볼 수 있도록 허용
- 각 음성에 대해 인증 된 소개 사용
- 합성 음성에 대 한 자세한 정보를 제공 하는 진입점 제공

### <a name="parental-disclosure"></a>보호자 공개

COPPA 규정을 준수 하는 것 외에도 기본 의도 된 대상이 어린이 이며 노출 수준이 높으면 부모에 대 한 노출을 제공 합니다. 중요 한 사용을 위해 성인 들이 가상 음성 사용을 승인할 때까지 환경을 제어 하는 것이 좋습니다. 부모에 게 메시지를 자식으로 전달 하도록 권장 합니다.

부모](media/responsible-ai/disclosure-patterns/parental-disclosure.png)에 대 한 ![공개<br/>
*부모에 최적화 된 투명 소개를 사용 하면 자식이 상호 작용 하기 전에 성인이 음성의 가상 특성을 인식 하 게 됩니다.*

권장 사항

- 공개할 기본 대상으로 부모를 대상으로 합니다.
- 부모에서 자식에 대 한 공개를 전달 하도록 권장
- 합성 음성에 대 한 자세한 정보를 제공 하는 진입점 제공
- 부모에 게 간단한 &quot;&quot; 보호 하 여 공개를 읽고 있는지를 확인 하 여 환경을 성문 합니다.

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>음성에 대 한 자세한 정보를 제공 하는 기회 제공

가상 음성 기술에 대 한 자세한 정보를 제공 하는 페이지, 팝업 또는 외부 사이트에 대 한 상황에 맞는 진입점을 제공 합니다. 예를 들어 등록 하는 동안 또는 대화 중에 추가 정보를 묻는 메시지가 표시 될 때 자세히 알아보려면 링크를 표시할 수 있습니다.

추가 정보를 보려면 진입점을 ![](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*합성 음성에 대 한 자세한 정보를 얻을 수 있는 기회를 제공 하는 진입점의 예입니다.*

사용자가 가상 음성에 대 한 자세한 정보를 요청 하면 기본 목표는 가상 음성의 원본에 대해 교육 하 고 기술에 대해 투명 하 게 하는 것입니다.

사용자에 게 가상 음성에 대 한 자세한 정보를 제공 ![](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*추가 정보는 외부 사이트 도움말 사이트에서 제공 될 수 있습니다.*

권장 사항

- 복잡 한 개념을 단순화 하 고 legalese 및 기술 전문 용어 사용 방지
- 개인 정보 및 사용 약관에서이 콘텐츠를 bury 안 함
- 콘텐츠를 간결 하 게 유지 하 고 사용 가능한 경우 이미지 사용

## <a name="implicit-disclosure"></a>암시적 노출

일관성은 사용자 경험 전체에서 암시적으로 공개를 달성 하기 위한 핵심입니다. 장치 및 상호 작용 모드에서 시각적 및 청각 큐를 일관 되 게 사용 하는 것은 암시적 패턴과 명시적 공개 간의 연결을 만드는 데 도움이 될 수 있습니다.

![암시적 큐의 일관성](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>사용자 의견 & 암시적 큐

Anthropomorphism는 에이전트의 실제 시각적 표현에서 음성, 소리, 광원 패턴, 바운스 셰이프 또는 장치의 진동 등 다양 한 방식으로 매니페스트 될 수 있습니다. 사용자를 정의 하는 경우 매우 사람의 마음에 드는 대신 암시적 큐 및 피드백 패턴을 활용 합니다. 이는 보다 명시적인 공개의 필요성을 최소화 하는 한 가지 방법입니다.

시각적 신호 및 피드백을 ![](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*이러한 큐를 통해 사용자가 anthropomorphize 하지 않고도 에이전트를 쉽게 파악할 수 있습니다. 또한 시간이 지남에 따라 지속적으로 사용 되는 경우 자체에 대 한 효과적인 공개 메커니즘이 될 수 있습니다.*

다음과 같은 유형의 큐를 통합할 때 사용자 환경의 다양 한 상호 작용 모드를 고려 합니다.

| 시각적 신호                                                                                                                                                               | 청각 신호                                                      | 햅 큐 |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  아바타 <br>반응 형 실시간 신호 (예: 애니메이션)<br> 화면이 아닌 신호 (예: 장치의 조명 및 패턴)<br>  | Sonicon (예: 간단한 고유의 소리, 일련의 악기 노트) | Vibration   |

### <a name="capability-disclosure"></a>기능 공개

디지털 길잡이가 지 원하는 항목에 대 한 정확한 기대를 설정 하 여 암시적으로 공개를 구현할 수 있습니다. 사용자가 디지털 길잡이와 상호 작용 하는 방법을 배울 수 있도록 샘플 명령을 제공 하 고, 경험의 초기 단계에서 가상 음성에 대 한 자세한 정보를 제공 하는 상황에 맞는 도움말을 제공 합니다.

![시각적 신호 및 피드백](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>대화형 투명도

대화가 예상과 일치 하는 경우에는 예측을 다시 설정 하 고 투명도를 강화 하 고 사용자가 성공적인 경로를 향해 이동할 수 있는 기본 응답을 만드는 것이 좋습니다. 대화에 명시적 공개를 사용할 수도 있습니다.

![예기치 않은 경로 처리](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
에이전트에 게 전달 되는 개인&quot; 질문을 사용 하는 경우 에이전트의 가상 특성을 사용자에 게 알려 하 고 적절 하 게 참여 하거나 실제 사용자로 리디렉션하는 것이 좋은 시간입니다. &quot;

![작업 관련 질문 처리](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>공개 시점

사용자 경험 전체에서 공개 하기 위한 많은 기회가 있습니다. 첫 번째 사용을 위한 디자인, 두 번째 사용, n 사용 ..., 시스템에서 실수를 하는 경우 또는 사용자가 에이전트&#39;기능에 대 한 제한을 검색 하는 경우와 같이 투명도를 강조 표시 하는 &quot;실패를&quot; 합니다.

![사용자 경험 전체의 공개 기회](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

다양 한 공개 기회를 강조 표시 하는 표준 디지털 길잡이 사용자 경험의 예입니다.

### <a name="up-front"></a>위쪽-전면

사용자가 가상 음성에 처음으로 상호 작용 하는 경우 가장 적합 한 공개 시간입니다.  개인 음성 도우미 시나리오에서이 작업은 등록 하는 동안 이나 사용자가 처음으로 환경을 사용 취소할 때 발생 합니다. 다른 시나리오에서는 가상 음성이 웹 사이트의 콘텐츠를 처음으로 읽거나 사용자가 가상 문자와 상호 작용할 때 처음으로 발생할 수 있습니다.

- [투명 소개](#transparent-introduction)
- [기능 공개](#capability-disclosure)
- [사용자 지정 및 보정](#customization-and-calibration)
- [암시적 큐](#implicit-cues--feedback)

### <a name="upon-request"></a>요청 시

사용자는 요청 시 사용자 경험 중 언제 든 지 추가 정보에 쉽게 액세스 하 고, 기본 설정을 제어 하 고, 투명 한 통신을 받을 수 있습니다.

- [음성에 대 한 자세한 정보를 제공 하는 기회 제공](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [사용자 지정 및 보정](#customization-and-calibration)
- [대화형 투명도](#conversational-transparency)

### <a name="continuously"></a>연속

사용자 환경을 지속적으로 향상 시키는 암시적 디자인 패턴을 사용 합니다.

- [기능 공개](#capability-disclosure)
- [암시적 큐](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>시스템에 오류가 발생 한 경우

정상적으로 실패할 수 있는 기회로 공개를 사용 합니다.

- [대화형 투명도](#conversational-transparency)
- [음성에 대 한 자세한 정보를 제공 하는 기회 제공](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [사람에게 핸드오프](#conversational-transparency)



## <a name="additional-resources"></a>추가 리소스
- [Microsoft 봇 지침](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Cortana 디자인 지침](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [Microsoft Windows UWP 음성 디자인 지침](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [Microsoft Windows Mixed Reality 음성 명령 지침](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>참조 문서

* [음성 인재 공개](https://aka.ms/disclosure-voice-talent)
* [가상 음성 기술의 책임이 있는 배포에 대 한 지침](concepts-guidelines-responsible-deployment-synthetic.md)
* [제어 개요](concepts-gating-overview.md)
* [공개 방법](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>다음 단계

* [음성 인재 공개](https://aka.ms/disclosure-voice-talent)
