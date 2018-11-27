---
title: Azure 솔루션 자습서의 에지 변칙 검색 | Microsoft Docs
description: 이 자습서에서는 원격 모니터링 솔루션 가속기를 사용하여 IoT Edge 디바이스를 모니터링하는 방법을 알아봅니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 329bc41555f2def0e2b7001a7b445cd3de16d439
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2018
ms.locfileid: "51826336"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>자습서: 원격 모니터링 솔루션 가속기를 사용하여 에지에서 변칙 검색

이 자습서에서는 IoT Edge 디바이스에서 검색된 이상에 대응하도록 원격 모니터링 솔루션을 구성합니다. IoT Edge 디바이스에서는 Edge의 원격 분석을 처리하여 솔루션에 전송되는 원격 분석의 양을 줄이고 디바이스의 이벤트에 더 빠르게 대응할 수 있습니다. 에지 처리의 이점에 대한 자세한 내용은 [Azure IoT Edge란?](../iot-edge/about-iot-edge.md)을 참조하세요.

이 자습서에서는 원격 모니터링을 통한 에지 처리를 소개하기 위해 시뮬레이트된 오일 펌프 잭 디바이스를 사용합니다. 오일 펌프 잭은 Contoso라는 조직에서 관리하고 원격 모니터링 솔루션 가속기에 연결됩니다. 오일 펌프 잭의 센서는 온도 및 압력을 측정합니다. Contoso의 운영자는 온도가 비정상적으로 증가하면 오일 펌프 잭이 느려질 수 있다는 사실을 알고 있습니다. Contoso의 운영자는 디바이스 온도가 정상 범위 내에 있을 때는 온도를 모니터링할 필요가 없습니다.

Contoso는 오일 펌프 잭에 온도 이상을 감지하는 지능형 에지 모듈을 배포하려고 합니다. 또 다른 Edge 모듈은 원격 모니터링 솔루션에 경고를 보냅니다. 경고가 수신되면 Contoso 운영자는 유지 관리 기술자를 발송할 수 있습니다. 또한 Contoso는 솔루션이 경고를 수신할 때 이메일 전송과 같은 자동 작업이 실행되도록 구성할 수도 있습니다.

이 자습서에서는 로컬 Windows 개발 컴퓨터를 IoT Edge 디바이스로 사용합니다. 에지 모듈을 설치하여 오일 펌프 잭 디바이스를 시뮬레이트하고 온도 이상을 감지합니다.

이 자습서에서는 다음을 수행했습니다.

>[!div class="checklist"]
> * 솔루션에 IoT Edge 디바이스 추가
> * Edge 매니페스트 만들기
> * 디바이스에서 실행할 모듈을 정의하는 패키지 가져오기
> * IoT Edge 디바이스에 패키지 배포
> * 디바이스에서 경고 보기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>IoT Edge 디바이스 추가

원격 모니터링 솔루션 가속기에 IoT Edge 디바이스를 추가하는 방법은 다음 두 단계로 진행됩니다. 이 섹션에서는 다음을 사용하는 방법을 보여 줍니다.

* 원격 모니터링 웹 UI의 **디바이스** 페이지에서 IoT Edge 디바이스를 추가합니다.
* Linux VM(가상 머신)에 IoT Edge 런타임을 설치합니다.

### <a name="add-an-iot-edge-device-to-your-solution"></a>솔루션에 IoT Edge 디바이스 추가

IoT Edge 디바이스를 원격 모니터링 솔루션 가속기에 추가하려면 웹 UI의 **디바이스** 페이지로 이동한 후 **+새 디바이스**를 클릭합니다.

**새 디바이스** 패널에서 **IoT Edge 디바이스**를 선택하고 디바이스 ID로 **oil-pump**를 입력합니다. 다른 설정의 경우 기본값을 그대로 둡니다. 그런 다음 **적용**을 클릭합니다.

