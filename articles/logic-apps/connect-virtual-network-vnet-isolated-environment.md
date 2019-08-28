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
ms.date: 07/26/2019
ms.openlocfilehash: 7a3cdab8e05a873e67788a72350d1bf3fde3cd18
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018197"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>ISE(통합 서비스 환경)를 사용하여 Azure Logic Apps에서 Azure 가상 네트워크에 연결

논리 앱 및 통합 계정이 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)에 액세스해야 하는 시나리오의 경우 [*ISE*(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)을 만듭니다. ISE는 공용 또는 "글로벌" Logic Apps 서비스와 별도로 유지 되는 전용 저장소 및 기타 리소스를 사용 하는 격리 된 전용 환경입니다. 이러한 격리로 인해 다른 Azure 테넌트가 앱 성능에 줄 수 있는 영향이 감소됩니다.

ISE를 만들 때 Azure는이 ISE를 Azure 가상 네트워크에 *삽입* 하 여 가상 네트워크에 Logic Apps 서비스를 배포 합니다. 논리 앱 또는 통합 계정을 만들 때 ISE를 해당 위치로 선택 합니다. 그러면 논리 앱 및 통합 계정은 가상 네트워크에서 VM(가상 머신), 서버, 시스템 및 서비스와 같은 리소스에 직접 액세스할 수 있습니다.

![통합 서비스 환경 선택](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> 논리 앱과 통합 계정이 ISE에서 함께 작동 하려면 둘 다 해당 위치와 *동일한 ise* 를 사용 해야 합니다.

ISE는 실행 지속 시간, 저장소 보존, 처리량, HTTP 요청 및 응답 시간 제한, 메시지 크기 및 사용자 지정 커넥터 요청에 대 한 제한을 증가 시켰습니다. 자세한 내용은 [Azure Logic Apps에 대 한 제한 및 구성](logic-apps-limits-and-config.md)을 참조 하세요. ISEs에 대해 자세히 알아보려면 [Azure Logic Apps에서 Azure Virtual Network 리소스에 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)를 참조 하세요.

이 문서에서는 다음 작업을 완료하는 방법을 보여줍니다.

* 가상 네트워크의 서브넷에서 ISE를 통해 트래픽을 이동할 수 있도록 가상 네트워크에서 필요한 포트가 열려 있는지 확인 합니다.

* ISE를 만듭니다.

* ISE에 추가 용량을 추가 합니다.

> [!IMPORTANT]
> ISE에서 실행 되는 논리 앱, 기본 제공 트리거, 기본 제공 작업 및 커넥터는 소비 기반 요금제와 다른 가격 책정 계획을 사용 합니다. ISEs에 대 한 가격 책정 및 청구의 작동 방식에 대 한 자세한 [Logic Apps 내용은 가격 책정 모델](../logic-apps/logic-apps-pricing.md#fixed-pricing)을 참조 하세요. 가격 책정 요금은 [Logic Apps 가격 책정](../logic-apps/logic-apps-pricing.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독. Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)입니다. 가상 네트워크가 없는 경우 [Azure 가상 네트워크를 만드는](../virtual-network/quick-create-portal.md) 방법을 알아봅니다.

  * 가상 네트워크에는 ISE에서 리소스를 만들고 배포 하기 위한 4 개의 *빈* 서브넷이 있어야 합니다. 이러한 서브넷을 미리 만들 수도 있고, 한 번에 서브넷을 만들 수 있는 ISE를 만들 때까지 기다릴 수도 있습니다. [서브넷 요구 사항](#create-subnet)에 대해 자세히 알아보세요.
  
    > [!NOTE]
    > Microsoft 클라우드 서비스에 대 한 개인 연결을 제공 하는 [express](../expressroute/expressroute-introduction.md)경로를 사용 하는 경우 다음 경로를 포함 하는 [경로 테이블을 만들고](../virtual-network/manage-route-table.md) 해당 테이블을 ISE에서 사용 하는 각 서브넷과 연결 해야 합니다.
    > 
    > **이름**: <*경로 이름*><br>
    > **주소 접두사**: 0.0.0.0/0<br>
    > **다음 홉**: 인터넷

  * 가상 네트워크가 [이러한 포트를 사용할 수](#ports) 있도록 하 여 ISE가 제대로 작동 하 고 액세스할 수 있도록 해야 합니다.

* Azure 가상 네트워크에 사용자 지정 DNS 서버를 사용 하려면 가상 네트워크에 ISE를 배포 하기 전에 다음 단계를 수행 하 [여 해당 서버를 설정](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) 합니다. 설정하지 않으면 DNS 서버를 변경할 때마다 ISE도 다시 시작해야 합니다. 이 기능은 ISE 공개 미리 보기에서 사용할 수 있습니다.

<a name="ports"></a>

## <a name="check-network-ports"></a>네트워크 포트 확인

기존 가상 네트워크에서 ISE를 사용 하는 경우 일반적인 설치 문제는 하나 이상의 차단 된 포트를 포함 하는 것입니다. ISE와 대상 시스템 간에 연결을 만드는 데 사용 하는 커넥터에도 자체 포트 요구 사항이 있을 수 있습니다. 예를 들어 FTP 커넥터를 사용하여 FTP 시스템과 통신하는 경우 명령 전송을 위한 포트 21과 같이 FTP 시스템에서 사용하는 포트가 사용 가능한지 확인합니다.

제약 조건 없이 새 가상 네트워크 및 서브넷을 만든 경우 서브넷 간 트래픽을 제어할 수 있도록 가상 네트워크에서 [NSGs (네트워크 보안 그룹)](../virtual-network/security-overview.md) 를 설정할 필요가 없습니다. 기존 가상 네트워크의 경우 [서브넷 간 네트워크 트래픽을 필터링](../virtual-network/tutorial-filter-network-traffic.md)하 여 *필요* 에 따라 nsgs를 설정할 수 있습니다. 이 경로를 선택 하는 경우 다음 표에 설명 된 대로 NSGs가 있는 가상 네트워크에서 ISE가 특정 포트를 열어 두어야 합니다. 따라서 가상 네트워크에 있는 기존 NSGs 또는 방화벽의 경우 이러한 포트를 열어야 합니다. 이런 방식으로 ISE에 액세스할 수 있으며 ise에 대 한 액세스 권한이 손실 되지 않도록 제대로 작동할 수 있습니다. 그렇지 않으면 필요한 포트를 사용할 수 없는 경우 ISE 작동이 중지 됩니다.

> [!IMPORTANT]
> 서브넷 내부 통신의 경우 ISE를 사용 하려면 해당 서브넷 내에서 모든 포트를 열어야 합니다.

이 표에서는 ISE에서 사용 하는 가상 네트워크의 포트와 이러한 포트가 사용 되는 위치에 대해 설명 합니다. [리소스 관리자 서비스 태그](../virtual-network/security-overview.md#service-tags) 는 보안 규칙을 만들 때 복잡성을 최소화 하는 데 도움이 되는 IP 주소 접두사 그룹을 나타냅니다.

> [!NOTE]
> 원본 포트는 삭제 되기 때문에 모든 규칙 `*` 에 대해로 설정 합니다.

| 용도 | Direction | 대상 포트 | 원본 서비스 태그 | 대상 서비스 태그 | 참고 |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Azure Logic Apps에서 받는 통신 | 아웃바운드 | 80, 443 | VirtualNetwork | 인터넷 | 포트는 Logic Apps 서비스가 통신 하는 외부 서비스에 의존 합니다. |
| Azure Active Directory | 아웃바운드 | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| Azure Storage 종속성 | 아웃바운드 | 80, 443 | VirtualNetwork | 저장 공간 | |
| 상호 서브넷 통신 | 인바운드 및 아웃바운드 | 80, 443 | VirtualNetwork | VirtualNetwork | 서브넷 간 통신 |
| Azure Logic Apps로 보내는 통신 | 인바운드 | 443 | 내부 액세스 끝점: <br>VirtualNetwork <p><p>외부 액세스 끝점: <br>인터넷 <p><p>**참고**: 이러한 끝점은 [ISE 생성 시 선택](#create-environment)된 끝점 설정을 참조 합니다. 자세한 내용은 [끝점 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)를 참조 하세요. | VirtualNetwork | 논리 앱에 있는 요청 트리거 또는 webhook를 호출 하는 컴퓨터 또는 서비스의 IP 주소입니다. 이 포트를 닫거나 차단 하면 요청 트리거를 사용 하 여 논리 앱에 대 한 HTTP 호출을 수행할 수 없습니다. |
| 논리 앱 실행 기록 | 인바운드 | 443 | 내부 액세스 끝점: <br>VirtualNetwork <p><p>외부 액세스 끝점: <br>인터넷 <p><p>**참고**: 이러한 끝점은 [ISE 생성 시 선택](#create-environment)된 끝점 설정을 참조 합니다. 자세한 내용은 [끝점 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)를 참조 하세요. | VirtualNetwork | 논리 앱의 실행 기록을 확인 하는 컴퓨터의 IP 주소입니다. 이 포트를 닫거나 차단 해도 실행 기록이 표시 되지 않지만 해당 실행 기록의 각 단계에 대 한 입력 및 출력은 볼 수 없습니다. |
| 연결 관리 | 아웃바운드 | 443 | VirtualNetwork  | 인터넷 | |
| 진단 로그 및 메트릭 게시 | 아웃바운드 | 443 | VirtualNetwork  | AzureMonitor | |
| Azure Traffic Manager에서 통신 | 인바운드 | 443 | AzureTrafficManager | VirtualNetwork | |
| Logic Apps 디자이너 - 동적 속성 | 인바운드 | 454 | 인터넷 | VirtualNetwork | 요청은 해당 [지역의 끝점 인바운드 IP 주소에 대 한 액세스](../logic-apps/logic-apps-limits-and-config.md#inbound)Logic Apps에서 제공 됩니다. |
| App Service 관리 종속성 | 인바운드 | 454, 455 | AppServiceManagement | VirtualNetwork | |
| 커넥터 배포 | 인바운드 | 454, 3443 | 인터넷 | VirtualNetwork | 커넥터를 배포 및 업데이트 하는 데 필요 합니다. 이 포트를 닫거나 차단 하면 ISE 배포가 실패 하 고 커넥터 업데이트 또는 수정이 방지 됩니다. |
| Azure SQL 종속성 | 아웃바운드 | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | 아웃바운드 | 1886 | VirtualNetwork | AzureMonitor | Resource Health에 상태를 게시 하는 경우 |
| API Management - 관리 엔드포인트 | 인바운드 | 3443 | APIManagement | VirtualNetwork | |
| 이벤트 허브에 로그 정책 및 모니터링 에이전트의 종속성 | 아웃바운드 | 5672 | VirtualNetwork | EventHub | |
| 역할 인스턴스 간의 Azure Cache for Redis 인스턴스 액세스 | 인바운드 <br>아웃바운드 | 6379-6383 | VirtualNetwork | VirtualNetwork | 또한 ISE가 Redis 용 Azure Cache를 사용 하도록 하려면 [Azure cache For REDIS FAQ에 설명 된 이러한 아웃 바운드 및 인바운드 포트](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)를 열어야 합니다. |
| Azure Load Balancer | 인바운드 | * | AzureLoadBalancer | VirtualNetwork | |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>ISE 만들기

ISE(통합 서비스 환경)를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)의 기본 Azure 메뉴에서 **리소스 만들기**를 선택합니다.
검색 상자에서 필터로 "통합 서비스 환경"을 입력합니다.

   ![새 리소스 만들기](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 통합 서비스 환경 만들기 창에서 **만들기**를 선택 합니다.

   !["만들기" 선택](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. 예를 들어 사용자 환경에 대한 이러한 세부 정보를 제공한 다음, **검토 + 만들기**를 선택합니다.

   ![환경 세부 정보 제공](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | 속성 | 필수 | Value | Description |
   |----------|----------|-------|-------------|
   | **구독** | 예 | <*Azure-subscription-name*> | 환경에 사용할 Azure 구독 |
   | **리소스 그룹** | 예 | <*Azure-resource-group-name*> | 환경을 만들려는 Azure 리소스 그룹 |
   | **Integration service environment 이름** | 예 | <*environment-name*> | 문자, 숫자, 하이픈 (`-`), 밑줄 (`_`) 및 마침표 (`.`)만 포함할 수 있는 ISE 이름입니다. |
   | **위치** | 예 | <*Azure-datacenter-region*> | 환경을 배포할 Azure 데이터 센터 지역 |
   | **SKU** | 예 | **프리미엄** 또는 **개발자 (SLA 없음)** | 만들고 사용할 ISE SKU입니다. 이러한 Sku 간의 차이점은 [ISE sku](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)를 참조 하세요. <p><p>**중요**: 이 옵션은 ISE를 만들 때만 사용할 수 있으며 나중에 변경할 수 없습니다. |
   | **추가 용량** | 프리미엄: <br>예 <p><p>Developer: <br>해당 사항 없음 | 프리미엄: <br>0 ~ 10 <p><p>Developer: <br>해당 사항 없음 | 이 ISE 리소스에 사용할 추가 처리 단위의 수입니다. 만든 후 용량을 추가 하려면 [ISE 용량 추가](#add-capacity)를 참조 하세요. |
   | **액세스 끝점** | 예 | **내부** 또는 **외부** | Ise에 사용할 액세스 끝점의 유형입니다. ise에서 논리 앱에 대 한 요청 또는 webhook 트리거는 가상 네트워크 외부에서 호출을 받을 수 있는지 여부를 결정 합니다. 끝점 형식은 논리 앱 실행 기록의 입력 및 출력에 대 한 액세스에도 영향을 줍니다. 자세한 내용은 [끝점 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)를 참조 하세요. <p><p>**중요**: 이 옵션은 ISE를 만들 때만 사용할 수 있으며 나중에 변경할 수 없습니다. |
   | **가상 네트워크** | 예 | <*Azure-virtual-network-name*> | 해당 환경의 논리 앱이 가상 네트워크에 액세스할 수 있도록 환경을 삽입하려는 Azure 가상 네트워크입니다. 네트워크가 없는 경우 [먼저 Azure virtual network를 만듭니다](../virtual-network/quick-create-portal.md). <p>**중요**: ISE를 만들 때*만* 이 삽입을 수행할 수 있습니다. |
   | **서브넷** | 예 | <*subnet-resource-list*> | ISE에는 사용자 환경에서 리소스를 만들고 배포 하기 위해 네 개의 *빈* 서브넷이 필요 합니다. 각 서브넷을 만들려면 [이 테이블 아래의 단계를 따릅니다](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **서브넷 만들기**

   사용자 환경에서 리소스를 만들고 배포 하기 위해 ISE에는 어떤 서비스에도 위임 되지 않은 *빈* 서브넷 4 개가 필요 합니다. 환경을 만든 후에는 이러한 서브넷 주소를 변경할 *수 없습니다* . 각 서브넷은 이러한 조건을 충족해야 합니다.

   * 에는 알파벳 문자 또는 밑줄로 시작 `<`하 고 `%`, `\\` `>` `&`,,,, ,등의문자가없습니다.`?``/`

   * 는 클래스 없는 [CIDR (도메인 간 라우팅) 형식](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 및 클래스 B 주소 공간을 사용 합니다.

   * 각 서브넷에 최소한 `/27` 32 *이상의* 주소가 있어야 하므로 주소 공간에 적어도를 사용 합니다. 예를 들어:

     * `10.0.0.0/27`2<sup>(32-27)</sup> 가 2<sup>5</sup> 또는 32 이기 때문에 32 주소가 있습니다.

     * `10.0.0.0/24`는 2<sup>(32-24)</sup> 가 2<sup>8</sup> 또는 256 이기 때문에 256 주소를 가집니다.

     * `10.0.0.0/28`에는 주소가 16 개만 있고 2<sup>(32-28)</sup> 가 2<sup>4</sup> 또는 16 이므로 너무 작습니다.

     주소 계산에 대해 자세히 알아보려면 [IPV4 CIDR 블록](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)을 참조 하세요.

   * [Express](../expressroute/expressroute-introduction.md)경로를 사용 하는 경우 다음 경로를 포함 하는 [경로 테이블을 만들고](../virtual-network/manage-route-table.md) 해당 테이블을 ISE에서 사용 하는 각 서브넷과 연결 해야 합니다.

     **이름**: <*경로 이름*><br>
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
      > 만들려고 하는 서브넷이 유효 하지 않은 경우 Azure Portal 메시지를 표시 하지만 진행 상황을 차단 하지는 않습니다.

   서브넷을 만드는 방법에 대 한 자세한 내용은 [가상 네트워크 서브넷 추가](../virtual-network/virtual-network-manage-subnet.md)를 참조 하세요.

1. 예를 들어 Azure에서 성공적으로 ISE 정보의 유효성 검사를 완료하면 **만들기**를 선택합니다.

   ![유효성 검사에 성공하면 "만들기" 선택](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure는 환경 배포를 시작하지만 이 프로세스는 완료하기까지 최대 2시간이 걸릴 *수* 있습니다. 배포 상태를 확인하려면 Azure 도구 모음에서 알림 창을 여는 알림 아이콘을 선택합니다.

   ![배포 상태 확인](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   배포가 성공적으로 완료되면 Azure에 이 알림이 표시됩니다.

   ![배포 성공](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   그렇지 않으면 배포 문제 해결에 대 한 Azure Portal 지침을 따르세요.

   > [!NOTE]
   > 배포가 실패 하거나 ISE를 삭제 하는 경우 Azure는 서브넷을 해제 하기 전까지 최대 한 시간이 걸릴 수 있습니다. 이 지연은 다른 ISE에서 해당 서브넷을 다시 사용 하기 전에 기다려야 한다는 것을 의미 합니다.
   >
   > 가상 네트워크를 삭제 하는 경우 Azure는 일반적으로 서브넷을 해제 하기 전까지 최대 2 시간이 걸리지만이 작업은 더 오래 걸릴 수 있습니다. 
   > 가상 네트워크를 삭제 하는 경우 아직 연결 된 리소스가 없는지 확인 합니다. 
   > [가상 네트워크 삭제](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)를 참조 하세요.

1. 배포가 완료된 후에 Azure가 환경으로 자동으로 이동하지 않는 경우 환경을 보려면 **리소스로 이동**을 선택합니다.

1. ISE에 대 한 네트워크 상태를 확인 하려면 [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)를 참조 하세요.

1. ISE에서 논리 앱 및 기타 아티팩트 만들기를 시작 하려면 [integration service environment에 아티팩트 추가](../logic-apps/add-artifacts-integration-service-environment-ise.md)를 참조 하세요.

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>ISE 용량 추가

프리미엄 ISE 기본 단위는 고정 용량을 가지 므로 더 많은 처리량이 필요한 경우 생성 중 또는 나중에 확장 단위를 추가할 수 있습니다. 성능 메트릭 또는 다양 한 추가 처리 단위에 따라 자동으로 크기를 조정할 수 있습니다. 메트릭에 따라 자동 크기 조정을 선택 하는 경우 다양 한 조건에서 선택 하 고 해당 조건에 맞는 임계값 조건을 지정할 수 있습니다. 개발자 SKU에는 배율 단위를 추가 하는 기능이 포함 되어 있지 않습니다.

1. Azure Portal에서 ISE를 찾습니다.

1. Ise의 주 메뉴에서 **개요**를 선택 하 여 ise에 대 한 사용 현황 및 성능 메트릭을 검토 합니다.

   ![ISE에 대 한 사용 현황 보기](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. 자동 크기 조정을 설정 하려면 **설정**에서 **Scale out**을 선택 합니다. **구성** 탭에서 **자동 크기 조정 사용**을 선택 합니다.

   ![자동 크기 조정 설정](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. **자동 크기 조정 설정 이름**에 설정의 이름을 입력 합니다.

1. **기본** 섹션에서 **메트릭 기반 크기 조정** 또는 **특정 인스턴스 수에 대 한 크기**조정 중 하나를 선택 합니다.

   * 인스턴스 기반을 선택 하는 경우 0부터 10 까지의 처리 단위 수를 입력 합니다.

   * 메트릭 기반을 선택 하는 경우 다음 단계를 수행 합니다.

     1. **규칙** 섹션에서 **규칙 추가**를 선택 합니다.

     1. 규칙 **크기 조정** 창에서 규칙을 트리거할 때 사용할 조건 및 작업을 설정 합니다.

     1. 완료 되 면 **추가**를 선택 합니다.

1. 자동 크기 조정 설정에 대 한 작업이 완료 되 면 변경 내용을 저장 합니다.

## <a name="next-steps"></a>다음 단계

* [Integration service environment에 아티팩트 추가](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [통합 서비스 환경의 네트워크 상태 확인](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)에 대해 자세히 알아보기
* [Azure 서비스에 대한 가상 네트워크 통합](../virtual-network/virtual-network-for-azure-services.md)에 대해 알아보기
