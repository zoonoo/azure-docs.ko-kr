---
title: 음향 시뮬레이션을 사용한 디자인 개념
titlesuffix: Azure Cognitive Services
description: 이 개념적 개요에서는 프로젝트 Acoustics 음향 시뮬레이션을 소리 디자인 프로세스에 통합 하는 방법에 대해 설명 합니다.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 11e1e3f45b5198ddedb6c31fcd354185adef445d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854304"
---
# <a name="project-acoustics-design-process-concepts"></a>Project Acoustics 디자인 프로세스 개념

이 개념적 개요에서는 프로젝트 Acoustics 물리적 음향 시뮬레이션을 소리 디자인 프로세스에 통합 하는 방법에 대해 설명 합니다.

## <a name="sound-design-with-audio-dsp-parameters"></a>오디오 DSP 매개 변수를 사용한 소리 디자인

3D 대화형 제목은 오디오 엔진에서 호스트 되는 오디오 DSP (디지털 신호 처리) 블록을 사용 하 여 특정 소리를 얻습니다. 이러한 블록의 범위는 단순한 혼합, reverberation, 에코, 지연, 이퀄라이제이션, 압축 및 제한, 기타 효과의 복잡성입니다. 이러한 효과에 대 한 매개 변수를 선택 하 고 정렬 하 고 설정 하는 작업은 환경에 대 한 미적 및 플레이 목표를 달성 하는 오디오 그래프를 작성 하는 사운드 디자이너의 책임입니다.

대화형 제목에서 소리와 수신기가 3D 공간 전체에서 이동 하는 경우 이러한 매개 변수는 변화 하는 조건에 어떻게 적응 하나요? 사운드 디자이너는 reverberation 효과의 변경 (예:)을 트리거하기 위해 프로그래밍 된 공간 전체에서 볼륨을 정렬 하 고, 수신기가 장면의 한 부분에서 다른 부분으로 이동할 때 혼합 된 소리를 오리는 경우가 많습니다. 이러한 효과 중 일부를 자동화할 수 있는 Acoustics 시스템도 사용할 수 있습니다.

3D 제목은 집중 교육 및 플레이 목표를 혼합 하는 데 사용할 수 있는, 디자이너 조정 되는 조명 및 운동학 적 목표 시스템을 사용 합니다. 비주얼 디자이너는 개별 픽셀 값을 설정 하지 않고, 시각적 미관 및 CPU 비용을 절충 하기 위해 실제로 기반으로 하는 3D 모델, 자료 및 광원 전송 시스템을 조정 합니다. 오디오에 대 한 동일한 프로세스는 무엇 인가요? Project Acoustics는이 질문을 탐색 하는 첫 번째 단계입니다. 먼저 공간을 통해 acoustical 에너지를 전송 하는 것을 의미 합니다.

