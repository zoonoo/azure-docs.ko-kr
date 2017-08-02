---
title: "Azure에서 Windows VM 이미지 선택 | Microsoft Docs"
description: "Azure PowerSHell을 사용하여 Marketplace VM 이미지의 게시자, 제품, SKU 및 버전을 확인하는 방법을 알아봅니다."
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/12/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 630f555b003b0efc45b372a7009dbf036aa8c737
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="how-to-find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Marketplace에서 Windows VM 이미지를 찾는 방법

이 항목에서는 Azure PowerShell을 사용하여 Azure Marketplace에서 VM 이미지를 찾는 방법을 설명합니다. 이 정보를 사용하여 Windows VM을 만들 때 Marketplace 이미지를 지정합니다.

최신 [Azure PowerShell 모듈](/powershell/azure/install-azurerm-ps)을 설치 및 구성했는지 확인합니다.



## <a name="table-of-commonly-used-windows-images"></a>일반적으로 사용하는 Windows 이미지 테이블
| PublisherName | 제안 | SKU |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |2016-Nano-Server |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2016-WS2016 |Enterprise |
| MicrosoftSQLServer |SQL2014SP2-WS2012R2 |Enterprise |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |

## <a name="find-specific-images"></a>특정 이미지 찾기


Azure 리소스 관리자를 사용하여 새 가상 컴퓨터를 만들 때 다음 이미지 속성을 조합하여 이미지를 지정해야 하는 경우도 있습니다.

* 게시자
* 제안
* SKU

예를 들어 [Set-AzureRMVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) PowerShell cmdlet 또는 만든 VM 유형을 지정해야 하는 리소스 그룹 템플릿에서 이러한 값을 사용합니다.

이러한 값을 확인해야 할 경우 [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) 및 [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku)를 실행하여 이미지를 탐색할 수 있습니다. 다음 값을 확인합니다.

1. 이미지 게시자를 나열합니다.
2. 지정된 게시자에 제안을 나열합니다.
3. 지정된 제안에 SKU를 나열합니다.

먼저 다음 명령을 사용하여 게시자를 나열합니다.

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

선택한 게시자 이름을 입력하고 다음 명령을 실행합니다.

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

선택한 제품 이름을 입력하고 다음 명령을 실행합니다.

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

`Get-AzureRMVMImageSku` 명령 출력에 새 가상 컴퓨터에 대한 이미지를 지정하는 데 필요한 모든 정보가 있습니다.

전체 예제는 다음과 같습니다.

```powershell
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

```

출력:

```
PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

게시자가 "MicrosoftWindowsServer"인 경우:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

출력:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServer-HUB
```

"WindowsServer" 제품인 경우:

```powershell
$offerName="WindowsServer"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

출력:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Nano-Server
```

이 목록에서 선택한 SKU 이름을 복사합니다. 그러면 `Set-AzureRMVMSourceImage` PowerShell cmdlet 또는 리소스 그룹 템플릿에 대한 모든 정보를 알 수 있습니다.

## <a name="next-steps"></a>다음 단계
이제 사용할 이미지를 정밀하게 선택할 수 있습니다. 방금 찾은 이미지 정보를 사용하여 가상 컴퓨터를 빠르게 만들려면 [PowerShell을 사용하여 Windows 가상 컴퓨터 만들기](quick-create-powershell.md)를 참조하세요.

