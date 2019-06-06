---
title: Azure PowerShell에서 IoT Central 관리 | Microsoft Docs
description: Azure PowerShell에서 IoT Central을 관리합니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/14/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 086c7d303fd199090de3be77b2456c4ebcd053a8
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66726928"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Azure PowerShell에서 IoT Central 관리

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

IoT Central [Application Manager](https://aka.ms/iotcentral) 페이지에서 IoT Central 애플리케이션을 만들고 관리할 수 있을 뿐 아니라 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)을 사용하여 애플리케이션을 관리할 수도 있습니다.

## <a name="prerequisites"></a>필수 조건

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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

[New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) cmdlet을 사용하여 Azure 구독에 IoT Central 애플리케이션을 만듭니다. 예를 들면 다음과 같습니다.

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

스크립트는 먼저 애플리케이션의 미국 동부 지역에 리소스 그룹을 만듭니다. 다음 표에서는 **New-AzIotCentralApp** 명령에 사용되는 매개 변수를 설명합니다.

|매개 변수         |설명 |
|------------------|------------|
|ResourceGroupName |애플리케이션을 포함하는 리소스 그룹입니다. 리소스 그룹이 구독에 이미 있어야 합니다. |
|위치 |기본적으로 이 cmdlet은 리소스 그룹의 위치를 사용합니다. 현재 **미국 동부**, **미국 서부**, **북유럽** 또는 **유럽 서부** 지역에서 IoT Central 애플리케이션을 만들 수 있습니다. |
|이름              |Azure Portal의 애플리케이션 이름입니다. |
|하위 도메인         |애플리케이션 URL의 하위 도메인입니다. 예제에서 애플리케이션 URL은 https://mysubdomain.azureiotcentral.com입니다. |
|SKU               |현재 유일한 값은 **S1**(표준 계층)입니다. [Azure IoT Central 가격 책정](https://azure.microsoft.com/pricing/details/iot-central/)을 참조하세요. |
|Template          | 사용할 애플리케이션 템플릿입니다. 자세한 내용은 다음 표를 참조하세요. |
|DisplayName       |UI에 표시되는 애플리케이션 이름입니다. |

**애플리케이션 템플릿**

|템플릿 이름  |설명 |
|---------------|------------|
|iotc-default@1.0.0 |사용자 고유의 장치 템플릿 및 장치로 채울 빈 애플리케이션을 만듭니다. |
|iotc-demo@1.0.0    |Refrigerated Vending Machine에 대해 이미 만든 장치 템플릿을 포함하는 애플리케이션을 만듭니다. 이 템플릿을 사용하여 Azure IoT Central 탐색을 시작하세요. |
|iotc-devkit-sample@1.0.0 |MXChip 또는 Raspberry Pi 장치를 연결할 수 있는 장치 템플릿을 사용하여 애플리케이션을 만듭니다. 다음 디바이스 중 하나를 실험하는 디바이스 개발자인 경우 이 템플릿을 사용합니다. |

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

[Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) cmdlet을 사용하여 IoT Central 애플리케이션을 삭제합니다. 예를 들면 다음과 같습니다.

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>다음 단계

이제 Azure PowerShell에서 Azure IoT Central 애플리케이션을 관리하는 방법을 알아보았으므로 다음 단계를 진행하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)
