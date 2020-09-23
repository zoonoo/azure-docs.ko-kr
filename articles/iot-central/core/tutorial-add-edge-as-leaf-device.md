---
title: Azure IoT Central에 Azure IoT Edge 디바이스 추가 | Microsoft Docs
description: 운영자가 Azure IoT Edge 디바이스를 Azure IoT Central 애플리케이션에 추가합니다.
author: rangv
ms.author: rangv
ms.date: 05/29/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
- iot-edge
ms.openlocfilehash: cc612d5dc21594da855799ed965effeb4b547d00
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90018665"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>자습서: Azure IoT Central 애플리케이션에 Azure IoT Edge 디바이스 추가

*이 문서는 운영자, 솔루션 빌더 및 디바이스 개발자에게 적용됩니다.*

이 자습서에서는 Azure IoT Edge 디바이스를 구성하고 Azure IoT Central 애플리케이션에 추가하는 방법을 보여 줍니다. 자습서에서 IoT Edge 사용 Linux VM(가상 머신)을 사용하여 IoT Edge 디바이스를 시뮬레이션합니다. IoT Edge 디바이스는 시뮬레이션된 환경 원격 분석을 생성하는 모듈을 사용합니다. 원격 분석은 IoT Central 애플리케이션의 대시보드에서 볼 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * IoT Edge 디바이스에 대한 디바이스 템플릿 만들기
> * IoT Central에서 IoT Edge 디바이스 만들기
> * Linux VM에 시뮬레이션된 IoT Edge 디바이스 배포

## <a name="prerequisites"></a>필수 구성 요소

[Azure IoT Central 애플리케이션 만들기](./quick-deploy-iot-central.md) 빠른 시작을 완료하여 **사용자 지정 앱 > 사용자 지정 애플리케이션** 템플릿을 사용하여 IoT Central 애플리케이션을 만듭니다.

이 자습서의 단계를 완료하려면 활성 Azure 구독이 필요합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

