---
title: Azure 트래픽 관리자 서브넷 Azure PowerShell을 사용하여 재정의 | 마이크로 소프트 문서
description: 이 문서에서는 트래픽 관리자 서브넷 재정의를 사용하여 트래픽 관리자 프로필의 라우팅 방법을 재정의하여 미리 정의된 IP 범위를 통해 최종 사용자 IP 주소를 기반으로 엔드포인트로 트래픽을 Azure를 사용하는 엔드포인트 매핑으로 전달하는 방법을 이해하는 데 도움이 됩니다. Powershell.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 323093ec78a9486d19496b0ee90e37cb42eea341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938415"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Azure Powershell을 사용하여 트래픽 관리자 서브넷 재정의

트래픽 관리자 서브넷 재정의를 사용하면 프로필의 라우팅 방법을 변경할 수 있습니다.  재정의를 추가하면 최종 사용자의 IP 주소를 기반으로 트래픽이 미리 정의된 IP 범위와 끝점 매핑으로 전달됩니다. 

## <a name="how-subnet-override-works"></a>서브넷 재정의 작동 방식

서브넷 재정의가 트래픽 관리자 프로필에 추가되면 트래픽 관리자는 먼저 최종 사용자의 IP 주소에 대한 서브넷 재정의가 있는지 확인합니다. 검색이 발견되면 사용자의 DNS 쿼리가 해당 끝점으로 이동됩니다.  매핑을 찾을 수 없는 경우 트래픽 관리자는 프로필의 원래 라우팅 방법으로 대체됩니다. 

IP 주소 범위는 CIDR 범위(예: 1.2.3.0/24) 또는 주소 범위(예: 1.2.3.4-5.6.7.8)로 지정할 수 있습니다. 각 끝점과 연결된 IP 범위는 해당 끝점에 고유해야 합니다. 서로 다른 끝점 간에 IP 범위가 겹치면 트래픽 관리자가 프로필을 거부합니다.

서브넷 재정의를 지원하는 라우팅 프로필에는 두 가지 유형이 있습니다.

* **지리적** - 트래픽 관리자가 DNS 쿼리의 IP 주소에 대한 서브넷 재정의를 발견하면 끝점의 상태와 관계없이 쿼리를 끝점으로 라우팅합니다.
* **성능** - 트래픽 관리자가 DNS 쿼리의 IP 주소에 대한 서브넷 재정의를 발견하면 정상인 경우에만 트래픽을 끝점으로 라우팅합니다.  서브넷 오버라이드 끝점이 정상적이지 않은 경우 트래픽 관리자는 성능 라우팅 휴리스틱으로 돌아갑니다.

## <a name="create-a-traffic-manager-subnet-override"></a>트래픽 관리자 서브넷 재정의 만들기

트래픽 관리자 서브넷 재정의를 만들려면 Azure PowerShell을 사용하여 재정의에 대한 서브넷을 트래픽 관리자 끝점에 추가할 수 있습니다.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure Cloud Shell](https://shell.azure.com/powershell) 뒤에 오는 명령을 실행하거나 또는 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 무료 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 컴퓨터에서 PowerShell을 실행하는 경우 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 설치된 버전을 확인할 수 있습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. PowerShell을 로컬로 실행하는 경우 `Login-AzAccount`도 실행하여 Azure에 로그인해야 합니다.


1. **트래픽 관리자 끝점 검색:**

    서브넷 재정의를 활성화하려면 재정의를 추가하고 [Get-AzTrafficManagerEndpoint를](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0)사용하여 변수에 저장할 끝점을 검색합니다.

    이름, ProfileName 및 ResourceGroupName을 변경하려는 끝점의 값으로 바꿉니다.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **끝점에 IP 주소 범위를 추가합니다.**
    
    끝점에 IP 주소 범위를 추가하려면 [Add-AzTrafficManagerIpAddressRange를](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) 사용하여 범위를 추가합니다.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    범위가 추가되면 [Set-AzTrafficManagerEndpoint를](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) 사용하여 끝점을 업데이트합니다.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
IP 주소 범위 제거는 [제거-AzTrafficManagerIpAddressRange를](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0)사용하여 완료할 수 있습니다.

1.  **트래픽 관리자 끝점 검색:**

    서브넷 재정의를 활성화하려면 재정의를 추가하고 [Get-AzTrafficManagerEndpoint를](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0)사용하여 변수에 저장할 끝점을 검색합니다.

    이름, ProfileName 및 ResourceGroupName을 변경하려는 끝점의 값으로 바꿉니다.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **끝점에서 IP 주소 범위를 제거합니다.**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     범위가 제거되면 [Set-AzTrafficManagerEndpoint를](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) 사용하여 끝점을 업데이트합니다.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>다음 단계
Traffic Manager [트래픽 라우팅 방법](traffic-manager-routing-methods.md)에 대해 자세히 알아봅니다.

[서브넷 트래픽 라우팅 방법에](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method) 대해 알아보기
