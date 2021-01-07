---
title: IoT Edge 버전 탐색 및 기록-Azure IoT Edge
description: 최신 릴리스의 새로운 기능에 대 한 정보를 제공 하는 IoT Edge의 새로운 기능을 알아봅니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cdb84f817f63e6401d17f18319e161f4c3477293
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024656"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Azure IoT Edge 버전 및 릴리스 정보

Azure IoT Edge는 GitHub에 호스트 된 오픈 소스 IoT Edge 프로젝트에서 빌드된 제품입니다. 모든 새 릴리스는 [Azure IoT Edge 프로젝트](https://github.com/Azure/azure-iotedge)에서 사용할 수 있습니다. 기여 및 버그 보고서는 [오픈 소스 IoT Edge 프로젝트](https://github.com/Azure/iotedge)에서 수행할 수 있습니다.

## <a name="documented-versions"></a>문서화 된 버전

이 사이트에 대 한 IoT Edge 설명서는 서로 다른 두 버전의 제품에 사용할 수 있으므로 IoT Edge 환경에 적용 되는 콘텐츠를 선택할 수 있습니다. 현재 지원 되는 두 가지 버전은 다음과 같습니다.

* **IoT Edge 1.0.10** 에는 [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10)의 최신 릴리스를 통해 모든 기능 및 기능이 포함 되어 있습니다.
* **IoT Edge 1.2 (미리 보기)** 에는 최신 미리 보기 릴리스에 포함 된 기능에 대 한 추가 내용이 포함 되어 있습니다. [1.2-rc1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1)
  * IoT Edge 1.2는 미리 보기 상태 이지만 릴리스 후보 버전을 설치 해야 합니다. 자세한 내용은 [오프 라인 또는 특정 버전 설치](how-to-install-iot-edge.md?tabs=linux#offline-or-specific-version-installation)를 참조 하세요.

## <a name="version-history"></a>버전 기록

이 표에서는 IoT Edge 패키지 릴리스에 대 한 최신 버전 기록을 제공 하며, 각 버전에 대해 적용 된 문서 업데이트를 강조 표시 합니다.

| 릴리스 정보 및 자산 | 형식 | Date | 주요 항목 |
| ------------------------ | ---- | ---- | ---------- |
| [1.2-rc1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | 미리 보기 | 2020년 11월 | [게이트웨이 뒤에 있는 장치 IoT Edge](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[IoT Edge MQTT broker](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | 2020년 10월 | [UploadSupportBundle direct 메서드](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[런타임 메트릭 업로드](how-to-access-built-in-metrics.md)<br>[경로 우선 순위 및 ttl (time-to-live)](module-composition.md#priority-and-time-to-live)<br>[모듈 시작 순서](module-composition.md#configure-modules)<br>[X.509 수동 프로 비전](how-to-manual-provision-x509.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | 2020년 3월 | [X.509 자동 프로 비전 DPS](how-to-auto-provision-x509-certs.md)<br>[RestartModule direct 메서드](how-to-edgeagent-direct-method.md#restart-module)<br>[지원-번들 명령](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>다음 단계

* [모든 Azure IoT Edge 릴리스 보기](https://github.com/Azure/azure-iotedge/releases)
* [피드백 포럼에서 기능 요청을 만들거나 검토 합니다.](https://feedback.azure.com/forums/907045-azure-iot-edge)