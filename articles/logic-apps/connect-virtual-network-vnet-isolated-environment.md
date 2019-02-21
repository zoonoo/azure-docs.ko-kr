---
title: ISE(통합 서비스 환경)를 통해 Azure Logic Apps에서 Azure 가상 네트워크에 연결
description: 논리 앱 및 통합 계정이 공용이나 “글로벌” Azure에서 격리된 비공개 상태를 유지하면서 Azure VNET(가상 네트워크)에 액세스할 수 있도록 ISE(통합 서비스 환경) 만들기
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 02/15/2019
ms.openlocfilehash: d67bc99a63242dd56d65d6bdac0448c7742a6b9d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311905"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>ISE(통합 서비스 환경)를 사용하여 Azure Logic Apps에서 Azure 가상 네트워크에 연결

> [!NOTE]
> 이 기능은 *비공개 미리 보기* 상태입니다. 비공개 미리 보기에 참가하려면 [여기에서 요청을 만듭니다](https://aka.ms/iseprivatepreview).

논리 앱 및 통합 계정이 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)에 액세스해야 하는 시나리오의 경우 [*ISE*(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)을 만듭니다. ISE는 공용 또는 “글로벌” Logic Apps 서비스와 분리된 상태를 유지하는 전용 스토리지 및 기타 리소스를 사용하는 개인 및 격리된 환경입니다. 이러한 격리로 인해 다른 Azure 테넌트가 앱 성능에 줄 수 있는 영향이 감소됩니다. ISE를 Azure 가상 네트워크에 *삽입*한 다음, Logic Apps 서비스를 사용자의 가상 네트워크에 배포합니다. 논리 앱 또는 통합 계정을 만들 때 해당 위치로 이 ISE를 선택합니다. 그러면 논리 앱 및 통합 계정은 가상 네트워크에서 VM(가상 머신), 서버, 시스템 및 서비스와 같은 리소스에 직접 액세스할 수 있습니다.

![통합 서비스 환경 선택](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

이 문서에서는 다음 작업을 완료하는 방법을 보여줍니다.

* 트래픽이 ISE(통합 서비스 환경)를 통해 가상 네트워크의 서브넷 간에 이동할 수 있도록 Azure 가상 네트워크에 포트를 설정합니다.

* 전용 Logic Apps 인스턴스에서 가상 네트워크에 액세스할 수 있도록 Azure 가상 네트워크에 대한 권한을 설정합니다.

* ISE(통합 서비스 환경)를 만듭니다.

* ISE에서 실행할 수 있는 논리 앱을 만듭니다.

* ISE에서 논리 앱에 대한 통합 계정을 만듭니다.

통합 서비스 환경에 대한 자세한 내용은 [Azure Logic Apps에서 Azure Virtual Network 리소스에 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다.

  > [!IMPORTANT]
  > ISE에서 실행되는 논리 앱, 기본 제공 작업 및 커넥터의 경우 사용량 기반 가격 책정 플랜이 아닌 다른 가격 책정 플랜을 사용합니다. 자세한 내용은 [Logic Apps 가격 책정](../logic-apps/logic-apps-pricing.md)을 참조하세요.

* [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)입니다. 가상 네트워크가 없는 경우 [Azure 가상 네트워크를 만드는](../virtual-network/quick-create-portal.md) 방법을 알아봅니다. 또한 ISE를 배포하려면 가상 네트워크에 서브넷이 있어야 합니다. 이러한 서브넷을 미리 만들거나, 동시에 서브넷을 만들 수 있는 ISE를 만들 때까지 기다릴 수 있습니다. 또한 ISE가 올바르게 작동하고 액세스 가능한 상태로 유지되도록 [가상 네트워크에서 해당 포트를 사용할 수 있는지 확인](#ports)합니다.

* Azure 가상 네트워크에 대한 직접 액세스 권한을 논리 앱에 부여하려면 Logic Apps 서비스에 가상 네트워크 액세스 권한이 있도록 [네트워크의 RBAC(역할 기반 액세스 제어) 권한을 설정](#vnet-access)합니다.

* Azure 가상 네트워크를 배포하기 위해 하나 이상의 사용자 지정 DNS 서버를 사용하려면 가상 네트워크에 ISE를 배포하기 전에 [이 지침에 따라 해당 서버를 설정](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)합니다. 설정하지 않으면 DNS 서버를 변경할 때마다 ISE도 다시 시작해야 합니다. 이 기능은 ISE 공개 미리 보기에서 사용할 수 있습니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

<a name="ports"></a>

## <a name="set-up-network-ports"></a>네트워크 포트 설정

올바르게 작동하고 액세스 가능한 상태로 유지되려면 ISE(통합 서비스 환경)에 가상 네트워크에서 사용 가능한 특정 포트가 있어야 합니다. 이러한 포트 중 하나를 사용할 수 없는 경우 ISE에 액세스할 수 없게 되며, ISE 작동이 중지될 수 있습니다. 가상 네트워크에서 ISE를 사용할 때 일반적인 설정 문제는 차단된 포트가 하나 이상 있는 경우입니다. ISE와 대상 시스템 간의 연결에 사용하는 커넥터에 자체 포트 요구 사항이 있을 수도 있습니다. 예를 들어 FTP 커넥터를 사용하여 FTP 시스템과 통신하는 경우 명령 전송을 위한 포트 21과 같이 FTP 시스템에서 사용하는 포트가 사용 가능한지 확인합니다.

ISE를 배포하는 가상 네트워크의 서브넷에서 인바운드 및 아웃바운드 트래픽을 제어하기 위해 [서브넷에서 네트워크 트래픽을 필터링하는 방법](../virtual-network/tutorial-filter-network-traffic.md)을 학습하여 해당 서브넷에 대해 [네트워크 보안 그룹](../virtual-network/security-overview.md)을 설정할 수 있습니다. 이 표에서는 ISE에서 사용하는 가상 네트워크의 포트 및 해당 포트가 사용되는 위치를 설명합니다. 별표(*)는 모든 트래픽 원본을 나타냅니다. [서비스 태그](../virtual-network/security-overview.md#service-tags)는 보안 규칙을 만들 때 복잡성을 최소화하는 데 도움이 되는 IP 주소 접두사 그룹을 나타냅니다.

| 목적 | 방향 | 원본 포트 <br>대상 포트 | 원본 서비스 태그 <br>대상 서비스 태그 |
|---------|-----------|---------------------------------|-----------------------------------------------|
| Azure Logic Apps로 보내는 통신 <br>Azure Logic Apps에서 받는 통신 | 인바운드 <br>아웃바운드 | * <br>80 및 443 | 인터넷 <br>VIRTUAL_NETWORK |
| Azure Active Directory | 아웃바운드 | * <br>80 및 443 | VIRTUAL_NETWORK <br>AzureActiveDirectory |
| Azure Storage 종속성 | 아웃바운드 | * <br>80 및 443 | VIRTUAL_NETWORK <br>Storage |
| 논리 앱의 실행 기록 | 인바운드 | * <br>443 | 인터넷 <br>VIRTUAL_NETWORK |
| 연결 관리 | 아웃바운드 | * <br>443 | VIRTUAL_NETWORK <br>인터넷 |
| 진단 로그 및 메트릭 게시 | 아웃바운드 | * <br>443 | VIRTUAL_NETWORK <br>AzureMonitor |
| Logic Apps 디자이너 - 동적 속성 <br>커넥터 배포 <br>요청 트리거 엔드포인트 | 인바운드 | * <br>454 | 인터넷 <br>VIRTUAL_NETWORK |
| App Service 관리 종속성 | 인바운드 | * <br>454 및 455 | AppServiceManagement <br>VIRTUAL_NETWORK |
| API Management - 관리 엔드포인트 | 인바운드 | * <br>3443 | APIManagement <br>VIRTUAL_NETWORK |
| 이벤트 허브에 로그 정책 및 모니터링 에이전트의 종속성 | 아웃바운드 | * <br>5672 | VIRTUAL_NETWORK <br>EventHub |
| 역할 인스턴스 간의 Azure Cache for Redis 인스턴스 액세스 | 인바운드 <br>아웃바운드 | * <br>6381-6383 | VIRTUAL_NETWORK <br>VIRTUAL_NETWORK |
|||||

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>가상 네트워크 권한 설정

ISE(통합 서비스 환경)를 만들 때는 환경을 *삽입*하는 Azure 가상 네트워크를 선택합니다. 그러나 환경을 삽입하기 위해 Azure 가상 네트워크를 선택하기 전에 먼저 가상 네트워크에서 RBAC(역할 기반 액세스 제어) 권한을 설정해야 합니다. 권한을 설정하려면 Azure Logic Apps 서비스에 이러한 특정 역할을 할당합니다.

1. [Azure Portal](https://portal.azure.com)에서 가상 네트워크를 찾고 선택합니다.

1. 가상 네트워크의 메뉴에서 **액세스 제어(IAM)** 를 선택합니다.

1. **액세스 제어(IAM)** 에서 **역할 할당 추가**를 선택합니다.

   ![역할 추가](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. **역할 할당 추가** 창에 설명된 대로 Azure Logic Apps 서비스에 필요한 역할을 추가합니다.

   1. **역할**에서 **네트워크 기여자**를 선택합니다.

   1. **액세스 할당**에서 **Azure AD 사용자, 그룹 또는 서비스 보안 주체**를 선택합니다.

   1. **선택**에서 **Azure Logic Apps**를 입력합니다.

   1. 멤버 목록이 표시되면 **Azure Logic Apps**를 선택합니다.

      > [!TIP]
      > 이 서비스를 찾을 수 없는 경우 Logic Apps 서비스의 앱 ID를 입력합니다.`7cd684f4-8a78-49b0-91ec-6a35d38739ba`

   1. 완료하면 **저장**을 선택합니다.

   예: 

   ![역할 할당 추가](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

자세한 내용은 [가상 네트워크 액세스를 위한 권한](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)을 참조하세요.

<a name="create-environment"></a>

## <a name="create-your-ise"></a>ISE 만들기

ISE(통합 서비스 환경)를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)의 기본 Azure 메뉴에서 **리소스 만들기**를 선택합니다.

   ![새 리소스 만들기](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 검색 상자에서 필터로 "통합 서비스 환경"을 입력합니다.
결과 목록에서 **통합 서비스 환경(미리 보기)** 을 선택한 다음, **만들기**를 선택합니다.

   !["통합 서비스 환경" 선택](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   !["만들기" 선택](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. 예를 들어 사용자 환경에 대한 이러한 세부 정보를 제공한 다음, **검토 + 만들기**를 선택합니다.

   ![환경 세부 정보 제공](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | 자산 | 필수 | Value | 설명 |
   |----------|----------|-------|-------------|
   | **구독** | 예 | <*Azure-subscription-name*> | 환경에 사용할 Azure 구독 |
   | **리소스 그룹** | 예 | <*Azure-resource-group-name*> | 환경을 만들려는 Azure 리소스 그룹 |
   | **통합 서비스 환경 이름** | 예 | <*environment-name*> | 환경에 부여할 이름 |
   | **위치**: | 예 | <*Azure-datacenter-region*> | 환경을 배포할 Azure 데이터 센터 지역 |
   | **추가 용량** | 예 | 0, 1, 2, 3 | 이 ISE 리소스에 대해 사용할 처리 단위 수 |
   | **가상 네트워크** | 예 | <*Azure-virtual-network-name*> | 해당 환경의 논리 앱이 가상 네트워크에 액세스할 수 있도록 환경을 삽입하려는 Azure 가상 네트워크입니다. 네트워크가 없는 경우 여기서 만들 수 있습니다. <p>**중요**: ISE를 만들 때*만* 이 삽입을 수행할 수 있습니다. 그러나 이 관계를 만들려면 먼저 [Azure Logic Apps에 대한 가상 네트워크에서 역할 기반 액세스 제어를 설정](#vnet-access)했는지 확인합니다. |
   | **서브넷** | 예 | <*subnet-resource-list*> | ISE에는 사용자 환경에서 리소스를 만들기 위해 4개의 *빈* 서브넷이 필요합니다. 따라서 이러한 서브넷은 어떤 서비스에도 *위임되지 않아야* 합니다. 환경을 만든 후 이러한 서브넷 주소를 *변경할 수 없습니다*. <p><p>각 서브넷을 만들려면 [이 테이블 아래의 단계를 따릅니다](#create-subnet). 각 서브넷은 이러한 조건을 충족해야 합니다. <p>- 비어 있어야 합니다. <br>- 숫자 또는 하이픈으로 시작하지 않는 이름을 사용합니다. <br>- [CIDR(Classless Inter-Domain Routing) 형식](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 및 클래스 B 주소 공간을 사용합니다. <br>- 서브넷이 32개 이상의 주소를 가져오도록 주소 공간에 `/27` 이상을 포함합니다. 주소 수를 계산에 대해 자세히 알아보려면 [IPv4 CIDR 블록](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)을 참조하세요. 예:  <p>- 2<sup>(32-24)</sup>은 2<sup>8</sup> 또는 256이므로 `10.0.0.0/24`는 256개의 주소를 포함합니다. <br>- 2<sup>(32-27)</sup>은 2<sup>5</sup> 또는 32이므로 `10.0.0.0/27`는 32개의 주소를 포함합니다. <br>- 2<sup>(32-28)</sup>은 2<sup>4</sup> 또는 16이므로 `10.0.0.0/28`는 16개의 주소만 포함합니다. |
   |||||

   <a name="create-subnet"></a>

   **서브넷 만들기**

   1. **서브넷** 목록에서 **서브넷 구성 관리**를 선택합니다.

      ![서브넷 구성 관리](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. **서브넷** 창에서 **서브넷**을 선택합니다.

      ![서브넷 추가](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. **서브넷 추가** 창에서 이 정보를 제공합니다.

      * **이름**: 서브넷의 이름
      * **주소 범위(CIDR 블록)**: 가상 네트워크 및 CIDR 형식의 서브넷 범위

      ![서브넷 세부 정보 추가](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. 작업을 완료하면 **확인**을 선택합니다.

   1. 세 개 이상의 서브넷에 대해 이 단계를 반복합니다.

1. 예를 들어 Azure에서 성공적으로 ISE 정보의 유효성 검사를 완료하면 **만들기**를 선택합니다.

   ![유효성 검사에 성공하면 "만들기" 선택](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure는 환경 배포를 시작하지만 이 프로세스는 완료하기까지 최대 2시간이 걸릴 *수* 있습니다. 
   배포 상태를 확인하려면 Azure 도구 모음에서 알림 창을 여는 알림 아이콘을 선택합니다.

   ![배포 상태 확인](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   배포가 성공적으로 완료되면 Azure에 이 알림이 표시됩니다.

   ![배포 성공](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   > [!NOTE]
   > 배포가 실패하거나 ISE를 삭제하는 경우 Azure에서 서브넷을 릴리스하기까지 최대 1시간이 걸릴 *수* 있습니다. 따라서 다른 ISE에서 해당 서브넷을 다시 사용하기 전에 대기해야 할 수 있습니다.

1. 배포가 완료된 후에 Azure가 환경으로 자동으로 이동하지 않는 경우 환경을 보려면 **리소스로 이동**을 선택합니다.  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>논리 앱 만들기 - ISE

ISE(통합 서비스 환경)를 사용하는 논리 앱을 만들려면 이러한 차이점이 있는 [논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)의 단계를 따릅니다. 

* 예를 들어 논리 앱을 만들 때 **Location** 속성에서 **통합 서비스 환경** 섹션에서 ISE를 선택합니다.

  ![통합 서비스 환경 선택](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* 논리 앱과 동일한 ISE에서 실행되는 HTTP와 같은 기본 제공 트리거 및 작업을 사용할 수 있습니다. **ISE** 레이블이 있는 커넥터는 논리 앱과 동일한 ISE에서 실행됩니다. **ISE** 레이블이 없는 커넥터는 전역 Logic Apps 서비스에서 실행됩니다.

  ![ISE 커넥터 선택](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Azure 가상 네트워크에 ISE를 삽입하면 ISE의 논리 앱은 해당 가상 네트워크의 리소스에서 직접 액세스할 수 있습니다. 가상 네트워크에 연결된 온-프레미스 시스템의 경우 논리 앱이 이러한 항목 중 하나를 사용하여 해당 시스템에 직접 액세스할 수 있도록 해당 네트워크에 ISE를 삽입합니다. 

  * SQL Server 등의 해당 시스템용 ISE 커넥터
  
  * HTTP 동작 
  
  * 사용자 지정 커넥터

  가상 네트워크에 있지 않거나 ISE 커넥터가 없는 온-프레미스 시스템의 경우 먼저 [온-프레미스 데이터 게이트웨이를 설정](../logic-apps/logic-apps-gateway-install.md)합니다.

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>통합 계정 만들기 - ISE

ISE(통합 서비스 환경)에서 논리 앱으로 통합 계정을 사용하려면 해당 통합 계정은 논리 앱으로 *동일한 환경*을 사용해야 합니다. ISE의 논리 앱은 같은 ISE에 있는 통합 계정만 참조할 수 있습니다. 

ISE를 사용하는 통합 계정을 만들려면 현재 **통합 서비스 환경** 섹션이 표시되는 **Location** 속성을 제외하고 [통합 계정을 만드는 방법](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)의 단계를 따릅니다. 예를 들어 지역 대신 ISE를 선택합니다.

![통합 서비스 환경 선택](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure Logic Apps 포럼</a>을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 <a href="https://aka.ms/logicapps-wish" target="_blank">Logic Apps 사용자 의견 사이트</a>를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)에 대해 자세히 알아보기
* [Azure 서비스에 대한 가상 네트워크 통합](../virtual-network/virtual-network-for-azure-services.md)에 대해 알아보기
