---
title: Azure CLI용 Azure IoT 확장을 사용하여 IoT 플러그 앤 플레이 미리 보기 디바이스와 상호 작용 | Microsoft Docs
description: Azure CLI용 Azure IoT 확장을 설치하고 사용하여 IoT Hub에 연결된 IoT 플러그 앤 플레이 디바이스와 상호 작용합니다.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1ccb32996cd8f15805a810dd5b5985aeb5f87c26
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770452"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Azure CLI용 Azure IoT 확장 설치 및 사용

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)는 IoT Hub 같은 Azure 리소스를 관리하기 위한 오픈 소스 교차 플랫폼 명령줄 도구입니다. Azure CLI는 Windows, Linux 및 MacOS에서 사용할 수 있습니다. Azure CLI는 [Azure Cloud Shell](https://shell.azure.com)에도 미리 설치됩니다. Azure CLI를 사용하면 확장을 설치하지 않고 Azure IoT Hub 리소스, 디바이스 프로비저닝 서비스 인스턴스 및 연결된 허브를 관리할 수 있습니다.

Azure CLI용 Azure IoT 확장은 IoT 플러그 앤 플레이 미리 보기 디바이스와 상호 작용하고 테스트하기 위한 명령줄 도구입니다. 이 확장을 사용하여 다음을 수행할 수 있습니다.

- 디바이스에 연결합니다.
- 디바이스에 전송하는 원격 분석을 확인합니다.
- 디바이스 속성을 사용합니다.
- 디바이스 명령을 호출합니다.

이 아티클에서는 다음을 수행하는 방법을 보여줍니다.

- Azure CLI용 Azure IoT 확장을 설치하고 구성합니다.
- 확장을 사용하여 디바이스와 상호 작용하고 테스트합니다.
- 확장을 사용하여 모델 리포지토리에서 인터페이스를 관리합니다.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Azure CLI용 Azure IoT 확장 설치

### <a name="step-1---install-the-azure-cli"></a>1단계 - Azure CLI 설치

[설치 지침](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)에 따라 환경에 Azure CLI를 설치합니다. 아래의 모든 명령을 사용하려면 Azure CLI 버전이 버전 2.0.73 이상이어야 합니다. `az -–version` 명령을 사용하여 유효성을 검사합니다.

### <a name="step-2---install-iot-extension"></a>2단계 - IoT 확장 설치

[IoT 확장 추가 정보](https://github.com/Azure/azure-iot-cli-extension)에는 확장을 설치하는 여러 가지 방법이 설명되어 있습니다. 가장 간단한 방법은 `az extension add --name azure-iot` 명령을 사용하는 것입니다. 설치 후 `az extension list` 명령을 사용하여 현재 설치된 확장의 유효성을 검사하거나 `az extension show --name azure-iot` 명령을 사용하여 IoT 확장에 대한 세부 정보를 볼 수 있습니다. 확장을 제거하려면 `az extension remove --name azure-iot` 명령을 사용합니다.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Azure CLI용 Azure IoT 확장 사용

### <a name="prerequisites"></a>필수 구성 요소

Azure 구독에 로그인하려면 다음 명령을 실행합니다.

```azurecli
az login
```

> [!NOTE]
> Azure Cloud Shell을 사용하는 경우 자동으로 로그인되며 이전 명령을 실행할 필요가 없습니다.

Azure CLI용 Azure IoT 확장을 사용하려면 다음이 필요합니다.

- Azure IoT Hub - Azure 구독에 IoT Hub를 추가하는 방법에는 [Azure CLI를 사용하여 IoT Hub 만들기](../iot-hub/iot-hub-create-using-cli.md) 등을 비롯한 여러 가지가 있습니다. Azure IoT 확장 명령을 실행하려면 IoT Hub의 연결 문자열이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- IoT Hub에 등록된 디바이스. 다음 Azure CLI 명령을 사용하여 디바이스를 등록할 수 있습니다. `{YourIoTHubName}` 및 `{YourDeviceID}` 자리 표시자를 원하는 값으로 바꾸어야 합니다.

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- 일부 명령에는 회사 모델 리포지토리에 대한 연결 문자열이 필요합니다. 회사의 모델 리포지토리는 처음에 [IoT용 Azure Certified 포털에 온보딩](howto-onboard-portal.md)할 때 생성됩니다. 타사에서는 해당 인터페이스 및 모델에 대한 액세스를 제공하기 위해 모델 리포지토리 연결 문자열을 사용자와 공유할 수 있습니다.

### <a name="interact-with-a-device"></a>디바이스와 상호 작용

확장을 사용하여 IoT Hub에 연결된 IoT 플러그 앤 플레이 디바이스를 보고 상호 작용할 수 있습니다. 확장은 IoT 플러그 앤 플레이 디바이스를 나타내는 디지털 쌍에서 작동합니다.

#### <a name="list-devices-and-interfaces"></a>디바이스 및 인터페이스 나열

IoT Hub의 모든 디바이스를 나열합니다.

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

IoT 플러그 앤 플레이 디바이스에서 등록된 모든 인터페이스를 나열합니다.

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>속성

디바이스에서 인터페이스에 대한 모든 속성 및 속성 값을 나열합니다.

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

읽기/쓰기 속성의 값을 설정합니다.

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

디바이스의 **센서** 인터페이스에서 **name** 속성을 **Contoso**로 설정하는 예제 페이로드 파일은 다음과 같습니다.

```json
{
  "sensor": {
    "properties": {
      "name": {
        "desired": {
          "value": "Contoso"
        }
      }
    }
  }
}
```

#### <a name="commands"></a>명령

디바이스에서 인터페이스에 대한 모든 명령을 나열합니다.

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

`--repo-login` 매개 변수를 사용하지 않으면 이 명령은 퍼블릭 모델 리포지토리를 사용합니다.

명령을 호출합니다.

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>디지털 쌍 이벤트

특정 디바이스 및 인터페이스에서 **$Default** 이벤트 허브 소비자 그룹으로 이동하는 모든 IoT 플러그 앤 플레이 디지털 쌍 이벤트를 모니터링합니다.

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

특정 디바이스 및 인터페이스에서 특정 소비자 그룹으로 이동하는 모든 IoT 플러그 앤 플레이 디지털 쌍 이벤트를 모니터링합니다.

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>모델 리포지토리에서 인터페이스 관리

다음 명령은 퍼블릭 IoT 플러그 앤 플레이 모델 리포지토리를 사용합니다. 회사 모델 리포지토리를 사용하려면 모델 리포지토리 연결 문자열과 함께 `--login` 인수를 추가합니다.

퍼블릭 IoT 플러그 앤 플레이 모델 리포지토리의 인터페이스를 나열합니다.

```azurecli
az iot pnp interface list
```

퍼블릭 IoT 플러그 앤 플레이 모델 리포지토리의 인터페이스를 표시합니다.

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

IoT 플러그 앤 플레이 회사 모델 리포지토리에서 인터페이스를 만듭니다.

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

퍼블릭 모델 리포지토리에서는 인터페이스를 직접 만들 수 없습니다.

IoT 플러그 앤 플레이 회사 모델 리포지토리에서 인터페이스를 업데이트합니다.

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

퍼블릭 모델 리포지토리에서는 인터페이스를 직접 업데이트할 수 없습니다.

IoT 플러그 앤 플레이 회사 모델 리포지토리의 인터페이스를 퍼블릭 모델 리포지토리에 게시합니다. 이 작업을 수행하면 인터페이스를 변경할 수 없습니다.

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Microsoft 파트너만 인터페이스를 퍼블릭 모델 리포지토리에 게시할 수 있습니다.

### <a name="manage-device-capability-models-in-a-model-repository"></a>모델 리포지토리에서 디바이스 기능 모델 관리

다음 명령은 퍼블릭 IoT 플러그 앤 플레이 모델 리포지토리를 사용합니다. 회사 모델 리포지토리를 사용하려면 모델 리포지토리 연결 문자열과 함께 `--login` 인수를 추가합니다.

IoT 플러그 앤 플레이 퍼블릭 모델 리포지토리의 디바이스 기능 모델을 나열합니다.

```azurecli
az iot pnp capability-model list
```

IoT 플러그 앤 플레이 퍼블릭 모델 리포지토리의 디바이스 기능 모델을 표시합니다.

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

IoT 플러그 앤 플레이 회사 모델 리포지토리에 디바이스 기능 모델을 만듭니다.

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

퍼블릭 모델 리포지토리에서 모델을 직접 만들 수는 없습니다.

IoT 플러그 앤 플레이 회사 모델 리포지토리에 디바이스 기능 모델을 업데이트합니다.

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

퍼블릭 모델 리포지토리에서 모델을 직접 업데이트할 수는 없습니다.

IoT 플러그 앤 플레이 회사 모델 리포지토리의 디바이스 기능 모델을 퍼블릭 모델 리포지토리에 게시합니다. 이 작업을 수행하면 모델을 변경할 수 없습니다.

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Microsoft 파트너만 모델을 퍼블릭 모델 리포지토리에 게시할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 방법 문서에서는 Azure CLI용 Azure IoT 확장을 설치 및 사용하여 플러그 앤 플레이 디바이스와 상호 작용하는 방법을 배웠습니다. 제안된 다음 단계는 [모델 관리](./howto-manage-models.md) 방법을 배우는 것입니다.
