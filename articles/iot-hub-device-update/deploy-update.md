---
title: Azure IoT Hub에 대한 디바이스 업데이트를 사용하여 업데이트 배포 | Microsoft Docs
description: Azure IoT Hub에 대한 디바이스 업데이트를 사용하여 업데이트를 배포합니다.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 0a11c8e8946229941c1fe60f7f2ce84d9fadb2ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101679514"
---
# <a name="deploy-an-update-using-device-update-for-iot-hub"></a>IoT Hub에 대한 디바이스 업데이트를 사용하여 업데이트 배포

IoT Hub에 대한 디바이스 업데이트를 사용하여 IoT 디바이스에 업데이트를 배포하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

* [IoT Hub용 디바이스 업데이트를 사용으로 설정하여 IoT Hub에 액세스합니다](create-device-update-account.md). IoT Hub에 대해 S1(표준) 계층 이상을 사용하는 것이 좋습니다. 
* [프로비전된 디바이스에 대한 업데이트를 하나 이상 가져왔습니다.](import-update.md) 
* IoT Hub에서 디바이스 업데이트를 위해 프로비저닝된 IoT 디바이스(또는 시뮬레이터).
* [업데이트하려는 IoT 디바이스에 태그가 할당되었습니다. 디바이스가 하나 이상의 업데이트 그룹에 속해 있습니다.](create-update-group.md)
* 지원되는 브라우저:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="deploy-an-update"></a>업데이트 배포

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

2. IoT Hub의 디바이스 업데이트 블레이드로 이동합니다.

  :::image type="content" source="media/deploy-update/device-update-iot-hub.png" alt-text="IoT Hub" lightbox="media/deploy-update/device-update-iot-hub.png":::

3. 페이지 맨 위에서 그룹 탭을 선택합니다. 디바이스 그룹에 대해 [자세히 알아봅니다](device-update-groups.md). 

  :::image type="content" source="media/deploy-update/updated-view.png" alt-text="그룹 탭" lightbox="media/deploy-update/updated-view.png":::

4. 업데이트 규정 준수 차트 및 그룹 목록을 표시합니다. 보류 중인 업데이트 아래에 업데이트 링크와 함께 디바이스 그룹에 사용할 수 있는 새 업데이트가 표시되어야 합니다(한 번 새로 고쳐야 할 수 있음). [업데이트 준수에 대해 자세히 알아보세요.](device-update-compliance.md) 

5. 사용 가능한 업데이트를 클릭합니다.

6. 올바른 그룹이 대상 그룹으로 선택되었는지 확인합니다. 배포를 예약한 후 업데이트 배포를 선택합니다.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="업데이트 선택" lightbox="media/deploy-update/select-update.png":::

7. 규정 준수 차트를 봅니다. 이제 업데이트가 진행 중인 것으로 표시됩니다. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="업데이트 진행 중" lightbox="media/deploy-update/update-in-progress.png":::

8. 디바이스를 성공적으로 업데이트한 후에는 규정 준수 차트와 배포 세부 정보가 업데이트되어 동일한 내용을 반영할 것입니다. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="업데이트 성공" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>업데이트 배포 모니터링

1. 페이지 맨 위에서 배포 탭을 선택합니다.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="배포 탭" lightbox="media/deploy-update/deployments-tab.png":::

2. 배포 세부 정보를 보려면 만든 배포를 선택합니다.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="배포 세부 정보" lightbox="media/deploy-update/deployment-details.png":::

3. 최신 상태 세부 정보를 보려면 새로 고침을 선택합니다. 상태가 성공으로 변경될 때까지 이 프로세스를 계속합니다.


## <a name="retry-an-update-deployment"></a>업데이트 배포 다시 시도

어떤 이유로 배포가 실패하는 경우, 실패한 디바이스에 배포를 다시 시도할 수 있습니다. 

1. 배포 탭으로 이동하여 실패한 배포를 선택합니다. 

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="배포 세부 정보" lightbox="media/deploy-update/deployment-details.png":::

2. 자세한 배포 정보 창에서 "실패" 디바이스 상태를 클릭합니다.

3. "실패한 디바이스 다시 시도"를 클릭하고 확인 알림을 승인합니다. 

## <a name="next-steps"></a>다음 단계

[일반적인 문제 해결](troubleshoot-device-update.md)
