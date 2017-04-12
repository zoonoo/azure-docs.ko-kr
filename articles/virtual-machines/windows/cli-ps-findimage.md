---
title: "Windows VM 이미지 탐색 및 선택 | Microsoft Docs"
description: "리소스 관리자 배포 모델로 Windows 가상 컴퓨터를 만들 경우 이미지의 게시자, 제품 및 SKU를 확인하는 방법에 대해 알아보세요."
services: virtual-machines-windows
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: b36fb2b94dcee94eb299221bbb5c82080d23bfb1
ms.lasthandoff: 03/31/2017


---
# <a name="navigate-and-select-windows-virtual-machine-images-in-azure-with-powershell"></a>Azure에서 PowerShell을 사용하여 Windows 가상 컴퓨터 이미지 이동 및 선택
이 항목은 배포할 수 있는 각 위치에 대한 VM 이미지 게시자, 제안, SKU 및 버전을 찾는 방법을 설명합니다. 예를 들면 일부 자주 사용되는 Windows VM 이미지는 다음과 같습니다.

## <a name="table-of-commonly-used-windows-images"></a>일반적으로 사용하는 Windows 이미지 테이블
| PublisherName | 제안 | SKU |
|:--- |:--- |:--- |:--- |
| MicrosoftDynamicsNAV |DynamicsNAV |2015 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2013 |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-DW |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-OLTP |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftWindowsServer |WindowsServer |Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |

## <a name="find-azure-images-with-powershell"></a>PowerShell을 사용하여 Azure 이미지 찾기
> [!NOTE]
> [최신 Azure PowerShell](/powershell/azureps-cmdlets-docs)을 설치하고 구성합니다. Azure PowerShell 모듈 1.0 이전을 사용 중인 경우 다음 명령을 사용하지만 먼저 `Switch-AzureMode AzureResourceManager`를 사용해야 합니다. 
> 
> 

Azure 리소스 관리자를 사용하여 새 가상 컴퓨터를 만들 때 다음 이미지 속성을 조합하여 이미지를 지정해야 하는 경우도 있습니다.

* 게시자
* 제안
* SKU

예를 들어 만들 가상 컴퓨터 형식을 지정해야 하는 리소스 그룹 템플릿 파일 또는 `Set-AzureRMVMSourceImage` PowerShell cmdlet에 대해 이러한 값이 필요합니다.

이러한 값을 결정해야 하는 경우 이미지를 탐색하여 다음의 값을 결정할 수 있습니다.

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

`Get-AzureRMVMImageSku` 명령 표시에 새 가상 컴퓨터에 대한 이미지를 지정하는 데 필요한 모든 정보가 있습니다.

전체 예제는 다음과 같습니다.

```powershell
PS C:\> $locName="West US"
PS C:\> Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

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
PS C:\> $pubName="MicrosoftWindowsServer"
PS C:\> Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Offer
-----
WindowsServer
```

"WindowsServer" 제품인 경우:

```powershell
PS C:\> $offerName="WindowsServer"
PS C:\> Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Skus
----
2008-R2-SP1
2012-Datacenter
2012-R2-Datacenter
2016-Nano-Server-Technical-Previe
2016-Technical-Preview-with-Conta
Windows-Server-Technical-Preview
```

이 목록에서 선택한 SKU 이름을 복사합니다. 그러면 `Set-AzureRMVMSourceImage` PowerShell cmdlet 또는 리소스 그룹 템플릿에 대한 모든 정보를 알 수 있습니다.

## <a name="next-steps"></a>다음 단계
이제 사용할 이미지를 정밀하게 선택할 수 있습니다. 방금 찾은 이미지 정보를 사용하여 가상 컴퓨터를 빠르게 만들거나 해당 이미지 정보로 템플릿을 사용하려면 [Resource Manager 및 PowerShell을 사용하여 Windows VM 만들기](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
