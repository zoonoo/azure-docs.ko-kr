---
ms.openlocfilehash: f5e180cb85e65cf832ffe0a3746e25790644e1ba
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91829062"
---
1. Visual Studio Code에서 **확장** 탭을 열고(또는 Ctrl+Shift+X를 누르고) Azure IoT Hub를 검색합니다.
1. 마우스 오른쪽 단추를 클릭하고 **확장 설정**을 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="확장 설정":::
1. "자세한 정보 메시지 표시"를 검색하고 활성화합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="확장 설정"
        }
      ]
    }
  }
  ```
    
  * 그래프 인스턴스 및 비디오 흐름을 시작하는 `GraphInstanceActivate`에 대한 호출입니다.
  * 그래프 인스턴스가 실행 중 상태임을 나타내는 `GraphInstanceList`에 대한 두 번째 호출입니다.
1. **터미널** 창의 출력이 `Press Enter to continue`에서 일시 중지됩니다. 아직 Enter 키를 선택하지 마세요. 위로 스크롤하면 호출한 직접 메서드에 대한 JSON 응답 페이로드가 보입니다.
1. Visual Studio Code에서 **출력** 창으로 전환합니다. Live Video Analytics on IoT Edge 모듈에서 IoT 허브에 보내는 메시지가 표시됩니다. 이 빠른 시작의 다음 섹션에서는 이러한 메시지를 설명합니다.
1. 미디어 그래프가 계속 실행되어 결과를 출력합니다. RTSP 시뮬레이터가 원본 비디오를 계속 반복합니다. 미디어 그래프를 중지하려면 **터미널** 창으로 돌아가서 Enter 키를 선택합니다. 

    일련의 다음 호출은 리소스를 정리합니다.

    * `GraphInstanceDeactivate` 호출은 그래프 인스턴스를 비활성화합니다.
    * `GraphInstanceDelete`에 대한 호출은 인스턴스를 삭제합니다.
    * `GraphTopologyDelete` 호출은 토폴로지를 삭제합니다.
    * 마지막 `GraphTopologyList` 호출은 목록이 비어 있음을 보여줍니다.
