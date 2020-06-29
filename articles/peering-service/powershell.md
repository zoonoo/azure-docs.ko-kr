---
title: 'Peering Service 연결 등록 - Azure PowerShell '
description: 이 자습서에서는 PowerShell을 사용하여 Peering Service 연결을 등록하는 방법에 대해 알아봅니다.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service .
ms.openlocfilehash: 163f93ab93890f27eb2a9b18cc23804595fcb822
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84870621"
---
# <a name="tutorial-register-a-peering-service-connection-using-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 Peering Service 연결 등록

이 자습서에서는 Azure PowerShell을 사용하여 Peering Service를 등록하는 방법에 대해 알아봅니다.

Azure Peering Service는 Office 365, Dynamics 365, SaaS(Software as a Service) 서비스, Azure 또는 공용 인터넷을 통해 액세스할 수 있는 Microsoft 서비스와 같은 Microsoft 클라우드 서비스에 대한 고객의 연결을 향상시키는 네트워킹 서비스입니다. 이 문서에서는 Azure PowerShell을 사용하여 Peering Service 연결을 등록하는 방법에 대해 알아봅니다.

Azure 구독이 없는 경우 지금 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

대신 PowerShell을 로컬에 설치하고 사용하려는 경우 이 빠른 시작을 진행하려면 Azure PowerShell 모듈 버전 1.0.0 이상을 사용해야 합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 설치 및 업그레이드 정보는 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

마지막으로, PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`도 실행해야 합니다. 해당 명령은 Azure와 연결을 만듭니다.

Azure PowerShell 모듈을 사용하여 Peering Service를 등록하고 관리합니다. PowerShell 명령줄 또는 스크립트에서 Peering Service를 등록하거나 관리할 수 있습니다.


## <a name="prerequisites"></a>필수 구성 요소  
다음이 있어야 합니다.

### <a name="azure-account"></a>Azure 계정

유효한 활성 Microsoft Azure 계정이 있어야 합니다. 이 계정은 Peering Service 연결을 설정하는 데 필요합니다. Peering Service는 Azure 구독 내에 있는 리소스입니다.

### <a name="connectivity-provider"></a>연결 공급자

인터넷 서비스 공급자 또는 인터넷 교환 파트너와 협력하여 Peering Service를 얻어 네트워크를 Microsoft 네트워크와 연결할 수 있습니다.

연결 공급자가 Microsoft와 파트너 관계를 맺고 있는지 확인합니다.

### <a name="register-a-subscription-with-the-resource-provider-and-feature-flag"></a>리소스 공급자 및 기능 플래그에 구독 등록

Peering Service를 등록하는 단계를 진행하기 전에 Azure PowerShell을 사용하여 리소스 공급자 및 기능 플래그에 구독을 등록합니다. Azure PowerShell 명령은 다음과 같이 지정됩니다.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowPeeringService ProviderNamespace Microsoft.Peering 

Register-AzResourceProvider -ProviderNamespace Microsoft.Peering 

```

### <a name="fetch-the-location-and-service-provider"></a>위치 및 서비스 공급자 가져오기 

Azure PowerShell에서 다음 명령을 실행하여 Peering Service를 사용하도록 설정해야 하는 위치 및 서비스 공급자를 가져옵니다. 

Peering Service 위치 가져오기:

```azurepowershell-interactive
# Gets a list of available countries
Get-AzPeeringServiceCountry 
# Gets a list of metro locations serviced by country
Get-AzPeeringServiceLocation -Country "United States"
```

Peering Service 공급자 가져오기:
              
```azurepowershell-interactive
Get-AzPeeringServiceProvider
```

### <a name="register-the-peering-service-connection"></a>Peering Service 연결 등록

Azure PowerShell을 통해 다음 명령 세트를 사용하여 Peering Service 연결을 등록합니다. 이 예제에서는 myPeeringService라는 Peering Service를 등록합니다.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
```

### <a name="register-the-peering-service-prefix"></a>Peering Service 접두사 등록

Azure PowerShell을 통해 다음 명령을 실행하여 연결 공급자가 제공하는 접두사를 등록합니다. 이 예제에서는 myPrefix라는 접두사를 등록합니다.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
$prefixName = "myPrefix"
$prefix = “192.168.1.0/24”
$serviceKey = "6f48cdd6-2c2e-4722-af89-47e27b2513af"
$prefixService = $peeringService | New-AzPeeringServicePrefix -Name $prefixName -Prefix $prefix -ServiceKey $serviceKey
```

### <a name="list-all-the-peering-services-connections"></a>모든 Peering Service 연결 나열

모든 Peering Service 목록을 보려면 다음 명령을 실행합니다.

```azurepowershell-interactive
$peeringService = Get-AzPeeringService
```

### <a name="list-all-the-peering-service-prefixes"></a>모든 Peering Service 접두사 나열

모든 Peering Service 접두사 목록을 보려면 다음 명령을 실행합니다.

```azurepowershell-interactive
 $prefixName = "myPrefix"
```

```azurepowershell-interactive
$prefix = Get-AzPeeringServicePrefix -PeeringServiceName "myPeeringService" -ResourceGroupName "MyResourceGroup" -Name "myPrefix"
```

### <a name="remove-the-peering-service-prefix"></a>Peering Service 접두사 제거

Peering Service 접두사를 제거하려면 다음 명령을 실행합니다.

```azurepowershell-interactive
Remove-AzPeeringServicePrefix -ResourceGroupName  "MyResourceGroup" -Name "myPrefix" -PeeringServiceName "myPeeringService"
```

## <a name="next-steps"></a>다음 단계

- Peering Service 연결에 대해 알아보려면 [Peering Service 연결](connection.md)을 참조하세요.
- Peering Service 연결 원격 분석에 대해 알아보려면 [Peering Service 연결 원격 분석](connection-telemetry.md)을 참조하세요.
- Azure Portal을 사용하여 Peering Service 연결을 등록하려면 [Peering Service 연결 등록 - Azure Portal](azure-portal.md)을 참조하세요.
- Azure CLI를 사용하여 Peering Service 연결을 등록하려면 [Peering Service 연결 등록 - Azure CLI](cli.md)를 참조하세요.
