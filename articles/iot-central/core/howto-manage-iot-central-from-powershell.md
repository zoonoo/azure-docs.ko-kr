---
title: Azure PowerShell에서 IoT Central 관리 | Microsoft Docs
description: 이 문서에서는 Azure PowerShell에서 IoT Central 응용 프로그램을 만들고 관리 하는 방법을 설명 합니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: a95b59c6cc0d486c1d4b10f39d0d272dd4b34f54
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018995"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Azure PowerShell에서 IoT Central 관리

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

[Azure IoT Central application manager](https://aka.ms/iotcentral) 웹 사이트에서 IoT Central 응용 프로그램을 만들고 관리 하는 대신 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 를 사용 하 여 응용 프로그램을 관리할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

로컬 머신에서 Azure PowerShell을 실행하려면 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)를 참조하세요. 로컬에서 Azure PowerShell을 실행하는 경우 이 문서의 cmdlet을 사용하기 전에 **Connect-AzAccount** cmdlet을 사용하여 Azure에 로그인합니다.

## <a name="install-the-iot-central-module"></a>IoT Central 모듈 설치

다음 명령을 실행하여 PowerShell 환경에 [IoT Central 모듈](https://docs.microsoft.com/powershell/module/az.iotcentral/)이 설치되었는지 확인합니다.

```powershell
Get-InstalledModule -name Az.I*
```

설치된 모듈 목록에 **Az.IotCentral**이 포함되지 않은 경우 다음 명령을 실행합니다.

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>애플리케이션 만들기

[New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) cmdlet을 사용하여 Azure 구독에 IoT Central 애플리케이션을 만듭니다. 예:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

스크립트는 먼저 애플리케이션의 미국 동부 지역에 리소스 그룹을 만듭니다. 다음 표에서는 **New-AzIotCentralApp** 명령에 사용되는 매개 변수를 설명합니다.

|매개 변수         |Description |
|------------------|------------|
|ResourceGroupName |애플리케이션을 포함하는 리소스 그룹입니다. 리소스 그룹이 구독에 이미 있어야 합니다. |
|위치 |기본적으로 이 cmdlet은 리소스 그룹의 위치를 사용합니다. 현재 **미국 동부**, **미국 서 부**, **유럽**서 부, **유럽 서부** 지역 또는 **오스트레일리아** 또는 **아시아 태평양** 지역에서 IoT Central 응용 프로그램을 만들 수 있습니다.  |
|이름              |Azure Portal의 애플리케이션 이름입니다. |
|하위 도메인         |애플리케이션 URL의 하위 도메인입니다. 예제에서 애플리케이션 URL은 https://mysubdomain.azureiotcentral.com 입니다. |
|SKU               |현재 **ST1** 또는 **ST2**중 하나를 사용할 수 있습니다. [Azure IoT Central 가격 책정](https://azure.microsoft.com/pricing/details/iot-central/)을 참조하세요. |
|템플릿          | 사용할 애플리케이션 템플릿입니다. 자세한 내용은 다음 표를 참조하세요. |
|DisplayName       |UI에 표시되는 애플리케이션 이름입니다. |

**애플리케이션 템플릿**

| 템플릿 이름            | Description |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | 사용자 고유의 디바이스 템플릿 및 디바이스로 채울 빈 애플리케이션을 만듭니다.
| iotc-pnp-preview@1.0.0   | 사용자 고유의 장치 템플릿 및 장치로 채울 빈 플러그 앤 플레이 (미리 보기) 응용 프로그램을 만듭니다. |
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

## <a name="view-your-iot-central-applications"></a>IoT Central 애플리케이션 보기

[Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) cmdlet을 사용하여 IoT Central 애플리케이션을 나열하고 메타데이터를 봅니다.

## <a name="modify-an-application"></a>애플리케이션 수정

[Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) cmdlet을 사용하여 IoT Central 애플리케이션의 메타데이터를 업데이트합니다. 예를 들어 애플리케이션의 표시 이름을 변경합니다.

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>애플리케이션 제거

[Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) cmdlet을 사용하여 IoT Central 애플리케이션을 삭제합니다. 예:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>다음 단계

이제 Azure PowerShell에서 Azure IoT Central 애플리케이션을 관리하는 방법을 알아보았으므로 다음 단계를 진행하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)
