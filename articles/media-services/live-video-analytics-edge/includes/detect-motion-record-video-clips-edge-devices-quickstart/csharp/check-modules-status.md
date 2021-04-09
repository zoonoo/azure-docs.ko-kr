---
ms.openlocfilehash: 3f92bae608284c8b619be34a0e08f15e831bf88e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750898"
---
[IoT Edge 배포 매니페스트 생성 및 배포](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) 단계에서 Visual Studio Code의 **AZURE IOT HUB**(왼쪽 하단 섹션) 아래에서 **lva-sample-device** 노드를 확장합니다. 다음 모듈이 배포된 것을 볼 수 있습니다.

* `lvaEdge`라는 이름의 Live Video Analytics 모듈
* RTSP 서버를 시뮬레이션하고 라이브 비디오 피드의 원본으로 작동하는 `rtspsim` 모듈

  ![모듈](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> 위의 단계는 설치 스크립트에서 만든 가상 머신을 사용한다고 가정합니다. 사용자 고유의 에지 디바이스를 사용하는 경우 에지 디바이스로 이동하고, **관리자 권한** 으로 다음 명령을 실행하여 이 빠른 시작에 사용되는 샘플 비디오 파일을 가져와 저장합니다.  

```
mkdir /home/lvaedgeuser/samples
mkdir /home/lvaedgeuser/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
```
