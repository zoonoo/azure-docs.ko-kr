---
title: '빠른 시작: 표준 Load Balancer 만들기 - Azure Resource Manager 템플릿'
titlesuffix: Azure Load Balancer
description: 이 빠른 시작에서는 Azure Resource Manager 템플릿을 사용하여 표준 Load Balancer를 만드는 방법을 보여줍니다.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer by using Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 0e47560ca43b23f4779da701f3e6f11f53a6b1ce
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480392"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>빠른 시작: Azure Resource Manager 템플릿을 사용하여 VM 부하를 분산하는 표준 Load Balancer 만들기

부하를 분산하면 들어오는 요청이 여러 가상 머신에 분산되어 가용성 및 확장성이 향상됩니다. Azure Resource Manager 템플릿을 배포하여 VM(가상 머신)의 부하를 분산하는 부하 분산 장치를 만들 수 있습니다. 이 빠른 시작에서는 표준 부하 분산 장치를 사용하여 VM의 부하를 분산하는 방법을 보여줍니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-standard-load-balancer"></a>표준 부하 분산 장치 만들기

이 섹션에서는 가상 머신의 부하를 분산하는 데 도움이 되는 표준 Load Balancer를 만듭니다. 표준 부하 분산 장치는 표준 공용 IP 주소만 지원합니다. 표준 부하 분산 장치를 만들 때 표준 부하 분산 장치의 프런트 엔드(기본 이름은 *LoadBalancerFrontend*)로 구성된 새 표준 공용 IP 주소도 만들어야 합니다. 표준 부하 분산 장치를 만드는 데 사용할 수 있는 여러 가지 방법이 있습니다. 이 빠른 시작에서는 Azure PowerShell을 사용하여 [Resource Manager 템플릿](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-load-balancer-standard-create/azuredeploy.json)을 배포합니다. Resource Manager 템플릿은 솔루션에 배포해야 하는 리소스를 정의하는 JSON 파일입니다. Azure 솔루션의 배포 및 관리와 관련된 개념을 이해하려면 [Azure Resource Manager 설명서](/azure/azure-resource-manager/)를 참조하세요. 더 많은 Azure Load Balancer 관련 템플릿을 찾으려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)을 참조하세요.

템플릿을 배포하려면 **사용해 보세요!** 를 선택하여 Azure Cloud Shell을 열고, 다음 PowerShell 스크립트를 셸 창에 붙여넣습니다. 코드를 붙여넣으려면 셸 창을 마우스 오른쪽 단추로 클릭하고 **붙여넣기**를 선택합니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
$adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-load-balancer-standard-create/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword
```

리소스 그룹 이름은 **rg**가 추가된 프로젝트 이름입니다. 다음 섹션에서 리소스 그룹 이름이 필요합니다.  리소스를 만드는 데 몇 분 정도 걸립니다.

## <a name="test-the-load-balancer"></a>Load Balancer 테스트

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 패널에서 **리소스 그룹** 을 선택합니다.
1. 마지막 섹션에서 만든 리소스 그룹을 선택합니다.  기본 리소스 그룹 이름은 **rg**가 추가된 프로젝트 이름입니다.
1. 부하 분산 장치를 선택합니다.  부하 분산 장치가 하나밖에 없습니다. 기본 이름은 **-lb**가 추가된 프로젝트 이름입니다.
1. 공용 IP 주소(IP 주소 부분만)를 복사한 다음, 브라우저의 주소 표시줄에 붙여넣습니다. IIS 웹 서버의 기본 페이지가 브라우저에 표시됩니다.

   ![IIS 웹 서버](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Load Balancer가 3개 VM의 트래픽을 분산하는 것을 보려면 클라이언트 머신에서 웹 브라우저를 강제로 새로 고칩니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 리소스 그룹, Load Balancer 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 Azure Portal에서 Load Balancer가 포함된 리소스 그룹을 선택한 다음, **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 표준 Load Balancer를 만들고, 거기에 VM을 연결하고, Load Balancer 트래픽 규칙 및 상태 프로브를 구성한 다음, Load Balancer를 테스트합니다. Azure Load Balancer에 대해 자세히 알아보려면 Azure Load Balancer에 대한 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Load Balancer 자습서](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
