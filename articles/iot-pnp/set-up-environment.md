---
title: IoT 플러그 앤 플레이에 필요한 IoT 리소스를 설정 합니다. Microsoft Docs
description: IoT 플러그 앤 플레이 빠른 시작 및 자습서에서 사용할 IoT Hub 및 장치 프로 비전 서비스 인스턴스를 만듭니다.
author: dominicbetts
ms.author: dobett
ms.date: 08/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a78dce748a70c99581e047280aa9ed1ca6598558
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580042"
---
# <a name="set-up-your-environment-for-the-iot-plug-and-play-quickstarts-and-tutorials"></a>IoT 플러그 앤 플레이 퀵 스타트 및 자습서에 대 한 환경 설정

IoT 플러그 앤 플레이 퀵 스타트 및 자습서를 완료 하기 전에 Azure 구독에서 IoT hub 및 DPS (장치 프로 비전 서비스)를 구성 해야 합니다. 또한 샘플 응용 프로그램 및 Azure IoT 탐색기 도구에서 사용 하는 모델 파일의 로컬 복사본이 필요 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

Azure CLI를 로컬로 설치 해야 하는 요구 사항을 방지 하기 위해 Azure Cloud Shell를 사용 하 여 클라우드 서비스를 설정할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resources"></a>리소스 만들기

리소스에 대 한 Azure 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name my-pnp-resourcegroup --location centralus
```

IoT Hub를 만듭니다. 다음 명령은 이름을 `my-pnp-hub` 만들 IoT hub의 이름에 대 한 예제로 이름을 사용 합니다. 대신 사용할 IoT hub의 고유한 이름을 선택 합니다 `my-pnp-hub` .

```azurecli-interactive
az iot hub create --name my-pnp-hub --resource-group my-pnp-resourcegroup --sku F1 --partition-count 2
```

DPS 인스턴스를 만듭니다. 다음 명령은 이름을 `my-pnp-dps` 만들려는 DPS 인스턴스의 이름에 대 한 예로 사용 합니다. 대신 사용할 DPS 인스턴스에 대해 고유한 이름을 선택 합니다 `my-pnp-dps` .

```azurecli-interactive
az iot dps create --name my-pnp-dps --resource-group my-pnp-resourcegroup
```

DPS 인스턴스를 IoT hub에 연결 하려면 다음 명령을 사용 합니다. `my-pnp-dps`및를 `my-pnp-hub` 이전에 선택한 고유한 이름으로 바꿉니다.

```azurecli-interactive
hubConnectionString=$(az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString -o tsv)
az iot dps linked-hub create --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --location centralus --connection-string $hubConnectionString
```

## <a name="retrieve-the-settings"></a>설정 검색

일부 퀵 스타트 및 자습서에서는 IoT hub에 대 한 연결 문자열을 사용 합니다. Azure IoT 탐색기 도구를 설정할 때 연결 문자열도 필요 합니다. 연결 문자열을 검색 하 고 지금 적어 둡니다. `my-pnp-hub`을 IoT hub에 대해 선택한 고유한 이름으로 바꿉니다.

```azurecli-interactive
az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString
```

대부분의 빠른 시작 및 자습서에서는 DPS 구성의 *ID 범위* 를 사용 합니다. ID 범위를 검색 하 고 지금 적어 둡니다. `my-pnp-dps`을 DPS 인스턴스에 대해 선택한 고유한 이름으로 바꿉니다.

```azurecli-interactive
az iot dps show --name my-pnp-dps --query properties.idScope
```

모든 퀵 스타트 및 자습서는 DPS 장치 등록을 사용 합니다. 다음 명령을 사용 하 여 `my-pnp-device` DPS 인스턴스에 *개별 장치 등록* 을 만듭니다. `my-pnp-dps`을 DPS 인스턴스에 대해 선택한 고유한 이름으로 바꿉니다. 퀵 스타트 및 자습서에서 사용할 등록 ID 및 기본 키 값을 기록해 둡니다.

```azurecli-interactive
az iot dps enrollment create --attestation-type symmetrickey --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --enrollment-id my-pnp-device --device-id my-pnp-device --query '{registrationID:registrationId,primaryKey:attestation.symmetricKey.primaryKey}'
```

## <a name="create-environment-variables"></a>환경 변수 만들기

5 개의 환경 변수를 만들어 빠른 시작 및 자습서에서 DPS (장치 프로 비전 서비스)를 사용 하 여 IoT hub에 연결 하는 샘플을 구성 합니다.

* **IOTHUB_DEVICE_SECURITY_TYPE**: 값 `DPS` 입니다.
* **IOTHUB_DEVICE_DPS_ID_SCOPE**: 이전에 기록해 둔 DPS ID 범위입니다.
* **IOTHUB_DEVICE_DPS_DEVICE_ID**: 값 `my-pnp-device` 입니다.
* **IOTHUB_DEVICE_DPS_DEVICE_KEY**: 이전에 기록해 둔 등록 기본 키입니다.
* **IOTHUB_DEVICE_DPS_ENDPOINT**: 값 `global.azure-devices-provisioning.net`

서비스 샘플은 연결할 허브 및 장치를 식별 하기 위해 다음 환경 변수를 요구 합니다.

* **IOTHUB_CONNECTION_STRING**: 이전에 적어 둔 IoT hub 연결 문자열입니다.
* **IOTHUB_DEVICE_ID**: `my-pnp-device`
* **DEVICE_ID**: `my-pnp-device`

예를 들어 Linux bash 셸에서는 다음과 같습니다.

```bash
export IOTHUB_DEVICE_SECURITY_TYPE="DPS"
export IOTHUB_DEVICE_DPS_ID_SCOPE="<Your ID scope>"
export IOTHUB_DEVICE_DPS_DEVICE_ID="my-pnp-device"
export IOTHUB_DEVICE_DPS_DEVICE_KEY="<Your enrolment primary key>"
export IOTHUB_DEVICE_DPS_ENDPOINT="global.azure-devices-provisioning.net"
export IOTHUB_CONNECTION_STRING="<Your IoT hub connection string>"
export IOTHUB_DEVICE_ID="my-pnp-device"
export DEVICE_ID="my-pnp-device"
```

예를 들어 Windows 명령줄에서 다음을 수행 합니다.

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<Your ID scope>
set IOTHUB_DEVICE_DPS_DEVICE_ID=my-pnp-device
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<Your enrolment primary key>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
set IOTHUB_CONNECTION_STRING=<Your IoT hub connection string>
set IOTHUB_DEVICE_ID=my-pnp-device
set DEVICE_ID=my-pnp-device
```

