---
title: Azure 및 Azure 스택 하이브리드 클라우드 연결 구성 | Microsoft Docs
description: Azure 및 Azure 스택 하이브리드 클라우드 연결을 구성 하는 방법에 알아봅니다.
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
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 8b5f59d3fea402efa50bdafd7fc0439a93051e69
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>자습서: 하이브리드 클라우드 연결을 Azure 및 Azure 스택 구성

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

전역 Azure 및 하이브리드 연결 패턴을 사용 하 여 Azure 스택에서 보안 리소스에 액세스할 수 있습니다. 

이 자습서에서는 샘플 환경을 빌드합니다.

> [!div class="checklist"]
> - 온-프레미스 데이터 개인 정보 또는 규정 요구 사항에 대 한를 유지 하지만 글로벌 Azure 리소스에 액세스할 수 있습니다.
> - 레거시 시스템 글로벌 Azure의 클라우드 확장 앱 배포 및 리소스를 사용 하는 동안 유지 관리 합니다.

## <a name="prerequisites"></a>필수 조건

몇 가지 구성 요소에는 하이브리드 연결 배포를 빌드하는 데 필요한 및 준비 하는 데 시간이 걸릴 수 있습니다. 

Azure OEM/하드웨어 파트너 Azure 스택 프로덕션에 배포할 수 있습니다 및 모든 사용자는 ASDK 배포할 수 있습니다. Azure 스택 연산자 해야도 응용 프로그램 서비스를 배포, 계획 및 제안 테 넌 트 구독 및 만든 Windows Server 2016 이미지를 추가 합니다. 

이미 있는 경우 이러한 구성 요소 중 일부를 시작 하기 전에 요구 사항을 충족 해야 합니다.

