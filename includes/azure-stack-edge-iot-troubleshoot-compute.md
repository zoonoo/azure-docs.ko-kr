---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 06/08/2021
ms.openlocfilehash: 6bce3c5a01d182c67525caca9b75e3a03e59a4d9
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111983226"
---
IoT Edge 에이전트 런타임 응답을 사용하여 컴퓨팅 관련 오류를 해결할 수 있습니다. 가능한 응답 목록은 다음과 같습니다.

* 200 - 확인
* 400 - 배포 구성이 잘못되었거나 유효하지 않습니다.
* 417 - 디바이스에 배포 구성 집합이 없습니다.
* 412 - 배포 구성의 스키마 버전이 잘못되었습니다.
* 406 - IoT Edge 디바이스가 오프라인 상태이거나 상태 보고서를 전송하지 않습니다.
* 500 - IoT Edge 런타임에서 오류가 발생했습니다.

자세한 내용은 [IoT Edge 에이전트](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent)를 참조하세요.
