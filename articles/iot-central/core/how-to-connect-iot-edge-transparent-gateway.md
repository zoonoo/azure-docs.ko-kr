---
title: IoT Edge 투명 게이트웨이를 Azure IoT Central 응용 프로그램에 연결
description: IoT Edge 투명 게이트웨이를 통해 장치를 IoT Central 응용 프로그램에 연결 하는 방법
author: dominicbetts
ms.author: dobett
ms.date: 03/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 4e88ad58c7baba1c66c30df3f4effdbf11371c18
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045334"
---
# <a name="how-to-connect-devices-through-an-iot-edge-transparent-gateway"></a>IoT Edge 투명 게이트웨이를 통해 장치를 연결 하는 방법

IoT Edge 장치는 로컬 네트워크의 다른 장치와 IoT Central 응용 프로그램 간의 연결을 제공 하는 게이트웨이 역할을 할 수 있습니다. 장치가 IoT Central 응용 프로그램에 직접 액세스할 수 없는 경우 게이트웨이를 사용 합니다.

IoT Edge는 [ *투명* 및 *변환* 게이트웨이 패턴](../../iot-edge/iot-edge-as-gateway.md)을 지원 합니다. 이 문서에서는 투명 게이트웨이 패턴을 구현 하는 방법을 요약 합니다. 이 패턴에서 게이트웨이는를 통해 다운스트림 장치에서 IoT Central 응용 프로그램의 IoT Hub 끝점으로 메시지를 전달 합니다.

이 문서에서는 가상 컴퓨터를 사용 하 여 다운스트림 장치 및 게이트웨이를 호스팅합니다. 실제 시나리오에서 다운스트림 장치 및 게이트웨이는 로컬 네트워크의 물리적 장치에서 실행 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서의 단계를 완료하려면 활성 Azure 구독이 필요합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[Azure IoT Central 애플리케이션 만들기](./quick-deploy-iot-central.md) 빠른 시작을 완료하여 **사용자 지정 앱 > 사용자 지정 애플리케이션** 템플릿을 사용하여 IoT Central 애플리케이션을 만듭니다.

이 문서의 단계를 수행 하려면 다음 파일을 컴퓨터에 다운로드 합니다.

- [자동 온도 조절기 장치 모델](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-1.json)
- [투명 게이트웨이 매니페스트](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/EdgeTransparentGatewayManifest.json)

## <a name="add-device-templates"></a>장치 템플릿 추가

다운스트림 장치와 게이트웨이 장치에는 모두 IoT Central 장치 템플릿이 필요 합니다. IoT Central를 사용 하면 다운스트림 장치와 게이트웨이 간의 관계를 모델링할 수 있으므로 연결 된 후 보고 관리할 수 있습니다.

다운스트림 장치에 대 한 장치 템플릿을 만들려면 장치의 기능을 모델링 하는 표준 장치 템플릿을 만듭니다. 이 문서에 표시 된 예제에서는 자동 온도 조절기 장치 모델을 사용 합니다.

다운스트림 장치에 대 한 장치 템플릿을 만들려면 다음을 수행 합니다.

1. 장치 템플릿을 만들고, 템플릿 유형으로 **IoT 장치** 를 선택 합니다.

1. 마법사의 **사용자 지정** 페이지에서 장치 템플릿의 이름 (예: *자동 온도 조절기* )을 입력 합니다.

1. 장치 템플릿을 만든 후 **모델 가져오기** 를 선택 합니다. 이전에 다운로드 한 파일 *의thermostat-1.js* 와 같은 모델을 선택 합니다.

1. 자동 온도 조절기에 대 한 일부 기본 보기를 생성 하려면 뷰를 선택한 다음 **기본 보기 생성** 을 선택 합니다.

1. 디바이스 템플릿을 게시합니다.

투명 한 IoT Edge 게이트웨이에 대 한 장치 템플릿을 만들려면 다음을 수행 합니다.

1. 장치 템플릿을 만들고 템플릿 형식으로 **Azure IoT Edge** 를 선택 합니다.

1. 마법사의 **사용자 지정** 페이지에서 장치 템플릿에 대 한에 *지 게이트웨이* 등의 이름을 입력 합니다.

1. 마법사의 **사용자 지정** 페이지에서 **다운스트림 장치를 사용 하 여 게이트웨이 장치** 를 확인 합니다.