이 항목에는 Azure 및 Azure 스택에 대 한 지식이 있다고 가정 합니다. 계속 하기 전에 자세한 정보를 원하는 경우 다음이 항목으로 시작 해야 합니다.
 - [Azure 소개](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure 스택 주요 개념](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure
 - Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
 - 만들기는 [웹 앱](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) Azure에서. 나중에 사용 중 이므로 새 웹 앱 URL을 기록해 두십시오.

### <a name="azure-stack"></a>Azure Stack
 - Azure 스택 프로덕션 또는 배포할 Azure 스택 개발 키트 아래 링크를 사용 합니다.
    - https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 
    - 설치는 일반적으로 있으므로 그에 따라 계획을 완료 하려면 몇 시간을 사용 합니다.
 - 배포 [앱 서비스](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) Azure 스택에 PaaS 서비스입니다. 
 - [계획/제안 만들기](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) Azure 스택 환경 내에서. 
 - [테 넌 트 구독 만들기](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) Azure 스택 환경 내에서. 


### <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음 기준을 충족하는지 확인합니다.

 - VPN 장치에 대한 외부 연결 공용 IPv4 주소가 있는지 확인합니다. 이 IP 주소는 NAT 뒤에 배치할 수 없습니다.
 - 모든 리소스가 동일한 지역/위치에 배포를 확인 합니다.

#### <a name="example-values"></a>예제 값

이 문서의 예제에서는 다음 값을 사용합니다. 이 문서의 예제를 보다 잘 이해 하려면를 참조 하거나 테스트 환경을 만들 이러한 값을 사용할 수 있습니다. 특정 게이트웨이 설정에 대한 자세한 내용은 [VPN Gateway 설정 정보](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings)를 참조하세요.

연결 사양:
 - **VPN 유형**: 경로 기반
 - **연결 유형**: 사이트 (IPsec)
 - **게이트웨이 유형**: VPN
 - **Azure 연결 이름**: Azure-게이트웨이-AzureStack-S2SGateway (포털은 자동 채우기가이 값)
 - **Azure 스택 연결 이름**: AzureStack-게이트웨이-Azure-S2SGateway (포털은 자동 채우기가이 값)
 - **공유 키**: 모든 연결의 양쪽 모두에 값을 일치 하는 VPN 하드웨어와 호환
 - **구독**: 모든 기본 구독
 - **리소스 그룹**: 테스트 인프라

| 스택 azure/Azure 연결 | 이름 | 서브넷 | IP 주소 |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Azure vNet | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Azure 스택 vNet | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Azure 가상 네트워크 게이트웨이 | Azure 게이트웨이 |  |  |
| Azure 스택 가상 네트워크 게이트웨이 | AzureStack 게이트웨이 |  |  |
| Azure 공용 IP | Azure GatewayPublicIP |  | 생성 시 결정 |
| Azure 스택 공용 IP | AzureStack GatewayPublicIP |  | 생성 시 결정 |
| Azure의 로컬 네트워크 게이트웨이 | AzureStack S2SGateway<br>   10.100.100.0/23 |  | Azure 스택 공용 IP 값 |
| Azure 스택 로컬 네트워크 게이트웨이 | Azure S2SGateway<br>10.100.102.0/23 |  | Azure 공용 IP 값 |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>전역 Azure 및 Azure 스택에서 가상 네트워크 만들기

> [!note]  
> Azure 또는 Azure 스택 vNet 주소 공간에서 Ip 순위 임을 확인 해야 합니다. 

Azure 포털을 사용 하 여 리소스 관리자 배포 모델에서 vNet을 만들려면 합니다. 이러한 단계를 자습서로 사용하는 경우 [예제 값](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values)을 사용합니다. 이러한 단계를 자습서로 사용하지 않는 경우 고유한 값으로 바꿔야 합니다. 

1. 브라우저에서 [Azure 포털](http://portal.azure.com/) 로 이동하고 Azure 계정으로 로그인합니다.
2. **리소스 만들기**를 클릭합니다. 에 **마켓플레이스 검색** 필드에, 입력 `virtual network`'. 찾을 **가상 네트워크** 반환된 된 목록에서 및 open의 **가상 네트워크** 페이지.
3. 가상 네트워크 페이지 하단에 있는 **배포 모델 선택** 목록에서 선택 **리소스 관리자**를 선택한 후 **만들기**합니다. 그러면 '가상 네트워크 만들기' 페이지가 열립니다.
4. **가상 네트워크 만들기** 페이지에서 VNet 설정을 구성합니다. 필드를 채울 때 필드에 입력한 문자가 유효하면 빨간색 느낌표가 녹색 확인 표시가 됩니다.
5. 부터 이러한 단계를 반복 하는 **테 넌 트 포털** Azure 스택의 합니다.

## <a name="add-a-gateway-subnet"></a>게이트웨이 서브넷 추가

가상 네트워크를 게이트웨이에 연결하기 전에 먼저 연결하려는 가상 네트워크에 대한 게이트웨이 서브넷을 만들어야 합니다. 게이트웨이 서비스는 게이트웨이 서브넷에 지정된 IP 주소를 사용합니다.

[포털](http://portal.azure.com/)에서 가상 네트워크 게이트웨이를 만들려는 Resource Manager 가상 네트워크로 이동합니다.

1. 에 **설정** 선택 VNet 페이지의 섹션 **서브넷** 확장 하는 **서브넷** 페이지.
2. 에 **서브넷** 페이지에서 **+ 게이트웨이 서브넷** 열려는 **서브넷 추가** 페이지.

    ![](media/azure-stack-solution-hybrid-connectivity/image4.png)

3. 서브넷의 **이름**에 'GatewaySubnet' 값이 자동으로 채워집니다. 이 값은 서브넷의 게이트웨이 서브넷으로 인식 하도록 Azure에 필요 합니다. 자동으로 채워진 조정 **주소 범위** 다음 사용자 구성 요구 사항 일치 하도록 값을 선택 **확인** 서브넷 만들기 페이지의 맨 아래에 있습니다.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Azure 및 Azure에서 가상 네트워크 게이트웨이 만들기 스택

1. 포털 페이지의 왼쪽에서 선택 + 검색에 ' 가상 네트워크 게이트웨이 '를 입력 합니다. **결과**을 찾아 선택 **가상 네트워크 게이트웨이**합니다.
2. 맨 아래에 **가상 네트워크 게이트웨이** 페이지에서 **만들기**합니다. 그러면 **가상 네트워크 게이트웨이 만들기** 페이지가 열립니다.
3. 에 **가상 네트워크 게이트웨이 만들기** 페이지에 예제 값 외에 아래에서 자세히 설명 하는 추가 값에 설명 된 대로 가상 네트워크 게이트웨이에 대 한 값을 지정 합니다.
    - **SKU**: 기본
    - **가상 네트워크**: 이전에 만든 가상 네트워크를 선택 합니다. 앞에서 만든 게이트웨이 서브넷이 자동으로 선택 됩니다. 
    - **첫 번째 IP 구성**: 게이트웨이의 공용 IP입니다. 
        - 클릭는 **게이트웨이 IP 구성 만들기** 그러면 이동 하 고는 **공용 IP 주소 선택** 페이지.
        - 클릭 **+ 새로 만들기** 열려는 **공용 IP 주소 만들기** 페이지.
        - 입력 한 **이름** 공용 IP 주소에 대 한 합니다. SKU로 두고 **기본**을 선택한 후 **확인** 변경 내용을 저장 하려면이 페이지의 맨 아래에 있습니다.

    > [!note]  
    > VPN 게이트웨이 현재 지원 동적 공용 IP 주소를 할당 합니다. 하지만 IP 주소가 VPN Gateway에 할당된 후 변경되는 것은 아닙니다. 게이트웨이가 삭제되고 다시 만들어지는 경우에만 공용 IP 주소가 변경됩니다. VPN Gateway의 크기 조정, 다시 설정 또는 기타 내부 유지 관리/업그레이드 시에는 변경되지 않습니다.

4. 설정을 확인합니다. 
5. **만들기**를 클릭하여 VPN Gateway를 만들기 시작합니다. 설정을 확인하면 대시보드에 "가상 네트워크 게이트웨이 배포" 타일이 표시됩니다. 하나의 게이트웨이를 만드는 데 최대 45분이 걸릴 수 있습니다. 완료 상태를 확인하기 위해 포털 페이지를 새로 고쳐야 할 수 있습니다.

    게이트웨이를 만든 후 포털에서 가상 네트워크를 살펴보면 게이트웨이에 할당된 IP 주소를 볼 수 있습니다. 게이트웨이가 연결된 장치로 표시됩니다. 자세한 내용을 보려면 연결된 된 장치 (가상 네트워크 게이트웨이)를 선택할 수 있습니다.
6. Azure 스택 배포에서이 단계를 반복 합니다.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Azure 및 Azure 스택에서 로컬 네트워크 게이트웨이 만들기

로컬 네트워크 게이트웨이는 일반적으로 온-프레미스 위치를 가리킵니다. 사이트 기준인 Azure 또는 Azure 스택 수 참조할 그런 다음 연결을 만듭니다 온-프레미스 VPN 장치의 IP 주소를 지정 하는 이름을 지정 합니다. 또한 VPN Gateway를 통해 VPN 장치로 라우팅될 IP 주소 접두사를 지정합니다. 사용자가 지정하는 주소 접두사는 온-프레미스 네트워크에 있는 접두사입니다. 온-프레미스 네트워크가 변경되거나 VPN 장치에서 공용 IP 주소를 변경해야 하는 경우 나중에 값을 쉽게 업데이트할 수 있습니다.

1. 포털에서 선택 **리소스를 만들 +** 합니다.
2. 검색 상자에 입력 **로컬 네트워크 게이트웨이**, 키를 누릅니다 **Enter** 을 찾으려고 합니다. 그러면 결과 목록이 반환됩니다. 클릭 **로컬 네트워크 게이트웨이**을 선택한 후는 **만들기** 버튼을 클릭은 **로컬 네트워크 게이트웨이 만들기** 페이지.
3. 에 **로컬 네트워크 게이트웨이 만들기 페이지**, 우리의 예제 값 아래에서 자세히 설명 하는 추가 값에 설명 된 대로 사용자 로컬 네트워크 게이트웨이에 대 한 값을 지정 합니다.
    - **IP 주소**:에 연결 하는 Azure 또는 Azure 스택 원하는 VPN 장치의 공용 IP 주소입니다. 유효한 공용 IP 주소를 지정합니다. IP 주소는 NAT 뒤에 있을 수 없고 Azure에서 도달할 수 있어야 합니다. IP 주소가 지금 당장 없는 경우 예제에 표시된 값을 사용할 수 있지만 다시 이동해서 VPN 장치의 공용 IP 주소로 자리 표시자 IP 주소를 바꿔야 합니다. 그렇지 않으면 Azure를 연결할 수 없습니다.
    - **주소 공간** 은 이 로컬 네트워크가 나타내는 네트워크에 대한 주소 범위를 가리킵니다. 주소 공간 범위를 여러 개 추가할 수 있습니다. 여기에서 지정한 범위가 연결하려는 다른 네트워크의 범위와 겹치지 않는지 확인합니다. Azure는 지정한 주소 범위를 온-프레미스 VPN 장치 IP 주소에 라우팅합니다. 이 예제에 표시 된 값이 아닌 온-프레미스 사이트에 연결 하려는 경우 여기 고유한 값을 사용 합니다.
    - **BGP 설정을 구성**: BGP를 구성할 때에 사용 합니다. 그렇지 않으면 이것을 선택하지 마십시오.
    - **구독**:에서 올바른 구독이 표시 되어 있는지 확인 합니다.
    - **리소스 그룹**: 리소스 그룹을 사용 하려면 선택 합니다. 새 리소스 그룹을 만들거나 이미 만든 리소스 그룹을 선택할 수 있습니다.
    - **위치**:이 개체에서 생성 되는 위치를 선택 합니다. VNet이 있는 동일한 위치를 선택하는 것이 좋지만 그렇게 하지 않아도 됩니다.
4. 값을 지정 했으면 때 선택 된 **만들기** 로컬 네트워크 게이트웨이를 만들어 페이지의 맨 아래에 있는 단추입니다.
5. Azure 스택 배포에서이 단계를 반복 합니다.

## <a name="configure-your-connection"></a>연결 구성

온-프레미스 네트워크에 대한 사이트 간 연결에는 VPN 장치가 필요합니다. 이 단계에서는 연결으로 알려진 VPN 장치를 구성 합니다. 연결을 구성할 때 다음이 필요 합니다.
    - 공유 키 - 사이트 간 VPN 연결을 만들 때 지정하는 것과 동일한 공유 키입니다. 이 예제에서는 기본적인 공유 키를 사용합니다. 실제로 사용할 키는 좀 더 복잡하게 생성하는 것이 좋습니다.
    - 가상 네트워크 게이트웨이의 공용 IP 주소 Azure Portal, PowerShell 또는 CLI를 사용하여 공용 IP 주소를 볼 수 있습니다. Azure 포털을 사용 하 여 VPN 게이트웨이의 공용 IP 주소를 확인 하려면 가상 네트워크 게이트웨이와 이동한 다음 게이트웨이의 이름을 선택 합니다.
가상 네트워크 게이트웨이와 온-프레미스 VPN 장치 사이의 사이트 간 VPN 연결을 만듭니다.
1. 포털에서 선택 **리소스를 만들 +** 합니다.
2. 검색 상자에 입력 **연결**, 키를 누릅니다 **Enter** 을 찾으려고 합니다. 그러면 결과 목록이 반환됩니다. 클릭 **연결**를 열려는 만들기 단추를 선택 합니다는 **연결 만들기** 페이지.
3. 에 **연결 만들기** 페이지 연결에 대 한 값을 구성 합니다.
     - 기본 사항:
        1. **연결 유형**: 사이트 (IPSec)을 선택 합니다.
        2. **리소스 그룹**: (테스트 리소스 그룹을 선택 합니다.)
     - 설정:
        1. **가상 네트워크 게이트웨이**: 이전에 만든 가상 네트워크 게이트웨이 선택 합니다.
        2. **로컬 네트워크 게이트웨이**: 이전에 만든 로컬 네트워크 게이트웨이 선택 합니다. 
        3. **연결 이름**: 자동이 두 게이트웨이의 값으로 채웁니다. 
        4. **공유 키**: 여기에서 값에는 로컬 온-프레미스 VPN 장치에 사용 하는 값과 일치 해야 합니다. 예제에서는 'abc123'을 사용하지만 좀 더 복잡한 항목을 사용할 수 있습니다. 중요한 점은 여기에서 지정한 값이 VPN 장치를 구성할 때 지정한 값과 동일해야 한다는 것입니다.
    - **구독**, **리소스 그룹** 및 **위치**에 대한 나머지 값이 고정됩니다.
4. **확인** 을 클릭하여 연결을 만듭니다. 만드는 연결이 화면에 표시 됩니다.
5. 연결을 볼 수는 * *의 가상 네트워크 게이트웨이 연결 페이지입니다. 상태에서 이동 **알 수 없는** 를 **연결**, 한 다음 **Succeeded**합니다.

## <a name="next-steps"></a>다음 단계

- Azure 클라우드 패턴에 대 한 자세한 참조 [클라우드 디자인 패턴](https://docs.microsoft.com/azure/architecture/patterns)합니다.
