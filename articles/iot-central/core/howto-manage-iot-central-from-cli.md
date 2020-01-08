---
title: Azure CLI에서 IoT Central 관리 | Microsoft Docs
description: 이 문서에서는 CLI를 사용 하 여 IoT Central 응용 프로그램을 만들고 관리 하는 방법을 설명 합니다. CLI를 사용 하 여 응용 프로그램을 보고, 수정 하 고, 제거할 수 있습니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 1051ea91378cc2e2facec7e34f6d303297b91ce8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454060"
---
# <a name="manage-iot-central-from-azure-cli"></a>Azure CLI에서 IoT Central 관리

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

[Azure IoT Central application manager](https://aka.ms/iotcentral) 웹 사이트에서 IoT Central 응용 프로그램을 만들고 관리 하는 대신 [Azure CLI](/cli/azure/) 를 사용 하 여 응용 프로그램을 관리할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

로컬 컴퓨터에서 Azure CLI를 실행 하려면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조 하세요. Azure CLI를 로컬로 실행 하는 경우 **az login** 명령을 사용 하 여 Azure에 로그인 한 후이 문서의 명령을 시도 합니다.

## <a name="create-an-application"></a>애플리케이션 만들기

[Az iotcentral app create](/cli/azure/iotcentral/app#az-iotcentral-app-create) 명령을 사용 하 여 Azure 구독에 IoT Central 응용 프로그램을 만듭니다. 예:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku S1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

이러한 명령은 먼저 응용 프로그램의 미국 동부 위치에 리소스 그룹을 만듭니다. 다음 표에서는 **az iotcentral app create** 명령에 사용 되는 매개 변수에 대해 설명 합니다.

| 매개 변수         | Description |
| ----------------- | ----------- |
| resource-group    | 애플리케이션을 포함하는 리소스 그룹입니다. 리소스 그룹이 구독에 이미 있어야 합니다. |
| 위치          | 기본적으로이 명령은 리소스 그룹의 위치를 사용 합니다. 현재 **미국**, **오스트레일리아**, **아시아 태평양**또는 **유럽** 위치에서 IoT Central 응용 프로그램을 만들 수 있습니다. |
| name              | Azure Portal의 애플리케이션 이름입니다. |
| 도메인이         | 애플리케이션 URL의 하위 도메인입니다. 예제에서 애플리케이션 URL은 https://mysubdomain.azureiotcentral.com 입니다. |
| sku               | 현재 유일한 값은 **S1**(표준 계층)입니다. [Azure IoT Central 가격 책정](https://azure.microsoft.com/pricing/details/iot-central/)을 참조하세요. |
| template          | 사용할 애플리케이션 템플릿입니다. 자세한 내용은 다음 표를 참조하세요. |
| display-name      | UI에 표시되는 애플리케이션 이름입니다. |

**일반적으로 제공 되는 기능을 사용 하는 응용 프로그램 템플릿**

| 템플릿 이름            | Description |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | 사용자 고유의 디바이스 템플릿 및 디바이스로 채울 빈 애플리케이션을 만듭니다.


**공개 미리 보기 기능을 사용 하는 응용 프로그램 템플릿**

| 템플릿 이름            | Description |
| ------------------------ | ----------- |
| iotc-pnp-preview@1.0.0   | 사용자 고유의 장치 템플릿 및 장치로 채울 빈 플러그 앤 플레이 미리 보기 응용 프로그램을 만듭니다. |
| iotc-condition@1.0.0     | 저장소 내 분석-조건 모니터링 템플릿을 사용 하 여 응용 프로그램을 만듭니다. 이 템플릿을 사용 하 여 저장소 환경을 연결 하 고 모니터링 합니다. |
| iotc-consumption@1.0.0   | 워터 마크 사용 모니터링 템플릿을 사용 하 여 응용 프로그램을 만듭니다. 이 템플릿을 사용 하 여 급수 흐름을 모니터링 하 고 제어할 수 있습니다. |
| iotc-distribution@1.0.0  | 디지털 배포 템플릿을 사용 하 여 응용 프로그램을 만듭니다. 이 템플릿을 사용 하 여 주요 자산 및 작업을 digitalizing 하 여 웨어하우스 출력 효율성을 향상 시킵니다. |
| iotc-inventory@1.0.0     | 스마트 재고 관리 템플릿을 사용 하 여 응용 프로그램을 만듭니다. 이 템플릿을 사용 하 여 수신, 제품 이동, 주기 횟수 및 센서 추적을 자동화할 수 있습니다. |
| iotc-logistics@1.0.0     | 연결 된 물류 템플릿을 사용 하 여 응용 프로그램을 만듭니다. 이 템플릿을 사용 하 여 위치 및 조건 모니터링과 함께 항공, 물 및 땅에서 실시간으로 배송을 추적할 수 있습니다. |
| iotc-meter@1.0.0         | 스마트 측정기 모니터링 템플릿을 사용 하 여 응용 프로그램을 만듭니다. 이 템플릿을 사용 하 여 에너지 소비량, 네트워크 상태를 모니터링 하 고 고객 지원 및 스마트 측정기 관리를 개선 하는 추세를 식별 합니다.  |
| iotc-patient@1.0.0       | 연속 환자 모니터링 템플릿을 사용 하 여 응용 프로그램을 만듭니다. 이 템플릿을 사용 하 여 환자를 확장 하 고, 다시 입학, 질병을 관리 합니다. |
| iotc-power@1.0.0         | 양력인 패널 모니터링 템플릿을 사용 하 여 응용 프로그램을 만듭니다. 이 템플릿을 사용 하 여 양력인 패널 상태, 에너지 생성 추세를 모니터링할 수 있습니다. |
| iotc-quality@1.0.0       | 급수 품질 모니터링 템플릿을 사용 하 여 응용 프로그램을 만듭니다. 이 템플릿을 사용 하 여 물 품질을 디지털 모니터링할 수 있습니다.|
| iotc-store@1.0.0         | 저장소 내 분석 – 체크 아웃 템플릿을 사용 하 여 응용 프로그램을 만듭니다. 이 템플릿을 사용 하 여 스토어 내에서 체크 아웃 흐름을 모니터링 하 고 관리 합니다. |
| iotc-waste@1.0.0         | 연결 된 폐기물 관리 템플릿을 사용 하 여 응용 프로그램을 만듭니다. 이 템플릿을 사용 하 여 폐기물을 모니터링 하 고 필드 연산자를 디스패치합니다. |

> [!NOTE]
> Preview 응용 프로그램 템플릿은 현재 **유럽** 및 **미국** 지역 에서만 사용할 수 있습니다.

## <a name="view-your-applications"></a>애플리케이션 보기

[Az iotcentral app list](/cli/azure/iotcentral/app#az-iotcentral-app-list) 명령을 사용 하 여 IoT Central 응용 프로그램을 나열 하 고 메타 데이터를 볼 수 있습니다.

## <a name="modify-an-application"></a>애플리케이션 수정

[Az iotcentral app update](/cli/azure/iotcentral/app#az-iotcentral-app-update) 명령을 사용 하 여 IoT Central 응용 프로그램의 메타 데이터를 업데이트 합니다. 예를 들어 애플리케이션의 표시 이름을 변경합니다.

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>애플리케이션 제거

[Az iotcentral app delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) 명령을 사용 하 여 IoT Central 응용 프로그램을 삭제 합니다. 예:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>다음 단계

이제 Azure CLI에서 Azure IoT Central 응용 프로그램을 관리 하는 방법을 배웠으므로 제안 된 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)
