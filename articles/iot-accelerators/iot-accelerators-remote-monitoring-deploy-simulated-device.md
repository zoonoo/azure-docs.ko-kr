---
title: IoT의 사용자 지정 시뮬레이트된 디바이스 배포 - Azure | Microsoft Docs
description: 이 방법 가이드에서는 원격 모니터링 솔루션 가속기에 사용자 지정 시뮬레이트된 디바이스를 배포하는 방법을 보여 줍니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: 7cbab38db859935c9f4490d79a131d6c9a7e302b
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427573"
---
# <a name="deploy-a-new-simulated-device"></a>시뮬레이트된 새 디바이스 배포

원격 모니터링 및 디바이스 시뮬레이션 솔루션 가속기 둘 다에서 시뮬레이트된 디바이스를 정의할 수 있습니다. 이 문서에서는 원격 모니터링 솔루션 가속기에 사용자 지정된 냉각기 디바이스 유형 및 새 전구 디바이스 유형을 배포하는 방법을 보여 줍니다.

이 문서의 단계에서는 [시뮬레이트된 새 디바이스 만들기 및 테스트](iot-accelerators-remote-monitoring-create-simulated-device.md) 방법 가이드를 완료했으며, 사용자 지정된 냉각기 및 새 전구 디바이스 유형을 정의하는 파일이 있다고 가정합니다.

이 방법 가이드의 단계는 다음 작업을 수행하는 방법을 제공합니다.

1. SSH를 사용하여 원격 모니터링 솔루션 가속기를 호스트하는 가상 머신의 파일 시스템에 액세스합니다.

1. Docker 컨테이너 외부 위치에서 디바이스 모델을 로드하도록 Docker를 구성합니다.

1. 사용자 지정 디바이스 모델 파일을 사용하여 원격 모니터링 솔루션 가속기를 실행합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

이 방법 가이드의 단계를 완료하려면 활성 Azure 구독이 필요합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 방법 가이드를 수행하려면 다음이 필요합니다.

