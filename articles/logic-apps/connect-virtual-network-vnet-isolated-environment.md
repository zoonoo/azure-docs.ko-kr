---
title: ISE(통합 서비스 환경)를 통해 Azure Logic Apps에서 Azure 가상 네트워크에 연결
description: 논리 앱 및 통합 계정이 공용이나 "글로벌" Azure에서 격리된 프라이빗 상태를 유지하면서 Azure VNET(가상 네트워크)에 액세스할 수 있도록 ISE(통합 서비스 환경) 만들기
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 05/20/2019
ms.openlocfilehash: bd1f06c93a75673f86f0c52f78cad8a60f7a1a1e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65961456"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>ISE(통합 서비스 환경)를 사용하여 Azure Logic Apps에서 Azure 가상 네트워크에 연결

논리 앱 및 통합 계정이 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)에 액세스해야 하는 시나리오의 경우 [*ISE*(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)을 만듭니다. ISE에는 전용된 저장소 및 공용 또는 "전역" Logic Apps 서비스를 별도로 보관 되는 기타 리소스를 사용 하는 개인 및 격리 된 환경입니다. 이러한 격리로 인해 다른 Azure 테넌트가 앱 성능에 줄 수 있는 영향이 감소됩니다. ISE를 Azure 가상 네트워크에 *삽입*한 다음, Logic Apps 서비스를 사용자의 가상 네트워크에 배포합니다. 논리 앱 또는 통합 계정을 만들 때 해당 위치로 이 ISE를 선택합니다. 그러면 논리 앱 및 통합 계정은 가상 네트워크에서 VM(가상 머신), 서버, 시스템 및 서비스와 같은 리소스에 직접 액세스할 수 있습니다.

![통합 서비스 환경 선택](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

이 문서에서는 다음 작업을 완료하는 방법을 보여줍니다.

* 트래픽이 해당 가상 네트워크의 서브넷에서 통합 서비스 환경 (ISE)을 통해 이동할 수 있도록 가상 네트워크에서 모든 필요한 포트가 열려 있는지 확인 합니다.

* ISE(통합 서비스 환경)를 만듭니다.

* ISE에서 실행할 수 있는 논리 앱을 만듭니다.

* ISE에서 논리 앱에 대한 통합 계정을 만듭니다.

통합 서비스 환경에 대한 자세한 내용은 [Azure Logic Apps에서 Azure Virtual Network 리소스에 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다.

  > [!IMPORTANT]
  > Logic apps, 기본 제공 트리거, 기본 제공 작업 및 커넥터 가격 책정 ISE 사용에서 실행 되는 사용량 기반 가격 책정 계획에서 다른 계획 합니다. 자세한 내용은 [Logic Apps 가격 책정](../logic-apps/logic-apps-pricing.md)을 참조하세요.

* [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)입니다. 가상 네트워크가 없는 경우 [Azure 가상 네트워크를 만드는](../virtual-network/quick-create-portal.md) 방법을 알아봅니다. 

  * 4 개의 가상 네트워크에 있어야 *빈* 에 ISE에서 리소스를 만들고 배포에 대 한 서브넷입니다. 이러한 서브넷을 미리 만들 수 있습니다 또는 동시에 서브넷을 만들 수 있습니다 하 여 ISE를 만들 때까지 기다릴 수 있습니다. 에 대해 자세히 알아보세요 [서브넷 요구](#create-subnet)합니다. 
  
    > [!NOTE]
    > 사용 하는 경우 [ExpressRoute](../expressroute/expressroute-introduction.md)Microsoft 클라우드 서비스에 개인 연결을 제공 하는, 해야 [경로 테이블을 만드는](../virtual-network/manage-route-table.md) 라우팅하고 여 ISE에 의해 사용 되는 각 서브넷을 사용 하 여 해당 테이블을 링크 다음에는:
    > 
    > **이름을**: <*경로 이름*><br>
    > **주소 접두사**: 0.0.0.0/0<br>
    > **다음 홉**: 인터넷

  * 확인 가상 네트워크 [이러한 포트를 사용할 수 있도록](#ports) ISE에 제대로 작동 하 고 계속 액세스할 수 있도록 합니다.

* Azure 가상 네트워크에 대 한 사용자 지정 DNS 서버를 사용 하려는 경우 [다음 단계를 수행 하 여 해당 서버를 설정](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) ISE에 가상 네트워크에 배포 하기 전에 합니다. 설정하지 않으면 DNS 서버를 변경할 때마다 ISE도 다시 시작해야 합니다. 이 기능은 ISE 공개 미리 보기에서 사용할 수 있습니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

<a name="ports"></a>

## <a name="check-network-ports"></a>네트워크 포트를 확인 합니다.

가상 네트워크를 사용 하 여 통합 서비스 환경 (ISE)를 사용 하는 경우 설치 프로그램의 일반적인 문제는 하나 이상의 차단 된 포트로 것입니다. 커넥터에 ISE와 대상 시스템 간의 연결을 만드는 데 사용 하는 고유한 포트 요구 사항이 있을 수도 있습니다. 예를 들어 FTP 커넥터를 사용하여 FTP 시스템과 통신하는 경우 명령 전송을 위한 포트 21과 같이 FTP 시스템에서 사용하는 포트가 사용 가능한지 확인합니다.

ISE 배포할 가상 네트워크의 서브넷 간 트래픽을 제어 하려면 설정할 수 있습니다 [네트워크 보안 그룹](../virtual-network/security-overview.md) 하 여 [서브넷 간에 네트워크 트래픽을 필터링](../virtual-network/tutorial-filter-network-traffic.md)합니다. 그러나 ISE에 특정 포트를 열어 네트워크 보안 그룹을 사용 하는 가상 네트워크에 있어야 합니다. 이런 방식으로 ISE에 액세스할 수 있는 상태를 유지 및 여 ISE에 액세스 권한을 잃지 않도록 제대로 작동할 수 있습니다. 그렇지 않으면 모든 필요한 포트를 사용할 수 없는 경우에 ISE에 작동이 중지 됩니다.

이 표에서는 ISE에서 사용하는 가상 네트워크의 포트 및 해당 포트가 사용되는 위치를 설명합니다. 합니다 [Resource Manager 서비스 태그](../virtual-network/security-overview.md#service-tags) 보안 규칙을 만들 때 복잡성을 최소화 하는 데 도움이 되는 IP 주소 접두사의 그룹을 나타냅니다.

> [!IMPORTANT]
> 서브넷 내에서 내부 통신용 ISE 이러한 서브넷 내에서 모든 포트를 열어야에 필요 합니다.

| 목적 | 방향 | 포트 | 원본 서비스 태그 | 대상 서비스 태그 | 메모 |
|---------|-----------|-------|--------------------|-------------------------|-------|
| Azure Logic Apps에서 받는 통신 | 아웃바운드 | 80 및 443 | VirtualNetwork | 인터넷 | Logic Apps 서비스 통신 하는 외부 서비스에 종속 된 포트 |
| Azure Active Directory | 아웃바운드 | 80 및 443 | VirtualNetwork | AzureActiveDirectory | |
| Azure Storage 종속성 | 아웃바운드 | 80 및 443 | VirtualNetwork | Storage | |
| Intersubnet 통신 | 인바운드 및 아웃바운드 | 80 및 443 | VirtualNetwork | VirtualNetwork | 서브넷 간 통신 |
| Azure Logic Apps로 보내는 통신 | 인바운드 | 443 | 인터넷  | VirtualNetwork | 모든 요청 트리거 또는 논리 앱에 존재 하는 웹 후크를 호출 하는 서비스를 컴퓨터에 대 한 IP 주소입니다. 닫거나이 포트를 차단 하는 요청 트리거가 있는 논리 앱에 대 한 HTTP 호출을 방지 합니다.  |
| 논리 앱 실행 기록 | 인바운드 | 443 | 인터넷  | VirtualNetwork | 논리 앱을 볼 수 있는 컴퓨터에 대 한 IP 주소의 실행 기록 합니다. 닫거나이 포트를 차단 하지 않도록 있지만 있습니다 실행된 기록 보기, 입력을 확인할 수 없습니다 하 고 출력 하는 각 단계에 대 한 실행 기록 합니다. |
| 연결 관리 | 아웃바운드 | 443 | VirtualNetwork  | 인터넷 | |
| 진단 로그 및 메트릭 게시 | 아웃바운드 | 443 | VirtualNetwork  | AzureMonitor | |
| Azure Traffic Manager에서 통신 | 인바운드 | 443 | AzureTrafficManager | VirtualNetwork | |
| Logic Apps 디자이너 - 동적 속성 | 인바운드 | 454 | 인터넷  | VirtualNetwork | Logic Apps에서 요청이 들어 올 [끝점에 액세스 해당 지역의 IP 주소를 인바운드](../logic-apps/logic-apps-limits-and-config.md#inbound)합니다. |
| App Service 관리 종속성 | 인바운드 | 454 및 455 | AppServiceManagement | VirtualNetwork | |
| 커넥터 배포 | 인바운드 | 454 & 3443 | 인터넷  | VirtualNetwork | 배포 하 고 커넥터 업데이트 필요 합니다. 닫거나이 포트를 차단한 ISE 배포 실패 시키고 커넥터 업데이트 또는 수정을 방지 합니다. |
| Azure SQL 종속성 | 아웃바운드 | 1433 | VirtualNetwork | SQL |
| Azure Resource Health | 아웃바운드 | 1886 | VirtualNetwork | 인터넷 | 리소스 상태에 상태를 게시 하기 위한 |
| API Management - 관리 엔드포인트 | 인바운드 | 3443 | APIManagement  | VirtualNetwork | |
| 이벤트 허브에 로그 정책 및 모니터링 에이전트의 종속성 | 아웃바운드 | 5672 | VirtualNetwork  | EventHub | |
| 역할 인스턴스 간의 Azure Cache for Redis 인스턴스 액세스 | 인바운드 <br>아웃바운드 | 6379-6383 | VirtualNetwork  | VirtualNetwork | 또한 Redis 용 Azure 캐시를 사용 하는 ISE를 열어야 이러한 [Redis FAQ에 대 한 Azure 캐시에서 설명 하는 아웃 바운드 및 인바운드 포트](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)합니다. |
| Azure Load Balancer | 인바운드 | * | AzureLoadBalancer | VirtualNetwork |  |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>ISE 만들기

ISE(통합 서비스 환경)를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)의 기본 Azure 메뉴에서 **리소스 만들기**를 선택합니다.
검색 상자에서 필터로 "통합 서비스 환경"을 입력합니다.

   ![새 리소스 만들기](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 통합 서비스 환경 만들기 창에서 선택 **만들기**합니다.

   !["만들기" 선택](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. 예를 들어 사용자 환경에 대한 이러한 세부 정보를 제공한 다음, **검토 + 만들기**를 선택합니다.

   ![환경 세부 정보 제공](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | 자산 | 필수 | Value | 설명 |
   |----------|----------|-------|-------------|
   | **구독** | 예 | <*Azure-subscription-name*> | 환경에 사용할 Azure 구독 |
   | **리소스 그룹** | 예 | <*Azure-resource-group-name*> | 환경을 만들려는 Azure 리소스 그룹 |
   | **통합 서비스 환경 이름** | 예 | <*environment-name*> | 환경에 부여할 이름 |
   | **위치**: | 예 | <*Azure-datacenter-region*> | 환경을 배포할 Azure 데이터 센터 지역 |
   | **추가 용량** | 예. | 0 ~ 10 | 이 ISE 리소스에 사용할 추가 처리 단위의 수입니다. 참조를 만든 후 용량을 추가 [ISE 추가 용량](#add-capacity)합니다. |
   | **가상 네트워크** | 예 | <*Azure-virtual-network-name*> | 해당 환경의 논리 앱이 가상 네트워크에 액세스할 수 있도록 환경을 삽입하려는 Azure 가상 네트워크입니다. 네트워크에 없는 경우 [Azure virtual network를 먼저 만들어야](../virtual-network/quick-create-portal.md)합니다. <p>**중요**: ISE를 만들 때*만* 이 삽입을 수행할 수 있습니다. |
   | **서브넷** | 예 | <*subnet-resource-list*> | ISE에는 사용자 환경에서 리소스를 만들기 위해 4개의 *빈* 서브넷이 필요합니다. 각 서브넷을 만들려면 [이 테이블 아래의 단계를 따릅니다](#create-subnet).  |
   |||||

   <a name="create-subnet"></a>

   **서브넷 만들기**

   ISE에 필요한 네 가지 환경에서 리소스를 만들려면 *빈* 서브넷의 모든 서비스에 위임 되지 않습니다. 
   있습니다 *없습니다* 환경을 만든 후 이러한 서브넷 주소를 변경 합니다. 각 서브넷은 이러한 조건을 충족해야 합니다.

   * 이름이 없습니다. 이러한 문자는 영문자 또는 밑줄을 사용 하 여 시작 하는: `<`, `>`를 `%`, `&`합니다 `\\`, `?`, `/`

   * 사용 하는 [Classless Inter-domain 라우팅 (CIDR) 형식](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 및 클래스 B 주소 공간입니다.

   * 적어도 사용 하 여 `/27` 주소에서 각 서브넷 주소를 32 개 있어야 하므로 공간 합니다 *최소*합니다. 예를 들면 다음과 같습니다.

     * `10.0.0.0/27` 때문에 32 주소가 2<sup>(32-27)</sup> 는 2<sup>5</sup> 또는 32입니다.

     * `10.0.0.0/24` 때문에 256 주소가 2<sup>(32-24)</sup> 는 2<sup>8</sup> 256 또는 합니다.

     * `10.0.0.0/28` 에 16 개의 주소가 있고 너무 작은 때문에 2<sup>(32-28)</sup> 는 2<sup>4</sup> 또는 16.

     주소를 계산 하는 방법에 대 한 자세한 내용은 참조 하세요 [IPv4 CIDR 블록](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)합니다.

   * 사용 하는 경우 [ExpressRoute](../expressroute/expressroute-introduction.md), 해야 [경로 테이블을 만드는](../virtual-network/manage-route-table.md) 라우팅하고 여 ISE에 의해 사용 되는 각 서브넷을 사용 하 여 해당 테이블을 링크 다음에는:

     **이름을**: <*경로 이름*><br>
     **주소 접두사**: 0.0.0.0/0<br>
     **다음 홉**: 인터넷

   1. **서브넷** 목록에서 **서브넷 구성 관리**를 선택합니다.

      ![서브넷 구성 관리](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. **서브넷** 창에서 **서브넷**을 선택합니다.

      ![서브넷 추가](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. **서브넷 추가** 창에서 이 정보를 제공합니다.

      * **이름**: 서브넷의 이름
      * **주소 범위(CIDR 블록)** : 가상 네트워크 및 CIDR 형식의 서브넷 범위

      ![서브넷 세부 정보 추가](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. 작업을 완료하면 **확인**을 선택합니다.

   1. 세 개 이상의 서브넷에 대해 이 단계를 반복합니다.

      > [!NOTE]
      > 만들려고 시도 하면 서브넷 유효 하지 않으면 Azure 포털 메시지를 표시 하지만 진행률을 차단 하지 않습니다.

1. 예를 들어 Azure에서 성공적으로 ISE 정보의 유효성 검사를 완료하면 **만들기**를 선택합니다.

   ![유효성 검사에 성공하면 "만들기" 선택](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure는 환경 배포를 시작하지만 이 프로세스는 완료하기까지 최대 2시간이 걸릴 *수* 있습니다. 
   배포 상태를 확인하려면 Azure 도구 모음에서 알림 창을 여는 알림 아이콘을 선택합니다.

   ![배포 상태 확인](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   배포가 성공적으로 완료되면 Azure에 이 알림이 표시됩니다.

   ![배포 성공](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   그렇지 않으면, 배포 문제 해결에 대 한 Azure portal 지침을 따릅니다.

   > [!NOTE]
   > 배포가 실패 하거나 프로그램 ISE를 삭제 하는 경우 Azure가 서브넷을 해제 하기 전에 한 시간 걸릴 수 있습니다. 이 지연 다른 ISE에서 해당 서브넷을 다시 사용 하기 전에 대기 해야 하는 수단을 의미 합니다. 
   >
   > 가상 네트워크를 삭제 하면 Azure 일반적으로 서브넷, 등록을 해제 하기 전에 최대 2 시간이 걸리지만이 작업은 오래 걸릴 수 있습니다. 
   > 가상 네트워크를 삭제 하는 경우 리소스가 아직 연결 되어 있는지 확인 합니다. 참조 [가상 네트워크 삭제](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)합니다.

1. 배포가 완료된 후에 Azure가 환경으로 자동으로 이동하지 않는 경우 환경을 보려면 **리소스로 이동**을 선택합니다.  

서브넷을 만드는 방법에 대 한 자세한 내용은 참조 하세요. [가상 네트워크 서브넷을 추가](../virtual-network/virtual-network-manage-subnet.md)합니다.

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>논리 앱 만들기 - ISE

통합 서비스 환경 (ISE)에서 실행 되는 논리 앱을 만드는 [일반적인 방법으로 논리 앱을 만들](../logic-apps/quickstart-create-first-logic-app-workflow.md) 설정 하는 경우를 제외 하 고는 **위치** 속성에 ISE에서 선택 합니다  **통합 서비스 환경** 섹션 예를 들어:

  ![통합 서비스 환경 선택](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

트리거 및 동작 작업 하는 방법을 해당 하는 레이블이 지정 된 전역 Logic Apps 서비스에 비해 ISE를 사용 하는 경우 참조 하는 방법의 차이 [와 격리 된 ISE 개요에서 전역](connect-virtual-network-vnet-isolated-environment-overview.md#difference)합니다.

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>통합 계정 만들기 - ISE

통합 서비스 환경 (ISE)에 logic apps와 통합 계정을 사용 하려는 경우 해당 통합 계정을 사용 해야 합니다 *동일한 환경* logic apps로 합니다. ISE의 논리 앱은 같은 ISE에 있는 통합 계정만 참조할 수 있습니다.

ISE를 사용 하는 통합 계정을 만드는 [일반적인 방법으로 통합 계정 만들기](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 설정 하는 경우를 제외 하 고는 **위치** 속성에 ISE에서 선택 합니다 **통합 환경 서비스** 섹션 예를 들어:

![통합 서비스 환경 선택](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>ISE 용량 추가

ISE 기본 단위는 용량을 수정 하므로 더 많은 처리량을 할 경우 배율 단위를 더 추가할 수 있습니다. 성능 메트릭에 따라 또는 추가 처리 단위 수에 따라 자동 크기 조정을 수 있습니다. 메트릭을 기반으로 하는 자동 크기 조정 하면 다양 한 조건에서 선택 하 고 해당 조건을 충족 하는 것에 대 한 임계값 조건을 지정할 수 있습니다.

1. Azure portal에서 사용자 ISE를 찾습니다.

1. ISE의 주 메뉴에 ISE에 대 한 사용 현황 및 성능 메트릭을 검토 하려면 **개요**합니다.

   ![ISE에 대 한 사용 현황 보기](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. 아래에 있는 자동 크기 조정을 설정 하려면 **설정**를 선택 **확장**합니다. 에 **구성** 탭에서 **자동 크기 조정 사용**합니다.

   ![자동 크기 조정 설정](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. 에 대 한 **자동 크기 조정 설정 이름**, 설정에 대 한 이름을 제공 합니다.

1. 에 **기본** 섹션을 선택 **메트릭에 따라 크기 조정** 또는 **특정 인스턴스 수로 크기 조정**합니다.

   * 인스턴스 기반을 선택 하면 까지입니다 처리 단위 수가 0과 10 사이의 입력 합니다.

   * 메트릭 기반을 선택 하면 다음이 단계를 수행 합니다.

     1. 에 **규칙** 섹션을 선택 **규칙을 추가**합니다.

     1. 에 **크기 조정 규칙** 창, 조건 및 작업 규칙이 트리거되면을 설정 합니다.

     1. 완료 되 면 선택 **추가**합니다.

1. 완료 되 면 자동 크기 조정 설정을 사용 하 여, 변경 내용을 저장 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)에 대해 자세히 알아보기
* [Azure 서비스에 대한 가상 네트워크 통합](../virtual-network/virtual-network-for-azure-services.md)에 대해 알아보기
