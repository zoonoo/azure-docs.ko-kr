---
title: Azure CLI용 Azure IoT 확장을 사용하여 IoT 플러그 앤 플레이 미리 보기 디바이스와 상호 작용 | Microsoft Docs
description: Azure CLI용 Azure IoT 확장을 설치하고 사용하여 IoT Hub에 연결된 IoT 플러그 앤 플레이 디바이스와 상호 작용합니다.
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: dadb1f044547acd6e5f0d274143123e89d7dae46
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475484"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Azure CLI용 Azure IoT 확장 설치 및 사용

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)는 IoT Hub 같은 Azure 리소스를 관리하기 위한 오픈 소스 교차 플랫폼 명령줄 도구입니다. Azure CLI는 Windows, Linux 및 macOS에서 사용할 수 있습니다. Azure CLI를 사용하면 확장을 설치하지 않고 Azure IoT Hub 리소스, 디바이스 프로비저닝 서비스 인스턴스 및 연결된 허브를 관리할 수 있습니다.

Azure CLI용 Azure IoT 확장은 IoT 플러그 앤 플레이 미리 보기 디바이스와 상호 작용하고 테스트하기 위한 명령줄 도구입니다. 이 확장을 사용하여 다음을 수행할 수 있습니다.

- 디바이스에 연결합니다.
- 디바이스에 전송하는 원격 분석을 확인합니다.
- 디바이스 속성을 사용합니다.
- 디바이스 명령을 호출합니다.

이 아티클에서는 다음을 수행하는 방법을 보여줍니다.

- Azure CLI용 Azure IoT 확장을 설치하고 구성합니다.
- 확장을 사용하여 디바이스와 상호 작용하고 테스트합니다.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Azure CLI용 Azure IoT 확장 설치

### <a name="step-1---install-the-azure-cli"></a>1단계 - Azure CLI 설치

[설치 지침](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)에 따라 환경에 Azure CLI를 설치합니다. 최상의 환경을 위해 Azure CLI 버전은 2.9.1 이상 버전 이어야 합니다. `az -–version` 명령을 사용하여 유효성을 검사합니다.

### <a name="step-2---install-iot-extension"></a>2단계 - IoT 확장 설치

