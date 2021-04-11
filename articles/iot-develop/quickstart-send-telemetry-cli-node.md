---
title: Azure IoT Hub에 디바이스 원격 분석 보내기 빠른 시작(Node.js)
description: 이 빠른 시작에서는 Node.js용 Azure IoT Hub 디바이스 SDK를 사용하여 원격 분석을 디바이스에서 IoT 허브로 보냅니다.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 047700be674dfab997b5c87f7446c19fdea9e0eb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605963"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>빠른 시작: 원격 분석을 디바이스에서 IoT 허브로 보내기(Node.js)

**적용 대상**: [디바이스 애플리케이션 개발](about-iot-develop.md#device-application-development)

이 빠른 시작에서는 기본 IoT 디바이스 애플리케이션 개발 워크플로에 대해 알아봅니다. Azure CLI를 사용하여 Azure IoT 허브와 시뮬레이션된 디바이스를 만든 다음, Azure IoT Node.js SDK를 사용하여 디바이스에 액세스하고 허브로 원격 분석을 보냅니다.

## <a name="prerequisites"></a>사전 요구 사항
- Azure 구독이 아직 없는 경우 시작하기 전에 [무료 구독을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure CLI. 브라우저에서 실행되는 대화형 CLI 셸인 Azure Cloud Shell을 사용하여 이 빠른 시작의 모든 명령을 실행할 수 있습니다. Cloud Shell을 사용하는 경우에는 아무 것도 설치할 필요가 없습니다. CLI를 로컬로 사용하려면 이 빠른 시작에 Azure CLI 버전 2.0.76 이상이 필요합니다. 버전을 찾으려면 az --version을 실행합니다. 설치하거나 업그레이드하려면 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.
- [Node.js 10 이상](https://nodejs.org). Azure Cloud Shell을 사용하는 경우 설치된 Node.js 버전을 업데이트하지 마세요. Azure Cloud Shell에는 이미 최신 Node.js 버전이 있습니다.

    다음 명령을 사용하여 개발 머신에서 Node.js의 현재 버전을 확인합니다.

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Node.js SDK를 사용하여 메시지 보내기
이 섹션에서는 Node.js SDK를 사용하여 시뮬레이션된 디바이스에서 IoT 허브로 메시지를 보냅니다. 

1. 새 터미널 창을 엽니다. 이 터미널을 사용하여 Node.js SDK를 설치하고 Node.js 샘플 코드로 작업합니다. 이제 두 개의 터미널 즉, Node.js로 작업하기 위해 방금 연 터미널과 이전 섹션에서 Azure CLI 명령을 입력하기 위해 사용한 CLI 셸이 열려 있어야 합니다.

1. [Azure IoT Node.js SDK 디바이스 샘플](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)을 로컬 컴퓨터에 복사합니다.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. *azure-iot-sdk-node/device/samples/pnp* 디렉터리로 이동합니다.

    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```

1. Azure IoT Node.js SDK 및 필요한 종속성을 설치합니다.

    ```console
    npm install
    ```

    이 명령은 디바이스 샘플 디렉터리의 *package.json* 파일에 지정된 대로 적절한 종속성을 설치합니다.

1. 시뮬레이션된 디바이스에서 Azure IoT에 연결할 수 있도록 다음 환경 변수를 모두 설정합니다.
    * `IOTHUB_DEVICE_CONNECTION_STRING`이라는 환경 변수를 설정합니다. 변수 값의 경우 이전 섹션에서 저장한 디바이스 연결 문자열을 사용합니다.
    * `IOTHUB_DEVICE_SECURITY_TYPE`이라는 환경 변수를 설정합니다. 변수의 경우 리터럴 문자열 값 `connectionString`을 사용합니다.

    **Windows(cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```
    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > Windows CMD의 경우 각 변수의 문자열 값을 묶는 따옴표가 없습니다.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    ```
    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash(Linux 또는 Windows)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```
    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```
1. 열려 있는 CLI 셸에서 [az iot hub monitor-events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) 명령을 실행하여 시뮬레이션된 IoT 디바이스의 이벤트 모니터링을 시작합니다.  이벤트 메시지가 도착하면 터미널에 출력됩니다.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Node.js 터미널에서 설치된 샘플 파일 *simple_thermostat.js* 의 코드를 실행합니다. 이 코드는 시뮬레이션된 IoT 디바이스에 액세스하고, 메시지를 IoT 허브에 보냅니다.

    터미널에서 Node.js 샘플을 실행하려면 다음을 수행합니다.
    ```console
    node ./simple_thermostat.js
    ```
    > [!NOTE]
    > 이 코드 샘플에서는 수동 구성 없이 솔루션에 스마트 디바이스를 통합할 수 있도록 하는 Azure IoT 플러그 앤 플레이를 사용합니다.  기본적으로 이 설명서에 있는 대부분의 샘플은 IoT 플러그 앤 플레이를 사용합니다. IoT PnP의 장점과 사용 여부에 대한 자세한 내용은 [IoT 플러그 앤 플레이란?](../iot-pnp/overview-iot-plug-and-play.md)을 참조하세요.

Node.js 코드가 디바이스에서 IoT 허브로 시뮬레이션된 원격 분석 메시지를 보내면 이벤트를 모니터링하는 CLI 셸에 메시지가 나타납니다.

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device ID>
  payload:
    temperature: 36.87027777131555
```

이제 디바이스가 안전하게 연결되어 Azure IoT Hub로 원격 분석을 보냅니다.

## <a name="clean-up-resources"></a>리소스 정리
이 빠른 시작에서 만든 Azure 리소스가 더 이상 필요하지 않은 경우 Azure CLI를 사용하여 삭제할 수 있습니다.

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹 및 그 안에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 

리소스 그룹을 이름으로 삭제하려면:
1. [az group delete](/cli/azure/group#az-group-delete) 명령을 실행합니다. 이 명령을 실행하면 만든 리소스 그룹, IoT Hub 및 디바이스 등록이 제거됩니다.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. [az group list](/cli/azure/group#az-group-list) 명령을 실행하여 리소스 그룹을 삭제했는지 확인합니다.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 디바이스를 클라우드에 안전하게 연결하고 디바이스-클라우드 원격 분석을 보내는 데 사용할 수 있는 기본 Azure IoT 애플리케이션 워크플로를 알아보았습니다. Azure CLI를 사용하여 IoT 허브와 시뮬레이션된 디바이스를 만든 다음, Azure IoT Node.js SDK를 사용하여 디바이스에 액세스하고 허브로 원격 분석을 보냈습니다. 

다음 단계로 애플리케이션 샘플을 통해 Azure IoT Node.js SDK를 살펴봅니다.

- [추가 Node.js 샘플](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples): 이 디렉터리에는 IoT Hub 시나리오를 보여주기 위해 Node.js SDK 리포지토리의 추가 샘플이 포함되어 있습니다.