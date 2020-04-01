---
title: ISE를 사용하여 Azure 가상 네트워크에 연결
description: Azure 논리 앱에서 Azure 가상 네트워크(VNET)에 액세스할 수 있는 ISE(통합 서비스 환경 만들기)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 6683c1b78b0e7ecba162026708c83843e2c08180
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478877"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>ISE(통합 서비스 환경)를 사용하여 Azure Logic Apps에서 Azure 가상 네트워크에 연결

논리 앱 및 통합 계정이 [Azure 가상 네트워크에](../virtual-network/virtual-networks-overview.md)액세스해야 하는 시나리오의 경우 [ *ISE(통합 서비스 환경)를* ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)만듭니다. ISE는 전용 저장소 및 "글로벌" 다중 테넌트 논리 앱 서비스와 별도로 유지되는 기타 리소스를 사용하는 격리된 환경입니다. 이러한 격리로 인해 다른 Azure 테넌트가 앱 성능에 줄 수 있는 영향이 감소됩니다. ISE는 또한 사용자 고유의 정적 IP 주소를 제공합니다. 이러한 IP 주소는 공용 다중 테넌트 서비스의 논리 앱에서 공유하는 정적 IP 주소와 는 별개입니다.

ISE를 만들 때 *Azure는* ISE를 Azure 가상 네트워크에 삽입한 다음 논리 앱 서비스를 가상 네트워크에 배포합니다. 논리 앱 또는 통합 계정을 만들 때 ISE를 해당 위치로 선택합니다. 그러면 논리 앱 및 통합 계정은 가상 네트워크에서 VM(가상 머신), 서버, 시스템 및 서비스와 같은 리소스에 직접 액세스할 수 있습니다.

