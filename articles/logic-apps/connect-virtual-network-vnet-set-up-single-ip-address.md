---
title: ISE의 공용 아웃바운드 IP 주소 설정
description: Azure Logic Apps에서 ISE(통합 서비스 환경)의 단일 공용 아웃바운드 IP 주소를 설정하는 방법 알아보기
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 7ec495dd52607f2f65e0bef50489dd182c2a3253
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874192"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Azure Logic Apps에서 통합 서비스 환경 하나 이상에 단일 IP 주소 설정

Azure Logic Apps에서 작업하는 경우 [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)의 리소스에 액세스해야 하는 논리 앱을 호스팅하는 데 사용되는 [ISE(*통합 서비스 환경*)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)을 설정할 수 있습니다. IP 제한이 있는 다른 엔드포인트에 액세스해야 하는 ISE 인스턴스가 여러 개 있으면 [Azure Firewall](../firewall/overview.md) 또는 [네트워크 가상 어플라이언스](../virtual-network/virtual-networks-overview.md#filter-network-traffic)를 가상 네트워크에 배포하고 해당 방화벽이나 네트워크 가상 어플라이언스를 통해 아웃바운드 트래픽을 라우팅합니다. 그러면 가상 네트워크의 모든 ISE 인스턴스에서 단일, 공용, 정적 및 예측 가능한 IP 주소를 사용하여 원하는 대상 시스템과 통신합니다. 이런 방식으로 각 ISE의 대상 시스템에서 추가 방화벽을 설정할 필요가 없습니다.

이 주제에서는 Azure Firewall을 통해 아웃바운드 트래픽을 라우팅하는 방법을 설명합니다. 하지만 Azure Marketplace의 타사 방화벽과 같은 네트워크 가상 어플라이언스에 유사한 개념을 적용할 수 있습니다. 이 주제에서는 여러 ISE 인스턴스 설정을 주로 설명합니다. 하지만 시나리오에서 액세스해야 하는 IP 주소 수를 제한해야 하는 경우 단일 ISE에 이 방식을 사용할 수도 있습니다. 방화벽 또는 가상 네트워크 어플라이언스에 대한 추가 비용이 시나리오에 적합한지 고려하십시오. [Azure Firewall 가격 책정](https://azure.microsoft.com/pricing/details/azure-firewall/)에 대해 자세히 알아보세요.

## <a name="prerequisites"></a>사전 요구 사항

* ISE와 동일한 가상 네트워크에서 실행되는 Azure Firewall. 방화벽이 없으면 먼저 가상 네트워크에 `AzureFirewallSubnet`이라는 [서브넷을 추가](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)합니다. 그러면 가상 네트워크에 [방화벽을 만들고 배포](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)할 수 있습니다.

* Azure [경로 테이블](../virtual-network/manage-route-table.md). 없으면 먼저 [경로 테이블을 만듭니다](../virtual-network/manage-route-table.md#create-a-route-table). 라우팅에 대한 자세한 내용은 [가상 네트워크 트래픽 라우팅](../virtual-network/virtual-networks-udr-overview.md)을 참조하세요.

## <a name="set-up-route-table"></a>경로 테이블 설정

1. [Azure Portal](https://portal.azure.com)에서 경로 테이블을 선택합니다. 예를 들면 다음과 같습니다.

   ![아웃바운드 트래픽 전송 규칙이 있는 경로 테이블 선택](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. 경로 테이블 메뉴에 [새 경로를 추가](../virtual-network/manage-route-table.md#create-a-route)하려면 **경로** > **추가** 를 선택합니다.

   ![아웃바운드 트래픽 전송 경로 추가](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. **경로 추가** 창에서 대상 시스템으로 나가는 모든 아웃바운드 트래픽이 이 동작을 따르도록 지정하는 규칙과 함께 [새 경로를 설정](../virtual-network/manage-route-table.md#create-a-route)합니다.

   * [**가상 어플라이언스**](../virtual-network/virtual-networks-udr-overview.md#user-defined)를 다음 홉 형식으로 사용합니다.

   * 다음 홉 주소로써 방화벽 인스턴스의 개인 IP 주소로 이동합니다.

     이 IP 주소를 찾으려면 방화벽 메뉴에서 **개요** 를 선택하고 **개인 IP 주소** 에서 주소를 찾습니다. 예를 들면 다음과 같습니다.

     ![방화벽 개인 IP 주소 찾기](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   다음은 이러한 규칙이 표시되는 방식을 보여주는 예제입니다.

   ![아웃바운드 트래픽 전송 규칙 설정](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | 속성 | 값 | Description |
   |----------|-------|-------------|
   | **경로 이름** | <*unique-route-name*> | 경로 테이블의 고유한 경로 이름 |
   | **주소 접두사** | <*destination-address*> | 아웃바운드 트래픽을 이동하려는 대상 시스템의 주소 접두사입니다. 이 주소에 [CIDR(클래스 없는 도메인 간 라우팅) 표기법](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)을 사용했는지 확인합니다. 이 예제에서 이 주소 접두사는 [네트워크 규칙 설정](#set-up-network-rule) 섹션에서 설명한 SFTP 서버에 사용됩니다. |
   | **다음 홉 유형** | **가상 어플라이언스** | 아웃바운드 트래픽에서 사용하는 [홉 형식](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) |
   | **다음 홉 주소** | <*firewall-private-IP-address*> | 방화벽의 개인 IP 주소 |
   |||

<a name="set-up-network-rule"></a>

## <a name="set-up-network-rule"></a>네트워크 규칙 설정

1. Azure Portal에서 방화벽을 찾고 선택합니다. 방화벽 메뉴의 **설정** 에서 **규칙** 을 선택합니다. 규칙 창에서 **네트워크 규칙 컬렉션** > **네트워크 규칙 컬렉션 추가** 를 선택합니다.

   ![방화벽에 네트워크 규칙 컬렉션 추가](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. 컬렉션에서 트래픽을 허용하는 규칙을 대상 시스템에 추가합니다.

   예를 들어 ISE에서 실행되는 논리 앱이 있고 SFTP 서버와 통신해야 한다고 가정합니다. `ISE_SFTP_Outbound`라는 네트워크 규칙이 포함된 `LogicApp_ISE_SFTP_Outbound`라는 네트워크 규칙 컬렉션을 만듭니다. 이 규칙은 방화벽의 개인 IP 주소를 사용하여 ISE가 가상 네트워크에서 실행되는 서브넷의 IP 주소에서 대상 SFTP 서버로 나가는 트래픽을 허용합니다.

   ![방화벽의 네트워크 규칙 설정](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **네트워크 규칙 컬렉션 속성**

   | 속성 | 값 | Description |
   |----------|-------|-------------|
   | **이름** | <*network-rule-collection-name*> | 네트워크 규칙 컬렉션 이름 |
   | **우선 순위** | <*priority-level*> | 규칙 컬렉션을 실행하는 데 사용할 우선 순위 순서입니다. 자세한 내용은 [Azure Firewall의 몇 가지 개념을 알려주세요](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)를 참조하세요. |
   | **작업** | **허용** | 이 규칙에 수행할 작업 유형입니다. |
   |||

   **네트워크 규칙 속성**

   | 속성 | 값 | Description |
   |----------|-------|-------------|
   | **이름** | <*network-rule-name*> | 네트워크 규칙 이름 |
   | **프로토콜** | <*connection-protocols*> | 사용할 연결 프로토콜. 예를 들어 NSG 규칙을 사용하는 경우 **TCP** 만이 아니라 **TCP** 와 **UDP** 를 모두 선택합니다. |
   | **원본 주소** | <*ISE-subnet-addresses*> | ISE가 실행되고 논리 앱의 트래픽이 시작하는 서브넷 IP 주소 |
   | **대상 주소** | <*destination-IP-address*> | 아웃바운드 트래픽을 이동하려는 대상 시스템의 IP 주소입니다. 이 예제에서 이 IP 주소는 SFTP 서버용입니다. |
   | **대상 포트** | <*destination-ports*> | 대상 시스템에서 인바운드 통신에 사용하는 포트 |
   |||

   네트워크 규칙에 대한 자세한 내용은 다음 문서를 참조하세요.

   * [네트워크 규칙 구성](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Azure Firewall 규칙 처리 논리](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Azure Firewall FAQ](../firewall/firewall-faq.yml)
   * [Azure PowerShell: New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: az network firewall network-rule](/cli/azure/network/firewall/network-rule#az_network_firewall_network_rule_create)

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에서 Azure 가상 네트워크에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
