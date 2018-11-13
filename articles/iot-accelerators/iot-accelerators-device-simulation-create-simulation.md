---
title: 새 IoT 시뮬레이션 만들기 - Azure | Microsoft Docs
description: 이 자습서에서는 장치 시뮬레이션을 사용하여 새 시뮬레이션을 만들고 실행합니다.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: 268efa35f63ac8b9e0c6ea3f22a71242a2984726
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50756781"
---
# <a name="tutorial-create-and-run-an-iot-device-simulation"></a>자습서: IoT 장치 시뮬레이션 만들기 및 실행

이 자습서에서는 장치 시뮬레이션을 사용하여 하나 이상의 시뮬레이션된 장치를 사용하는 새 IoT 시뮬레이션을 만들고 실행합니다.

이 자습서에서는 다음을 수행했습니다.

>[!div class="checklist"]
> * 모든 활성 및 과거 시뮬레이션 보기
> * 새 시뮬레이션 만들기 및 실행
> * 시뮬레이션에 대한 메트릭 보기
> * 시뮬레이션 중지

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 실행하려면 Azure 구독에서 장치 시뮬레이션의 배포된 인스턴스가 필요합니다.

장치 시뮬레이션을 아직 배포하지 않은 경우 [Azure에서 IoT 장치 시뮬레이션 배포 및 실행](quickstart-device-simulation-deploy.md) 빠른 시작을 완료해야 합니다.

## <a name="open-device-simulation"></a>장치 시뮬레이션 열기

브라우저에서 장치 시뮬레이션을 실행하려면 먼저 [Microsoft Azure IoT 솔루션 가속기](https://www.azureiotsolutions.com)로 이동합니다. 

Azure 구독 자격 증명을 사용하여 로그인하라는 메시지가 표시될 수 있습니다.

그런 다음, [빠른 시작](quickstart-device-simulation-deploy.md)에서 배포한 장치 시뮬레이션에 대한 타일에서 **시작**을 클릭합니다.

## <a name="view-simulations"></a>시뮬레이션 보기

메뉴 모음에서 **시뮬레이션**을 선택합니다. **시뮬레이션** 페이지는 사용 가능한 모든 시뮬레이션에 대한 정보를 표시합니다. 이 페이지에서 현재 실행되고 있는 시뮬레이션 및 이전에 실행한 시뮬레이션을 볼 수 있습니다. 이전 시뮬레이션을 다시 실행하려면 시뮬레이션 타일을 클릭하여 시뮬레이션 세부 정보를 엽니다.

![시뮬레이션](media/iot-accelerators-device-simulation-create-simulation/dashboard.png)

## <a name="create-a-simulation"></a>시뮬레이션 만들기

시뮬레이션을 만들려면 오른쪽 위 모서리에서 **+ 새 시뮬레이션**을 클릭합니다.

시뮬레이션은 하나 이상의 장치 모델로 구성됩니다. 장치 모델은 시뮬레이션될 장치의 동작, 원격 분석 및 메시지 형식을 정의합니다.

장치 모델을 추가하려면 **+ 장치 유형 추가**를 클릭하고 목록에서 장치 모델을 선택합니다. 시뮬레이션은 둘 이상의 장치 모델을 포함할 수 있습니다. 각 장치 모델에는 다양한 장치 수 및 메시지 빈도가 있을 수 있습니다.

새 시뮬레이션 양식이 완료되면 **시뮬레이션 시작**을 클릭하여 시뮬레이션을 시작합니다.

시뮬레이션된 장치 수에 따라 시뮬레이션을 구성 및 시작하는 데 몇 분 정도 걸릴 수 있습니다.

![새 시뮬레이션](media/iot-accelerators-device-simulation-create-simulation/newsimulation.png)

## <a name="stop-a-simulation"></a>시뮬레이션 중지

**시뮬레이션 시작**을 클릭하면 시뮬레이션 세부 정보 페이지가 표시됩니다. 이 세부 정보 페이지는 IoT Hub의 실시간 시뮬레이션 통계 및 메트릭을 표시합니다. **시뮬레이션** 페이지에서 시뮬레이션의 타일을 클릭하여 이 세부 정보 페이지로 이동할 수도 있습니다.

시뮬레이션을 중지하려면 오른쪽 위의 작업 표시줄에서 **시뮬레이션 중지**를 클릭합니다.

![시뮬레이션 중지](media/iot-accelerators-device-simulation-create-simulation/simulationdetails.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 시뮬레이션을 생성, 실행 및 중지하는 방법을 알아보았습니다. 또한 시뮬레이션 세부 정보를 확인하는 방법을 배웠습니다. 시뮬레이션을 실행하는 방법에 대해 자세히 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [사용자 지정 시뮬레이션된 장치 만들기](iot-accelerators-device-simulation-create-custom-device.md)