[IoT 확장 추가 정보](https://github.com/Azure/azure-iot-cli-extension)에는 확장을 설치하는 여러 가지 방법이 설명되어 있습니다. 가장 간단한 방법은 `az extension add --name azure-iot` 명령을 사용하는 것입니다. 설치 후 `az extension list` 명령을 사용하여 현재 설치된 확장의 유효성을 검사하거나 `az extension show --name azure-iot` 명령을 사용하여 IoT 확장에 대한 세부 정보를 볼 수 있습니다. 작성 시 확장 버전 번호는 `0.9.7` 입니다.

확장을 제거하려면 `az extension remove --name azure-iot` 명령을 사용합니다.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Azure CLI용 Azure IoT 확장 사용

### <a name="prerequisites"></a>필수 구성 요소

Azure 구독에 로그인하려면 다음 명령을 실행합니다.

```azurecli
az login
```

Azure CLI용 Azure IoT 확장을 사용하려면 다음이 필요합니다.

- Azure IoT Hub - Azure 구독에 IoT Hub를 추가하는 방법에는 [Azure CLI를 사용하여 IoT Hub 만들기](../iot-hub/iot-hub-create-using-cli.md) 등을 비롯한 여러 가지가 있습니다. Azure IoT 확장 명령을 실행하려면 IoT Hub의 연결 문자열이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- IoT Hub에 등록된 디바이스. 다음 Azure CLI 명령을 사용하여 디바이스를 등록할 수 있습니다. `{YourIoTHubName}` 및 `{YourDeviceID}` 자리 표시자를 원하는 값으로 바꾸어야 합니다.

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

### <a name="interact-with-a-device"></a>디바이스와 상호 작용

확장을 사용하여 IoT Hub에 연결된 IoT 플러그 앤 플레이 디바이스를 보고 상호 작용할 수 있습니다. 확장은 IoT 플러그 앤 플레이 디바이스를 나타내는 디지털 쌍에서 작동합니다.

#### <a name="list-devices"></a>디바이스 나열

IoT Hub의 모든 디바이스를 나열합니다.

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

#### <a name="view-digital-twin"></a>디지털 쌍 보기

장치의 디지털 쌍 보기:

```azurecli
az iot pnp twin show -n {iothub_name} -d {device_id}
```

#### <a name="properties"></a>속성

읽기/쓰기 속성의 값을 설정합니다.

```azurecli
az iot pnp twin update --hub-name {iothub_name} --device-id {device_id} --json-patch '{"op":"add", "path":"/thermostat1/targetTemperature", "value": 54}'
```

#### <a name="commands"></a>명령

명령을 호출합니다.

```azurecli
az iot pnp twin invoke-command --cn getMaxMinReport -n {iothub_name} -d {device_id} --component-path thermostat1
```

#### <a name="digital-twin-events"></a>디지털 쌍 이벤트

특정 디바이스 및 인터페이스에서 **$Default** 이벤트 허브 소비자 그룹으로 이동하는 모든 IoT 플러그 앤 플레이 디지털 쌍 이벤트를 모니터링합니다.

```azurecli
az iot hub monitor-events -n {iothub_name} -d {device_id} -i {interface_id}
```

### <a name="manage-models-in-the-model-repository"></a>모델 리포지토리에서 모델 관리

Azure CLI 모델 리포지토리 명령을 사용 하 여 리포지토리의 모델을 관리할 수 있습니다.

#### <a name="create-model-repository"></a>모델 리포지토리 만들기

테 넌 트의 첫 번째 사용자 인 경우 테 넌 트에 대해 새 IoT 플러그 앤 플레이 회사 리포지토리를 만듭니다.

```azurecli
az iot pnp repo create
```

#### <a name="manage-model-repository-tenant-roles"></a>모델 리포지토리 테 넌 트 역할 관리

특정 리소스에 대 한 사용자 또는 서비스 주체에 대 한 역할 할당을 만듭니다.

예를 들어 user@consoso.com 테 넌 트에 대해 **ModelsCreator** 의 역할을 제공 합니다.

```azurecli
az iot pnp role-assignment create --resource-id {tenant_id} --resource-type Tenant --subject-id {user@contoso.com} --subject-type User --role ModelsCreator
```

또는 user@consoso.com 특정 모델에 대해 **modeladministrator** 의 역할을 부여 합니다.

```azurecli
az iot pnp role-assignment create --resource-id {model_id} --resource-type Model --subject-id {user@contoso.com} --subject-type User --role ModelAdministrator
```

#### <a name="create-a-model"></a>모델 만들기

회사 리포지토리에서 새 모델을 만듭니다.

```azurecli
az iot pnp model create --model {model_json or path_to_file}
```

#### <a name="search-a-model"></a>모델 검색

특정 키워드와 일치 하는 모델 나열:

```azurecli
az iot pnp model list -q {search_keyword}
```

#### <a name="publish-a-model"></a>모델 게시

회사 리포지토리에 있는 장치 모델을 공용 리포지토리에 게시 합니다.

예를 들어 ID를 사용 하 여 모델을 공개로 설정 합니다 `dtmi:com:example:ClimateSensor;1` .

```azurecli
az iot pnp model publish --dtmi "dtmi:com:example:ClimateSensor;1"
```

모델을 게시 하려면 다음 요구 사항을 충족 해야 합니다.

- 회사 또는 조직 테 넌 트는 Microsoft 파트너 여야 합니다. 
- 사용자 또는 서비스 주체는 저장소 테 넌 트의 **게시자** 역할의 멤버 여야 합니다.

## <a name="next-steps"></a>다음 단계

이 방법 문서에서는 Azure CLI에 대 한 Azure IoT 확장을 설치 하 고 사용 하 여 IoT 플러그 앤 플레이 장치와 상호 작용 하는 방법을 배웠습니다. 제안 된 다음 단계는 [장치에서 Azure IoT 탐색기](./howto-use-iot-explorer.md)를 사용 하는 방법을 알아보는 것입니다.