[![IoT Edge 디바이스 추가](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

디바이스 연결 문자열을 기록해 둡니다. 이 문자열은 이 자습서의 다음 섹션에서 필요합니다.

원격 모니터링 솔루션 가속기에서 IoT Hub에 디바이스를 등록하면 웹 UI의 **디바이스**에 표시됩니다.

[![새 IoT Edge 디바이스](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

솔루션에서 IoT Edge 디바이스를 보다 쉽게 관리하도록 하려면 디바이스 그룹을 만들고 IoT Edge 디바이스를 추가합니다.

1. **디바이스** 페이지의 목록에서 **oil-pump** 디바이스를 선택하고 **작업**을 클릭합니다.

1. 다음 설정을 사용하여 디바이스에 **IsEdge** 태그를 추가하는 작업을 만듭니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 작업     | 태그들  |
    | 작업 이름 | AddEdgeTag |
    | 키     | IsOilPump |
    | 값   | Y     |
    | type    | 텍스트  |

    [![태그 추가](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. **적용**을 클릭하고 **닫기**를 클릭합니다.

1. **디바이스** 페이지에서 **디바이스 그룹 관리**를 클릭합니다.

1. **새 디바이스 그룹 만들기**를 클릭합니다. 다음 설정을 사용하여 새 디바이스 그룹을 만듭니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 이름    | OilPumps |
    | 필드   | Tags.IsOilPump |
    | 연산자 | = Equals |
    | 값    | Y |
    | type     | 텍스트 |

    [![디바이스 그룹 만들기](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. **저장**을 클릭합니다.

IoT Edge 디바이스는 이제 **OilPumps** 그룹에 속합니다.

### <a name="install-the-edge-runtime"></a>Edge 런타임 설치

Edge 디바이스에는 Edge 런타임을 설치해야 합니다. 이 자습서에서는 시나리오를 테스트하기 위해 Azure Linux VM에 Edge 런타임을 설치합니다. 다음 단계에서는 설치에 Azure Cloud Shell을 사용하고 VM을 구성합니다.

1. Azure에서 Linux VM을 만들려면 다음 명령을 실행합니다. 현재 위치에 가까운 위치를 사용할 수 있습니다.

    ```azurecli-interactive
    az group create \
      --name IoTEdgeDevices \
      --location eastus
    az vm create \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --image Canonical:UbuntuServer:16.04-LTS:latest \
      --admin-username azureuser \
      --generate-ssh-keys \
      --size Standard_B1ms
    ```

    공용 IP 주소를 기록해 둡니다. 다음 단계에서 SSH를 사용하여 연결할 때 필요합니다.

1. SSH를 사용하여 VM에 연결하려면 Cloud Shell에서 다음 명령을 실행합니다.

    ```azurecli-interactive
    ssh azureuser@{vm IP address}
    ```

1. VM에 연결하는 경우 다음 명령을 실행하여 VM에서 리포지토리를 설정합니다.

    ```azurecli-interactive
    curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

1. VM에 컨테이너와 Edge 런타임을 설치하려면 다음 명령을 실행합니다.

    ```azurecli-interactive
    sudo apt-get update
    sudo apt-get install moby-engine
    sudo apt-get install moby-cli
    sudo apt-get update
    sudo apt-get install iotedge
    ```

1. 디바이스 연결 문자열을 사용하여 Edge 런타임을 구성하려면 구성 파일을 편집합니다.

    ```azurecli-interactive
    sudo nano /etc/iotedge/config.yaml
    ```

    디바이스 연결 문자열을 **device_connection_string** 변수에 할당하고 변경 내용을 저장한 후 편집기를 종료합니다.

1. Edge 런타임을 다시 시작하여 새 구성을 사용합니다.

    ```azurecli-interactive
    sudo systemctl restart iotedge
    ```

1. 이제 SSH 세션을 종료하고 Cloud Shell을 닫을 수 있습니다.

이제 Linux 디바이스에서 IoT Edge 런타임을 설치 및 구성했습니다. 이 자습서의 뒷부분에서 원격 모니터링 솔루션을 사용하여 이 디바이스에 IoT Edge 모듈을 배포합니다.

## <a name="create-an-edge-manifest"></a>Edge 매니페스트 만들기

오일 잭 펌프 디바이스를 시뮬레이트하려면 Edge 디바이스에 다음 모듈을 추가해야 합니다.

* 온도 시뮬레이션 모듈
* Azure Stream Analytics 변칙 검색

다음 단계에서는 이러한 모듈을 포함하는 Edge 배포 매니페스트를 만드는 방법을 보여 줍니다. 이 자습서의 뒷부분에서 이 매니페스트를 원격 모니터링 솔루션 가속기의 패키지로 가져옵니다.

### <a name="create-the-azure-stream-analytics-job"></a>Azure Stream Analytics 작업 만들기

Edge 모듈로 패키징하기 전에 포털에서 Stream Analytics 작업을 정의합니다.

1. Azure Portal에서 기본 옵션을 사용하여 **IoTEdgeDevices** 리소스 그룹에 Azure Storage 계정을 만듭니다. 선택한 이름을 기록해 둡니다.

1. Azure Portal에서 **IoTEdgeDevices** 리소스 그룹에 **Stream Analytics 작업**을 만듭니다. 다음 구성 값을 사용합니다.

    | 옵션 | 값 |
    | ------ | ----- |
    | 작업 이름 | EdgeDeviceJob |
    | 구독 | Azure 구독 |
    | 리소스 그룹 | IoTEdgeDevices |
    | 위치 | 미국 동부 |
    | 호스팅 환경 | Edge |
    | 스트리밍 단위 | 1 |

1. 포털에서 **EdgeDeviceJob** Stream Analytics 작업을 열고 입력을 클릭한 후 **telemetry**라는 **Edge Hub** 입력 스트림을 추가합니다.

1. 포털의 **EdgeDeviceJob** Stream Analytics 작업에서 **출력**을 클릭하고 **output**이라는 **Edge Hub**를 추가합니다.

1. 포털의 **EdgeDeviceJob** Stream Analytics 작업에서 **출력**을 클릭하고 **alert**라는 두 번째 **Edge Hub**를 추가합니다.

1. 포털의 **EdgeDeviceJob** Stream Analytics 작업에서 **쿼리**를 클릭하고 다음 **select** 문을 추가합니다.

    ```sql
    SELECT  
    avg(machine.temperature) as temperature, 
    'F' as temperatureUnit 
    INTO output 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 5) 
    SELECT 'reset' as command 
    INTO alert 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 3) 
    HAVING avg(machine.temperature) > 400
    ```

1. 포털의 **EdgeDeviceJob** Stream Analytics 작업에서 **저장소 계정 설정**을 클릭합니다. **IoTEdgeDevices** 리소스 그룹에 추가한 저장소 계정을 이 섹션의 시작으로 추가합니다. **edgeconfig**라는 새 컨테이너를 만듭니다.

다음 스크린샷에서는 저장된 Stream Analytics 작업을 보여 줍니다.

[![Stream Analytics 작업](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

이제 Edge 디바이스에서 실행할 Stream Analytics 작업을 정의했습니다. 이 작업은 5초 기간 동안의 평균 온도를 계산합니다. 또한 이 작업은 3초 간의 평균 온도가 400을 넘는 경우 경고를 보냅니다.

### <a name="create-the-iot-edge-deployment"></a>IoT Edge 배포 만들기

다음으로 Edge 디바이스에서 실행할 모듈을 정의하는 IoT Edge 배포 매니페스트를 만듭니다. 다음 섹션에서는 원격 모니터링 솔루션에서 이 매니페스트를 패키지로 가져옵니다.

1. Azure Portal에서 원격 모니터링 솔루션의 IoT Hub로 이동합니다. 원격 모니터링 솔루션과 이름이 같은 IoT Hub를 리소스 그룹에서 찾을 수 있습니다.

1. IoT Hub의 **자동 디바이스 관리** 섹션에서 **IoT Edge**를 클릭합니다. **IoT Edge 배포 추가**를 클릭합니다.

1. **배포 만들기 > 이름 및 레이블** 페이지에서 이름 **oil-pump-device**를 입력합니다. **다음**을 클릭합니다.

1. **배포 만들기 > 모듈 추가** 페이지에서 **+추가**를 클릭합니다. **IoT Edge 모듈**을 선택합니다.

1. **IoT Edge 사용자 지정 모듈** 패널에서 **temperatureSensor**를 이름으로, **asaedgedockerhubtest/asa-edge-test-module:sensor-ad-linux-amd64**를 이미지 URI로 입력합니다. **저장**을 클릭합니다.

1. **배포 만들기 > 모듈 추가** 페이지에서 **+추가**를 클릭하여 두 번째 모듈을 추가합니다. **Azure Stream Analytics 모듈**을 선택합니다.

1. **Edge 배포** 패널에서 구독과 이전 섹션에서 만든 **EdgeDeviceJob**을 선택합니다. **저장**을 클릭합니다.

1. **배포 만들기 > 모듈 추가** 페이지에서 **다음**을 클릭합니다.

1. **배포 만들기 > 경로 지정** 페이지에서 다음 코드를 추가합니다.

    ```sql
    {
      "routes": { 
        "alertsToReset": "FROM /messages/modules/EdgeDeviceJob/outputs/alert INTO BrokeredEndpoint(\"/modules/temperatureSensor/inputs/control\")", 
        "telemetryToAsa": "FROM /messages/modules/temperatureSensor/* INTO BrokeredEndpoint(\"/modules/EdgeDeviceJob/inputs/telemetry\")", 
        "ASAToCloud": "FROM /messages/modules/EdgeDeviceJob/* INTO $upstream" 
      } 
    }
    ```

    이 코드는 Stream Analytics 모듈의 출력을 올바른 위치로 라우팅합니다.

    **다음**을 클릭합니다.

1. **배포 만들기 > 메트릭 지정** 페이지에서 **다음**을 클릭합니다.

1. **배포 만들기 > 대상 디바이스** 페이지에서 우선 순위로 10을 입력합니다. **다음**을 클릭합니다.

1. **배포 만들기 > 배포 검토** 페이지에서 **제출**을 클릭합니다.

    [![배포 검토](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. 주 **IoT Edge** 페이지에서 **IoT Edge 배포**를 클릭합니다. 배포 목록에서 **oil-pump-device**를 볼 수 있습니다.

1. **oil-pump-device** 배포를 클릭한 후 **IoT Edge 매니페스트 다운로드**를 클릭합니다. 이 파일을 로컬 컴퓨터의 적절한 위치에 **oil-pump-device.json**으로 저장합니다. 이 자습서의 다음 섹션에서 이 파일이 필요합니다.

이제 원격 모니터링 솔루션에 패키지로 가져올 IoT Edge 매니페스트를 만들었습니다. 일반적으로 개발자가 IoT Edge 모듈 및 매니페스트 파일을 만듭니다.

## <a name="import-a-package"></a>패키지 가져오기

이 섹션에서는 원격 모니터링 솔루션에서 Edge 매니페스트를 패키지로 가져옵니다.

1. 원격 모니터링 웹 UI에서 **패키지** 페이지로 이동한 후 **+새 패키지**를 클릭합니다.

    [![새 패키지](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. **새 패키지** 패널에서 패키지 유형으로 **Edge 매니페스트**를 선택하고 **찾아보기**를 클릭하여 로컬 컴퓨터에서 **oil-pump-device.json**을 찾은 후 **업로드**를 클릭합니다.

    [![패키지 로드](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    이제 패키지의 목록에는 **oil-pump-device.json** 패키지가 포함됩니다.

다음 섹션에서는 Edge 디바이스에 패키지를 적용하는 배포를 만듭니다.

## <a name="deploy-a-package"></a>패키지 배포

이제 디바이스에 패키지를 배포할 준비가 되었습니다.

1. 원격 모니터링 웹 UI에서 **배포** 페이지로 이동한 후 **+새 배포**를 클릭합니다.

    [![새 배포](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. **새 배포** 패널에서 다음 설정을 사용하여 배포를 만듭니다.

    | 옵션 | 값 |
    | ------ | ----- |
    | 이름   | OilPumpDevices |
    | 패키지 유형 | Edge 매니페스트 |
    | 패키지 | oil-pump-device.json |
    | 디바이스 그룹 | OilPumps |
    | 우선 순위 | 10 |

    [![배포 만들기](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    **적용**을 클릭합니다.

패키지가 디바이스로 배포되고, 원격 분석이 디바이스에서의 흐름을 시작할 때까지 몇 분 정도 기다려야 합니다.

[![배포 활성화](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

**배포** 페이지에는 다음 메트릭이 표시됩니다.

* **대상 지정됨**은 디바이스 그룹의 디바이스 수를 표시합니다.
* **적용됨**은 배포 콘텐츠가 적용된 디바이스 수를 표시합니다.
* **성공함**은 IoT Edge 클라이언트 런타임에서 성공을 보고하는 배포의 Edge 디바이스 수입니다.
* **실패함**은 IoT Edge 클라이언트 런타임에서 실패를 보고하는 배포의 Edge 디바이스 수입니다.

## <a name="monitor-the-device"></a>디바이스 모니터링

원격 모니터링 웹 UI에서 오일 펌프 디바이스의 온도 원격 분석을 볼 수 있습니다.

1. **디바이스** 페이지로 이동한 후 오일 펌프 디바이스를 선택합니다.
1. **디바이스 세부 정보** 패널의 **원격 분석** 섹션에서 **온도**를 클릭합니다.

    [![원격 분석 보기](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

온도가 임계값에 도달할 때까지 증가하는 방식을 확인할 수 있습니다. Stream Analytics Edge 모듈은 온도가 이 임계값에 도달할 때를 감지하고 디바이스에 명령을 보내 온도를 즉시 줄입니다. 이러한 모든 처리는 클라우드와 통신하지 않고 디바이스에서 진행됩니다.

임계값에 도달할 때 운영자에 게 알리려면 원격 모니터링 웹 UI에서 규칙을 만들 수 있습니다.

1. **규칙** 페이지로 이동한 후 **+새 규칙**을 클릭합니다.
1. 다음 설정을 사용하여 새 규칙을 만듭니다.

    | 옵션 | 값 |
    | ------ | ----- |
    | 규칙 이름 | 오일 펌프 온도 |
    | 설명 | 오일 펌프 온도가 300을 초과했습니다. |
    | 장치 그룹 | OilPumps |
    | 계산 | 인스턴트 |
    | 필드 | 온도 |
    | 연산자 | > |
    | 값 | 300 |
    | 심각도 수준 | 정보 |

    [![규칙 만들기](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    **적용**을 클릭합니다.

1. **대시보드** 페이지로 이동합니다. **oil-pump** 디바이스의 온도가 300을 넘으면 **경고** 패널에 경고가 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 원격 모니터링 솔루션 가속기에서 IoT Edge 디바이스를 추가 및 구성하는 방법을 보여 주었습니다. 원격 모니터링 솔루션에서 IoT Edge 패키지를 사용하는 방법에 대한 자세한 내용은 다음 방법 가이드를 참조하세요.

> [!div class="nextstepaction"]
> [원격 모니터링 솔루션 가속기로 IoT Edge 패키지 가져오기](iot-accelerators-remote-monitoring-import-edge-package.md)

IoT Edge 런타임을 설치하는 방법에 대한 자세한 내용은 [Linux(x64)에서 Azure IoT Edge 런타임 설치](../iot-edge/how-to-install-iot-edge-linux.md)를 참조하세요.

Edge 디바이스의 Azure Stream Analytics에 대한 자세한 내용은 [Azure Stream Analytics를 IoT Edge 모듈로 배포](../iot-edge/tutorial-deploy-stream-analytics.md)를 참조하세요.