## <a name="download-the-model-files"></a>모델 파일 다운로드

퀵 스타트 및 자습서에서는 온도 컨트롤러 및 자동 온도 조절기 장치에 대 한 샘플 모델 파일을 사용 합니다. 샘플 모델 파일을 다운로드 하려면 다음을 수행 합니다.

1. *모델*이라는 폴더를 로컬 머신에 만듭니다.

1. [TemperatureController.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json)을 마우스 오른쪽 단추로 클릭하고 JSON 파일을 *모델* 폴더에 저장합니다.

1. [Thermostat.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json)을 마우스 오른쪽 단추로 클릭하고 JSON 파일을 *모델* 폴더에 저장합니다.

## <a name="install-the-azure-iot-explorer"></a>Azure IoT 탐색기 설치

퀵 스타트 및 자습서에서는 **Azure IoT 탐색기** 도구를 사용 합니다. [Azure IoT explorer 릴리스](https://github.com/Azure/azure-iot-explorer/releases) 로 이동 하 여 최신 릴리스에 대 한 자산 목록을 확장 합니다. 운영 체제용 응용 프로그램의 최신 버전을 다운로드 하 여 설치 합니다.

도구를 처음 실행할 때 IoT hub 연결 문자열을 입력 하 라는 메시지가 표시 됩니다. 이전에 적어 둔 연결 문자열을 사용 합니다.

이전에 다운로드 한 모델 파일을 사용 하도록 도구를 구성 합니다. 도구의 홈 페이지에서 **IoT 플러그 앤 플레이 설정**을 선택 하 고 **+ > 로컬 폴더 추가**를 선택 합니다. 이전에 만든 *모델* 폴더를 선택 합니다. 그런 다음, **저장** 을 선택 하 여 설정을 저장 합니다.

자세히 알아보려면 [Azure IoT 탐색기 설치 및 사용](howto-use-iot-explorer.md)을 참조 하세요.

## <a name="remove-the-resources"></a>리소스 제거

모든 IoT 플러그 앤 플레이 빠른 시작 및 자습서에 대해 IoT hub 및 DPS 인스턴스를 사용할 수 있으므로이 문서의 단계를 한 번만 완료 하면 됩니다. 완료 되 면 다음 명령을 사용 하 여 구독에서 제거할 수 있습니다.

```azurecli-interactive
az group delete --name my-pnp-resourcegroup
```

## <a name="next-steps"></a>다음 단계

환경을 설정 했으므로 다음과 같은 빠른 시작 또는 자습서 중 하나를 시도할 수 있습니다.

> [!div class="nextstepaction"]
> [샘플 IoT 플러그 앤 플레이 장치 응용 프로그램을 IoT Hub (Node.js)에 연결](quickstart-connect-device-node.md)