1. 마법사의 **사용자 지정** 페이지에서 **찾아보기** 를 선택 합니다. 이전에 다운로드 한 파일 *에EdgeTransparentGatewayManifest.js* 를 업로드 합니다.

1. 다운스트림 장치 템플릿에 **관계** 의 항목을 추가 합니다.

다음 스크린샷에서는 **자동 온도 조절기** 장치 템플릿을 사용 하는 다운스트림 장치를 포함 하는 IoT Edge 게이트웨이 장치에 대 한 **관계** 페이지를 보여 줍니다.

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/device-template-relationship.png" alt-text="자동 온도 조절기 다운스트림 장치 템플릿과 IoT Edge 게이트웨이 장치 템플릿 관계를 보여 주는 스크린샷":::

위의 스크린샷은 모듈이 정의 되지 않은 IoT Edge 게이트웨이 장치 템플릿을 보여 줍니다. IoT Edge 런타임이 다운스트림 장치에서 IoT Central로 메시지를 전달 하기 때문에 투명 게이트웨이에는 모듈이 필요 하지 않습니다. 게이트웨이 자체에서 원격 분석을 보내거나, 속성을 동기화 하거나, 명령을 처리 해야 하는 경우 기본 구성 요소나 모듈에서 이러한 기능을 정의할 수 있습니다.

게이트웨이 및 다운스트림 장치 템플릿을 게시 하기 전에 필요한 클라우드 속성 및 보기를 추가 합니다.

## <a name="add-the-devices"></a>장치 추가

IoT Central 응용 프로그램에 장치를 추가 하는 경우 다운스트림 장치와 투명 게이트웨이 간의 관계를 정의할 수 있습니다.

장치를 추가 하려면:

1. IoT Central 응용 프로그램에서 장치 페이지로 이동 합니다.

1. 투명 게이트웨이 IoT Edge 장치 인스턴스를 추가 합니다. 이 문서에서 게이트웨이 장치 ID는 `edgegateway` 입니다.

1. 하나 이상의 다운스트림 장치 인스턴스를 추가 합니다. 이 문서에서 다운스트림 장치는 Id 및로 온도 조절 장치 됩니다 `thermostat1` `thermostat2` .

1. 장치 목록에서 각 다운스트림 장치를 선택 하 고 **게이트웨이 연결을** 선택 합니다.

다음 스크린샷에서는 **다운스트림 장치** 페이지에서 게이트웨이에 연결 된 장치 목록을 볼 수 있습니다.

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-devices.png" alt-text="투명 게이트웨이에 연결 된 다운스트림 장치 목록을 보여 주는 스크린샷":::

투명 게이트웨이에서 다운스트림 장치는 게이트웨이에서 호스트 되는 사용자 지정 모듈이 아니라 게이트웨이 자체에 연결 됩니다.

장치를 배포 하기 전에 다음이 필요 합니다.

- IoT Central 응용 프로그램의 **ID 범위** 입니다.
- 게이트웨이 및 다운스트림 장치에 대 한 **장치 ID** 값입니다.
- 게이트웨이 및 다운스트림 장치에 대 한 **기본 키** 값입니다.

이러한 값을 찾으려면 장치 목록에서 각 장치로 이동 하 고 **연결** 을 선택 합니다. 계속 하기 전에 이러한 값을 기록해 둡니다.

## <a name="deploy-the-gateway-and-devices"></a>게이트웨이 및 장치 배포

이 시나리오를 시험해 볼 수 있도록 다음 단계에서는 게이트웨이 및 다운스트림 장치를 Azure virtual machines에 배포 하는 방법을 보여 줍니다. 실제 시나리오에서 다운스트림 장치 및 게이트웨이는 로컬 네트워크의 물리적 장치에서 실행 됩니다.

투명 게이트웨이 시나리오를 사용해 보려면 다음 단추를 선택 하 여 두 개의 Linux 가상 머신을 배포 합니다. 가상 컴퓨터 하나는 투명 IoT Edge 게이트웨이이 고, 다른 하나는 자동 온도 조절기을 시뮬레이트하는 다운스트림 장치입니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

두 가상 컴퓨터를 배포 하 고 실행 하는 경우 가상 컴퓨터에서 IoT Edge 게이트웨이 장치가 실행 중인지 확인 합니다 `edgegateway` .

1. IoT Central 응용 프로그램에서 **장치** 페이지로 이동 합니다. IoT Edge 게이트웨이 장치가 IoT Central에 연결 된 경우 상태가 **프로 비전** 됩니다.

