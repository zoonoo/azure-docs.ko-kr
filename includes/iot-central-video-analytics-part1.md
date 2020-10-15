---
title: 포함 파일
description: 포함 파일
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 7246375468b3419c3d52ee3d5a51a95aa20050a9
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876724"
---
샘플 애플리케이션에는 시뮬레이션된 디바이스 2개와 IoT Edge 게이트웨이 하나가 포함되어 있습니다. 다음 자습서에서는 게이트웨이의 기능을 실험하고 이해하는 두 가지 방법을 보여줍니다.

* Azure VM에서 IoT Edge 게이트웨이를 만들고 시뮬레이션된 카메라를 연결합니다.
* Intel NUC 같은 실제 디바이스에서 IoT Edge 게이트웨이를 만들고 실제 카메라를 연결합니다.

이 자습서에서는 다음을 수행하는 방법을 알아봅니다.
> [!div class="checklist"]
> * Azure IoT Central 비디오 분석 애플리케이션 템플릿을 사용하여 소매점 애플리케이션 만들기
> * 애플리케이션 설정 사용자 지정
> * IoT Edge 게이트웨이 디바이스에 대한 디바이스 템플릿 만들기
> * IoT Central 애플리케이션에 게이트웨이 디바이스 추가

## <a name="prerequisites"></a>사전 요구 사항

이 자습서 시리즈를 완료하려면 다음이 필요합니다.

* Azure 구독 Azure 구독이 아직 없는 경우 [Azure 가입 페이지](https://aka.ms/createazuresubscription)에서 만들 수 있습니다.
* 실제 카메라를 사용하는 경우 IoT Edge 디바이스와 카메라를 연결해야 하며, **실시간 스트리밍 프로토콜** 채널이 필요합니다.

## <a name="initial-setup"></a>초기 설정

이 자습서에서는 여러 구성 파일을 업데이트하고 사용합니다. 이러한 파일의 초기 버전은 [LVA-게이트웨이](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) GitHub 리포지토리에서 얻을 수 있습니다. 이 리포지토리에는 배포하는 서비스의 구성 값을 기록하는 데 사용할 수 있는 scratchpad 텍스트 파일도 포함되어 있으며, 이 파일을 다운로드해야 합니다.

로컬 머신에 *lva-configuration*이라는 폴더를 만들고 이러한 파일의 복사본을 저장합니다. 그리고 다음 링크를 각각 마우스 오른쪽 단추로 클릭하고 **다른 이름으로 저장**을 선택하여 파일을 *lva-configuration* 폴더에 저장합니다.
