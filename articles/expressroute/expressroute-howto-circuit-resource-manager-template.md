---
title: ExpressRoute 회로 Resource Manager 템플릿을 만듭니다. Azure | Microsoft Docs
description: 만들기, 프로 비전, 삭제 및 ExpressRoute 회로 프로 비전 해제 합니다.
services: expressroute;azure-resource-manager
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 07/05/2019
ms.author: cherylmc;ganesr
ms.openlocfilehash: bf56145d0a8cd3b01d0d74fcaf3348c1916cee5a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659685"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하 여 ExpressRoute 회로 만들기

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager 템플릿](expressroute-howto-circuit-resource-manager-template.md)
> * [비디오 - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell(클래식)](expressroute-howto-circuit-classic.md)
>

Azure PowerShell을 사용 하 여 Azure Resource Manager 템플릿을 배포 하 여 ExpressRoute 회로 만드는 방법에 알아봅니다. Resource Manager 탬플릿 개발에 대한 자세한 내용은 [Resource Manager 설명서](/azure/azure-resource-manager/) 및 [템플릿 참조](/azure/templates/microsoft.network/expressroutecircuits)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

* 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md) 및 [워크플로](expressroute-workflows.md)를 검토합니다.
* 새 네트워킹 리소스를 만들 권한이 있는지 확인합니다. 적절한 권한이 없는 경우에는 계정 관리자에게 문의합니다.
* 단계를 더 잘 이해하기 위해 시작 전에 [비디오 보기](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)를 할 수 있습니다.

## <a name="create"></a>ExpressRoute 회로 만들기 및 프로비전

[Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/) 에 Resource Manager 템플릿의 좋은 컬렉션이 있습니다. 중 하나를 사용 합니다 [기존 템플릿을](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) ExpressRoute 회로 만드는 합니다.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

관련 템플릿을 보려면를 선택 [여기](https://azure.microsoft.com/resources/templates/?term=expressroute)합니다.

템플릿을 배포 하 여 ExpressRoute 회로 만들려면:

1. 선택 **사용해** 에서 다음 코드 블록을 및 Azure Cloud shell에 로그인 하 고 지침을 따릅니다.

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * **계층** 은 ExpressRoute 표준 또는 ExpressRoute Premium 추가 기능이 사용되는지 여부를 결정합니다. **표준**을 지정하여 표준 SKU를 가져오거나 프리미엄 추가 기능을 위해 **프리미엄**을 지정할 수 있습니다.

   * **피어링 위치**는 Microsoft와 피어링하는 물리적 위치입니다.

     > [!IMPORTANT]
     > 피어링 위치는 Microsoft와 피어링하는 [물리적 위치](expressroute-locations.md)를 나타냅니다. 이 위치는 Azure Network Resource Provider가 있는 지리적 위치를 참조하는 "Location" 속성에 **연결되지 않습니다** . 이 속성에 연결되지 않는 대신 회로의 피어링 위치와 지리적으로 가까운 네트워크 리소스 공급자를 선택 하는 것이 좋습니다.

    리소스 그룹 이름은 사용 하 여 service bus 네임 스페이스 이름 **rg** 추가 합니다.

2. **복사**를 선택하여 PowerShell 스크립트를 복사합니다.
3. 셸 콘솔을 마우스 오른쪽 단추로 클릭 한 다음 선택 **붙여넣기**합니다.

이벤트 허브를 만들려면 몇 분 정도 걸립니다.

이 자습서에서 템플릿을 배포 하려면 azure PowerShell이 사용 됩니다. 다른 템플릿 배포 방법에 대 한 참조.

* [Azure portal을 사용 하 여](../azure-resource-manager/resource-group-template-deploy-portal.md)입니다.
* [Azure CLI를 사용 하 여](../azure-resource-manager/resource-group-template-deploy-cli.md)입니다.
* [REST API를 사용 하 여](../azure-resource-manager/resource-group-template-deploy-rest.md)입니다.

## <a name="delete"></a>ExpressRoute 회로 프로비전 해제 및 삭제

**삭제** 아이콘을 선택하여 ExpressRoute 회로를 삭제할 수 있습니다. 다음 정보에 유의하세요.

* 모든 가상 네트워크를 ExpressRoute 회로에서 연결 해제해야 합니다. 이 작업에 실패한 경우 회로에 연결된 가상 네트워크가 있는지 확인하세요.
* ExpressRoute 회로 서비스 공급자 프로비전 상태가 **프로비전 중** 또는 **프로비전됨**인 경우에는 서비스 공급자에게 회로 프로비전 해제를 요청해야 합니다. 서비스 공급자가 회로의 프로비전을 해제한 다음 통지를 보낼 때까지 리소스가 계속 예약되며 요금이 청구됩니다.
* 서비스 공급자가 회로 프로비전을 해제하여 서비스 공급자 프로비전 상태가 **프로비전되지 않음**이 되면 회로를 삭제할 수 있습니다. 그러면 회로에 대한 요금 청구가 중지됩니다.

다음 PowerShell 명령을 실행 하 여 ExpressRoute 회로 삭제할 수 있습니다.

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>다음 단계

회로를 만든 후에 다음 단계를 진행합니다.

* [ExpressRoute 회로의 라우팅 만들기 및 수정](expressroute-howto-routing-portal-resource-manager.md)
* [가상 네트워크를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)
