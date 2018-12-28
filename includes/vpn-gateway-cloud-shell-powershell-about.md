---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/25/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6a0ea318f2e9b8f392ac7c0a1f1091c062c59d41
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852359"
---
이 문서에서는 PowerShell cmdlet을 사용합니다. cmdlet을 실행하기 위해 무료 대화형 셸인 Azure Cloud Shell을 사용할 수 있습니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. **복사**를 클릭하여 코드를 복사하고, Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다. Cloud Shell을 시작하는 몇 가지 방법이 있습니다.

|  |   |
|-----------------------------------------------|---|
| 코드 블록의 오른쪽 위 모서리에서 **사용**을 클릭합니다.  | ![이 문서의 Cloud Shell](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| 브라우저에서 Cloud Shell을 엽니다. | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Azure Portal의 오른쪽 위 모서리에 있는 메뉴에서 **Cloud Shell** 단추를 클릭합니다. | [![포털의 Cloud Shell](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

Azure Cloud Shell을 사용하지 않으려면, 대신 PowerShell을 로컬에 설치할 수 있습니다. PowerShell을 로컬에 설치하여 사용하는 경우 최신 기능을 활용하려면 최신 버전의 Azure Resource Manager PowerShell cmdlet을 설치해야 합니다.

로컬에서 실행 중인 PowerShell 버전을 찾으려면 ‘Get-Module-ListAvailable AzureRM’ cmdlet을 사용합니다. 업데이트하려면 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요.