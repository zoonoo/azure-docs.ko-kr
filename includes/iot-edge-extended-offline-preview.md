---
title: 포함 파일
description: IoT Edge
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: kgremban
ms.openlocfilehash: a8160e677fa99d8cb691db39d7f29ba6eddbd261
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004682"
---
## <a name="enabling-extended-offline-operation-preview"></a>확장된 오프라인 작업을 사용하도록 설정(미리 보기)
Edge 런타임의 [v1.0.2 릴리스](https://aka.ms/edge102)로 시작하면 확장된 오프라인 작업에 대해 Edge 장치 및 이에 연결하는 다운스트림 장치를 구성할 수 있습니다. 

이 기능을 사용하여 로컬 모듈 또는 다운스트림 장치는 필요에 따라 Edge 장치에서 다시 인증하고 IoT Hub에서 연결이 끊긴 경우에도 메시지 및 메서드를 통해 서로 통신할 수 있습니다. 이 기능의 자세한 정보 및 범위는 이 [블로그 게시물](https://aka.ms/iot-edge-offline) 및 [개념 문서](../articles/iot-edge/offline-capabilities.md)를 참조하세요.

게이트웨이 시나리오에서 확장된 오프라인을 사용하도록 설정하는 경우 Edge 장치 및 이에 연결할 다운스트림 장치 간 부모-자식 관계를 설정합니다.

1. IoT Hub 포털의 Edge 장치 세부 정보 블레이드에서 맨 위 명령 모음에 있는 **자식 장치 관리(미리 보기)** 단추를 클릭합니다.

1. **+추가** 단추를 클릭합니다.

1. 장치 목록에서 자식 장치를 선택하고 오른쪽 화살표를 사용하여 장치를 선택한 후 자식으로 추가합니다.

1. **확인** 을 클릭하여 확인합니다.

1. Edge 장치 세부 정보의 **설정 모듈** 화면에서 **고급 Edge 런타임 설정 구성**을 클릭하고, **Edge 허브** 환경 변수 아래에서 **MQTT** 값이 포함된 **UpstreamProtocol** 항목을 추가합니다. **Edge 에이전트**에 대해서도 동일한 환경 변수 및 값을 추가합니다. 

1. **저장**을 클릭하고 **다음**을 두 번 클릭한 후 변경 내용을 **제출**해야 합니다.

이제 확장된 오프라인 작업에 대해 Edge 장치 및 해당 자식 장치를 사용하도록 설정합니다.  