![반향 영역으로 중첩 된 AltSpace 장면 스크린샷](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>임펄스 응답: 공간에 두 개의 요소를 연결 하는 Acoustically

오디오 디자인에 익숙한 경우에는 음향 임펄스 응답에 익숙할 수 있습니다. 음향 임펄스 응답은 원본에서 수신기로의 소리 전송을 모델링 합니다. 따라서 임펄스 응답은 폐색 및 reverberation와 같은 대화방 acoustics의 모든 흥미로운 효과를 캡처할 수 있습니다. 임펄스 응답에는 오디오 DSP 효과를 조정 하는 데 사용할 수 있는 특정 강력한 속성도 있습니다. 두 개의 오디오 신호를 함께 추가 하 고 임펄스 response를 사용 하 여 처리 하면 임펄스 응답을 각 신호에 개별적으로 적용 하 고 결과를 추가 하는 것과 동일한 결과가 발생 합니다. 또한 음향 전파 및 임펄스 응답은 처리 되는 오디오, 모델링 중인 장면 및 원본 및 수신기 위치에 따라 달라 집니다. 즉, 임펄스 response는 소리 전파에 대 한 장면 효과를 추출 합니다.

임펄스 응답은 흥미로운 실내 음향 효과를 모두 캡처하고, 필터를 사용 하 여 효율적으로 오디오에 적용 하 고, 측정 또는 시뮬레이션에서 임펄스 응답을 받을 수 있습니다. 하지만 acoustics를 정확 하 게 정확 하 게 일치 시 키 지 않도록 하 고, 장면의 감정적 수요와 일치 하도록 몰드 하려면 어떻게 해야 하나요? 그러나 픽셀 값과 매우 유사 하 게 임펄스 응답은 수천 개의 숫자 목록에 불과합니다. 미적 요구에 맞게 조정 하는 것이 좋습니다. Doorways 또는 장애를 통과 하는 동안 원활 하 게 변하는 폐색/장애물을 보유 하려는 경우, 원활한 효과를 얻기 위해 필요한 임펄스 응답은 몇 개입니까? 소스가 빠르게 이동 하는 경우 어떻게 되나요? 어떻게 보간 하나요?

대화형 타이틀에서 acoustics의 일부 측면에 대해 시뮬레이션 및 임펄스 응답을 사용 하기 어렵습니다. 그러나 시뮬레이션에서 친숙 한 audio DSP 효과 매개 변수를 사용 하 여 임펄스 응답을 연결할 수 있는 경우 디자이너 조정을 지 원하는 오디오 전송 시스템을 빌드할 수 있습니다.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>매개 변수를 사용 하 여 오디오 DSP에 시뮬레이션 연결

임펄스 응답에는 모든 흥미로운 (및 모든 필요 하지 않은) acoustical 효과가 포함 됩니다. Audio DSP 블록은 해당 매개 변수를 올바르게 설정 하면 흥미로운 acoustical 효과를 렌더링할 수 있습니다. Acoustical 시뮬레이션을 사용 하 여 3D 장면에서 오디오 전송을 자동화 하는 오디오 DSP 블록을 사용 하는 것은 임펄스 응답에서 오디오 DSP 매개 변수를 측정 하는 것입니다. 이러한 측정은 폐색, 장애물, portalling 및 reverberation를 포함 하 여 공통적이 고 중요 한 acoustical 효과를 잘 이해 하 고 있습니다.

하지만 시뮬레이션이 오디오 DSP 매개 변수에 직접 연결 된 경우 디자이너를 조정 하는 방법은 무엇입니까? 얻게 되는 사항은 무엇 인가요? 임펄스 응답을 삭제 하 고 몇 가지 DSP 매개 변수를 유지 하 여 상당한 양의 메모리를 얻을 수 있습니다. 디자이너에 최종 결과를 제공 하는 기능을 제공 하기 위해 시뮬레이션 및 오디오 DSP 사이에 디자이너를 삽입 하는 방법을 찾아야 합니다.

![매개 변수가 중첩 된 스타일이 있는 임펄스 응답이 있는 그래프](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>시뮬레이션에서 오디오 DSP 매개 변수를 변환 하는 사운드 디자인

선글라스가 전 세계 보기에 미치는 영향을 고려 합니다. 밝은 날에는 빛이 빛을 더 편안 하 게 줄일 수 있습니다. 다크 공간에 아무것도 표시 되지 않을 수 있습니다. 모든 상황에서 특정 수준의 밝기를 설정 하지는 않습니다. 모든 항목을 더 어둡게 만듭니다.

시뮬레이션을 사용 하 여 폐색 및 reverberation 매개 변수를 통해 오디오 DSP를 구동 하는 경우 시뮬레이터 뒤에 필터를 추가 하 여 DSP에서 ' 확인 ' 하는 매개 변수를 조정할 수 있습니다. 필터는 특정 수준의 폐색 또는 반향 꼬리 길이를 적용 하지 않습니다. 선글라스은 모든 방에 동일한 밝기를 적용 하지 않습니다. 필터를 사용 하면 모든 occluder 려 줄어듭니다. 또는 려. 하나는 ' 어둡게 ' 폐색 매개 변수 필터를 추가 하 고 조정 하는 것이 고, 큰 경우에는 폐색 효과가 거의 없습니다. 반면 doorways는 미디어에서 강력한 폐색 효과로 증가 하는 반면 효과 전환의 효과는 그대로 유지 됩니다. 시뮬레이션에서 제공 하는입니다.

이 패러다임에서 디자이너의 태스크는 시뮬레이션에서 제공 되는 가장 중요 한 DSP 매개 변수에 적용할 필터를 선택 하 고 조정 하기 위해 각 상황 및 모든 상황에 대 한 음향 매개 변수 선택에서 변경 됩니다. 이 기능을 통해 폐색 및 reverberation 효과 강도 및 혼합 된 소스의 존재에 대 한 높은 문제에 대 한 원활한 전환을 설정 하는 것에 대 한 좁은 문제 로부터 디자이너의 활동을 상승 시킬 수 있습니다. 물론,이 경우에는 특정 상황에서 특정 원본에 대 한 DSP 매개 변수를 선택 하는 것으로 돌아가서 항상 사용할 수 있는 필터를 사용할 수 있습니다.

## <a name="sound-design-in-project-acoustics"></a>프로젝트 Acoustics의 소리 디자인

Project Acoustics 패키지는 위에 설명 된 각 구성 요소를 통합 합니다. 시뮬레이터는 매개 변수를 추출 하 고 Acoustics 자산, 오디오 DSP 및 선택 된 필터를 작성 하는 인코더입니다. Project Acoustics를 사용 하는 사운드 디자인은 시뮬레이션에서 파생 되 고 오디오 DSP에 적용 되는 폐색 및 reverberation 매개 변수를 조정 하는 필터에 대 한 매개 변수를 선택 합니다. 게임 편집기와 오디오 엔진 내에 동적 컨트롤이 노출 됩니다.

## <a name="next-steps"></a>다음 단계
* [Unity 용 Project Acoustics 퀵 스타트](unity-quickstart.md) 또는 [Unreal의 project Acoustics 빠른](unreal-quickstart.md) 시작을 사용 하 여 디자인 패러다임을 사용해 보세요.
* [Unity 용 Project Acoustics 디자인 컨트롤](unity-workflow.md) 또는 [Unreal 용 project Acoustics 디자인 컨트롤](unreal-workflow.md) 살펴보기

