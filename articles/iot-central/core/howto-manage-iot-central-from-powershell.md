---
title: Azure PowerShell에서 IoT Central 관리 | Microsoft Docs
description: This article describes how to create and manage your IoT Central applications from Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 506eb38a2844ed8e8eb9739b116d7647bc1810ec
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480286"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Azure PowerShell에서 IoT Central 관리

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Instead of creating and managing IoT Central applications on the [Azure IoT Central application manager](https://aka.ms/iotcentral) website, you can use [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) to manage your applications.

## <a name="prerequisites"></a>전제 조건

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

[New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) cmdlet을 사용하여 Azure 구독에 IoT Central 애플리케이션을 만듭니다. 다음은 그 예입니다.

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

The script first creates a resource group in the east US location for the application. 다음 표에서는 **New-AzIotCentralApp** 명령에 사용되는 매개 변수를 설명합니다.

|매개 변수를 포함해야 합니다.         |설명 |
|------------------|------------|
|ResourceGroupName |애플리케이션을 포함하는 리소스 그룹입니다. 리소스 그룹이 구독에 이미 있어야 합니다. |
|위치 |기본적으로 이 cmdlet은 리소스 그룹의 위치를 사용합니다. Currently, you can create an IoT Central application in the **United States**, **Australia**, **Asia Pacific**, or in the **Europe** locations.  |
|name              |Azure Portal의 애플리케이션 이름입니다. |
|하위 도메인         |애플리케이션 URL의 하위 도메인입니다. 예제에서 애플리케이션 URL은 https://mysubdomain.azureiotcentral.com 입니다. |
|SKU               |현재 유일한 값은 **S1**(표준 계층)입니다. [Azure IoT Central 가격 책정](https://azure.microsoft.com/pricing/details/iot-central/)을 참조하세요. |
|Template          | 사용할 애플리케이션 템플릿입니다. 자세한 내용은 다음 표를 참조하세요. |
|displayName       |UI에 표시되는 애플리케이션 이름입니다. |

**Application templates with generally available features**

| 템플릿 이름            | 설명 |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | 사용자 고유의 디바이스 템플릿 및 디바이스로 채울 빈 애플리케이션을 만듭니다. |
| iotc-demo@1.0.0          | Refrigerated Vending Machine에 대해 이미 만든 디바이스 템플릿을 포함하는 애플리케이션을 만듭니다. 이 템플릿을 사용하여 Azure IoT Central 탐색을 시작하세요. |
| iotc-devkit-sample@1.0.0 | MXChip 또는 Raspberry Pi 디바이스를 연결할 수 있는 디바이스 템플릿을 사용하여 애플리케이션을 만듭니다. 다음 디바이스 중 하나를 실험하는 디바이스 개발자인 경우 이 템플릿을 사용합니다. |


**Application templates with public preview features**

| 템플릿 이름            | 설명 |
| ------------------------ | ----------- |
| iotc-pnp-preview@1.0.0   | Creates an empty plug and play preview application for you to populate with your own device templates and devices. |
| iotc-condition@1.0.0     | Creates an application with a in-store analytics – condition monitoring template. Use this template to connect and monitor store environment. |
| iotc-consumption@1.0.0   | Creates an application with water consumption monitoring template. Use this template to monitor and control water flow. |
| iotc-distribution@1.0.0  | Creates an application with a Digital distribution template. Use this template to improve warehouse output efficiency by digitalizing key assets and actions. |
| iotc-inventory@1.0.0     | Creates an application with a smart inventory management template. Use this template to automate receiving, product movement, cycle counting, and tracking of sensors. |
| iotc-logistics@1.0.0     | Creates an application with a Connected logistics template. Use this template to track your shipment in real-time across air, water and land with location and condition monitoring. |
| iotc-meter@1.0.0         | Creates an application with smart meter monitoring template. Use this template to monitor energy consumption, network status, and identify trends to improve customer support and smart meter management.  |
| iotc-patient@1.0.0       | Creates an application with continuous patient monitoring template. Use this template to extend patient care, re-admissions, and manage diseases. |
| iotc-power@1.0.0         | Creates an application with solar panel monitoring template. Use this template to monitor solar panel status, energy generation trends. |
| iotc-quality@1.0.0       | Creates an application with water quality monitoring template. Use this template to digitally monitor water quality.|
| iotc-store@1.0.0         | Creates an application with a in-store analytics – checkout template. Use this template to monitor and manage the checkout flow inside your store. |
| iotc-waste@1.0.0         | Creates an application with a Connected waste management template. Use this template to monitor waste bins and dispatch field operators. |

> [!NOTE]
> The preview application templates are currently only available in the **Europe** and **United States** locations.

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

[Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) cmdlet을 사용하여 IoT Central 애플리케이션을 삭제합니다. 다음은 그 예입니다.

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>다음 단계

이제 Azure PowerShell에서 Azure IoT Central 애플리케이션을 관리하는 방법을 알아보았으므로 다음 단계를 진행하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)
