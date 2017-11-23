---
title: "퀵 스타트 - PowerShell로 첫 번째 Azure Container Instances 컨테이너 만들기"
description: "PowerShell로 Windows 컨테이너 인스턴스를 만들어 Azure Container Instances를 시작합니다."
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: ca10274fc6a23d7f5e7436dbaf72a6e7a918f275
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Azure Container Instances에서 첫 번째 컨테이너 만들기

Azure Container Instances를 사용하면 가상 컴퓨터를 프로비전하거나 상위 수준 서비스를 도입하지 않고도 Azure에서 Docker 컨테이너를 쉽게 만들고 관리할 수 있습니다.

이 빠른 시작에서는 Azure에서 Windows 컨테이너를 만들고 공용 IP 주소를 사용하여 인터넷에 공개합니다. 이 작업은 단일 명령으로 완료됩니다. 단 몇 분 안에 브라우저에서 응용 프로그램이 실행됩니다.

![Azure Container Instances를 사용하여 배포된 앱이 브라우저에 표시됨][qs-powershell-01]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택한 경우 이 자습서에서는 Azure PowerShell 모듈 버전 3.6 이상을 실행해야 합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup][New-AzureRmResourceGroup]을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>컨테이너 만들기

[New-AzureRmContainerGroup][New-AzureRmContainerGroup] cmdlet에 이름, Docker 이미지 및 Azure 리소스 그룹을 제공하여 컨테이너를 만들 수 있습니다. 선택적으로 공용 IP 주소로 컨테이너를 인터넷에 공개할 수 있습니다. 이 경우 IIS(인터넷 정보 서비스)를 실행하는 Windows Nano Server 컨테이너를 사용합니다.

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -IpAddressType Public
```

몇 초 안에 요청에 대한 응답을 얻게 됩니다. 처음에는 컨테이너가 **만드는 중** 상태가 되지만 1~2분 안에 시작됩니다. [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] cmdlet을 사용하여 상태를 확인할 수 있습니다.

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

컨테이너의 프로비전 상태 및 IP 주소가 cmdlet의 출력에 표시됩니다.

```
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/12345678-1234-1234-1234-12345678abcd/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            :
IpAddress                : 40.71.248.73
Ports                    : {80}
OsType                   : Windows
Volumes                  :
```

**ProvisioningState** 컨테이너가 `Succeeded` 상태로 전환되면 제공된 IP 주소를 사용하여 브라우저에서 해당 컨테이너에 연결할 수 있습니다.

![Azure Container Instances를 사용하여 배포된 IIS가 브라우저에 표시됨][qs-powershell-01]

## <a name="delete-the-container"></a>컨테이너 삭제

컨테이너 작업을 완료했으면 [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup] cmdlet을 사용하여 제거할 수 있습니다.

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Container Instances에서 미리 구축된 Windows 컨테이너를 시작했습니다. 컨테이너를 직접 빌드하고 Azure Container Registry를 사용하여 Azure Container Instances에 배포하려면 Azure Container Instances 자습서를 계속합니다.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서](./container-instances-tutorial-prepare-app.md)

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png