1. IoT Edge 게이트웨이 장치를 열고 **모듈** 페이지에서 모듈의 상태를 확인 합니다. IoT Edge 런타임이 성공적으로 시작 되 면 **$edgeAgent** 및 **$edgeHub** 모듈의 상태가 **실행** 중입니다.

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/iot-edge-runtime.png" alt-text="IoT Edge 게이트웨이에서 실행 되는 $edgeAgent 및 $edgeHub 모듈을 보여 주는 스크린샷":::

> [!TIP]
> 가상 컴퓨터가 시작 되는 동안 몇 분 정도 기다려야 할 수 있으며 장치가 IoT Central 응용 프로그램에 프로 비전 됩니다.

## <a name="configure-the-gateway"></a>게이트웨이 구성

IoT Edge 장치가 투명 게이트웨이로 작동 하려면 모든 다운스트림 장치에 대 한 id를 증명 하는 인증서가 필요 합니다. 이 문서에서는 데모 인증서를 사용 합니다. 프로덕션 환경에서는 인증 기관의 인증서를 사용 합니다.

데모 인증서를 생성 하 고 게이트웨이 장치에 설치 하려면 다음을 수행 합니다.

1. SSH를 사용 하 여 게이트웨이 장치 가상 머신에서 연결 하 고 로그인 합니다.

1. 다음 명령을 실행 하 여 IoT Edge 리포지토리를 복제 하 고 데모 인증서를 생성 합니다.

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    이전 명령을 실행 한 후 다음 단계에서 사용할 준비가 된 파일은 다음과 같습니다.

    - *~/certs/certs/azure-iot-test-only.root.ca.cert.pem* -IoT Edge 시나리오를 테스트 하기 위한 다른 모든 데모 인증서를 만드는 데 사용 되는 루트 ca 인증서입니다.
    - *~/certs/certs/iot-edge-device-mycacert-full-chain.cert.pem* -config.xml 파일에서 참조 되는 장치 CA 인증서입니다 *.* 게이트웨이 시나리오에서이 CA 인증서는 IoT Edge 장치가 다운스트림 장치에 대 한 id를 확인 하는 방법입니다.
    - *~/certs/private/iot-edge-device-mycacert.key.pem* -장치 CA 인증서와 연결 된 개인 키입니다.

    이러한 데모 인증서에 대해 자세히 알아보려면 [데모 인증서를 만들어 IoT Edge 장치 기능 테스트](../../iot-edge/how-to-create-test-certificates.md)를 참조 하세요.

1. 텍스트 편집기에서 *config.xml* 파일을 엽니다. 예를 들어:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. 설정을 찾습니다 `Certificate settings` . 인증서 설정을 다음과 같이 주석 처리를 제거 하 고 수정 합니다.

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    위에 표시 된 예제는 **Azureuser** 로 로그인 하 고 "mycacert" 라는 장치 CA 인증서을 만들었다고 가정 합니다.

1. 변경 내용을 저장 하 고 IoT Edge 런타임을 다시 시작 합니다.

    ```bash
    sudo systemctl restart iotedge
    ```

변경 후 IoT Edge 런타임이 성공적으로 시작 되 면 **$edgeAgent** 및 **$edgeHub** 모듈의 상태가 IoT Central의 게이트웨이 장치에 대 한 **모듈** 페이지에서 **실행 중** 으로 변경 됩니다.

런타임이 시작 되지 않으면 *config.xml* 에서 변경한 내용을 확인 하 고 [IoT Edge 장치 문제 해결](../../iot-edge/troubleshoot.md)을 참조 하세요.

이제 투명 게이트웨이가 구성 되 고 다운스트림 장치에서 원격 분석 전달을 시작할 준비가 되었습니다.

## <a name="provision-a-downstream-device"></a>다운스트림 장치 프로 비전