![통합 서비스 환경 선택](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> 논리 앱과 통합 계정이 ISE에서 함께 작동하려면 둘 다 위치와 *동일한 ISE를* 사용해야 합니다.

ISE는 실행 기간, 저장소 보존, 처리량, HTTP 요청 및 응답 시간 제한, 메시지 크기 및 사용자 지정 커넥터 요청에 대한 제한을 늘렸습니다. 자세한 내용은 [Azure 논리 앱에 대한 제한 및 구성을](../logic-apps/logic-apps-limits-and-config.md)참조하십시오. ISEs에 대한 자세한 내용은 [Azure 논리 앱의 Azure 가상 네트워크 리소스에 대한 액세스를](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)참조하십시오.

이 문서에서는 Azure 포털을 사용하여 이러한 작업을 완료하는 방법을 보여 주며 다음과 같은 방법을 보여 주며 있습니다.

* ISE에 대한 액세스를 활성화합니다.
* ISE를 만듭니다.
* ISE에 용량을 추가합니다.

[샘플 Azure 리소스 관리자 퀵스타트 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) 사용하거나 고객 관리 키 설정을 포함하여 Logic Apps REST API를 사용하여 ISE를 만들 수도 있습니다.

* [논리 앱 REST API를 사용하여 통합 서비스 환경(ISE) 만들기](../logic-apps/create-integration-service-environment-rest-api.md)
* [ISEs의 미사용 데이터를 암호화하도록 고객 관리 키 설정](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

  > [!IMPORTANT]
  > ISE에서 실행되는 논리 앱, 기본 제공 트리거, 기본 제공 작업 및 커넥터는 소비 기반 가격 책정 계획과 다른 가격 책정 계획을 사용합니다. ISEs의 가격 책정 및 청구 작동 방식은 [논리 앱 가격 책정 모델을](../logic-apps/logic-apps-pricing.md#fixed-pricing)참조하십시오. 가격 책정 요금은 [논리 앱 가격 책정을](../logic-apps/logic-apps-pricing.md)참조하십시오.

* [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md). 가상 네트워크가 없는 경우 [Azure 가상 네트워크를 만드는](../virtual-network/quick-create-portal.md) 방법을 알아봅니다.

  * 가상 네트워크에는 ISE에 리소스를 만들고 배포하기 위해 4개의 *빈* 서브넷이 있어야 합니다. 각 서브넷은 ISE에 사용되는 다른 논리 앱 구성 요소를 지원합니다. 이러한 서브넷을 미리 만들거나 ISE를 만들 때까지 기다릴 수 있습니다. [서브넷 요구 사항에](#create-subnet)대해 자세히 알아보십시오.

  * 서브넷 이름은 알파벳 문자 또는 밑줄로 시작해야 하며 이러한 문자를 사용할 `<` `>`수 `%` `&` `\\`없습니다. `?` `/` 
  
  * Azure 리소스 관리자 템플릿을 통해 ISE를 배포하려면 먼저 빈 서브넷 하나를 Microsoft.Logic/integrationServiceEnvironment에 위임해야 합니다. Azure 포털을 통해 배포할 때 이 위임을 수행할 필요가 없습니다.

  * ISE가 올바르게 작동하고 계속 액세스할 수 있도록 가상 네트워크에서 [ISE에 액세스할](#enable-access) 수 있도록 해야 합니다.

  * 연결 공급자가 제공하는 Microsoft 클라우드 서비스에 대한 개인 연결을 제공하는 [ExpressRoute를](../expressroute/expressroute-introduction.md)사용하는 경우 다음 경로가 있는 [경로 테이블을 만들고](../virtual-network/manage-route-table.md) ISE에서 사용하는 각 서브넷에 해당 테이블을 연결해야 합니다.

    **이름**: <*경로 이름*><br>
    **주소 접두사**: 0.0.0.0/0<br>
    **다음 홉**: 인터넷

* Azure 가상 네트워크에 사용자 지정 DNS 서버를 사용하려면 ISE를 가상 네트워크에 배포하기 전에 [다음 단계에 따라 해당 서버를 설정합니다.](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) DNS 서버 설정 관리에 대한 자세한 내용은 [가상 네트워크 만들기, 변경 또는 삭제를](../virtual-network/manage-virtual-network.md#change-dns-servers)참조하십시오.

  > [!NOTE]
  > DNS 서버 또는 DNS 서버 설정을 변경하는 경우 ISE가 이러한 변경 사항을 선택할 수 있도록 ISE를 다시 시작해야 합니다. 자세한 내용은 [ISE 를 다시 시작 을](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE)참조하십시오.

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>ISE에 대한 액세스를 사용하도록 설정

Azure 가상 네트워크에서 ISE를 사용하는 경우 공통 설치 문제중 하나 이상의 차단된 포트가 있는 것입니다. ISE와 대상 시스템 간의 연결을 만드는 데 사용하는 커넥터에도 고유한 포트 요구 사항이 있을 수 있습니다. 예를 들어 FTP 커넥터를 사용하여 FTP 시스템과 통신하는 경우 FTP 시스템에서 사용하는 포트(예: 명령을 보내기 위한 포트 21)를 사용할 수 있어야 합니다.

ISE에 액세스할 수 있고 ISE의 논리 앱이 가상 네트워크의 각 서브넷에서 통신할 수 있는지 확인하려면 [각 서브넷에 대해 이 테이블에 설명된 포트를 엽니다.](#network-ports-for-ise) 필요한 포트를 사용할 수 없는 경우 ISE가 제대로 작동하지 않습니다.

* IP 제한이 있는 다른 끝점에 액세스해야 하는 ISE 인스턴스가 여러 개 있는 경우 [Azure 방화벽](../firewall/overview.md) 또는 네트워크 [가상 어플라이언스를](../virtual-network/virtual-networks-overview.md#filter-network-traffic) 가상 네트워크에 배포하고 해당 방화벽 또는 네트워크 가상 어플라이언스를 통해 아웃바운드 트래픽을 라우팅합니다. 그런 다음 가상 네트워크의 모든 ISE 인스턴스가 대상 시스템과 통신하는 데 사용할 수 있는 [단일 아웃바운드, 공용, 정적 및 예측 가능한 IP 주소를 설정할](connect-virtual-network-vnet-set-up-single-ip-address.md) 수 있습니다. 이렇게 하면 각 ISE에 대해 해당 대상 시스템에 추가 방화벽 개구부를 설정할 필요가 없습니다.

   > [!NOTE]
   > 시나리오에서 액세스가 필요한 IP 주소 수를 제한해야 하는 경우 단일 ISE에 이 방법을 사용할 수 있습니다. 방화벽 또는 가상 네트워크 어플라이언스에 대한 추가 비용이 시나리오에 적합한지 여부를 고려합니다. [Azure 방화벽 가격 책정에](https://azure.microsoft.com/pricing/details/azure-firewall/)대해 자세히 알아보십시오.

* 제약 조건 없이 새 Azure 가상 네트워크 및 서브넷을 만든 경우 서브넷 간 트래픽을 제어하기 위해 가상 네트워크에서 [NSG(네트워크 보안 그룹)를](../virtual-network/security-overview.md#network-security-groups) 설정할 필요가 없습니다.

* 기존 가상 네트워크에서 [서브넷에서 네트워크 트래픽을 필터링하여 NSG를](../virtual-network/tutorial-filter-network-traffic.md) *선택적으로* 설정할 수 있습니다. 이 경로를 사용하거나 이미 NSG를 사용 중인 경우 NSG가 있거나 NSG를 설정하려는 가상 네트워크의 [이 테이블의 포트를 열어야](#network-ports-for-ise) 합니다.

  > [!NOTE]
  > [NSG 보안 규칙을](../virtual-network/security-overview.md#security-rules)사용하는 경우 TCP 및 UDP 프로토콜을 *모두* 사용해야 합니다. NSG 보안 규칙은 해당 포트에 액세스해야 하는 IP 주소에 대해 열어야 하는 포트를 설명합니다. 이러한 끝점 사이에 있는 방화벽, 라우터 또는 기타 항목도 해당 IP 주소에 액세스할 수 있도록 유지해야 합니다.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>ISE에서 사용하는 네트워크 포트

이 표에서는 ISE가 사용하는 Azure 가상 네트워크의 포트와 해당 포트가 사용되는 위치에 대해 설명합니다. 보안 규칙을 만들 때 복잡성을 줄이기 위해 테이블의 [서비스 태그는](../virtual-network/service-tags-overview.md) 특정 Azure 서비스에 대한 IP 주소 접두사 그룹을 나타냅니다.

> [!IMPORTANT]
> 원본 포트는 임시이므로 모든 규칙에 대해 포트를 `*` 설정해야 합니다. 언급된 경우 내부 ISE 및 외부 [ISE는 ISE 생성시 선택된 끝점을 참조합니다.](connect-virtual-network-vnet-isolated-environment.md#create-environment) 자세한 내용은 [끝점 액세스를](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)참조하십시오. 

| 목적 | Direction | 대상 포트 | 원본 서비스 태그 | 대상 서비스 태그 | 메모 |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| 가상 네트워크 내의 인터서브넷 통신 | 인바운드 및 아웃바운드 | * | ISE의 서브넷이 있는 가상 네트워크의 주소 공간 | ISE의 서브넷이 있는 가상 네트워크의 주소 공간 | 가상 네트워크의 서브넷 *간에* 트래픽이 흐르는 데 필요합니다. <p><p>**중요**: 트래픽이 각 서브넷의 *구성 요소* 간에 흐르려면 각 서브넷 내의 모든 포트를 열어야 합니다. |
| 논리 앱에 대한 통신 | 인바운드 | 443 | 내부 ISE: <br>VirtualNetwork <p><p>외부 ISE: <br>인터넷 <br>(메모 **Notes** 열 참조) | VirtualNetwork | **인터넷** 서비스 태그를 사용하는 대신 논리 앱에서 요청 트리거 또는 웹후크를 호출하는 컴퓨터 또는 서비스의 원본 IP 주소를 지정할 수 있습니다. <p><p>**중요**: 이 포트를 닫거나 차단하면 요청 트리거가 있는 논리 앱에 대한 HTTP 호출이 방지됩니다. |
| 논리 앱 실행 기록 | 인바운드 | 443 | 내부 ISE: <br>VirtualNetwork <p><p>외부 ISE: <br>인터넷 <br>(메모 **Notes** 열 참조) | VirtualNetwork | **인터넷** 서비스 태그를 사용하는 대신 논리 앱의 실행 기록을 보려는 컴퓨터 또는 서비스의 원본 IP 주소를 지정할 수 있습니다. <p><p>**중요:** 이 포트를 닫거나 차단해도 실행 기록이 보이지 않지만 해당 실행 기록의 각 단계에 대한 입력 및 출력을 볼 수 없습니다. |
| Logic Apps 디자이너 - 동적 속성 | 인바운드 | 454 | 로직앱스매니지먼트 | VirtualNetwork | 요청은 해당 리전에 대한 논리 앱 액세스 엔드포인트 [인바운드](../logic-apps/logic-apps-limits-and-config.md#inbound) IP 주소에서 제공됩니다. |
| 커넥터 배포 | 인바운드 | 454 | Azure 커넥터 | VirtualNetwork | 커넥터 배포 및 업데이트에 필요합니다. 이 포트를 닫거나 차단하면 ISE 배포가 실패하고 커넥터 업데이트 또는 수정이 방지됩니다. |
| 네트워크 상태 확인 | 인바운드 | 454 | LogicApps | VirtualNetwork | 요청은 해당 리전의 [인바운드](../logic-apps/logic-apps-limits-and-config.md#inbound) 및 [아웃바운드](../logic-apps/logic-apps-limits-and-config.md#outbound) IP 주소모두에 대한 Logic Apps 액세스 엔드포인트에서 제공됩니다. |
| App Service 관리 종속성 | 인바운드 | 454, 455 | AppServiceManagement | VirtualNetwork | |
| Azure 트래픽 관리자의 통신 | 인바운드 | 내부 ISE: 454 <p><p>외부 ISE: 443 | AzureTrafficManager | VirtualNetwork | |
| API Management - 관리 엔드포인트 | 인바운드 | 3443 | APIManagement | VirtualNetwork | |
| 커넥터 정책 배포 | 인바운드 | 3443 | APIManagement | VirtualNetwork | 커넥터 배포 및 업데이트에 필요합니다. 이 포트를 닫거나 차단하면 ISE 배포가 실패하고 커넥터 업데이트 또는 수정이 방지됩니다. |
| 논리 앱의 통신 | 아웃바운드 | 80, 443 | VirtualNetwork | 목적지에 따라 다름 | 논리 앱이 통신해야 하는 외부 서비스의 끝점입니다. |
| Azure Active Directory | 아웃바운드 | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| 연결 관리 | 아웃바운드 | 443 | VirtualNetwork  | AppService | |
| 진단 로그 및 메트릭 게시 | 아웃바운드 | 443 | VirtualNetwork  | AzureMonitor | |
| Azure Storage 종속성 | 아웃바운드 | 80, 443, 445 | VirtualNetwork | 스토리지 | |
| Azure SQL 종속성 | 아웃바운드 | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | 아웃바운드 | 1886 | VirtualNetwork | AzureMonitor | 상태 상태를 리소스 상태에 게시하는 데 필요합니다. |
| 이벤트 허브에 로그 정책 및 모니터링 에이전트의 종속성 | 아웃바운드 | 5672 | VirtualNetwork | EventHub | |
| 역할 인스턴스 간의 Azure Cache for Redis 인스턴스 액세스 | 인바운드 <br>아웃바운드 | 6379 - 6383 | VirtualNetwork | VirtualNetwork | 또한 ISE가 Redis에 대한 Azure 캐시로 작업하려면 [Redis FAQ에 대한 Azure 캐시에 설명된 이러한 아웃바운드 및 인바운드 포트를](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)열어야 합니다. |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>ISE 만들기

1. Azure [Portal에서](https://portal.azure.com)기본 Azure 검색 상자에서 `integration service environments` 필터로 입력하고 **통합 서비스 환경을**선택합니다.

   !["통합 서비스 환경" 찾기 및 선택](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 통합 **서비스 환경** 창에서 **추가 를**선택합니다.

   !["통합 서비스 환경" 찾기 및 선택](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. 환경에 이러한 세부 정보를 제공한 다음 검토를 선택 **+ 만들기**를 선택합니다.

   ![환경 세부 정보 제공](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **구독** | 예 | <*Azure 구독 이름*> | 환경에 사용할 Azure 구독 |
   | **리소스 그룹** | 예 | <*Azure-리소스 그룹 이름*> | 환경을 만들려는 새 또는 기존 Azure 리소스 그룹 |
   | **통합 서비스 환경 이름** | 예 | <*환경 이름*> | 문자, 숫자, 하이픈 (),`-`밑줄 (),`_`및 마침표 ()만`.`포함 할 수있는 ISE 이름. |
   | **위치** | 예 | <*Azure 데이터 센터-지역*> | 환경을 배포할 Azure 데이터 센터 지역 |
   | **Sku** | 예 | **프리미엄** 또는 **개발자(SLA 없음)** | ISE SKU를 만들고 사용할 수 있습니다. 이러한 SCO 간의 차이점은 [ISE SCO를](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)참조하십시오. <p><p>**중요**: 이 옵션은 ISE 생성 시에만 사용할 수 있으며 나중에 변경할 수 없습니다. |
   | **추가 용량** | 프리미엄: <br>예 <p><p>개발자: <br>해당 없음 | 프리미엄: <br>0에서 10까지 <p><p>개발자: <br>해당 없음 | 이 ISE 리소스에 사용할 추가 처리 단위의 수입니다. 생성 후 용량을 추가하려면 [ISE 용량 추가](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)를 참조하십시오. |
   | **액세스 끝점** | 예 | **내부** 또는 **외부** | ISE에 사용할 액세스 끝점의 유형입니다. 이러한 끝점은 ISE의 논리 앱에 대한 요청 또는 웹후크 트리거가 가상 네트워크 외부에서 전화를 받을 수 있는지 여부를 결정합니다. <p><p>선택 영역은 논리 앱에서 입력 및 출력을 보고 액세스할 수 있는 방식에도 영향을 줍니다. 자세한 내용은 [ISE 끝점 액세스를](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)참조하십시오. <p><p>**중요**: 이 옵션은 ISE 생성 시에만 사용할 수 있으며 나중에 변경할 수 없습니다. |
   | **가상 네트워크** | 예 | <*Azure 가상 네트워크 이름*> | 해당 환경의 논리 앱이 가상 네트워크에 액세스할 수 있도록 환경을 삽입하려는 Azure 가상 네트워크입니다. 네트워크가 없는 경우 [먼저 Azure 가상 네트워크를 만듭니다.](../virtual-network/quick-create-portal.md) <p><p>**중요**: *ISE를* 만들 때만 이 주입을 수행할 수 있습니다. |
   | **서브넷** | 예 | <*서브넷 리소스 목록*> | ISE에는 사용자 환경에서 리소스를 만들고 배포하기 위해 4개의 *빈* 서브넷이 필요합니다. 각 서브넷을 만들려면 [이 테이블 아래의 단계를 따릅니다](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **서브넷 만들기**

   사용자 환경에서 리소스를 만들고 배포하려면 ISE에 서비스에 위임되지 않은 4개의 *빈* 서브넷이 필요합니다. 각 서브넷은 ISE에 사용되는 다른 논리 앱 구성 요소를 지원합니다. 환경을 만든 후에는 이러한 서브넷 주소를 변경할 *수 없습니다.* 각 서브넷은 다음 요구 사항을 충족해야 합니다.

   * 알파벳 문자 또는 밑줄(숫자 없음)으로 시작하는 이름이 있으며 다음 문자를 사용하지 `<` `>` `%`않습니다. `&` `\\` `?` `/`

   * [클래스리스 도메인 간 라우팅(CIDR) 형식과](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 클래스 B 주소 공간을 사용합니다.

   * 각 서브넷에는 `/27` *최소*32개의 주소가 필요하기 때문에 주소 공간에서 최소한 a를 사용합니다. 다음은 그 예입니다.

     * `10.0.0.0/28`주소가 16개에 불과하고<sup>2(32-28)가</sup> 2<sup>4</sup> 또는 16이기 때문에 너무 작습니다.

     * `10.0.0.0/27`2<sup>(32-27)</sup>은 2<sup>5</sup> 또는 32이므로 는 32개의 주소를 포함합니다.

     * `10.0.0.0/24`2<sup>(32-24)</sup>은 2<sup>8</sup> 또는 256이므로 는 256개의 주소를 포함합니다. 그러나 더 많은 주소는 추가 혜택을 제공하지 않습니다.

     주소 계산에 대한 자세한 내용은 [IPv4 CIDR 블록을](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)참조하십시오.

   * [ExpressRoute를](../expressroute/expressroute-introduction.md)사용하는 경우 다음 경로가 있는 [경로 테이블을 만들고](../virtual-network/manage-route-table.md) 해당 테이블을 ISE에서 사용하는 각 서브넷과 연결해야 합니다.

     **이름**: <*경로 이름*><br>
     **주소 접두사**: 0.0.0.0/0<br>
     **다음 홉**: 인터넷

   1. 서브넷 목록에서 **서브넷 구성 관리를** **선택합니다.**

      ![서브넷 구성 관리](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. **서브넷** 창에서 **서브넷을**선택합니다.

      ![4개의 빈 서브넷 추가](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. **서브넷 추가** 창에서 이 정보를 제공합니다.

      * **이름**: 서브넷의 이름
      * **주소 범위(CIDR 블록)**: 가상 네트워크 및 CIDR 형식의 서브넷 범위

      ![서브넷 세부 정보 추가](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. 완료되면 **확인**을 선택합니다.

   1. 세 개 이상의 서브넷에 대해 이 단계를 반복합니다.

      > [!NOTE]
      > 만들려는 서브넷이 유효하지 않으면 Azure 포털에 메시지가 표시되지만 진행률을 차단하지는 않습니다.

   서브넷 을 만드는 것에 대한 자세한 내용은 [가상 네트워크 서브넷 추가를](../virtual-network/virtual-network-manage-subnet.md)참조하십시오.

1. Azure에서 ISE 정보의 유효성을 검사한 후 다음과 같은 **만들기를**선택합니다.

   ![유효성 검사에 성공하면 "만들기"를 선택합니다.](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure는 일반적으로 완료하는 데 2시간 이내에 걸리는 환경 배포를 시작합니다. 경우에 따라 배포에 최대 4시간이 걸릴 수 있습니다. Azure 도구 모음에서 배포 상태를 확인하려면 알림 창을 여는 알림 아이콘을 선택합니다.

   ![배포 상태 확인](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   배포가 성공적으로 완료되면 Azure에 이 알림이 표시됩니다.

   ![배포 성공](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   그렇지 않으면 Azure 포털 지침에 따라 배포 문제를 해결합니다.

   > [!NOTE]
   > 배포가 실패하거나 ISE를 삭제하는 경우 Azure에서 서브넷을 릴리스하기까지 최대 1시간이 걸릴 수 있습니다. 이 지연은 다른 ISE에서 해당 서브넷을 다시 사용하기 전에 기다려야 할 수 있음을 의미합니다.
   >
   > 가상 네트워크를 삭제하는 경우 Azure는 일반적으로 서브넷을 해제하기까지 최대 2시간이 걸리지만 이 작업은 더 오래 걸릴 수 있습니다. 
   > 가상 네트워크를 삭제할 때는 여전히 연결된 리소스가 없는지 확인합니다. 
   > [가상 네트워크 삭제를](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)참조하십시오.

1. 환경을 보려면 배포가 완료된 후 Azure가 자동으로 환경으로 이동하지 않는 경우 **리소스로 이동을** 선택합니다.

1. ISE의 네트워크 상태를 확인하려면 [통합 서비스 환경 관리를](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)참조하십시오.

1. ISE에서 논리 앱 및 기타 아티팩트 만들기를 시작하려면 [통합 서비스 환경에 리소스 추가를](../logic-apps/add-artifacts-integration-service-environment-ise.md)참조하세요.

   > [!IMPORTANT]
   > ISE를 만든 후 사용할 수 있는 관리되는 ISE 커넥터는 논리 앱 디자이너의 커넥터 선택기에 자동으로 나타나지 않습니다. 이러한 ISE 커넥터를 사용하려면 논리 앱 디자이너에 표시되도록 [ISE에 해당 커넥터를](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) 수동으로 추가해야 합니다.

## <a name="next-steps"></a>다음 단계

* [통합 서비스 환경에 리소스 추가](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* [Azure 가상 네트워크에](../virtual-network/virtual-networks-overview.md) 대해 자세히 알아보기
* [Azure 서비스에 대한 가상 네트워크 통합](../virtual-network/virtual-network-for-azure-services.md)에 대해 알아보기
