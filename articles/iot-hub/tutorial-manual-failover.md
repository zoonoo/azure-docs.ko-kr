---
title: Azure IoT Hub의 수동 장애 조치 | Microsoft Docs
description: Azure IoT Hub에 대해 수동 장애 조치를 수행하는 방법을 보여줍니다.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: bd9cb76557c65832de5d249cdccdc36101edf646
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55821289"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub-public-preview"></a>자습서: IoT Hub에 대해 수동 장애 조치(failover) 수행(공개 미리 보기)

수동 장애 조치는 고객이 해당 허브의 작업을 주 지역에서 해당 Azure 지역 쌍을 이루는 지역으로 [장애 조치](https://en.wikipedia.org/wiki/Failover)할 수 있는 IoT Hub 서비스의 기능입니다. 수동 장애 조치는 지역 재해 또는 확장된 서비스 중단 발생 시 수행될 수 있습니다. 프로덕션에서 실행 중인 기능보다 테스트 IoT Hub를 사용하는 것이 좋지만 재해 복구 기능을 테스트하기 위해 계획된 장애 조치를 수행할 수도 있습니다. 수동 장애 조치 기능은 추가 비용 없이 고객에게 제공됩니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 IoT Hub를 만듭니다. 
> * 장애 조치(failover)를 수행합니다. 
> * 보조 위치에서 실행되는 허브를 참조하세요.
> * 장애 복구를 수행하여 기본 위치에 IoT Hub의 작업을 반환합니다. 
> * 허브가 올바른 위치에서 올바르게 실행 중인지 확인합니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

2. **+ 리소스 만들기**를 클릭하고, **사물 인터넷**을 선택한 다음, **IoT Hub**를 선택합니다.

   ![IoT Hub를 만드는 방법을 보여주는 스크린샷](./media/tutorial-manual-failover/create-hub-01.png)

3. **기본 사항** 탭을 선택합니다. 다음 필드를 입력합니다.

    **구독**: 사용할 Azure 구독을 선택합니다.

    **리소스 그룹**: **새로 만들기**를 클릭하고, 리소스 그룹 이름에 **ManlFailRG**를 지정합니다.

    **지역**: 미리 보기의 일부인 가까운 지역을 선택합니다. 이 자습서에서는 `westus2`를 사용합니다. Azure 지역 쌍을 이루는 지역 간에서만 장애 조치를 수행할 수 있습니다. westus2를 사용하는 지역 쌍을 이루는 지역은 WestCentralUS입니다.
    
   > [!NOTE]
   > 수동 장애 조치(failover)는 현재 공개 미리 보기 상태이며 다음 Auzre 지역에서는 제공되지 *않습니다*. 미국 동부, 미국 서부, 북유럽, 유럽 서부, 브라질 남부, 미국 중남부.

   **IoT Hub이름**: IoT Hub의 이름을 지정합니다. 허브 이름은 전역적으로 고유해야 합니다. 

   ![IoT Hub를 만드는 기본 사항 창을 보여주는 스크린샷](./media/tutorial-manual-failover/create-hub-02-basics.png)

   **검토 + 만들기**를 클릭합니다. (크기 및 규모에 대해 기본값을 사용합니다.) 

4. 요약을 검토한 다음, **만들기**를 클릭하여 IoT Hub를 만듭니다. 

   ![IoT Hub를 만드는 마지막 단계를 보여주는 스크린샷](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>수동 장애 조치(failover) 수행

IoT Hub에 대해 하루 최대 두 번의 장애 조치 및 두 번의 장애 복구가 발생합니다.

1. **리소스 그룹**을 클릭한 다음, 리소스 그룹 **ManlFailRG**를 선택합니다. 리소스 목록에서 허브를 클릭합니다. 

2. IoT Hub 창의 **복원력**에서 **수동 장애 조치(미리 보기)** 를 클릭합니다. 허브에서 유효한 영역을 설정하지 않으면 수동 장애 조치 옵션이 사용할 수 없게 됩니다.

   ![IoT Hub 속성 창을 보여주는 스크린샷](./media/tutorial-manual-failover/trigger-failover-01.png)

3. 수동 장애 조치 창에 **IoT Hub에 대한 기본 위치** 및 **IoT Hub에 대한 보조 위치**를 표시합니다. 기본 위치는 초기에 IoT Hub를 만들 때 지정한 위치에 설정되고 항상 허브가 현재 활성화된 위치를 나타냅니다. 보조 위치는 기본 위치에 페어링된 표준 [Azure 지역 쌍을 이루는 지역](../best-practices-availability-paired-regions.md)입니다. 위치 값을 변경할 수 없습니다. 이 자습서의 경우 기본 위치가 `westus2`이며 보조 위치는 `WestCentralUS`입니다.

   ![수동 장애 조치 창을 보여주는 스크린샷](./media/tutorial-manual-failover/trigger-failover-02.png)

3. 수동 장애 조치 창의 맨 위에서 **장애 조치 시작**을 클릭합니다. **수동 장애 조치 확인** 창이 표시됩니다. 장애 조치할 항목이 있는지 확인하려는 IoT Hub의 이름을 입력합니다. 그런 다음, 장애 조치를 시작하려면 **확인**을 클릭합니다.

   수동 장애 조치를 수행하는 데 걸리는 시간은 허브에 등록된 디바이스의 수에 비례합니다. 예를 들어 100,000개의 디바이스가 있는 경우 15분 정도가 걸릴 수 있지만 5백만 개의 디바이스가 있는 경우 1시간 이상 걸릴 수 있습니다.

4. **수동 장애 조치(failover) 확인** 창에서 장애 조치할 항목이 있는지 확인하려는 IoT Hub의 이름을 입력합니다. 그런 다음, 장애 조치를 시작하려면 확인을 클릭합니다. 

   ![수동 장애 조치 창을 보여주는 스크린샷](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   수동 장애 조치 프로세스가 실행되는 동안 수동 장애 조치 창에 수동 장애 조치가 진행 중임을 알려주는 배너가 표시됩니다. 

   ![진행 중인 수동 장애 조치를 보여주는 스크린샷](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   IoT Hub 창을 닫고 리소스 그룹 창에서 클릭하여 다시 여는 경우 허브가 활성화되었음을 알려주는 배너가 표시됩니다. 

   ![비활성화된 IoT Hub를 보여주는 스크린샷](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   작업이 완료되면 수동 장애 조치 페이지에서 기본 및 보조 지역이 대칭 이동하고 허브가 다시 활성화됩니다. 이 예제에서 현재 기본 위치가 `WestCentralUS`이며 보조 위치는 `westus2`입니다. 

   ![장애 조치가 완료되었음을 보여주는 스크린샷](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

## <a name="perform-a-failback"></a>장애 복구(failback) 수행 

수동 장애 조치를 수행한 후에 허브의 작업을 원래 주 지역으로 다시 전환할 수 있습니다. 이를 장애 복구라고 합니다. 장애 조치를 수행한 경우 장애 복구를 요청하기 전에 1시간 동안 수신 대기해야 합니다. 짧은 시간 내에 장애 복구를 수행하려면 오류 메시지가 표시됩니다.

장애 복구는 수동 장애 조치처럼 수행됩니다. 단계는 다음과 같습니다. 

1. 장애 복구를 수행하려면 Iot Hub에 대한 Iot Hub 창으로 돌아갑니다.

2. IoT Hub 창의 **복원력**에서 **수동 장애 조치(미리 보기)** 를 클릭합니다. 

3. 수동 장애 조치 창의 맨 위에서 **장애 조치 시작**을 클릭합니다. **수동 장애 조치 확인** 창이 표시됩니다. 

4. **수동 장애 조치(failover) 확인** 창에서 장애 복구(failback)할 항목이 있는지 확인하려는 IoT Hub의 이름을 입력합니다. 그런 다음, 장애 복구를 시작하려면 확인을 클릭합니다. 

   ![수동 장애 복구 요청 스크린샷](./media/tutorial-manual-failover/trigger-failback-01-regions.png)

   배너는 장애 조치 수행 섹션에 설명된 대로 표시됩니다. 장애 복구를 완료한 후에 원래 설정된 대로 `westus2`를 기본 위치로 표시하고 `WestCentralUS`를 보조 위치로 표시합니다.

## <a name="clean-up-resources"></a>리소스 정리 

이 자습서에서 만든 리소스를 제거하려면 리소스 그룹을 삭제합니다. 이렇게 하면 그룹 내에 포함된 모든 리소스가 삭제됩니다. 이 경우에 IoT Hub 및 리소스 그룹 자체가 제거됩니다. 

1. **리소스 그룹**을 클릭합니다. 

2. 리소스 그룹 **ManlFailRG**를 찾고 선택합니다. 클릭하여 엽니다. 

3. **리소스 그룹 삭제**를 클릭합니다. 메시지가 표시되면 리소스 그룹의 이름을 입력하고 **삭제**를 클릭하여 확인합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 수동 장애 조치를 구성하고 수행하는 방법 및 다음 작업을 수행하여 장애 복구를 요청하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 IoT Hub를 만듭니다. 
> * 장애 조치(failover)를 수행합니다. 
> * 보조 위치에서 실행되는 허브를 참조하세요.
> * 장애 복구를 수행하여 기본 위치에 IoT Hub의 작업을 반환합니다. 
> * 허브가 올바른 위치에서 올바르게 실행 중인지 확인합니다.

IoT 디바이스의 상태를 관리하는 방법에 대해 알아보려면 다음 자습서로 이동합니다. 

> [!div class="nextstepaction"]
[IoT 장치의 상태 관리](tutorial-device-twins.md)
