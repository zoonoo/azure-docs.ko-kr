---
title: IoT Edge 버전 탐색 및 기록-Azure IoT Edge
description: 최신 릴리스의 새로운 기능에 대 한 정보를 제공 하는 IoT Edge의 새로운 기능을 알아봅니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 877b488fc888b4ced8165c19bcd5c51b5f982745
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492509"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Azure IoT Edge 버전 및 릴리스 정보

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge는 GitHub에 호스트 된 오픈 소스 IoT Edge 프로젝트에서 빌드된 제품입니다. 모든 새 릴리스는 [Azure IoT Edge 프로젝트](https://github.com/Azure/azure-iotedge)에서 사용할 수 있습니다. 기여 및 버그 보고서는 [오픈 소스 IoT Edge 프로젝트](https://github.com/Azure/iotedge)에서 수행할 수 있습니다.

## <a name="documented-versions"></a>문서화 된 버전

이 사이트에 대 한 IoT Edge 설명서는 서로 다른 두 버전의 제품에 사용할 수 있으므로 IoT Edge 환경에 적용 되는 콘텐츠를 선택할 수 있습니다. 현재 지원 되는 두 가지 버전은 다음과 같습니다.

* **LTS (IoT Edge 1.1** )는 IoT Edge의 lts (장기 지원) 버전입니다. 이 버전에 대 한 설명서에서는 모든 이전 버전의 모든 기능 및 기능을 1.1 이상 다룹니다. 이 설명서 버전은 지원 되는 버전 1.1의 수명 동안 안정적 이며 이후 버전에서 릴리스된 새로운 기능을 반영 하지 않습니다. 1.1 릴리스는 일반적으로 사용 가능한 최신 버전의 IoT Edge입니다.
* **IoT Edge 1.2 (미리 보기)** 에는 최신 미리 보기 릴리스에 포함 된 기능에 대 한 추가 내용이 포함 되어 있습니다. [1.2-rc4](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc4).

IoT Edge 릴리스에 대 한 자세한 내용은 [지원 되는 시스템 Azure IoT Edge](support.md)을 참조 하세요.

## <a name="version-history"></a>버전 기록

이 표에서는 IoT Edge 패키지 릴리스에 대 한 최신 버전 기록을 제공 하며, 각 버전에 대해 적용 된 문서 업데이트를 강조 표시 합니다.

| 릴리스 정보 및 자산 | Type | 날짜 | 주요 항목 |
| ------------------------ | ---- | ---- | ---------- |
| [1.2-rc4](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | 미리 보기 | 2021년 3월 | 새로운 설치 및 구성 단계를 포함 하는 새로운 IoT Edge 패키지가 도입 되었습니다. 자세한 내용은 [1.0 또는 1.1에서 1.2로 업데이트](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)를 참조 하세요.
| [1.1](https://github.com/Azure/azure-iotedge/releases/tag/1.1.0) | LTS(장기 지원) | 2021년 2월 | [장기적인 지원 계획 및 지원 되는 시스템 업데이트](support.md) |
| [1.2-rc1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | 미리 보기 | 2020년 11월 | [게이트웨이 뒤에 있는 장치 IoT Edge](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[IoT Edge MQTT broker](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | 2020년 10월 | [UploadSupportBundle direct 메서드](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[런타임 메트릭 업로드](how-to-access-built-in-metrics.md)<br>[경로 우선 순위 및 ttl (time-to-live)](module-composition.md#priority-and-time-to-live)<br>[모듈 시작 순서](module-composition.md#configure-modules)<br>[X.509 수동 프로 비전](how-to-register-device.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | 2020년 3월 | [X.509 자동 프로 비전 DPS](how-to-auto-provision-x509-certs.md)<br>[RestartModule direct 메서드](how-to-edgeagent-direct-method.md#restart-module)<br>[지원-번들 명령](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>다음 단계

* [모든 Azure IoT Edge 릴리스 보기](https://github.com/Azure/azure-iotedge/releases)
* [피드백 포럼에서 기능 요청을 만들거나 검토 합니다.](https://feedback.azure.com/forums/907045-azure-iot-edge)