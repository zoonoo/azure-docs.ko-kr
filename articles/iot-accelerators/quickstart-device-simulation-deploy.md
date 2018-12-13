---
title: Azure에서 디바이스 시뮬레이션 솔루션 시도 및 실행 | Microsoft Docs
description: 이 빠른 시작에서는 Azure IoT 디바이스 시뮬레이션을 배포하고 시뮬레이션을 실행합니다.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/28/2018
ms.author: troyhop
ms.openlocfilehash: a109f3536ea8709313de3d1d6d17ce69c5652289
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50753936"
---
# <a name="quickstart-deploy-and-run-an-iot-device-simulation-in-azure"></a>빠른 시작: Azure에서 IoT 디바이스 시뮬레이션 배포 및 실행

이 빠른 시작에서는 Azure IoT 디바이스 시뮬레이션을 배포하여 IoT 솔루션을 테스트하는 방법을 보여 줍니다. 솔루션 가속기를 배포한 후 샘플 시뮬레이션을 실행하여 시작합니다.

이 빠른 시작을 완료하려면 활성 Azure 구독이 필요합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="deploy-device-simulation"></a>디바이스 시뮬레이션 배포

Azure 구독에 디바이스 시뮬레이션을 배포할 때 일부 구성 옵션을 설정해야 합니다.

Azure 계정 자격 증명을 사용하여 [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators)에 로그인합니다.

**장치 시뮬레이션** 타일을 클릭합니다.

![장치 시뮬레이션 선택](./media/quickstart-device-simulation-deploy/devicesimulation.png)

디바이스 시뮬레이션 설명 페이지에서 **지금 사용해보기**를 클릭합니다.

![지금 사용해보기 클릭](./media/quickstart-device-simulation-deploy/devicesimulationPDP.png)

**장치 시뮬레이션 솔루션 만들기** 페이지에서 고유한 **솔루션 이름**을 입력합니다.

솔루션 가속기를 배포하는 데 사용하려는 **구독** 및 **지역**을 선택합니다. 일반적으로, 자신에게 가장 가까운 지역을 선택합니다. 구독 내에서 [전역 관리자 또는 사용자](iot-accelerators-permissions.md)여야 합니다.

장치 시뮬레이션 솔루션과 함께 사용하려면 상자를 확인하여 IoT 허브를 배포합니다. 나중에 시뮬레이션이 사용하는 IoT 허브를 언제든 변경할 수 있습니다.

**솔루션 만들기** 를 클릭하여 솔루션의 프로비전을 시작합니다. 이 프로세스는 실행하는 데 5분 이상이 걸립니다.

![디바이스 시뮬레이션 솔루션 세부 정보](./media/quickstart-device-simulation-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>솔루션에 로그인

프로비전 프로세스가 완료되면 **시작** 단추를 클릭하여 디바이스 시뮬레이션의 인스턴스에 로그인할 수 있습니다.

![디바이스 시뮬레이션 열기](./media/quickstart-device-simulation-deploy/choosenew.png)

**수락**을 클릭하여 권한 요청을 수락하면 장치 시뮬레이션 솔루션 대시보드가 브라우저에 표시됩니다.

처음으로 열리면 **시작** 가이드가 있는 디바이스 시뮬레이션 대시보드가 표시됩니다. 첫 번째 타일을 클릭하여 샘플 시뮬레이션을 엽니다. **시작** 가이드를 닫는 경우 해당 타일을 클릭하여 대시보드에서 **샘플 간단한 시뮬레이션**을 열 수 있습니다.

![솔루션 대시보드](./media/quickstart-device-simulation-deploy/GettingStarted.png)

## <a name="sample-simulation"></a>샘플 시뮬레이션

샘플 시뮬레이션으로 편집할 수 없습니다. 시뮬레이션은 다음 설정으로 구성됩니다.

| 설정             | 값                       |
| ------------------- | --------------------------- |
| 대상 IoT Hub      | 미리 프로비전된 IoT Hub 사용 |
| 디바이스 모델        | 트럭                       |
| 디바이스 수   | 10                          |
| 원격 분석 빈도 | 10초                  |
| 시뮬레이션 기간 | 무기한 실행            |

![시뮬레이션 구성](./media/quickstart-device-simulation-deploy/SampleSimulation.png)

## <a name="run-the-simulation"></a>시뮬레이션 실행

**시뮬레이션 시작**을 클릭합니다. 시뮬레이션은 구성된 대로 무기한 실행됩니다. 언제든 **시뮬레이션 중지**를 클릭하여 시뮬레이션을 중지할 수 있습니다. 시뮬레이션은 현재 실행에 대한 통계를 보여줍니다.

![시뮬레이션 실행](./media/quickstart-device-simulation-deploy/runningsimulation.png)

디바이스 시뮬레이션 인스턴스에서 한 번에 하나의 시뮬레이션만 실행할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

추가 탐색하려는 경우 디바이스 시뮬레이션을 배포된 대로 둡니다.

디바이스 시뮬레이션이 더 이상 필요하지 않은 경우 해당 타일을 클릭한 다음, **솔루션 삭제**를 클릭하여 [프로비전된 솔루션](https://www.azureiotsolutions.com/Accelerators#dashboard) 페이지에서 삭제합니다.

![솔루션 삭제](media/quickstart-device-simulation-deploy/deletesolution.png)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 디바이스 시뮬레이션을 배포하고 샘플 IoT 디바이스 시뮬레이션을 실행했습니다.

> [!div class="nextstepaction"]
> [하나 이상의 장치 유형으로 시뮬레이션 만들기](iot-accelerators-device-simulation-create-simulation.md)