GitHub에서 IoT Edge 매니페스트 파일을 다운로드합니다. 다음 링크를 마우스 오른쪽 단추로 클릭하고 **다른 이름으로 링크 저장**을 선택합니다. [EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>디바이스 템플릿 만들기

이 섹션에서는 IoT Edge 디바이스에 대한 IoT Central 디바이스 템플릿을 만듭니다. IoT Edge 매니페스트를 가져와서 시작한 다음, 원격 분석 정의 및 보기를 추가하도록 템플릿을 수정합니다.

### <a name="import-manifest-to-create-template"></a>템플릿을 만드는 매니페스트 가져오기

IoT Edge 매니페스트에서 디바이스 템플릿을 만들려면 다음을 수행합니다.

1. IoT Central 애플리케이션에서 **디바이스 템플릿**으로 이동하고 **+ 새로 만들기**를 선택합니다.

1. **템플릿 유형 선택** 페이지에서 **Azure IoT Edge** 타일을 선택합니다. 그런 다음, **Next: 사용자 지정**을 선택합니다.

1. **Azure IoT Edge 배포 매니페스트 업로드** 페이지에서 디바이스 템플릿 이름으로 *환경 센서 에지 디바이스*를 입력합니다. 그런 다음, **찾아보기**를 선택하여 이전에 다운로드한 **EnvironmentalSensorManifest.json**을 업로드합니다. 그런 다음, **Next: 검토**를 클릭합니다.

1. **검토** 페이지에서 **만들기**를 선택합니다.

1. **SimulatedTemperatureSensor** 모듈에서 **관리** 인터페이스를 선택하여 매니페스트에 정의된 두 가지 속성을 확인합니다.

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/imported-manifest.png" alt-text="IoT Edge 매니페스트에서 만든 디바이스 템플릿":::

### <a name="add-telemetry-to-manifest"></a>매니페스트에 원격 분석 추가

IoT Edge 매니페스트는 모듈에서 보내는 원격 분석을 정의하지 않습니다. 원격 분석 정의를 IoT Central의 디바이스 템플릿에 추가합니다. **SimulatedTemperatureSensor** 모듈은 다음 JSON과 비슷한 원격 분석 메시지를 보냅니다.

```json
{
  "machine": {
    "temperature": 75.0,
    "pressure": 40.2
  },
  "ambient": {
    "temperature": 23.0,
    "humidity": 30.0
  },
  "timeCreated": ""
}
```

원격 분석 정의를 디바이스 템플릿에 추가하려면 다음을 수행합니다.

1. **환경 센서 에지 디바이스** 템플릿에서 **관리** 인터페이스를 선택합니다.

1. **+ 기능 추가**를 선택합니다. **표시 이름**으로 *machine*을 입력하고, **기능 유형**이 **원격 분석**인지 확인합니다.

1. 스키마 유형으로 **Object**를 선택한 다음, **정의**를 선택합니다. 개체 정의 페이지에서 *온도* 및 *압력*을 **Double** 형식의 특성으로 추가한 다음, **적용**을 선택합니다.

1. **+ 기능 추가**를 선택합니다. **표시 이름**으로 *ambient*를 입력하고, **기능 유형**이 **원격 분석**인지 확인합니다.

1. 스키마 유형으로 **Object**를 선택한 다음, **정의**를 선택합니다. 개체 정의 페이지에서 *온도* 및 *습도*를 **Double** 형식의 특성으로 추가한 다음, **적용**을 선택합니다.

1. **+ 기능 추가**를 선택합니다. **표시 이름**으로 *timeCreated*를 입력하고, **기능 유형**이 **원격 분석**인지 확인합니다.

1. 스키마 유형으로 **DateTime**을 선택합니다.

1. **저장**을 선택하여 템플릿을 업데이트합니다.

이제 **관리** 인터페이스에는 **machine**, **ambient** 및 **timeCreated** 원격 분석 유형이 포함되어 있습니다.

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/manage-interface.png" alt-text="machine 및 ambient 원격 분석 유형을 사용하는 인터페이스":::

### <a name="add-views-to-template"></a>템플릿에 보기 추가

디바이스 템플릿에는 운영자가 IoT Edge 디바이스에서 원격 분석을 볼 수 있는 보기가 아직 없습니다. 디바이스 템플릿에 보기를 추가하려면 다음을 수행합니다.

1. **환경 센서 에지 디바이스** 템플릿에서 **보기**를 선택합니다.

1. **새 보기를 추가하려면 선택** 페이지에서 **디바이스를 시각화하는 중** 타일을 선택합니다.

1. 보기 이름을 *IoT Edge 디바이스 원격 분석 보기*로 변경합니다.

1. **ambient** 및 **machine** 원격 분석 유형을 선택합니다. 그런 다음, **타일 추가**를 선택합니다.

1. **저장**을 선택하여 **IoT Edge 디바이스 원격 분석 보기** 보기를 저장합니다.

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png" alt-text="원격 분석 보기를 사용하는 디바이스 템플릿":::

### <a name="publish-the-template"></a>템플릿 게시

**환경 센서 에지 디바이스** 템플릿을 사용하는 디바이스를 추가하려면 먼저 해당 템플릿을 게시해야 합니다.

**환경 센서 에지 디바이스** 템플릿으로 이동하고, **게시**를 선택합니다. **이 디바이스 템플릿을 애플리케이션에 게시** 패널에서 **게시**를 선택하여 템플릿을 게시합니다.

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/publish-template.png" alt-text="디바이스 템플릿 게시":::

## <a name="add-iot-edge-device"></a>IoT Edge 디바이스 추가

이제 **환경 센서 에지 디바이스** 템플릿이 게시되었으므로 디바이스를 IoT Central 애플리케이션에 추가할 수 있습니다.

1. IoT Central 애플리케이션에서 **디바이스** 페이지로 이동하고, 사용 가능한 템플릿 목록에서 **환경 센서 에지 디바이스**를 선택합니다.

1. **+ 새로 만들기**를 선택하여 템플릿에서 새 디바이스를 추가합니다. **새 디바이스 만들기** 페이지에서 **만들기**를 선택합니다.

이제 **등록됨**상태의 새 디바이스가 있습니다.

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/new-device.png" alt-text="새로 등록된 디바이스"::::

### <a name="get-the-device-credentials"></a>디바이스 자격 증명 가져오기

이 자습서의 뒷부분에서 IoT Edge 디바이스를 배포하는 경우 디바이스에서 IoT Central 애플리케이션에 연결할 수 있도록 하는 자격 증명이 필요합니다. 디바이스 자격 증명을 가져오려면 다음을 수행합니다.

1. **디바이스** 페이지에서 만든 디바이스를 선택합니다.

1. **연결**을 선택합니다.

1. **디바이스 연결** 페이지에서 **ID 범위**, **디바이스 ID** 및 **기본 키**를 적어 둡니다. 이러한 값은 나중에 사용합니다.

1. **닫기**를 선택합니다.

이제 IoT Edge 디바이스를 연결할 수 있도록 IoT Central 애플리케이션을 구성하는 작업이 완료되었습니다.

## <a name="deploy-an-iot-edge-device"></a>IoT Edge 디바이스 배포

이 자습서에서는 Azure에서 만든 Azure IoT Edge 사용 Linux VM을 사용하여 IoT Edge 디바이스를 시뮬레이션합니다. Azure 구독에 IoT Edge 사용 VM을 만들려면 다음을 클릭합니다.

[![iotedge-vm-deploy에 대한 Azure에 배포 단추](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

**사용자 지정 배포 페이지**에서 다음을 수행합니다.

1. Azure 구독을 선택합니다.

1. **새로 만들기**를 선택하여 *central-edge-rg*라는 새 리소스 그룹을 만듭니다.

1. 가까운 지역을 선택합니다.

1. 고유한 **DNS 레이블 접두사**를 추가합니다(예: *contoso-central-edge*).

1. 가상 머신의 관리 사용자 이름을 선택합니다.

1. 연결 문자열로 *temp*를 입력합니다. 나중에 DPS를 사용하여 연결하도록 디바이스를 구성합니다.

1. VM 크기, Ubuntu 버전 및 위치는 기본값을 수락합니다.

1. 인증 유형으로 **암호**를 선택합니다.

1. VM에 대한 암호를 입력합니다.

1. 그런 다음, **검토 + 만들기**를 선택합니다.

1. 선택 항목을 검토한 다음, **만들기**를 선택합니다.

    :::image type="content" source="media/tutorial-add-edge-as-leaf-device/vm-deployment.png" alt-text="IoT Edge VM 만들기":::

배포가 완료될 때까지 몇 분 정도 소요됩니다. 배포가 완료되면 Azure Portal에서**central-edge-rg** 리소스 그룹으로 이동합니다.

### <a name="configure-the-iot-edge-vm"></a>IoT Edge VM 구성

DPS를 사용하여 IoT Central 애플리케이션에 등록하고 연결하도록 VM에서 IoT Edge를 구성하려면 다음을 수행합니다.

1. **contoso-edge-rg** 리소스 그룹에서 가상 머신 인스턴스를 선택합니다.

1. **지원 + 문제 해결** 섹션에서 **직렬 콘솔**을 선택합니다. 부팅 진단을 구성하라는 메시지가 표시되면 포털의 지침을 따릅니다.

1. **Enter** 키를 눌러 `login:` 프롬프트를 표시합니다. 로그인할 사용자 이름과 암호를 입력합니다.

1. 다음 명령을 실행하여 IoT Edge 런타임 버전을 확인합니다. 이 문서를 작성한 시점에서 버전은 1.0.9.1입니다.

    ```bash
    sudo iotedge --version
    ```

1. `nano` 편집기를 사용하여 IoT Edge config.yaml 파일을 엽니다.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. `# Manual provisioning configuration`이 표시될 때까지 아래로 스크롤합니다. 다음 코드 조각과 같이 다음 세 줄을 주석으로 처리합니다.

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. `# DPS symmetric key provisioning configuration`이 표시될 때까지 아래로 스크롤합니다. 다음 코드 조각과 같이 다음 8개 줄의 주석 처리를 제거합니다.

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

    > [!TIP]
    > `provisioning:` 앞에 공백이 남아 있지 않도록 합니다.

1. `{scope_id}`를 이전에 적어 둔 **ID 범위**로 바꿉니다.

1. `{registration_id}`를 이전에 적어 둔 **디바이스 ID**로 바꿉니다.

1. `{symmetric_key}`를 이전에 적어 둔 **기본 키**로 바꿉니다.

1. 변경 내용을 저장하고(**Ctrl-O**), `nano` 편집기를 종료합니다(**Ctrl-X**).

1. 다음 명령을 실행하여 IoT Edge 디먼을 다시 시작합니다.

    ```bash
    sudo systemctl restart iotedge
    ```

1. IoT Edge 모듈의 상태를 확인하려면 다음 명령을 실행합니다.

    ```bash
    iotedge list
    ```

    다음 샘플 출력은 실행 중인 모듈을 보여줍니다.

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

    > [!TIP]
    > 모든 모듈의 실행이 시작될 때까지 기다려야 할 수 있습니다.

## <a name="view-the-telemetry"></a>원격 분석 보기

이제 시뮬레이션된 IoT Edge 디바이스가 VM에서 실행되고 있습니다. IoT Central 애플리케이션의 **디바이스** 페이지에서 디바이스 상태는 이제 **프로비저닝됨**입니다.

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/provisioned-device.png" alt-text="프로비저닝된 IoT Edge 디바이스":::

**IoT Edge 디바이스 원격 분석 보기** 페이지에서 디바이스의 원격 분석을 볼 수 있습니다.

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png" alt-text="디바이스 원격 분석":::

**모듈** 페이지에서 디바이스의 IoT Edge 모듈의 상태가 표시됩니다.

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/edge-module-status.png" alt-text="디바이스 모듈 상태":::

## <a name="clean-up-resources"></a>리소스 정리

IoT Edge VM을 계속 사용하려는 경우 이 자습서에서 사용한 리소스를 그대로 두고 다시 사용할 수 있습니다. 그렇지 않으면 추가 요금이 발생하지 않도록 이 자습서에서 만든 리소스를 삭제하세요.

* IoT Edge VM 및 관련 리소스를 삭제하려면 Azure Portal에서 **contoso-edge-rg** 리소스 그룹을 삭제합니다.
* IoT Central 애플리케이션을 삭제하려면 애플리케이션 **관리** 섹션의 **애플리케이션** 페이지로 이동하여 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

디바이스 개발자로서, 이제 IoT Central에서 IoT Edge 디바이스를 사용하고 관리하는 방법을 알아보았으므로 제안되는 다음 단계를 읽어보세요.

> [!div class="nextstepaction"]
> [Azure IoT Edge 모듈 개발](../../iot-edge/tutorial-develop-for-linux.md)

솔루션 개발자 또는 운영자로서, 이제 IoT Central에서 IoT Edge 디바이스를 사용하고 관리하는 방법을 알아보았으므로 제안되는 다음 단계를 수행하세요.

> [!div class="nextstepaction"]
> [디바이스 그룹을 사용하여 디바이스 원격 분석 수행](./tutorial-use-device-groups.md)