현재 IoT Edge는 IoT Central 응용 프로그램에 다운스트림 장치를 자동으로 프로 비전 할 수 없습니다. 다음 단계는 장치를 프로 비전 하는 방법을 보여 줍니다 `thermostat1` . 이러한 단계를 완료 하려면 Python 3.6 이상이 설치 되 고 인터넷에 연결 된 환경이 필요 합니다. [Azure Cloud Shell](https://shell.azure.com/) 에는 Python 3.7이 사전 설치 되어 있습니다.

1. 다음 명령을 실행 하 여 모듈을 설치 합니다 `azure.iot.device` .

    ```bash
    pip install azure.iot.device
    ```

1. 다음 명령을 실행 하 여 프로 비전을 수행 하는 Python 스크립트를 다운로드 합니다.

    ```bash
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/provision_device.py
    ```

1. `thermostat1`IoT Central 응용 프로그램에서 다운스트림 장치를 프로 비전 하려면 및를 대체 하 여 다음 명령을 실행 합니다 `{your application id scope}` `{your device primary key}` .

    ```bash
    export IOTHUB_DEVICE_DPS_DEVICE_ID=thermostat1
    export IOTHUB_DEVICE_DPS_ID_SCOPE={your application id scope}
    export IOTHUB_DEVICE_DPS_DEVICE_KEY={your device primary key}
    python provision_device.py
    ```

IoT Central 응용 프로그램에서 thermostat1 장치에 대 한 **장치 상태가** 이제 **프로 비전** 되었는지 확인 합니다. 

## <a name="configure-a-downstream-device"></a>다운스트림 디바이스 구성

이전 섹션에서는 `edgegateway` 가상 컴퓨터를 게이트웨이로 실행할 수 있도록 데모 인증서를 사용 하 여 구성 했습니다. `leafdevice`가상 컴퓨터에서 IoT Central에 연결 하는 데 게이트웨이를 사용 하는 자동 온도 조절기 시뮬레이터를 설치할 준비가 되었습니다.

가상 컴퓨터 `leafdevice` 에는 가상 컴퓨터에서 만든 루트 CA 인증서의 복사본이 필요 합니다 `edgegateway` . 가상 머신에서 */home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem* 파일 `edgegateway` 을 가상 머신의 홈 디렉터리에 복사 합니다 `leafdevice` . **Scp** 명령을 사용 하 여 Linux 가상 머신에 파일을 복사할 수 있습니다.

다운스트림 장치에서 게이트웨이로의 연결을 확인 하는 방법을 알아보려면 [게이트웨이 연결 테스트](../../iot-edge/how-to-connect-downstream-device.md#test-the-gateway-connection)를 참조 하세요.

가상 머신에서 자동 온도 조절기 시뮬레이터를 실행 하려면 `leafdevice` 다음을 수행 합니다.

1. Python 샘플을 홈 디렉터리에 다운로드 합니다.

    ```bash
    cd ~
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/simple_thermostat.py
    ```

1. Azure IoT 장치 Python 모듈을 설치 합니다.

    ```bash
    sudo apt update
    sudo apt install python3-pip
    pip3 install azure.iot.device
    ```

1. 환경 변수를 설정하여 샘플을 구성합니다. 을 `{your device shared key}` 이전에 기록해 둔의 기본 키로 바꿉니다 `thermostat1` . 이러한 변수는 게이트웨이 가상 컴퓨터의 이름이이 `edgegateway` 고 자동 온도 조절기 장치의 ID 인 것으로 가정 합니다 `thermostat1` .

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    export IOTHUB_DEVICE_CONNECTION_STRING="HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}"
    export IOTEDGE_ROOT_CA_CERT_PATH=~/azure-iot-test-only.root.ca.cert.pem
    ```

    연결 문자열에 IoT hub 이름이 아니라 게이트웨이 장치 이름이 사용 되는 방식을 확인 합니다.

1. 코드를 실행 하려면 다음 명령을 사용 합니다.

    ```bash
    python3 simple_thermostat.py
    ```

    이 명령의 출력은 다음과 같습니다.

    ```output
    Connecting using Connection String HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry for temperature
    Sent message
    Sent message
    Sent message
    ...
    ```

1. IoT Central에서 원격 분석을 보려면 **thermostat1** 장치에 대 한 **개요** 페이지로 이동 합니다.

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-device-telemetry.png" alt-text="다운스트림 장치의 원격 분석을 보여 주는 스크린샷":::

    **정보** 페이지에서 다운스트림 장치에서 전송 된 속성 값을 볼 수 있으며, **명령** 페이지에서 다운스트림 장치에 대 한 명령을 호출할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 IoT Central를 사용 하 여 투명 게이트웨이를 구성 하는 방법을 배웠으므로 제안 된 다음 단계는 [IoT Edge](../../iot-edge/about-iot-edge.md)에 대해 자세히 알아보는 것입니다.
