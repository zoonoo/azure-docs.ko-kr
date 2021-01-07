---
title: Azure Media Player 접근성
description: Azure Media Player의 접근성 설정에 대해 자세히 알아봅니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 2231c2969bbfce1668002ad4f5f719e0b8e13de5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "81727610"
---
# <a name="accessibility"></a>접근성 #

Azure Media Player는 Windows 내레이터 및 Apple OSX/iOS VoiceOver와 같은 화면 판독기 기능과 함께 작동합니다. UI 단추에는 대체 태그를 사용할 수 있으며 화면 판독기는 사용자가 해당 태그를 탐색할 때 이러한 대체 태그를 읽을 수 있습니다. 추가 구성은 운영 체제 수준에서 설정할 수 있습니다.

## <a name="captions-and-subtitles"></a>캡션 및 자막 ##

현재 Azure Media Player는 WebVTT 형식을 사용하는 주문형 이벤트와 CEA-708을 통해 라이브 이벤트에 대한 자막만 지원합니다. TTML 형식은 현재 지원되지 않습니다. 캡션과 자막을 사용하면 청각 장애인 또는 다른 언어로 따라 읽기를 원하는 사람들을 포함하여 더 많은 청중이 플레이어를 사용할 수 있습니다. 캡션은 비디오 참여도를 높이고, 이해를 돕고, 회사처럼 소리에 민감한 환경에서도 비디오를 볼 수 있게 해줍니다.  

## <a name="high-contrast-mode"></a>고대비 모드 ##

Azure Media Player의 기본 UI는 대부분의 디바이스/브라우저 고대비 보기 모드를 준수합니다. 구성은 운영 체제 수준에서 설정할 수 있습니다.

## <a name="mobility-options"></a>모바일 옵션 ##

### <a name="tabbing-focus"></a>탭 이동 포커스 ###

일반적인 HTML 표준에서 제공하는 탭 이동 포커스는 Azure Media Player에서 사용할 수 있습니다. 탭 포커스를 사용하도록 설정하려면 `<video ... tabindex=0>...</video>`와 같이 `tabindex=0`(또는 HTML에서 탭 순서의 영향을 이해한다면 다른 값)을 HTML `<video>`에 추가해야 합니다. 일부 플랫폼에서 컨트롤이 표시되고 플랫폼이 이러한 기능을 지원하는 경우에만 컨트롤에 대한 포커스가 있을 수 있습니다.

탭 이동 포커스가 활성화되면 최종 사용자는 마우스를 사용하지 않고도 비디오 플레이어를 효과적으로 탐색하고 제어할 수 있습니다. 각 바로 가기 메뉴 또는 제어 가능한 요소는 탭 단추를 눌러 탐색하고 Enter 또는 스페이스바를 사용하여 선택할 수 있습니다. 바로 가기 메뉴에서 Enter 또는 스페이스바를 누르면 최종 사용자가 메뉴 항목을 선택하기 위해 탭 이동을 계속할 수 있도록 확장됩니다. 선택하려는 항목의 컨텍스트가 있으면 Enter 또는 스페이스바를 다시 눌러 선택 항목을 완료합니다.

### <a name="hotkeys"></a>바로 가기 키 ###

Azure Media Player는 키보드 바로 가기 키를 통한 제어를 지원합니다. 웹 브라우저에서 기본 비디오 요소를 제어하는 유일한 방법은 플레이어에 집중하는 것입니다. 플레이어에 집중하면 바로 가기 키가 플레이어 기능을 제어할 수 있습니다.  아래 표에서는 다양한 바로 가기 키와 관련 동작을 설명합니다.

| 바로 가기 키              | 동작                                                                |
|----------------------|-------------------------------------------------------------------------|
| F/f                  | 플레이어가 FullScreen 모드를 시작하거나 종료함                                  |
| M/m                  | 플레이어 볼륨이 음소거/음소거 해제됨                                          |
| 위쪽 및 아래쪽 화살표    | 플레이어 볼륨이 증가/감소함                                    |
| 왼쪽 및 오른쪽 화살표 | 비디오 진행률이 증가/감소함                                  |
| 0,1,2,3,4,5,6,7,8,9  | 누른 키에 따라 비디오 진행률이 0%\- 90%로 변경됨 |
| 동작 클릭         | 비디오가 재생/일시 정지됨                                                   |

## <a name="next-steps"></a>다음 단계

<!---Some context for the following links goes here--->
- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)