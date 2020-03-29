---
title: 공개 설계 패턴
titleSuffix: Azure Cognitive Services
description: 디자인 패턴 및 공개 모범 사례
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: 3e7d8ee2b156a30b11cda79798a8af8a8ecf4f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74776625"
---
# <a name="disclosure-design-patterns"></a>디자인 패턴 공개
이제&#39;합성 음성 경험에 적합한 [수준의 공개를](concepts-disclosure-guidelines.md#disclosure-assessment) 결정했기 때문에 잠재적인 디자인 패턴을 탐색하기에 좋은 시기가&#39;.
## <a name="overview"></a>개요
합성 음성 경험에 적용할 수 있는 다양한 공개 디자인 패턴이 있습니다. 공개 평가 결과가 '높은 공개'인 경우, 합성 음성의 출처를 완전히 전달하는 것을 의미하는 [**명시적 공개를**](#explicit-disclosure)권장합니다. [**암시적 공개에는**](#implicit-disclosure) 요구되는 공개 수준이 높거나 낮든 음성 경험에 도움이 되는 단서 및 상호 작용 패턴이 포함됩니다.
![공개 패턴의 스펙트럼](media/responsible-ai/disclosure-patterns/affordances.png)






| 명시적 공개 패턴                                                                                                                                                                                    | 암시적 공개 패턴                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[투명한 소개](#transparent-introduction)<br> [구두 투명 소개](#verbal-transparent-introduction)<br>  [명시적 줄 바라인](#explicit-byline)<br>  [사용자 지정 및 교정](#customization-and-calibration)<br> [보호자 공개](#parental-disclosure)<br> [음성이 어떻게 만들어졌는지 자세히 알아볼 수 있는 기회 제공](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [기능 공개](#capability-disclosure)<br>[암시적 큐 및 피드백](#implicit-cues--feedback)<br> [회화적 투명성](#conversational-transparency) |



다음 차트를 사용하여 합성 음성에 적용되는 패턴을 직접 참조합니다. 이 차트의 다른 조건 중 일부는 시나리오에도 적용될 수 있습니다.<br/>



| 당신의 합성 음성 경험하는 경우 ... | 권장 사항 | 디자인 패턴 |
| --- | --- | --- |
| 높은 공개 필요  | 하나 이상의 명시적 패턴과 암시적 큐를 앞에 사용하여 사용자가 연결을 구축하는 데 도움이 됩니다. |[명시적 공개](#explicit-disclosure)<br>[암시적 공개](#implicit-disclosure)  |
| 낮은 공개 필요 | 공개는 최소하거나 불필요할 수 있지만 일부 암시적 패턴의 이점을 활용할 수 있습니다. | [기능 공개](#capability-disclosure)<br>[회화적 투명성](#conversational-transparency)  |
| 높은 수준의 참여를 가지고 있습니다. | 장기적으로 구축하고 사용자 여정을 통해 공개할 여러 진입점을 제공합니다. 온보딩 을 하는 것이 좋습니다. | [투명한 소개](#transparent-introduction)<br>[사용자 지정 및 교정](#customization-and-calibration)<br>[기능 공개](#capability-disclosure) |
| 어린이를 기본 대상대상으로 포함 | 부모를 1차 공개 대상자로 지정하고 자녀에게 공개를 효과적으로 전달할 수 있도록 합니다.  | [보호자 공개](#parental-disclosure)<br>[구두 투명 소개](#verbal-transparent-introduction)<br> [암시적 공개](#implicit-disclosure)<br> [회화적 투명성](#conversational-transparency)  |
| 시각 장애인 또는 시력이 낮은 사용자를 기본 의도 잠재고객으로 포함  | 모든 사용자를 포함하고 모든 형태의 시각적 공개가 대체 텍스트 또는 음향 효과와 연관되어 있는지 확인합니다. 명암비 및 디스플레이 크기에 대한 접근성 표준을 준수합니다. 청각 신호를 사용하여 공개를 전달합니다.  | [구두 투명 소개](#verbal-transparent-introduction) <br>[청각 큐](#implicit-cues--feedback)<br>[햅틱 큐](#implicit-cues--feedback)<br>[회화적 투명성](#conversational-transparency)<br>[접근성 표준](https://www.microsoft.com/accessibility) |
| 화면이 없고, 장치가 없거나, 음성을 상호 작용의 기본 또는 유일한 모드로 사용합니다. | 청각 신호를 사용하여 공개를 전달합니다. | [구두 투명 소개](#verbal-transparent-introduction) <br> [청각 큐](#implicit-cues--feedback)  |
| 잠재적으로 여러 사용자/청취자(예: 여러 세대의 개인 비서)가 포함될 수 있습니다.  | 다양한 사용자 컨텍스트와 이해 수준을 염두에 두고 사용자 여정에서 공개할 수 있는 여러 기회를 제공합니다.  | [투명한 소개(재방문 사용자)](#transparent-introduction)<br> [음성이 어떻게 만들어졌는지 자세히 알아볼 수 있는 기회 제공](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [회화적 투명성](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>명시적 공개
합성 음성 환경이 높은 공개를 요구하는 경우 다음 명시적 패턴 중 적어도 하나를 사용하여 합성 특성을 명확하게 지정하는 것이 가장 좋습니다.
### <a name="transparent-introduction"></a>투명한 소개

음성 환경이 시작되기 전에 음성의 기원과 기능에 대해 완전히 투명하게 공개하여 디지털 도우미를 소개합니다. 이 패턴을 사용하는 최적의 순간은 새 사용자를 온보딩하거나 재방문 사용자에게 새 기능을 도입할 때입니다. 소개 중에 암시적 단서를 구현하면 사용자가 디지털 에이전트의 합성 특성에 대한 정신 모델을 형성하는 데 도움이됩니다.

#### <a name="first-time-user-experience"></a>최초 사용자 환경

![첫 실행 경험 시 투명한 소개](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*새 사용자를 온보딩하는 동안 합성 음성이 도입됩니다.*

권장 사항
- 음성이 인위적이라고 설명합니다(예: &quot;&quot;디지털)
- 에이전트가 수행할 수 있는 작업을 설명합니다.
- 음성&#39;출처를 명시적으로 명시
- 합성 음성에 대해 자세히 알아볼 수 있는 진입점 제공

#### <a name="returning-user-experience"></a>사용자 환경 반환

사용자가 온보딩 환경을 건너뛰는 경우 사용자가 처음으로 음성을 트리거할 때까지 투명 소개 환경의 진입점을 계속 제공합니다.
<br/>

![복귀 사용자 경험 중 투명한 도입](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*합성 음성 경험에 대한 일관된 진입점을 제공합니다. 사용자가 사용자 여정의 어느 시점에서나 처음으로 음성을 트리거할 때 온보딩 환경으로 돌아갈 수 있도록 허용합니다.*


### <a name="verbal-transparent-introduction"></a>구두 투명 소개

디지털 비서의 기원을 알리는 음성 프롬프트&#39;음성은 공개를 달성하기에 충분히 명시적입니다. 이 패턴은 음성이 사용 가능한 유일한 상호 작용 모드인 높은 공개 시나리오에 가장 적합합니다.
<br/>

![구두로 말한 투명한 소개](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*사용자 경험에 사용자가 음성을 이미 소개하거나 특성을&#39;있는 순간이 있는 경우 투명한 소개를 사용합니다.*


![1인칭 으로 구두로 말한 투명한 소개](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*추가적인 투명성을 위해 성우는 1인칭 시 합성 음성의 기원을 공개할 수 있습니다.*

### <a name="explicit-byline"></a>명시적 줄 바라인

사용자가 오디오 플레이어 또는 대화형 구성 요소와 상호 작용하여 음성을 트리거하는 경우 이 패턴을 사용합니다.


![뉴스 미디어 시나리오에서 명시적 바이라인](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*명시적 줄은 음성이 어디에서 왔는지에 대한 기여입니다.*

권장 사항

- 합성된 음성에 대해 자세히 알아볼 수 있는 진입점 제공

### <a name="customization-and-calibration"></a>사용자 지정 및 교정

디지털 도우미가 응답하는 방식(예: 음성 소리)을 제어할 수 있습니다.  사용자가 자신의 조건과 특정 목표를 염두에 두고 시스템과 상호 작용할 때, 정의에 따르면, 그들은 이미 그것이 실제 사람이 아니라는 것을&#39;이해했습니다.

#### <a name="user-control"></a>사용자 정의 컨트롤

합성 음성 환경에 의미 있고 눈에 띄는 영향을 주는 선택 사항을 제공합니다.

![사용자 기본 설정](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*사용자 기본 설정을 통해 사용자는 환경을 사용자 지정하고 개선할 수 있습니다.*

권장 사항

- 사용자가 음성을 사용자 지정할 수 있도록 허용(예: 언어 및 음성 유형 선택)
- 사용자에게 자신의 고유한 음성(예: 음성 보정, 사용자 지정 명령)에 응답하도록 시스템을 가르칠 수 있는 방법을 제공합니다.
- 사용자 생성 또는 컨텍스트 상호 작용(예: 미리 알림)에 최적화

#### <a name="persona-customization"></a>페르소나 사용자 지정

음성을&#39;디지털 도우미를 사용자 정의할 수 있는 방법을 제공합니다. 음성이 유명 인사 나 널리 인식 할 수있는 사람을 기반으로하는 경우 사용자가 음성을 미리 볼 때 시각적 및 음성 소개를 모두 사용하는 것이 좋습니다.

![음성 사용자 지정](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*일련의 음성 중에서 선택할 수있는 기능을 제공하는 것은 인공적인 성격을 전달하는 데 도움이됩니다.*

권장 사항
- 사용자가 각 음성의 사운드를 미리 볼 수 있도록 허용
- 각 음성에 대한 본격적인 소개 사용
- 합성된 음성에 대해 자세히 알아볼 수 있는 진입점 제공

### <a name="parental-disclosure"></a>보호자 공개

COPPA 규정을 준수하는 것 외에도, 주요 의도 된 청중이 어린 자녀이고 노출 수준이 높은 경우 부모에게 공개하십시오. 민감한 용도의 경우 성인이 합성 음성사용을 인정할 때까지 체험을 가만하는 것을 고려해 보십시오. 부모가 자녀에게 그 메시지를 전하도록 격려한다.

![부모를 위한 공개](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*부모에게 최적화 된 투명한 소개는 성인이 아이가 목소리와 상호 작용하기 전에 음성의 합성 특성을 인식하게합니다.*

권장 사항

- 공개의 주요 대상으로 부모를 타겟팅
- 부모가 자녀에게 공개를 전달하도록 격려
- 합성된 음성에 대해 자세히 알아볼 수 있는 진입점 제공
- 부모에게 간단한 &quot;보호&quot; 질문을 요청하여 경험을 게이트하여 공개를 읽었습니다.

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>음성이 어떻게 만들어졌는지 자세히 알아볼 수 있는 기회 제공

가상 음성 기술에 대한 자세한 정보를 제공하는 페이지, 팝업 또는 외부 사이트에 상황에 맞는 진입점을 제공합니다. 예를 들어 온보딩 중에 또는 대화 중에 추가 정보를 묻는 메시지가 표시될 때 자세히 알아볼 수 있는 링크를 표시할 수 있습니다.

![자세한 내용은 진입점](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*합성된 음성에 대해 자세히 알아볼 수 있는 기회를 제공하는 진입점의 예입니다.*

사용자가 합성 음성에 대한 자세한 정보를 요청하면 합성 음성의 기원을 교육하고 기술에 대해 투명하게 공개하는 것이 주요 목표입니다.

![사용자에게 합성 음성에 대한 자세한 정보 제공](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*자세한 내용은 외부 사이트 도움말 사이트에서 제공될 수 있습니다.*

권장 사항

- 복잡한 개념을 단순화하고 법률 및 기술 전문 용어를 사용하지 마십시오.
- 이 콘텐츠를 개인 정보 보호 및 사용 명세서에 묻지 마십시오.
- 콘텐츠를 간결하게 유지하고 사용 가능한 경우 이미지 사용

## <a name="implicit-disclosure"></a>암시적 공개

일관성은 사용자 여정 전반에 걸쳐 암시적으로 공개를 달성하는 열쇠입니다. 장치 및 상호 작용 모드 간에 시각적 및 청각 적 단서를 일관되게 사용하면 암시적 패턴과 명시적 공개 간의 연결을 구축하는 데 도움이 될 수 있습니다.

![암시적 큐의 일관성](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>피드백에 & 암시적 단서

인류형성은 에이전트의 실제 시각적 표현에서 부터 음성, 소리, 빛의 패턴, 튀는 모양 또는 장치의 진동에 이르기까지 다양한 방식으로 나타날 수 있습니다. 페르소나 정의 할 때, 암시적 단서와 피드백 패턴을 활용보다는 매우 인간과 같은 아바타를 목표로한다. 이는 보다 명시적인 공개의 필요성을 최소화하는 한 가지 방법입니다.

![시각적 단서 및 피드백](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*이 단서는 너무 인간과 같은되지 않고 에이전트를 인위적으로 변신하는 데 도움이됩니다. 또한 시간이 지남에 따라 일관되게 사용될 때 자체적으로 효과적인 공개 메커니즘이 될 수 있습니다.*

다음과 같은 유형의 단서를 통합할 때 경험의 상호 작용의 다양한 모드를 고려하십시오.

| 비주얼 큐                                                                                                                                                               | 청각 큐                                                      | 햅틱 큐 |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  아바타 <br>반응형 실시간 큐(예: 애니메이션)<br> 비화면 큐(예: 장치의 조명 및 패턴)<br>  | 소닉온 (예 : 간단한 독특한 사운드, 일련의 음악 노트) | Vibration   |

### <a name="capability-disclosure"></a>기능 공개

공개는 디지털 도우미가 할 수 있는 것에 대한 정확한 기대치를 설정하여 암시적으로 달성될 수 있습니다. 사용자가 디지털 도우미와 상호 작용하는 방법을 배우고 환경의 초기 단계에서 합성 음성에 대해 자세히 알아볼 수 있는 상황에 맞는 도움말을 제공할 수 있도록 샘플 명령을 제공합니다.

![시각적 단서 및 피드백](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>회화적 투명성

대화가 예기치 않은 경로에 빠지면 기대치를 재설정하고 투명성을 강화하며 사용자를 성공적인 경로로 이끄는 데 도움이 되는 기본 응답을 만드는 것이 좋습니다. 대화에서 명시적 공개를 사용할 수 있는 기회도 있습니다.

![예기치 않은 경로 처리](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
에이전트에 지시 &quot;&quot; 오프 작업 또는 개인 질문은 에이전트의 합성 특성의 사용자를 생각 나게 하 고 적절 하 게 참여 하거나 실제 사람으로 리디렉션 을 조종 하는 좋은 시간.

![작업 문제 처리](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>공개 시기

사용자 여정 전반에 걸쳐 공개할 수 있는 많은 기회가 있습니다. 첫 번째 사용, 두 번째 사용, n번째 사용에 &quot;대한&quot; 설계뿐만 아니라 시스템이 실수를 하거나 사용자가 에이전트&#39;기능의 한계를 발견할 때와 같이 투명성을 강조하지 못하는 순간을 포용합니다.

![사용자 여정 전반에 걸친 공개 기회](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

다양한 공개 기회를 강조하는 표준 디지털 도우미 사용자 여정의 예입니다.

### <a name="up-front"></a>전면

공개를위한 최적의 순간은 사람이 합성 음성과 상호 작용하는 처음입니다.개인 음성 도우미 시나리오에서는 온보딩 중이거나 사용자가 환경을 가상으로 언박스할 때입니다. 다른 시나리오에서는 합성 음성이 웹 사이트에서 콘텐츠를 처음 읽거나 사용자가 가상 캐릭터와 처음 상호 작용할 수 있습니다.

- [투명한 소개](#transparent-introduction)
- [기능 공개](#capability-disclosure)
- [사용자 지정 및 교정](#customization-and-calibration)
- [암시적 큐](#implicit-cues--feedback)

### <a name="upon-request"></a>요청 시

사용자는 요청시 사용자 여정 중 언제든지 추가 정보에 쉽게 액세스하고 기본 설정을 제어하며 투명한 통신을 받을 수 있어야 합니다.

- [음성이 어떻게 만들어졌는지 자세히 알아볼 수 있는 기회 제공](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [사용자 지정 및 교정](#customization-and-calibration)
- [회화적 투명성](#conversational-transparency)

### <a name="continuously"></a>지속적 으로

사용자 환경을 지속적으로 향상시키는 암시적 디자인 패턴을 사용합니다.

- [기능 공개](#capability-disclosure)
- [암시적 큐](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>시스템이 실패할 때

공개를 정상적으로 실패할 수 있는 기회로 활용하십시오.

- [회화적 투명성](#conversational-transparency)
- [음성이 어떻게 만들어졌는지 자세히 알아볼 수 있는 기회 제공](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [사람에게 핸드오프](#conversational-transparency)



## <a name="additional-resources"></a>추가 리소스
- [마이크로소프트 봇 가이드라인](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [마이크로 소프트 코타나 디자인 지침](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [마이크로 소프트 윈도우 UWP 음성 디자인 지침](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [마이크로 소프트 윈도우 혼합 현실 음성 명령 지침](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>참조 문서

* [음성 재능에 대한 공개](https://aka.ms/disclosure-voice-talent)
* [합성 음성 기술의 책임있는 배포에 대한 지침](concepts-guidelines-responsible-deployment-synthetic.md)
* [게이팅 개요](concepts-gating-overview.md)
* [공개 방법](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>다음 단계

* [음성 재능에 대한 공개](https://aka.ms/disclosure-voice-talent)
