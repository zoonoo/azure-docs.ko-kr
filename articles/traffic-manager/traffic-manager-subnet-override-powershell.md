---
title: Azure PowerShell을 사용하여 Azure Traffic Manager 서브넷 재정의 | Microsoft Docs
description: 이 문서는 Traffic Manager 서브넷 재정의가 Traffic Manager 프로필의 라우팅 메서드를 재정의하고, Azure PowerShell을 사용하여 엔드포인트 매핑에 미리 정의된 IP 범위를 통해 최종 사용자의 IP 주소를 기반으로 하는 엔드포인트로 트래픽을 보내는 방법을 이해하는 데 도움이 됩니다.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 7dd7f43044a9643eb7e9d5296dfb209e425d5fb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102504783"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Azure Powershell을 사용하여 Traffic Manager 서브넷 재정의

Traffic Manager 서브넷 재정의를 사용하여 프로필의 라우팅 메서드를 변경할 수 있습니다.  재정의를 추가하면 엔드포인트 매핑에 미리 정의된 IP 범위를 사용하여 최종 사용자의 IP 주소를 기반으로 트래픽을 보냅니다. 

## <a name="how-subnet-override-works"></a>서브넷 재정의 작동 방법

트래픽 관리자 프로필에 서브넷 재정의를 추가하면 Traffic Manager는 먼저 최종 사용자의 IP 주소에 대한 서브넷 재정의가 있는지 확인합니다. 재정의가 발견되는 경우, 사용자의 DNS 쿼리는 해당 엔드포인트로 보내집니다.  매핑이 없으면 Traffic Manager은 프로필에 있는 원래 라우팅 메서드로 돌아갑니다. 

IP 주소 범위는 CIDR 범위(예: 1.2.3.0/24) 또는 주소 범위(예: 1.2.3.4-5.6.7.8)로 지정할 수 있습니다. 각 엔드포인트와 연결된 IP 범위는 해당 엔드포인트에 고유해야 합니다. 서로 다른 엔드포인트 간에 IP 범위가 겹치면 Traffic Manager가 프로필을 거부하게 됩니다.

서브넷 재정의를 지원하는 라우팅 프로필에는 다음 두 가지 유형이 있습니다.

* **지리** - Traffic Manager DNS 쿼리의 IP 주소에 대한 서브넷 재정의를 찾으면, 엔드포인트의 상태에 상관없이 엔드포인트로 쿼리를 라우팅합니다.
* **성능** -Traffic Manager가 DNS 쿼리의 IP 주소에 대한 서브넷 재정의를 찾으면, 정상 상태인 경우에만 엔드포인트로 트래픽을 라우팅합니다.  서브넷 재정의 엔드포인트가 정상이 아닌 경우, Traffic Manager은 성능 라우팅 추론으로 대체됩니다.

## <a name="create-a-traffic-manager-subnet-override"></a>Traffic Manager 서브넷 재정의 생성

Traffic Manager 서브넷 재정의를 만들려면 Azure PowerShell을 사용하여 재정의를 위한 서브넷을 Traffic Manager 엔드포인트에 추가할 수 있습니다.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure Cloud Shell](https://shell.azure.com/powershell) 뒤에 오는 명령을 실행하거나 또는 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 무료 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 컴퓨터에서 PowerShell을 실행하는 경우 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 설치된 버전을 확인할 수 있습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. PowerShell을 로컬로 실행하는 경우 `Login-AzAccount`도 실행하여 Azure에 로그인해야 합니다.


1. **Traffic Manager 엔드포인트를 검색합니다.**

    서브넷 재정의를 사용하도록 설정하려면 재정의를 추가할 엔드포인트를 검색하고 [AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint)를 사용하여 변수에 저장합니다.

    Name, ProfileName, ResourceGroupName을 변경하려는 엔드포인트의 값으로 바꿉니다.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **엔드포인트에 IP 주소 범위를 추가합니다.**
    
    IP 주소 범위를 엔드포인트에 추가하려면 [Add-AzTrafficManagerIpAddressRange](/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange)를 사용하여 범위를 추가합니다.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    범위가 추가되면 [Set-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint)를 사용하여 엔드포인트를 업데이트합니다.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
IP 주소 범위 제거를 완료하려면 [Remove-AzTrafficManagerIpAddressRange](/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange)를 사용합니다.

1.  **Traffic Manager 엔드포인트를 검색합니다.**

    서브넷 재정의를 사용하도록 설정하려면 재정의를 추가할 엔드포인트를 검색하고 [AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint)를 사용하여 변수에 저장합니다.

    Name, ProfileName, ResourceGroupName을 변경하려는 엔드포인트의 값으로 바꿉니다.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **엔드포인트에서 IP 주소 범위를 제거합니다.**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     범위가 제거되면 [Set-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint)를 사용하여 엔드포인트를 업데이트합니다.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>다음 단계
Traffic Manager [트래픽 라우팅 방법](traffic-manager-routing-methods.md)에 대해 자세히 알아봅니다.

[서브넷 트래픽 라우팅 메서드](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)에 대한 자세한 정보