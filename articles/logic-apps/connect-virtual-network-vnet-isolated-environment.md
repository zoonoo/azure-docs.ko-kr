---
title: ISE(통합 서비스 환경)를 통해 Azure Logic Apps에서 Azure 가상 네트워크에 연결
description: 논리 앱 및 통합 계정이 공용이나 “글로벌” Azure에서 격리된 비공개 상태를 유지하면서 Azure 가상 네트워크에 액세스할 수 있도록 ISE(통합 서비스 환경)를 만듭니다.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/25/2018
ms.openlocfilehash: d9a849fb5556332fab39467c270360c09c774cc9
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231783"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-through-an-integration-service-environment-ise"></a>ISE(통합 서비스 환경)를 통해 Azure Logic Apps에서 Azure 가상 네트워크에 연결

> [!NOTE]
> 이 기능은 *비공개 미리 보기* 상태입니다. 액세스를 요청하려면 [여기서 참여 요청을 작성](https://aka.ms/iseprivatepreview)하세요.

논리 앱 및 통합 계정이 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)에 액세스해야 하는 통합 시나리오의 경우 [*ISE*(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)를 만듭니다. 이는 전용 저장소를 사용하는 개인적이며 격리된 환경으로, 기타 리소스가 공용 또는 *글로벌* Logic Apps 서비스와는 분리됩니다. 이러한 격리로 인해 다른 Azure 테넌트가 앱 성능에 줄 수 있는 영향이 감소됩니다. 이 ISE를 Azure 가상 네트워크에 연결한 다음, Logic Apps 서비스를 사용자의 가상 네트워크에 배포할 수 있습니다. 논리 앱 또는 통합 계정을 만들 때 해당 위치로 이 ISE를 선택합니다. 그러면 논리 앱 및 통합 계정은 가상 네트워크에서 VM(가상 머신), 서버, 시스템 및 서비스와 같은 리소스에 직접 액세스할 수 있습니다. 

![통합 서비스 환경 선택](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

이 문서에서는 다음 작업을 수행하는 방법에 대해 설명합니다.

* 전용 Logic Apps 인스턴스에서 가상 네트워크에 액세스할 수 있도록 Azure 가상 네트워크에 대한 권한을 설정합니다.

* ISE(통합 서비스 환경)를 만듭니다. 

* ISE에서 실행할 수 있는 논리 앱을 만듭니다. 

* ISE에서 논리 앱에 대한 통합 계정을 만듭니다.

통합 서비스 환경에 대한 자세한 내용은 [격리된 Azure Logic Apps에서 Azure Virtual Network 리소스에 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다. 

* Azure 가상 네트워크가 없는 경우 [Azure 가상 네트워크를 만드는](../virtual-network/quick-create-portal.md) 방법을 알아봅니다. 

  > [!IMPORTANT]
  > 환경을 만드는 데 Azure 가상 네트워크가 필요하지 않지만 해당 환경을 만들 때 환경의 피어로 가상 네트워크를 선택할 수*만* 있습니다. 

* Azure 가상 네트워크에 논리 앱 직접 액세스 권한을 부여하려면 Logic Apps 서비스에서 가상 네트워크에 액세스하기 위한 권한을 갖도록 [RBAC(역할 기반 액세스 제어) 권한을 설정합니다](#vnet-access). 

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>가상 네트워크 권한 설정

통합 서비스 환경을 만들 때 환경의 *피어*로 Azure 가상 네트워크를 선택할 수 있습니다. 그러나 이 단계 또는 *피어링*은 환경을 만들 때만 수행할 수 있습니다. 이 관계를 통해 Logic Apps 서비스는 해당 가상 네트워크의 리소스에 직접 연결할 수 있으며 해당 리소스에 대한 환경 액세스를 제공합니다. 

가상 네트워크를 선택하기 전에 가상 네트워크에서 RBAC(역할 기반 액세스 제어) 권한을 설정해야 합니다. 이 작업을 완료하려면 Azure Logic Apps 서비스에 특정 역할을 할당해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 가상 네트워크를 찾고 선택합니다. 

1. 가상 네트워크의 메뉴에서 **액세스 제어(IAM)** 를 선택합니다. 

1. **액세스 제어** 아래에서 아직 선택하지 않은 경우 **역할 할당**을 선택합니다. **역할 할당** 도구 모음에서 **추가**를 선택합니다. 

   ![역할 할당 추가](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. **권한 추가** 창에서 Azure Logic Apps 서비스에 대한 이 테이블에서 각 역할을 설정합니다. 각 역할을 마친 후 **저장**을 선택했는지 확인합니다.

   | 역할 | 다음에 대한 액세스 할당 | 여기서 | 
   |------|------------------|--------|
   | **네트워크 기여자** | **Azure AD 사용자, 그룹 또는 응용 프로그램** | **Azure Logic Apps**를 입력합니다. 멤버 목록이 표시되면 동일한 값을 선택합니다. <p>**팁**: 이 서비스를 찾을 수 없는 경우 Logic Apps 서비스의 앱 ID를 입력합니다. `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   | **클래식 기여자** | **Azure AD 사용자, 그룹 또는 응용 프로그램** | **Azure Logic Apps**를 입력합니다. 멤버 목록이 표시되면 동일한 값을 선택합니다. <p>**팁**: 이 서비스를 찾을 수 없는 경우 Logic Apps 서비스의 앱 ID를 입력합니다. `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   |||| 

   예: 

   ![권한 추가](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

   피어링에 필요한 역할 권한에 대한 자세한 내용은 [가상 네트워크 피어링 만들기, 변경 또는 삭제의 권한 섹션](../virtual-network/virtual-network-manage-peering.md#permissions)을 참조하세요. 

가상 네트워크가 Azure ExpressRoute, Azure 지점 및 사이트 간 VPN 또는 Azure 사이트 간 VPN을 통해 연결되는 경우 필요한 게이트웨이 서브넷을 추가할 수 있도록 다음 섹션을 계속합니다. 그렇지 않은 경우 [환경 만들기](#create-environment)를 계속합니다.

<a name="add-gateway-subnet"></a>

## <a name="add-gateway-subnet-for-virtual-networks-with-expressroute-or-vpns"></a>ExpressRoute 또는 VPN을 사용하여 가상 네트워크에 대한 게이트웨이 서브넷 추가

이전 단계를 마친 후 [Azure ExpressRoute](../expressroute/expressroute-introduction.md), [Azure 지점 및 사이트 간 VPN](../vpn-gateway/point-to-site-about.md) 또는 [Azure 사이트 간 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)을 통해 연결된 Azure 가상 네트워크에 대한 ISE( 통합 서비스 환경) 액세스를 제공하려면 가상 네트워크에 [*게이트웨이 서브넷*](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsub)을 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 가상 네트워크를 찾고 선택합니다. 가상 네트워크의 메뉴에서 **서브넷**을 선택한 다음, **게이트웨이 서브넷** > **확인**을 선택합니다.

   ![게이트웨이 서브넷 추가](./media/connect-virtual-network-vnet-isolated-environment/add-gateway-subnet.png)

1. 이제 이전에 만든 게이트웨이 서브넷과 연결할 [*경로 테이블*](../virtual-network/manage-route-table.md)을 만듭니다.

   1. 기본 Azure 메뉴에서 **리소스 만들기** > 
   **네트워킹** > **경로 테이블**을 선택합니다.

      ![경로 테이블 만들기](./media/connect-virtual-network-vnet-isolated-environment/create-route-table.png)

   1. 이름, 사용할 Azure 구독, Azure 리소스 그룹 및 위치와 같은 경로 테이블에 대한 정보를 제공합니다. **BGP 경로 전파** 속성이 **사용**으로 설정되었는지 확인한 다음, **만들기**를 선택합니다.

      ![경로 테이블 세부 정보 제공](./media/connect-virtual-network-vnet-isolated-environment/enter-route-table-information.png)

   1. 경로 테이블 메뉴에서 **서브넷**을 선택한 다음, **연결**을 선택합니다. 

      ![서브넷에 경로 테이블 연결](./media/connect-virtual-network-vnet-isolated-environment/associate-route-table.png)

   1. **가상 네트워크**를 선택한 다음, 사용자의 가상 네트워크를 선택합니다.
   
   1. **서브넷**을 선택한 다음, 이전에 만든 게이트웨이 서브넷을 선택합니다.

   1. 작업을 완료하면 **확인**을 선택합니다.

1. 지점 및 사이트 간 VPN이 있는 경우 다음 단계도 완료합니다.

   1. Azure에서 가상 네트워크 게이트웨이 리소스를 찾고 선택합니다.

   1. 게이트웨이의 메뉴에서 **지점 및 사이트 간 구성**을 선택합니다. 
   그런 다음, 가장 최신 VPN 클라이언트 구성을 갖도록 **VPN 클라이언트 다운로드**를 선택합니다.

      ![최신 VPN 클라이언트 다운로드](./media/connect-virtual-network-vnet-isolated-environment/download-vpn-client.png)

이제 ExpressRoute, 지점 및 사이트 간 VPN 또는 사이트 간 VPN을 사용하는 가상 네트워크의 게이트웨이 서브넷을 설정했습니다. 통합 서비스 환경 만들기를 계속하려면 다음 단계를 수행합니다.

<a name="create-environment"></a>

## <a name="create-your-ise"></a>ISE 만들기

ISE(통합 서비스 환경)를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)의 기본 Azure 메뉴에서 **리소스 만들기**를 선택합니다.

   ![새 리소스 만들기](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 검색 상자에서 필터로 "통합 서비스 환경"을 입력합니다.
결과 목록에서 **통합 서비스 환경(미리 보기)** 을 선택한 다음, **만들기**를 선택합니다.

   !["통합 서비스 환경" 선택](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   !["만들기" 선택](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. 환경에 대한 세부 정보를 제공합니다.

   ![환경 세부 정보 제공](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | 자산 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **Name** | yes | <*environment-name*> | 환경에 부여할 이름 | 
   | **구독** | yes | <*Azure-subscription-name*> | 환경에 사용할 Azure 구독 | 
   | **리소스 그룹** | yes | <*Azure-resource-group-name*> | 환경을 만들려는 Azure 리소스 그룹 |
   | **위치**: | yes | <*Azure-datacenter-region*> | 환경에 대한 정보를 저장할 Azure 데이터 센터 지역 |
   | **피어 VNET** | 아니요 | <*Azure-VNET-name*> | 해당 환경의 논리 앱이 가상 네트워크에 액세스할 수 있도록 *피어*로 환경에 연결할 Azure 가상 네트워크입니다. 이 관계를 만들려면 먼저 [Azure Logic Apps에 대한 가상 네트워크에서 역할 기반 액세스 제어를 설정](#vnet-access)했는지 확인합니다. <p>**중요**: 가상 네트워크는 필요하지 않지만 가상 네트워크는 환경을 만들 때*만* 선택할 수 있습니다. | 
   | **피어링 이름** | 선택한 가상 네트워크를 사용하여 예 | <*peering-name*> | 피어 관계를 지정할 이름 | 
   | **VNET IP 범위** | 선택한 가상 네트워크를 사용하여 예 | <*IP-address-range*> | 환경에서 리소스를 만드는 데 사용할 IP 주소 범위입니다. 이 범위는 [CIDR(Classless Inter-Domain Routing) 형식](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)(예: 10.0.0.1/16)을 사용해야 하며 클래스 B 주소 공간이 필요합니다. 범위는 **피어 VNET** 속성에서 선택한 가상 네트워크의 주소 공간 내 또는 피어링 또는 게이트웨이를 통해 피어 네트워크가 연결된 다른 사설 IP 주소 내에 존재하지 않아야 합니다. <p><p>**중요**: 환경을 만든 후 이 주소 범위를 *변경할 수 없습니다*. |
   |||||
   
1. 작업을 완료하면 **만들기**를 선택합니다. 

   Azure는 환경 배포를 시작하지만 이 프로세스는 완료하기까지 *최대 2시간*이 걸릴 수 있습니다. 
   배포 상태를 확인하려면 Azure 도구 모음에서 알림 창을 여는 알림 아이콘을 선택합니다.

   ![배포 상태 확인](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   배포가 성공적으로 완료되면 Azure에서 이 알림을 표시합니다.

   ![배포 성공](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

1. 배포가 완료된 후에 Azure가 환경으로 자동으로 이동하지 않는 경우 환경을 보려면 **리소스로 이동**을 선택합니다.  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>논리 앱 만들기 - ISE

ISE(통합 서비스 환경)를 사용하는 논리 앱을 만들려면 다음과 같은 차이점 및 고려 사항이 있는 [논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)의 일반적인 단계를 따릅니다. 

* 논리 앱을 만들 때 **Location** 속성은 사용 가능한 지역과 함께 **통합 서비스 환경** 아래에 ISE를 나열합니다. 지역 대신 ISE를 선택합니다. 예를 들면 다음과 같습니다.

  ![통합 서비스 환경 선택](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* 부모 논리 앱과 동일한 ISE에서 실행하는 HTTP 트리거 또는 작업과 같은 동일한 기본 제공 항목을 사용할 수 있습니다. **ISE** 레이블이 있는 커넥터는 부모 논리 앱과 동일한 ISE에서 실행됩니다. **ISE** 레이블이 없는 커넥터는 전역 Logic Apps 서비스에서 실행됩니다.

  ![ISE 커넥터 선택](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* 피어로 Azure 가상 네트워크를 사용하여 이전에 ISE를 설정한 경우 ISE의 논리 앱은 해당 가상 네트워크의 리소스에서 직접 액세스할 수 있습니다. ISE에 연결된 가상 네트워크에 있는 온-프레미스 시스템의 경우에는 논리 앱이 다음 항목 중 하나를 사용하여 해당 시스템에 직접 액세스할 수 있습니다. 

  * SQL Server 등의 해당 시스템용 ISE 커넥터

  * HTTP 동작 

  * 사용자 지정 커넥터

  가상 네트워크에 있지 않거나 ISE 커넥터가 없는 온-프레미스 시스템의 경우에도 [온-프레미스 데이터 게이트웨이를 설정 및 사용](../logic-apps/logic-apps-gateway-install.md)하고 나면 연결이 가능합니다.

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>통합 계정 만들기 - ISE

ISE(통합 서비스 환경)에서 논리 앱으로 통합 계정을 사용하려면 해당 통합 계정은 논리 앱으로 *동일한 환경*을 사용해야 합니다. ISE의 논리 앱은 같은 ISE에 있는 통합 계정만 참조할 수 있습니다. 

ISE를 사용하는 통합 계정을 만들려면 이제 사용 가능한 지역과 함께 **통합 서비스 환경** 아래에 ISE를 나열하는 **Location** 속성을 제외하고 [통합 계정을 만드는 방법](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)의 일반적인 단계를 따릅니다. 지역 대신 ISE를 선택합니다. 예를 들면 다음과 같습니다.

![통합 서비스 환경 선택](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure Logic Apps 포럼</a>을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 <a href="https://aka.ms/logicapps-wish" target="_blank">Logic Apps 사용자 의견 사이트</a>를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)에 대해 자세히 알아보기
* [Azure 서비스에 대한 가상 네트워크 통합](../virtual-network/virtual-network-for-azure-services.md)에 대해 알아보기
