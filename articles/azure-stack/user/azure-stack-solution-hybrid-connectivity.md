---
title: Azure 및 Azure Stack을 사용 하 여 하이브리드 클라우드 연결 구성 | Microsoft Docs
description: Azure 및 Azure Stack을 사용 하 여 하이브리드 클라우드 연결을 구성 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 2530f6f59ef458d5a7c2de5850d8fab322798ba3
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752665"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>자습서: Azure 및 Azure Stack을 사용 하 여 하이브리드 클라우드 연결 구성

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

전역 Azure 및 하이브리드 연결 패턴을 사용 하 여 Azure Stack의 보안을 사용 하 여 리소스에 액세스할 수 있습니다.

이 자습서에서는 샘플 환경을 빌드합니다.

> [!div class="checklist"]
> - 온-프레미스 데이터 개인 정보 취급 규정 요구 사항에 맞게 없이 전역 Azure 리소스에 대 한 액세스를 유지 합니다.
> - 전역 Azure에서 클라우드 규모 앱 배포 및 리소스를 사용 하는 동안 레거시 시스템을 유지 합니다.

> [!Tip]  
> ![하이브리드 pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack은 Azure의 확장입니다. Azure Stack은 유연성 및 혁신을 온-프레미스 환경에 컴퓨팅 및 하이브리드 앱을 어디서 나 빌드 및 배포 할 수 있는 유일한 하이브리드 클라우드를 사용 하도록 설정 하는 클라우드를 제공 합니다.  
> 
> 백서 [하이브리드 응용 프로그램에 대 한 디자인 고려 사항](https://aka.ms/hybrid-cloud-applications-pillars) (배치, 확장성, 가용성, 복원 력, 관리 효율성 및 보안) 소프트웨어 품질 핵심 요소를 디자인, 배포 및 하이브리드 운영 검토 응용 프로그램입니다. 디자인 고려 사항을 프로덕션 환경에서 문제를 최소화 되는 하이브리드 응용 프로그램 디자인을 최적화 하는 데 도움이 됩니다.


## <a name="prerequisites"></a>필수 조건

몇 가지 구성 요소 빌드 하이브리드 연결 배포 해야 합니다. 이러한 구성 요소 중 일부가 시간이 걸립니다를 준비 하려면 적절 하 게 하려는 것입니다.

**Azure Stack**

Azure OEM/하드웨어 파트너를 프로덕션 Azure Stack을 배포 하 고 모든 사용자는 Azure Stack 개발 키트 ASDK ()를 배포할 수 있습니다.

**Azure Stack 구성 요소**

Azure Stack 운영자 해야 App Service 배포, 계획 및 제품은 테 넌 트 구독을 만들고 Windows Server 2016 이미지를 추가 합니다. 이미 있는 경우 이러한 구성 요소 중 일부를이 자습서를 시작 하기 전에 요구 사항을 충족 하는지 있는지 확인 합니다.

이 자습서에서는 Azure 및 Azure Stack에 대 한 기본 지식이 있다고 가정 합니다. 이 자습서를 시작 하기 전에 자세한 내용은 다음 문서를 참조 합니다.

 - [Azure 소개](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack의 주요 개념](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Azure 구독이 아직 없는 경우 시작하기 전에  [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.
 - 만들기는 [웹 앱](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts) Azure에서. 이 자습서에 필요 하기 때문에 웹 앱 URL을 기록해 두십시오.

### <a name="azure-stack"></a>Azure Stack

 - 프로덕션 Azure Stack을 사용 하거나 배포에서 Azure Stack 개발 키트 https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1합니다.
   >[!Note]
   >배포는 ASDK 최대 7 시간까지 걸릴를 적절 하 게 계획 수 있습니다.

 - 배포할 [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) Azure Stack에 PaaS 서비스입니다.
 - [계획 및 제품은 만들기](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) Azure Stack 환경에서.
 - [테 넌 트 구독 만들기](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) Azure Stack 환경 내에서.

### <a name="before-you-begin"></a>시작하기 전에

하이브리드 클라우드 연결을 구성 하기 전에 다음 조건을 충족 하는 확인 합니다.

 - VPN 장치 외부 연결 공용 IPv4 주소가 필요합니다. Nat 뒤이 IP 주소를 찾을 수 없습니다.
 - 모든 리소스는 동일한 지역/위치에 배포 됩니다.

#### <a name="tutorial-example-values"></a>자습서의 예제 값

이 자습서의 예제에는 다음 값을 사용 합니다. 테스트 환경을 만들거나 이해를 돕기 위해 예제를 참조 하려면 이러한 값을 사용할 수 있습니다. VPN Gateway에 대 한 자세한 설정을 일반적으로 참조 [VPN Gateway 설정 정보](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings)합니다.

연결 사양:

 - **VPN 유형**: 경로 기반
 - **연결 형식**: 사이트 간(IPsec)
 - **게이트웨이 유형**: VPN
 - **Azure 연결 이름**: Azure-게이트웨이-azurestack의 경우-S2SGateway (포털은 자동 채우기가이 값)
 - **Azure Stack 연결 이름**: AzureStack-게이트웨이-Azure-S2SGateway (포털은 자동 채우기가이 값)
 - **공유 키**: 모든 연결의 양쪽 모두에 값을 일치 하는 VPN 하드웨어와 호환
 - **구독**: 기본 설정 된 구독
 - **리소스 그룹**: 테스트 인프라

네트워크 및 서브넷 IP 주소:

| Azure/Azure Stack 연결 | 이름 | 서브넷 | IP 주소 |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Azure vNet | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Azure Stack vNet | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Azure Virtual Network 게이트웨이 | Azure-Gateway |  |  |
| Azure Stack Virtual Network 게이트웨이 | AzureStack-Gateway |  |  |
| Azure 공용 IP | Azure-GatewayPublicIP |  | 생성 시 결정 |
| Azure Stack 공용 IP | AzureStack-GatewayPublicIP |  | 생성 시 결정 |
| Azure 로컬 네트워크 게이트웨이 | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Azure Stack 공용 IP 값 |
| Azure Stack 로컬 네트워크 게이트웨이 | Azure-S2SGateway<br>10.100.102.0/23 |  | Azure 공용 IP 값 |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>전역 Azure 및 Azure Stack에서 가상 네트워크 만들기

포털을 사용 하 여 가상 네트워크를 만들려면 다음 단계를 사용 합니다. 사용할 수 있습니다 [예제 값](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) 자습서와이 문서를 사용 하는 경우. 하지만이 문서에서는 프로덕션 환경을 구성 하려면를 사용 하는 경우 고유한 값을 사용 하 여 예제 설정을 대체 합니다.

> [!IMPORTANT]
> Azure Stack 또는 Azure vNet 주소 공간에서 IP 주소 중복 없다는 확인 해야 합니다.

Azure에서 vNet을 만들려면:

1. 브라우저를 사용 하 여에 연결 하는 [Azure portal](http://portal.azure.com/) 및 Azure 계정으로 로그인 합니다.
2. 선택 **리소스 만들기**합니다. 에 **marketplace 검색** 필드에 입력 `virtual network`'. 찾을 **가상 네트워크** 을 선택 하 고 결과 목록에서 **Virtual Network**합니다.
3. **배포 모델 선택** 목록에서 선택 **Resource Manager**를 선택한 후 **만들기**합니다.
4. 온 **가상 네트워크 만들기**, VNet 설정을 구성 합니다. 필수 필드 이름은 빨간색 별표가 붙습니다.  유효한 값을 입력 하면 별표 녹색 확인 표시로 변경 합니다.

Azure Stack에서 vNet을 만들려면:

* (1-4) Azure Stack을 사용 하 여 이전 단계를 반복 **테 넌 트 포털**합니다.

## <a name="add-a-gateway-subnet"></a>게이트웨이 서브넷 추가

가상 네트워크 게이트웨이에 연결 하기 전에 게이트웨이 서브넷에 연결 하려는 가상 네트워크에 대 한 만들기 해야 합니다. 게이트웨이 서비스는 게이트웨이 서브넷에 지정 된 IP 주소를 사용 합니다.

에 [Azure portal](http://portal.azure.com/), 가상 네트워크 게이트웨이 만들려는 Resource Manager 가상 네트워크로 이동 합니다.

1. 열려는 vNet을 선택 합니다 **가상 네트워크** 페이지입니다.
2.  **설정을**를 선택 **서브넷**합니다.
3. 에 **서브넷** 페이지에서 **+ 게이트웨이 서브넷** 여는 **서브넷 추가** 페이지.

    ![게이트웨이 서브넷 추가](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. 합니다 **이름을** 에 서브넷 'GatewaySubnet' 값에 자동으로 채워집니다. Azure가 서브넷을 게이트웨이 서브넷으로 인식하기 위해 이 값이 필요합니다.
5. 변경 된 **주소 범위** 구성 요구 사항에 맞는 선택한 후에 제공 되는 값 **확인**합니다.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Azure 및 Azure에서 가상 네트워크 게이트웨이 만들기 스택

Azure에서 가상 네트워크 게이트웨이 만들려면 다음 단계를 사용 합니다.

1. 포털 페이지의 왼쪽에서 선택 **+**  검색 필드에 '가상 네트워크 게이트웨이'를 입력 합니다.
2.  **결과**를 선택 **가상 네트워크 게이트웨이**합니다.
3. **가상 네트워크 게이트웨이**를 선택 **만들기** 열려는 합니다 **가상 네트워크 게이트웨이 만들기** 페이지입니다.
4. **가상 네트워크 게이트웨이 만들기**에 표시 된 대로 네트워크 게이트웨이에 대 한 값을 지정할 **자습서의 예제 값**, 다음 추가 값:

    - **SKU**: basic
    - **Virtual Network**: 이전에 만든 가상 네트워크를 선택 합니다. 만든 게이트웨이 서브넷을 자동으로 선택 됩니다.
    - **첫 번째 IP 구성**:  이것이 게이트웨이의 공용 IP입니다.
        - 선택 **게이트웨이 IP 구성 만들기**를 이동 합니다 **공용 IP 주소 선택** 페이지입니다.
        - 선택 **+ 새로 만들기** 열려는 합니다 **공용 IP 주소 만들기** 페이지입니다.
        - 입력 한 **이름을** 공용 IP 주소에 대 한 합니다. SKU로 둡니다 **기본**를 선택한 후 **확인** 변경 내용을 저장 하려면.

       > [!Note]
       > 현재 VPN Gateway는 동적 공용 IP 주소 할당만 지원합니다. 그러나 VPN gateway에 할당 된 후의 IP 주소가 변경 되는 것은 아닙니다. 게이트웨이가 삭제되고 다시 만들어지는 경우에만 공용 IP 주소가 변경됩니다. 크기 조정, 다시 설정 또는 기타 내부 유지 관리/업그레이드 VPN gateway에는 IP 주소를 변경 하지 마세요.

4. 게이트웨이 설정을 확인 합니다.
5. 선택 **Create** VPN 게이트웨이를 만듭니다. 게이트웨이 설정 유효성을 검사 하 고 대시보드에 "가상 네트워크 게이트웨이 배포" 타일이 표시 됩니다.

   >[!Note]
   >하나의 게이트웨이를 만드는 데 최대 45분이 걸릴 수 있습니다. 완료 상태를 확인하기 위해 포털 페이지를 새로 고쳐야 할 수 있습니다.

    게이트웨이 만든 후 포털에서 가상 네트워크를 살펴보면 할당 된 IP 주소를 볼 수 있습니다. 게이트웨이가 연결된 디바이스로 표시됩니다. 게이트웨이에 대 한 자세한 정보를 보려면 장치를 선택 합니다.

6. Azure Stack 배포의 이전 단계 (1-5)를 반복 합니다.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Azure 및 Azure Stack에서 로컬 네트워크 게이트웨이 만들기

로컬 네트워크 게이트웨이는 일반적으로 온-프레미스 위치를 가리킵니다. 사이트를 Azure 또는 Azure Stack에 참조를 지정 하는 이름을 제공 합니다.

- 에 대 한 연결을 만들려는 온-프레미스 VPN 장치의 IP 주소입니다.
- VPN 장치에 VPN 게이트웨이 통해 라우팅되는 IP 주소 접두사입니다. 사용자가 지정하는 주소 접두사는 온-프레미스 네트워크에 있는 접두사입니다.

  >[!Note]
  >에 온-프레미스 네트워크가 변경 되거나 VPN 장치의 공용 IP 주소를 변경 하려면 필요한 경우 쉽게 업데이트할 수 있습니다 이러한 값 이상입니다.

1. 포털에서 선택 **+ 리소스 만들기**합니다.
2. 검색 상자에서 입력 **로컬 네트워크 게이트웨이**을 선택한 후 **Enter** 검색 합니다. 그러면 결과 목록이 반환됩니다.
3. 선택 **로컬 네트워크 게이트웨이**을 선택한 후 **만들기** 열려고 합니다 **로컬 네트워크 게이트웨이 만들기** 페이지.
4. **로컬 네트워크 게이트웨이 만들기**, 로컬 네트워크 게이트웨이에 대 한 값을 지정를 사용 하 여 당사의 **자습서의 예제 값**합니다. 다음 추가 값을 포함 합니다.

    - **IP 주소**: 이것이 Azure 또는 Azure Stack에 연결 하려는 VPN 장치의 공용 IP 주소입니다. Azure는 주소에 연결할 수 있도록 NAT 뒤에 없는 유효한 공용 IP 주소를 지정 합니다. IP 주소가 지금 당장 없는, 하는 경우는 자리 표시자로 예제의 값을 사용할 수 있지만 VPN 장치의 공용 IP 주소를 사용 하 여 자리 표시자를 바꾸고 다시 이동 해야 합니다. Azure는 올바른 주소를 제공할 때까지 장치에 연결할 수 없습니다.
    - **주소 공간**: 이 로컬 네트워크가 나타내는 네트워크에 대 한 주소 범위입니다. 주소 공간 범위를 여러 개 추가할 수 있습니다. 지정한 범위가 연결 하려는 다른 네트워크의 범위와 겹치지 않는지 확인 합니다. Azure는 지정한 주소 범위를 온-프레미스 VPN 디바이스 IP 주소에 라우팅합니다. 예제 값이 아닌 온-프레미스 사이트에 연결 하려는 경우 고유한 값을 사용 합니다.
    - **BGP 설정 구성**: BGP를 구성할 경우에만 사용합니다. 그렇지 않으면 이것을 선택하지 마십시오.
    - **구독**: 올바른 구독이 표시되는지 확인합니다.
    - **리소스 그룹**: 사용하려는 리소스 그룹을 선택합니다. 새 리소스 그룹을 만들 수 있습니다 하거나 이미 만든 선택 합니다.
    - **위치**: 이 개체를 만들 위치를 선택합니다. 에서는 VNet에 있는 동일한 위치를 선택 하는 것이 좋습니다 있지만 수행할 필요가 없습니다.
5. 필요한 값을 지정 하는 것이 했으면, 선택 **Create** 로컬 네트워크 게이트웨이를 만듭니다.
6. Azure Stack 배포에서 이러한 단계 (1-5)를 반복 합니다.

## <a name="configure-your-connection"></a>연결 구성

온-프레미스 네트워크에 대한 사이트 간 연결에는 VPN 디바이스가 필요합니다. 구성한 VPN 장치 연결 이라고 합니다. 연결을 구성 하려면 다음을 수행 해야 합니다.

- 공유 키 - 사이트 간 VPN 연결을 만들 때 지정하는 것과 동일한 공유 키입니다. 이 예제에서는 기본적인 공유 키를 사용합니다. 실제로 사용할 키는 좀 더 복잡하게 생성하는 것이 좋습니다.
- 가상 네트워크 게이트웨이의 공용 IP 주소 Azure Portal, PowerShell 또는 CLI를 사용하여 공용 IP 주소를 볼 수 있습니다. Azure portal을 사용 하 여 VPN gateway의 공용 IP 주소를 찾으려면 가상 네트워크 게이트웨이로 이동한 다음 게이트웨이의 이름을 선택 합니다.

가상 네트워크 게이트웨이와 온-프레미스 VPN 장치 사이의 사이트 간 VPN 연결을 만들려면 다음 단계를 사용 합니다.

1. Azure portal에서 선택 **+ 리소스 만들기**합니다.
2. 검색할 **연결**합니다.
3. **결과**를 선택 **연결**합니다.
4. 온 **연결**를 선택 **만들기**합니다.
5. 온 **대 한 연결 만들기**, 다음 설정을 구성 합니다.

    - **연결 형식**: 사이트-사이트 간 (IPSec)를 선택 합니다.
    - **리소스 그룹**: 테스트 리소스 그룹을 선택 합니다.
    - **가상 네트워크 게이트웨이**: 사용자가 만든 가상 네트워크 게이트웨이 선택 합니다.
    - **로컬 네트워크 게이트웨이**: 사용자가 만든 로컬 네트워크 게이트웨이 선택 합니다.
    - **연결 이름**: 두 게이트웨이 값을 사용 하 여 자동으로 채워집니다.
    - **공유 키**: 이 값에는 로컬 온-프레미스 VPN 장치를 사용 하는 값과 일치 해야 합니다. 자습서 예제에서는 'abc123' 사용 하지만 있습니다 수을 사용 하 여 더 복잡 한 항목입니다. 중요 한 점은이 값이 VPN 장치를 구성할 때 지정한 것과 동일한 값이 되도록 합니다.
    - **구독**, **리소스 그룹** 및 **위치**에 대한 값이 고정됩니다.

6. 선택 **확인** 사용자 연결을 만듭니다.

연결을 볼 수는 **연결** 가상 네트워크 게이트웨이 페이지입니다. 상태에서 이동 *알 수 없는* 를 *연결*, 한 다음 *성공*합니다.

## <a name="next-steps"></a>다음 단계

- Azure 클라우드 패턴에 대 한 자세한 내용은 참조 하세요 [클라우드 디자인 패턴](https://docs.microsoft.com/azure/architecture/patterns)합니다.
