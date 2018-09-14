---
title: IoT의 사용자 지정 시뮬레이트된 장치 배포 - Azure | Microsoft Docs
description: 이 방법 가이드에서는 장치 시뮬레이션 솔루션 가속기에 사용자 지정 시뮬레이트된 장치를 배포하는 방법을 보여 줍니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/14/2018
ms.topic: conceptual
ms.openlocfilehash: e51d0330c3ed804bff8cdb06265bb8c39141733f
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43346497"
---
# <a name="deploy-a-new-simulated-device"></a>시뮬레이트된 새 장치 배포

원격 모니터링 및 장치 시뮬레이션 솔루션 가속기 둘 다에서 시뮬레이트된 장치를 정의할 수 있습니다. 이 문서에서는 장치 시뮬레이션 솔루션 가속기에 사용자 지정된 냉각기 장치 유형 및 새 전구 장치 유형을 배포하는 방법을 보여 줍니다.

이 문서의 단계에서는 [시뮬레이트된 새 장치 만들기 및 테스트](iot-accelerators-remote-monitoring-create-simulated-device.md) 방법 가이드를 완료했으며, 사용자 지정된 냉각기 및 새 전구 장치 유형을 정의하는 파일이 있다고 가정합니다.

이 방법 가이드의 단계는 다음 작업을 수행하는 방법을 제공합니다.

1. SSH를 사용하여 장치 시뮬레이션 솔루션 가속기를 호스트하는 가상 머신의 파일 시스템에 액세스합니다.

1. Docker 컨테이너 외부 위치에서 장치 모델을 로드하도록 Docker를 구성합니다.

1. 사용자 지정 장치 모델 파일을 사용하여 시뮬레이션을 실행합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

이 방법 가이드의 단계를 완료하려면 활성 Azure 구독이 필요합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 방법 가이드를 수행하려면 다음이 필요합니다.

- [장치 시뮬레이션 솔루션 가속기](https://www.azureiotsolutions.com/Accelerators#solutions/types/DS)의 배포된 인스턴스
- `ssh` 및 `scp` 명령을 실행하기 위한 로컬 **bash** 셸. Windows에서 **bash**를 설치하는 쉬운 방법은 [git](https://git-scm.com/download/win)을 설치하는 것입니다.
- [시뮬레이트된 새 장치 만들기 및 테스트](iot-accelerators-remote-monitoring-create-simulated-device.md)에 설명된 것과 같은 사용자 지정 장치 모델 파일

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Docker 구성

이 섹션에서는 Docker 컨테이너 내부가 아닌 가상 머신의 **/tmp/devicemodels** 폴더에서 장치 모델 파일을 로드하도록 Docker를 구성합니다. 이 섹션의 명령은 로컬 컴퓨터의 **bash** 셸에서 실행합니다.

1. SSH를 사용하여 로컬 컴퓨터에서 Azure의 가상 머신에 연결합니다. 다음 명령은 가상 머신의 공용 IP 주소 **vm-vikxv**가 **104.41.128.108**이라고 가정합니다. 이 값을 이전 섹션의 가상 머신 공용 IP 주소로 바꿉니다.

   ```sh
    ssh azureuser@104.41.128.108
    ```

    지시에 따라 이전 섹션에서 설정한 암호를 사용하여 가상 머신에 로그인합니다.

1. 컨테이너 외부에서 장치 모델을 로드하도록 장치 시뮬레이션 서비스를 구성합니다. 먼저 Docker 구성 파일을 엽니다.

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    **devicesimulation** 컨테이너에 대한 설정을 찾고 다음 코드 조각에 표시된 것처럼 **volumes** 설정을 편집합니다.

    ```yml
    # To mount custom device models, upload the files to the VM, edit and uncomment the following line:
          - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    # To mount a custom service configuration, create a custom file, edit and uncomment the following line:
    #     - /app/custom-device-simulation-appsettings.ini:/app/webservice/appsettings.ini:ro
    ```

    변경 내용을 저장합니다.

1. JSON 및 스크립트 파일을 저장할 폴더를 만듭니다.

    ```sh
    sudo mkdir -p /tmp/devicemodels/scripts
    ```

    **bash** 창에서 SSH 세션을 열린 상태로 둡니다.

1. 가상 머신에 사용자 지정 장치 모델 파일을 복사합니다. 이 명령은 사용자 지정 장치 모델을 만든 컴퓨터의 다른 **bash** 셸에서 실행합니다. 먼저, 장치 모델 JSON 파일이 포함된 로컬 폴더로 이동합니다. 다음 명령은 가상 머신의 공용 IP 주소가 **104.41.128.108**이라고 가정합니다. 이 값을 가상 머신 공용 IP 주소로 바꿉니다. 가상 머신 암호가 요구되면 입력합니다.

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. 장치 시뮬레이션 Docker 컨테이너를 다시 시작하여 새 장치 모델을 사용합니다. 가상 머신에 대한 SSH 세션이 열려 있는 **bash** 셸에서 다음 명령을 실행합니다.

    ```sh
    sudo /app/start.sh
    ```

    실행 중인 Docker 컨테이너의 상태 및 해당 컨테이너 ID를 확인하려는 경우 다음 명령을 사용합니다.

    ```sh
    docker ps
    ```

    장치 시뮬레이션 컨테이너에서 로그를 확인하려는 경우 다음 명령을 실행합니다. 컨테이너 ID를 장치 시뮬레이션 컨테이너의 ID로 바꿉니다.

    ```sh
    docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>시뮬레이션 실행

이제 사용자 지정 장치 모델을 사용하여 시뮬레이션을 실행할 수 있습니다.

1. [Microsoft Azure IoT 솔루션 가속기](https://www.azureiotsolutions.com/Accelerators#dashboard)에서 장치 시뮬레이션 웹 UI를 시작합니다.

1. 웹 UI에서 사용자 지정 장치 모델 중 하나를 사용하여 시뮬레이션을 구성하고 실행합니다.

1. 시뮬레이션을 실행할 때 **Azure Portal에서 IoT Hub 메트릭 보기**를 클릭하여 시뮬레이션을 모니터링합니다. 또는 다음 Azure CLI 명령을 사용하여 장치의 클라우드 트래픽을 모니터링할 수 있습니다. IoT Hub의 이름을 허브 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub monitor-events -n contoso-simulation9dc75
    ```

## <a name="clean-up-resources"></a>리소스 정리

추가 탐색하려는 경우 장치 시뮬레이션 솔루션 가속기를 배포된 대로 둡니다.

솔루션 가속기가 더 이상 필요하지 않은 경우 선택한 다음, **솔루션 삭제**를 클릭하여 [프로비전된 솔루션](https://www.azureiotsolutions.com/Accelerators#dashboard) 페이지에서 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 장치 시뮬레이션 솔루션 가속기에 사용자 지정 장치 모델을 배포하는 방법을 살펴보았습니다. 다음 단계로 [장치 모델 스키마](iot-accelerators-device-simulation-device-schema.md)를 자세히 알아보는 것이 좋습니다.
