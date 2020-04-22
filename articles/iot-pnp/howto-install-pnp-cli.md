---
title: Azure CLI에 대한 Azure IoT 확장을 사용하여 IoT 플러그 앤 플레이 미리 보기 장치와 상호 작용 | 마이크로 소프트 문서
description: Azure CLI용 Azure IoT 확장을 설치하고 이를 사용하여 IoT 허브에 연결된 IoT 플러그 앤 플레이 장치와 상호 작용합니다.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1ccb32996cd8f15805a810dd5b5985aeb5f87c26
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770452"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Azure CLI에 대한 Azure IoT 확장 설치 및 사용

[Azure CLI는](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) IoT Hub와 같은 Azure 리소스를 관리하기 위한 오픈 소스 교차 플랫폼 명령줄 도구입니다. Azure CLI는 Windows, Linux 및 MacOS에서 사용할 수 있습니다. Azure CLI는 Azure [클라우드 셸에도](https://shell.azure.com)사전 설치되어 있습니다. Azure CLI를 사용하면 확장을 설치하지 않고도 Azure IoT Hub 리소스, 장치 프로비저닝 서비스 인스턴스 및 연결된 허브를 관리할 수 있습니다.

Azure CLI에 대한 Azure IoT 확장은 IoT 플러그 앤 플레이 미리 보기 장치와 상호 작용하고 테스트하기 위한 명령줄 도구입니다. 확장을 사용하여 다음을 수행할 수 있습니다.

- 장치에 연결합니다.
- 장치가 보내는 원격 분석을 봅니다.
- 장치 속성으로 작업합니다.
- 장치 명령을 호출합니다.

이 아티클에서는 다음을 수행하는 방법을 보여줍니다.

- Azure CLI에 대한 Azure IoT 확장을 설치하고 구성합니다.
- 확장을 사용하여 장치와 상호 작용하고 테스트합니다.
- 확장을 사용하여 모델 리포지토리의 인터페이스를 관리합니다.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Azure CLI에 대한 Azure IoT 확장 설치

### <a name="step-1---install-the-azure-cli"></a>1단계 - Azure CLI 설치

설치 [지침에](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 따라 사용자 환경에서 Azure CLI를 설정합니다. 아래의 모든 명령을 사용하려면 Azure CLI 버전이 버전 2.0.73 이상이어야 합니다. `az -–version` 명령을 사용하여 유효성을 검사합니다.

### <a name="step-2---install-iot-extension"></a>2단계 - IoT 확장 설치

[IoT 확장 추가 정보](https://github.com/Azure/azure-iot-cli-extension)에는 확장을 설치하는 여러 가지 방법이 설명되어 있습니다. 가장 간단한 방법은 `az extension add --name azure-iot` 명령을 사용하는 것입니다. 설치 후 `az extension list` 명령을 사용하여 현재 설치된 확장의 유효성을 검사하거나 `az extension show --name azure-iot` 명령을 사용하여 IoT 확장에 대한 세부 정보를 볼 수 있습니다. 확장을 제거하려면 `az extension remove --name azure-iot` 명령을 사용합니다.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Azure CLI에 Azure IoT 확장 사용

### <a name="prerequisites"></a>사전 요구 사항

Azure 구독에 로그인하려면 다음 명령을 실행합니다.

```azurecli
az login
```

> [!NOTE]
> Azure 클라우드 셸을 사용하는 경우 자동으로 로그인되어 이전 명령을 실행할 필요가 없습니다.

Azure CLI에 Azure IoT 확장을 사용하려면 다음이 필요합니다.

- Azure IoT Hub - Azure CLI를 사용하여 IoT 허브 만들기와 같이 Azure 구독에 [IoT 허브를](../iot-hub/iot-hub-create-using-cli.md)추가하는 방법에는 여러 가지가 있습니다. Azure IoT 확장 명령을 실행하려면 IoT 허브의 연결 문자열이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- IoT 허브에 등록된 장치입니다. 다음 Azure CLI 명령을 사용하여 장치를 등록하고 자리 `{YourIoTHubName}` `{YourDeviceID}` 표시자를 값으로 바꿔야 합니다.

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- 일부 명령에는 회사 모델 리포지토리에 대한 연결 문자열이 필요합니다. IoT 에 대한 Azure 인증 포털에 처음 [온보온할](howto-onboard-portal.md)때 회사에 대한 모델 리포지토리가 만들어집니다. 타사에서 모델 리포지토리 연결 문자열을 공유하여 해당 인터페이스 및 모델에 액세스할 수 있습니다.

### <a name="interact-with-a-device"></a>장치와 상호 작용

확장을 사용하여 IoT 허브에 연결된 IoT 플러그 앤 플레이 장치를 보고 상호 작용할 수 있습니다. 확장은 IoT 플러그 앤 플레이 장치를 나타내는 디지털 트윈과 함께 작동합니다.

#### <a name="list-devices-and-interfaces"></a>장치 및 인터페이스 목록

IoT 허브에 모든 장치를 나열합니다.

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

IoT 플러그 앤 플레이 장치에 등록된 모든 인터페이스를 나열합니다.

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>속성

장치에서 인터페이스의 모든 속성 및 속성 값을 나열합니다.

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

읽기-쓰기 속성의 값을 설정합니다.

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

장치의 **센서** 인터페이스에서 **Contoso로** **이름** 속성을 설정하는 예제 페이로드 파일은 다음과 같습니다.

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

장치에서 인터페이스에 대한 모든 명령을 나열합니다.

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

매개 `--repo-login` 변수가 없으면 이 명령은 공용 모델 리포지토리를 사용합니다.

명령을 호출합니다.

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>디지털 트윈 이벤트

**$Default** 이벤트 허브 소비자 그룹으로 가는 특정 장치 및 인터페이스의 모든 IoT 플러그 앤 플레이 디지털 트윈 이벤트를 모니터링합니다.

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

특정 소비자 그룹이 가는 특정 장치 및 인터페이스의 모든 IoT 플러그 앤 플레이 디지털 트윈 이벤트를 모니터링합니다.

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>모델 리포지토리에서 인터페이스 관리

다음 명령은 공용 IoT 플러그 앤 플레이 모델 리포지토리를 사용합니다. 회사 모델 리포지토리를 사용하려면 `--login` 모델 리포지토리 연결 문자열에 인수를 추가합니다.

공용 IoT 플러그 앤 플레이 모델 리포지토리에 인터페이스를 나열합니다.

```azurecli
az iot pnp interface list
```

공용 IoT 플러그 앤 플레이 모델 리포지토리에 인터페이스 표시:

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

IoT 플러그 앤 플레이 회사 모델 리포지토리에서 인터페이스 만들기:

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

공용 모델 리포지토리에서 인터페이스를 직접 만들 수 없습니다.

IoT 플러그 앤 플레이 회사 모델 리포지토리의 인터페이스 업데이트:

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

공용 모델 리포지토리에서 인터페이스를 직접 업데이트할 수 없습니다.

IoT 플러그 앤 플레이 회사 모델 리포지토리의 인터페이스를 공용 모델 리포지토리에 게시합니다. 이 작업을 수행하면 인터페이스를 변경할 수 없습니다.

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Microsoft 파트너만 공용 모델 리포지토리에 인터페이스를 게시할 수 있습니다.

### <a name="manage-device-capability-models-in-a-model-repository"></a>모델 리포지토리에서 장치 기능 모델 관리

다음 명령은 공용 IoT 플러그 앤 플레이 모델 리포지토리를 사용합니다. 회사 모델 리포지토리를 사용하려면 `--login` 모델 리포지토리 연결 문자열에 인수를 추가합니다.

IoT 플러그 앤 플레이 공용 모델 리포지토리에서 장치 기능 모델을 나열합니다.

```azurecli
az iot pnp capability-model list
```

IoT 플러그 앤 플레이 공용 모델 리포지토리에서 장치 기능 모델을 표시합니다.

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

IoT 플러그 앤 플레이 회사 모델 리포지토리에서 장치 기능 모델 만들기:

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

공용 모델 리포지토리에서 모델을 직접 만들 수 없습니다.

IoT 플러그 앤 플레이 회사 모델 리포지토리에서 장치 기능 모델을 업데이트합니다.

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

공용 모델 리포지토리에서 모델을 직접 업데이트할 수 없습니다.

IoT 플러그 앤 플레이 회사 모델 리포지토리의 장치 기능 모델을 공용 모델 리포지토리에 게시합니다. 이 작업을 수행하면 모델을 변경할 수 없습니다.

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Microsoft 파트너만 모델을 공용 모델 리포지토리에 게시할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 방법 문서에서는 Azure CLI가 플러그 앤 플레이 장치와 상호 작용하는 Azure IoT 확장을 설치하고 사용하는 방법을 배웠습니다. 다음 단계는 [모델을 관리하는](./howto-manage-models.md)방법을 알아보는 것입니다.