- [원격 모니터링 솔루션 가속기](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2)의 배포된 인스턴스
- `ssh` 및 `scp` 명령을 실행하기 위한 로컬 **bash** 셸. Windows에서 **bash**를 설치하는 쉬운 방법은 [git](https://git-scm.com/download/win)을 설치하는 것입니다.
- [시뮬레이트된 새 디바이스 만들기 및 테스트](iot-accelerators-remote-monitoring-create-simulated-device.md)에 설명된 것과 같은 사용자 지정 디바이스 모델 파일

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Docker 구성

이 섹션에서는 Docker 컨테이너 내부가 아닌 가상 머신의 **/tmp/devicemodels** 폴더에서 디바이스 모델 파일을 로드하도록 Docker를 구성합니다. 이 섹션의 명령은 로컬 컴퓨터의 **bash** 셸에서 실행합니다.

이 섹션에서는 Docker 컨테이너 내부가 아닌 가상 머신의 **/tmp/devicemodels** 폴더에서 디바이스 모델 파일을 로드하도록 Docker를 구성합니다. 이 섹션의 명령은 로컬 컴퓨터의 **bash** 셸에서 실행합니다.

1. SSH를 사용하여 로컬 컴퓨터에서 Azure의 가상 머신에 연결합니다. 다음 명령은 가상 머신의 공용 IP 주소 **vm-vikxv**가 **104.41.128.108**이라고 가정합니다. 이 값을 이전 섹션의 가상 머신 공용 IP 주소로 바꿉니다.

   ```sh
    ssh azureuser@104.41.128.108
    ```

    지시에 따라 이전 섹션에서 설정한 암호를 사용하여 가상 머신에 로그인합니다.

1. 컨테이너 외부에서 디바이스 모델을 로드하도록 디바이스 시뮬레이션 서비스를 구성합니다. 먼저 Docker 구성 파일을 엽니다.

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    **devicesimulation** 컨테이너에 대한 설정을 찾고 다음 코드 조각에 표시된 것처럼 **volumes** 설정을 편집합니다.

    ```yml
    devicesimulation:
      image: azureiotpcs/device-simulation-dotnet:1.0.0
      networks:
        - default_net
      depends_on:
        - storageadapter
      environment:
        - PCS_KEYVAULT_NAME
        - PCS_AAD_APPID
        - PCS_AAD_APPSECRET
      # How one could mount custom device models
      volumes:
        - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    ```

    변경 내용을 저장합니다.

1. 컨테이너의 기존 디바이스 모델 파일을 새 위치로 복사합니다. 먼저, 디바이스 시뮬레이션 컨테이너에 대한 컨테이너 ID를 찾습니다.

    ```sh
    sudo docker ps
    ```

    그런 후 디바이스 모델 파일을 가상 머신의 **tmp** 폴더에 복사합니다. 다음 명령은 컨테이너 ID가 c378d6878407이라고 가정합니다. 이 값을 디바이스 시뮬레이션 컨테이너 ID로 바꿉니다.

    ```sh
    sudo docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    sudo chown -R azureuser /tmp/devicemodels/
    ```

    **bash** 창에서 SSH 세션을 열린 상태로 둡니다.

1. 가상 머신에 사용자 지정 디바이스 모델 파일을 복사합니다. 이 명령은 사용자 지정 디바이스 모델을 만든 컴퓨터의 다른 **bash** 셸에서 실행합니다. 먼저, 디바이스 모델 JSON 파일이 포함된 로컬 폴더로 이동합니다. 다음 명령은 가상 머신의 공용 IP 주소가 **104.41.128.108**이라고 가정합니다. 이 값을 가상 머신 공용 IP 주소로 바꿉니다. 가상 머신 암호가 요구되면 입력합니다.

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. 디바이스 시뮬레이션 Docker 컨테이너를 다시 시작하여 새 디바이스 모델을 사용합니다. 가상 머신에 대한 SSH 세션이 열려 있는 **bash** 셸에서 다음 명령을 실행합니다.

    ```sh
    sudo /app/start.sh
    ```

    실행 중인 Docker 컨테이너의 상태 및 해당 컨테이너 ID를 확인하려는 경우 다음 명령을 사용합니다.

    ```sh
    sudo docker ps
    ```

    디바이스 시뮬레이션 컨테이너에서 로그를 확인하려는 경우 다음 명령을 실행합니다. 컨테이너 ID를 디바이스 시뮬레이션 컨테이너의 ID로 바꿉니다.

    ```sh
    sudo docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>시뮬레이션 실행

이제 원격 모니터링 솔루션에서 사용자 지정 디바이스 모델을 사용할 수 있습니다.

1. [Microsoft Azure IoT 솔루션 가속기](https://www.azureiotsolutions.com/Accelerators#dashboard)에서 원격 모니터링 대시보드를 시작합니다.

1. **디바이스** 페이지에서 시뮬레이트된 디바이스를 추가합니다. 시뮬레이트된 새 디바이스를 추가하면 새 디바이스 모델을 선택할 수 있습니다.

1. 대시보드에서 디바이스 원격 분석을 보고 디바이스 메서드를 호출할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

좀 더 알아보려면 원격 모니터링 솔루션 가속기를 배포된 대로 둡니다.

솔루션 가속기가 더 이상 필요하지 않은 경우 선택한 다음, **솔루션 삭제**를 클릭하여 [프로비전된 솔루션](https://www.azureiotsolutions.com/Accelerators#dashboard) 페이지에서 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 원격 모니터링 솔루션 가속기에 사용자 지정 디바이스 모델을 배포하는 방법을 살펴보았습니다. 제안되는 다음 단계는 [실제 디바이스를 원격 모니터링 솔루션에 연결](iot-accelerators-connecting-devices-node.md)하는 방법을 알아보는 것입니다.
