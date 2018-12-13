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
ms.openlocfilehash: be4d82577584e83e29f2511d51256fda0970e917
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51264312"
---
## <a name="enabling-extended-offline-operation-preview"></a>확장된 오프라인 작업을 사용하도록 설정(미리 보기)
Edge 런타임 [v1.0.4 릴리스](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4)부터 Edge 디바이스 및 해당 디바이스에 연결하는 다운스트림 디바이스를 구성하여 오프라인 작업을 확장할 수 있습니다. 

이 기능을 사용하여 로컬 모듈 또는 다운스트림 디바이스는 필요에 따라 Edge 디바이스에서 다시 인증하고 IoT Hub에서 연결이 끊긴 경우에도 메시지 및 메서드를 통해 서로 통신할 수 있습니다. 이 기능의 자세한 정보 및 범위는 이 [블로그 게시물](https://aka.ms/iot-edge-offline) 및 [개념 문서](../articles/iot-edge/offline-capabilities.md)를 참조하세요.

게이트웨이 시나리오에서 확장된 오프라인을 사용하도록 설정하는 경우 Edge 디바이스 및 이에 연결할 다운스트림 디바이스 간 부모-자식 관계를 설정합니다.

1. IoT Hub 포털의 Edge 디바이스 세부 정보 블레이드에서 맨 위 명령 모음에 있는 **자식 디바이스 관리(미리 보기)** 단추를 클릭합니다.

1. **+추가** 단추를 클릭합니다.

1. 디바이스 목록에서 자식 디바이스를 선택하고 오른쪽 화살표를 사용하여 디바이스를 선택한 후 자식으로 추가합니다.

1. **확인** 을 클릭하여 확인합니다.

이제 확장된 오프라인 작업에 대해 Edge 디바이스 및 해당 자식 디바이스를 사용하도록 설정합니다.  