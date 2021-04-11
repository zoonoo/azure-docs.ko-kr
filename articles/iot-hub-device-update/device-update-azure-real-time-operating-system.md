---
title: Azure 실시간 운영 체제에 대한 디바이스 업데이트 | Microsoft Docs
description: Azure 실시간 운영 체제에 대한 디바이스 업데이트 시작
author: valls
ms.author: valls
ms.date: 3/18/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 66da860a5cdae1f5c7c18e4136b1f2d960492ca8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629056"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-azure-real-time-operating-system-rtos"></a>Azure RTOS(실시간 운영 체제)를 사용하는 Azure IoT Hub 자습서에 대한 디바이스 업데이트

이 자습서에서는 Azure RTOS NetX Duo에서 IoT Hub 에이전트에 대한 디바이스 업데이트를 만드는 방법을 안내합니다. 또한 개발자가 해당 애플리케이션에 디바이스 업데이트 기능을 통합하는 데 사용할 수 있는 간단한 API를 제공합니다. 시작 가이드가 포함된 주요 반도체 평가 보드의 [샘플](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU)을 탐색하여 디바이스에 무선(OTA) 업데이트를 구성, 빌드 및 배포하는 방법을 알아봅니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 시작
> * 디바이스 태그
> * 디바이스 그룹 만들기
> * 이미지 업데이트 배포
> * 업데이트 배포 모니터링

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소
* IoT Hub에 대한 액세스 권한. S1(표준) 계층 이상을 사용하는 것이 좋습니다.
* IoT Hub에 연결된 디바이스 업데이트 인스턴스 및 계정 이전에 수행하지 않은 경우 지침에 따라 디바이스 업데이트 계정을 [만들고 연결](http://create-device-update-account.md/)합니다.

## <a name="get-started"></a>시작

각 보드 관련 샘플 Azure RTOS 프로젝트에는 IoT Hub용 디바이스 업데이트를 사용하는 방법에 대한 코드와 문서가 포함되어 있습니다. 
1. [Azure RTOS 및 디바이스 업데이트 샘플](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU)에서 보드 관련 샘플 파일을 다운로드합니다.
2. 다운로드한 샘플에서 문서 폴더를 찾습니다.
3. 문서에서 Azure 리소스, 계정을 준비하고 여기에 IoT 디바이스를 등록하는 방법에 대한 단계를 수행합니다.
5. 다음으로, 문서에 따라 새 펌웨어 이미지를 빌드하고 보드에 대한 매니페스트를 가져옵니다.
6. 그런 다음, 펨웨어 이미지와 매니페스트를 IoT Hub용 디바이스 업데이트에 게시합니다.
7. 마지막으로 디바이스에서 프로젝트를 다운로드하여 실행합니다.

[Azure RTOS](https://docs.microsoft.com/azure/rtos/)에 대해 자세히 알아봅니다.  

## <a name="tag-your-device"></a>디바이스 태그

1. 이전 단계에서 실행 중인 디바이스 애플리케이션을 유지합니다.
2. [Azure Portal](https://portal.azure.com)에 로그인하여 IoT Hub로 이동합니다.
3. 왼쪽 탐색 창의 'IoT 디바이스'에서 IoT 디바이스를 찾아 디바이스 쌍으로 이동합니다.
4. 디바이스 쌍에서 기존 디바이스 업데이트 태그 값을 null로 설정하여 삭제합니다.
5. 아래와 같이 새 디바이스 업데이트 태그 값을 추가합니다.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="create-update-group"></a>업데이트 그룹 만들기

1. 이전에 디바이스 업데이트 인스턴스에 연결한 IoT Hub로 이동합니다.
2. 왼쪽 탐색 모음에서 자동 디바이스 관리 아래에 있는 디바이스 업데이트 옵션을 선택합니다.
3. 페이지 맨 위에서 그룹 탭을 선택합니다. 
4. 추가 단추를 선택하여 새 그룹을 만듭니다.
5. 이전 단계에서 만든 IoT Hub 태그를 목록에서 선택합니다. 업데이트 그룹 만들기를 선택합니다.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="태그 선택을 보여 주는 스크린샷" lightbox="media/create-update-group/select-tag.PNG":::

태그를 추가하고 업데이트 그룹을 만드는 방법에 대해 [자세히 알아보기](create-update-group.md)

## <a name="deploy-new-firmware"></a>새 펌웨어 배포

1. 그룹이 만들어지면 업데이트 보류 중 업데이트에 대한 링크를 사용하여 디바이스 그룹에 사용할 수 있는 새 업데이트가 표시됩니다. 한 번 새로 고쳐야 할 수 있습니다. 
2. 사용 가능한 업데이트를 클릭합니다.
3. 올바른 그룹이 대상 그룹으로 선택되었는지 확인합니다. 배포를 예약한 후 업데이트 배포를 선택합니다.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="업데이트 선택" lightbox="media/deploy-update/select-update.png":::

4. 규정 준수 차트를 봅니다. 이제 업데이트가 진행 중인 것으로 표시됩니다. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="업데이트 진행 중" lightbox="media/deploy-update/update-in-progress.png":::

5. 디바이스를 성공적으로 업데이트한 후에는 규정 준수 차트와 배포 세부 정보가 업데이트되어 동일한 내용을 반영할 것입니다. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="업데이트 성공" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>업데이트 배포 모니터링

1. 페이지 맨 위에서 배포 탭을 선택합니다.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="배포 탭" lightbox="media/deploy-update/deployments-tab.png":::

2. 배포 세부 정보를 보려면 만든 배포를 선택합니다.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="배포 세부 정보" lightbox="media/deploy-update/deployment-details.png":::

3. 최신 상태 세부 정보를 보려면 새로 고침을 선택합니다. 상태가 성공으로 변경될 때까지 이 프로세스를 계속합니다.

이제 Raspberry Pi 3 B+ 디바이스에서 IoT Hub에 대한 디바이스 업데이트를 사용하여 엔드투엔드 이미지 업데이트를 성공적으로 완료했습니다. 

## <a name="cleanup-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 디바이스 업데이트 계정, 인스턴스, IoT Hub 및 IoT 디바이스를 정리합니다. 

## <a name="next-steps"></a>다음 단계

Azure RTOS 및 Azure IoT에서 작동하는 방식에 대해 자세히 알아보려면 https://azure.com/rtos 를 참조하세요.
