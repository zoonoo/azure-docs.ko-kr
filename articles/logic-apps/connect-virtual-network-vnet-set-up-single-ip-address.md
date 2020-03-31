---
title: ISEs에 대한 공용 아웃바운드 IP 주소 설정
description: Azure 논리 앱에서 통합 서비스 환경(ISEs)에 대한 단일 공용 아웃바운드 IP 주소를 설정하는 방법에 대해 알아봅니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 619c68b84291bc35b8216194ac4534393fde454c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191470"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Azure 논리 앱에서 하나 이상의 통합 서비스 환경에 대해 단일 IP 주소 설정

Azure Logic Apps로 작업할 때 [Azure 가상 네트워크의](../virtual-network/virtual-networks-overview.md)리소스에 액세스해야 하는 논리 앱을 호스팅하기 위한 [ *ISE(통합 서비스 환경)를* ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 설정할 수 있습니다. IP 제한이 있는 다른 끝점에 액세스해야 하는 ISE 인스턴스가 여러 개 있는 경우 [Azure 방화벽](../firewall/overview.md) 또는 네트워크 [가상 어플라이언스를](../virtual-network/virtual-networks-overview.md#filter-network-traffic) 가상 네트워크에 배포하고 해당 방화벽 또는 네트워크 가상 어플라이언스를 통해 아웃바운드 트래픽을 라우팅합니다. 그런 다음 가상 네트워크의 모든 ISE 인스턴스가 단일 공용, 정적 및 예측 가능한 IP 주소를 사용하여 대상 시스템과 통신하도록 할 수 있습니다. 이렇게 하면 각 ISE에 대해 해당 대상 시스템에 추가 방화벽 개구부를 설정할 필요가 없습니다.

이 항목에서는 Azure 방화벽을 통해 아웃바운드 트래픽을 라우팅하는 방법을 보여 주지만 Azure Marketplace의 타사 방화벽과 같은 네트워크 가상 어플라이언스에 유사한 개념을 적용할 수 있습니다. 이 항목에서는 여러 ISE 인스턴스에 대한 설정에 중점을 두지만 시나리오에서 액세스가 필요한 IP 주소 수를 제한해야 하는 경우 단일 ISE에 이 방법을 사용할 수도 있습니다. 방화벽 또는 가상 네트워크 어플라이언스에 대한 추가 비용이 시나리오에 적합한지 여부를 고려합니다. [Azure 방화벽 가격 책정에](https://azure.microsoft.com/pricing/details/azure-firewall/)대해 자세히 알아보십시오.

## <a name="prerequisites"></a>사전 요구 사항

* ISE와 동일한 가상 네트워크에서 실행되는 Azure 방화벽입니다. 방화벽이 없는 경우 먼저 가상 네트워크에 이름이 지정된 `AzureFirewallSubnet` [서브넷을 추가합니다.](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) 그런 다음 가상 네트워크에서 [방화벽을 만들고 배포할](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) 수 있습니다.

* Azure [라우트 테이블](../virtual-network/manage-route-table.md). 없는 경우 먼저 [라우트 테이블을 만듭니다.](../virtual-network/manage-route-table.md#create-a-route-table) 라우팅에 대한 자세한 내용은 [가상 네트워크 트래픽 라우팅을](../virtual-network/virtual-networks-udr-overview.md)참조하십시오.

## <a name="set-up-route-table"></a>경로 테이블 설정

1. Azure [포털에서](https://portal.azure.com)다음과 같은 경로 테이블을 선택합니다.

   ![아웃바운드 트래픽을 지시하기 위한 규칙이 있는 경로 테이블 선택](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. 새 경로 를 [추가하려면](../virtual-network/manage-route-table.md#create-a-route)경로 테이블 메뉴에서 **경로** > **추가를**선택합니다.

   ![아웃바운드 트래픽 을 유도하기 위한 경로 추가](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. 경로 **추가** 창에서 대상 시스템에 대한 모든 나가는 트래픽이 이 동작을 따르도록 지정하는 규칙으로 [새 경로를 설정합니다.](../virtual-network/manage-route-table.md#create-a-route)

   * 가상 [**어플라이언스를**](../virtual-network/virtual-networks-udr-overview.md#user-defined) 다음 홉 유형으로 사용합니다.

   * 방화벽 인스턴스의 개인 IP 주소로 이동하여 다음 홉 주소로 이동합니다.

     방화벽 메뉴에서 이 IP 주소를 찾으려면 **개요를**선택하여 **개인 IP 주소**아래에서 주소를 찾습니다.

     ![방화벽 개인 IP 주소 찾기](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   다음은 이러한 규칙의 모양을 보여 주는 예제입니다.

   ![아웃바운드 트래픽 을 유도하기 위한 규칙 설정](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | 속성 | 값 | 설명 |
   |----------|-------|-------------|
   | **경로 이름** | <*고유 경로 이름*> | 라우트 테이블의 경로에 대한 고유한 이름 |
   | **주소 접두사** | <*대상 주소*> | 트래픽을 이동하려는 대상 시스템의 주소입니다. 이 주소에 [클래스리스 도메인 간 라우팅(CIDR) 표기와를](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 사용해야 합니다. |
   | **다음 홉 유형** | **가상 어플라이언스** | 아웃바운드 트래픽에 사용되는 [홉 유형](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) |
   | **다음 홉 주소** | <*방화벽- 개인 IP 주소*> | 방화벽의 개인 IP 주소 |
   |||

## <a name="set-up-network-rule"></a>네트워크 규칙 설정

1. Azure 포털에서 방화벽을 찾아 선택합니다. 방화벽 메뉴에서 **설정에서** **규칙을**선택합니다. 규칙 창에서 네트워크 **규칙 모음** > **네트워크 규칙 모음 추가**를 선택합니다.

   ![방화벽에 네트워크 규칙 모음 추가](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. 컬렉션에서 대상 시스템에 대한 트래픽을 허용하는 규칙을 추가합니다.

   예를 들어 ISE에서 실행되고 SFTP 시스템과 통신해야 하는 논리 앱이 있다고 가정합니다. 라는 네트워크 규칙 컬렉션을 `LogicApp_ISE_SFTP_Outbound`만듭니다. `ISE_SFTP_Outbound` 이 규칙은 ISE가 가상 네트워크에서 실행되는 모든 서브넷의 IP 주소에서 방화벽의 개인 IP 주소를 사용하여 대상 SFTP 시스템으로 트래픽을 허용합니다.

   ![방화벽에 대한 네트워크 규칙 설정](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **네트워크 규칙 컬렉션 속성**

   | 속성 | 값 | 설명 |
   |----------|-------|-------------|
   | **이름** | <*네트워크 규칙-컬렉션 이름*> | 네트워크 규칙 컬렉션의 이름 |
   | **Priority** | <*우선 순위 수준*> | 규칙 컬렉션을 실행하는 데 사용할 우선 순위입니다. 자세한 내용은 [일부 Azure 방화벽 개념이란 무엇입니까?](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts) |
   | **작업** | **허용** | 이 규칙에 대해 수행할 작업 유형 |
   |||

   **네트워크 규칙 속성**

   | 속성 | 값 | 설명 |
   |----------|-------|-------------|
   | **이름** | <*네트워크 규칙 이름*> | 네트워크 규칙의 이름 |
   | **프로토콜** | <*연결 프로토콜*> | 사용할 연결 프로토콜입니다. 예를 들어 NSG 규칙을 사용하는 경우 **TCP** 및 **UDP를**모두 선택합니다. **TCP** |
   | **소스 주소** | <*ISE 서브넷 주소*> | 서브넷 IP는 ISE가 실행되는 위치와 논리 앱의 트래픽이 발생하는 위치를 해결합니다. |
   | **목적지 주소** | <*대상-IP 주소*> | 트래픽이 이동하려는 대상 시스템의 IP 주소 |
   | **대상 포트** | <*대상 포트*> | 대상 시스템이 인바운드 통신에 사용하는 모든 포트 |
   |||

   네트워크 규칙에 대한 자세한 내용은 다음 문서를 참조하십시오.

   * [네트워크 규칙 구성](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Azure Firewall 규칙 처리 논리](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Azure Firewall FAQ](../firewall/firewall-faq.md)
   * [Azure 파워쉘: 새 아즈방화벽네트워크룰](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: az 네트워크 방화벽 네트워크 규칙](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에서 Azure 가상 네트워크에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)