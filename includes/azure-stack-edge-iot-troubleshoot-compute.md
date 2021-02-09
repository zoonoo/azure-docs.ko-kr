---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 02/05/2021
ms.openlocfilehash: b06b91e972fd07543cf02105360cb0400ef6b0f1
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831549"
---
IoT Edge 에이전트 런타임 응답을 사용 하 여 계산 관련 오류 문제를 해결할 수 있습니다. 가능한 응답 목록은 다음과 같습니다.

* 200 - 확인
* 400 - 배포 구성이 잘못되었거나 유효하지 않습니다.
* 417-장치에 배포 구성 집합이 없습니다.
* 412 - 배포 구성의 스키마 버전이 잘못되었습니다.
* 406 - IoT Edge 디바이스가 오프라인 상태이거나 상태 보고서를 전송하지 않습니다.
* 500 - IoT Edge 런타임에서 오류가 발생했습니다.

자세한 내용은 [IoT Edge 에이전트](/azure/iot-edge/iot-edge-runtime?view=iotedge-2018-06&preserve-view=true#iot-edge-agent)를 참조 하세요.

다음 오류는 Azure Stack Edge Pro의 IoT Edge 서비스와 관련 되어 있습니다.<!--/ Data Box Gateway--> 답을 찾으세요.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>계산 모듈은 알 수 없는 상태 이므로 사용할 수 없습니다.

#### <a name="error-description"></a>오류 설명

장치에 있는 모든 모듈은 알 수 없는 상태를 표시 하며 사용할 수 없습니다. 알 수 없음 상태는 다시 부팅을 통해 유지 됩니다.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>추천 솔루션

IoT Edge 서비스를 삭제 한 다음 모듈을 다시 배포 합니다. 자세한 내용은 [IoT Edge 서비스 제거](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service)를 참조 하세요.
