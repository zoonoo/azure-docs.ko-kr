---
title: 연결 모니터 만들기 - Azure Portal
titleSuffix: Azure Network Watcher
description: 이 문서에서는 Azure Portal을 사용하여 연결 모니터에서 모니터를 만드는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: 9942b608601b495dd041a8b59aaecb3f5dd81fa7
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505232"
---
# <a name="create-a-monitor-in-connection-monitor-by-using-the-azure-portal"></a>Azure Portal을 사용하여 연결 모니터에서 모니터 만들기

> [!IMPORTANT]
> 2021년 7월 1일부터 새 테스트를 기존 작업 영역에 추가하거나 네트워크 성능 모니터에서 새 작업 영역을 사용하도록 설정할 수 없습니다. 또한 연결 모니터(클래식)에서 새 연결 모니터를 추가할 수 없습니다. 2021년 7월 1일 이전에 만든 테스트 및 연결 모니터는 계속 사용할 수 있습니다. 현재 워크로드에 대한 서비스 중단을 최소화하려면 2024년 2월 29일 이전에 [네트워크 성능 모니터에서 테스트를 마이그레이션](migrate-to-connection-monitor-from-network-performance-monitor.md)하거나 [연결 모니터(클래식)에서 Azure Network Watcher의 새 연결 모니터로 마이그레이션](migrate-to-connection-monitor-from-connection-monitor-classic.md)합니다.

연결 모니터를 사용하여 리소스 간의 통신을 모니터링하는 방법에 대해 알아봅니다. 이 문서에서는 Azure Portal을 사용하여 모니터를 만드는 방법에 대해 설명합니다. 연결 모니터는 하이브리드 및 Azure 클라우드 배포를 지원합니다.


## <a name="before-you-begin"></a>시작하기 전에 

연결 모니터를 사용하여 만드는 연결 모니터에서는 온-프레미스 컴퓨터와 Azure VM을 모두 원본으로 추가할 수 있습니다. 이러한 연결 모니터는 엔드포인트에 대한 연결을 모니터링할 수도 있습니다. 엔드포인트는 Azure에 있거나 다른 URL 또는 IP에 있을 수 있습니다.

시작하기 위한 몇 가지 정의는 다음과 같습니다.

* **연결 모니터 리소스**. 지역별 Azure 리소스입니다. 다음 엔터티는 모두 연결 모니터 리소스의 속성입니다.
* **엔드포인트**. 연결 확인에 참여하는 원본 또는 대상입니다. 엔드포인트의 예는 다음과 같습니다.
    * Azure VM.
    * Azure 가상 네트워크
    * Azure 서브넷
    * 온-프레미스 에이전트
    * 온-프레미스 서브넷
    * 여러 서브넷을 포함하는 온-프레미스 사용자 지정 네트워크
    * URL 및 IP
* **테스트 구성**. 테스트에 대한 프로토콜 관련 구성입니다. 선택한 프로토콜에 따라 포트, 임계값, 테스트 빈도 및 기타 매개 변수를 정의할 수 있습니다.
* **테스트 그룹**. 원본 엔드포인트, 대상 엔드포인트 및 테스트 구성을 포함하는 그룹입니다. 연결 모니터는 둘 이상의 테스트 그룹을 포함할 수 있습니다.
* **테스트**. 원본 엔드포인트, 대상 엔드포인트 및 테스트 구성의 조합입니다. 테스트는 모니터링 데이터를 사용할 수 있는 가장 세분화된 수준입니다. 모니터링 데이터에는 실패한 검사 비율과 RTT(왕복 시간)가 포함됩니다.

:::image type="content" source="./media/connection-monitor-2-preview/cm-tg-2.png" alt-text="연결 모니터를 보여 주고 테스트 그룹과 테스트 간의 관계를 정의하는 다이어그램":::


## <a name="create-a-connection-monitor"></a>연결 모니터 만들기

Azure Portal을 사용하여 연결 모니터에서 모니터를 만들려면 다음을 수행합니다.

1. Azure Portal 홈 페이지에서 **Network Watcher** 로 이동합니다.
1. 왼쪽 창의 **모니터링** 섹션에서 **연결 모니터** 를 선택합니다.

   연결 모니터에서 만든 모든 연결 모니터가 표시됩니다. 클래식 연결 모니터에서 만든 연결 모니터를 보려면 **연결 모니터** 탭으로 이동합니다.

   :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="연결 모니터에서 만든 연결 모니터를 보여 주는 스크린샷 ":::
   
    
1. **연결 모니터** 대시보드의 왼쪽 위 모서리에서 **만들기** 를 선택합니다.

   

1. **기본 사항** 탭에서 연결 모니터에 대한 정보를 입력합니다. 
   * **연결 모니터 이름**: 연결 모니터에 대한 이름을 입력합니다. Azure 리소스에 대한 표준 명명 규칙을 사용합니다.
   * **구독**: 연결 모니터에 대한 구독을 선택합니다.
   * **지역**: 연결 모니터에 대한 지역을 선택합니다. 이 지역에서 만든 원본 VM만 선택할 수 있습니다.
   * **작업 영역 구성**: 사용자 지정 작업 영역 또는 기본 작업 영역을 선택합니다. 작업 영역에는 모니터링 데이터가 저장됩니다.
       * 기본 작업 영역을 사용하려면 확인란을 선택합니다. 
       * 사용자 지정 작업 영역을 선택하려면 확인란의 선택을 취소합니다. 그런 다음, 사용자 지정 작업 영역에 대한 구독 및 지역을 선택합니다. 

   :::image type="content" source="./media/connection-monitor-2-preview/create-cm-basics.png" alt-text="연결 모니터의 기본 사항 탭을 보여 주는 스크린샷":::
   
1. 탭 아래쪽에서 **다음: 테스트 그룹** 을 선택합니다.

1. 원본, 대상 및 테스트 구성을 테스트 그룹에 추가합니다. 테스트 그룹을 설정하는 방법에 대한 자세한 내용은 [연결 모니터에서 테스트 그룹 만들기](#create-test-groups-in-a-connection-monitor)를 참조하세요. 

   :::image type="content" source="./media/connection-monitor-2-preview/create-tg.png" alt-text="연결 모니터의 테스트 그룹 탭을 보여 주는 스크린샷":::

1. 탭 아래쪽에서 **다음: 경고 만들기** 를 선택합니다. 경고를 만드는 방법에 대한 자세한 내용은 [연결 모니터에서 경고 만들기](#create-alerts-in-connection-monitor)를 참조하세요.

   :::image type="content" source="./media/connection-monitor-2-preview/create-alert.png" alt-text="경고 만들기 탭을 보여 주는 스크린샷":::

1. 탭 아래쪽에서 **다음: 검토 + 만들기** 를 선택합니다.

1. 연결 모니터를 만들기 전에 **검토 + 만들기** 탭에서 기본 정보 및 테스트 그룹을 검토합니다. 연결 모니터를 편집해야 하는 경우 해당 탭으로 돌아가서 편집할 수 있습니다. 
   :::image type="content" source="./media/connection-monitor-2-preview/review-create-cm.png" alt-text="연결 모니터의 검토 + 만들기 탭을 보여 주는 스크린샷":::
   > [!NOTE] 
   > **검토 + 만들기** 탭에는 연결 모니터 단계 동안 월별 비용이 표시됩니다. 지금은 **현재 비용/월** 열에 요금이 표시되지 않습니다. 연결 모니터가 일반 공급되면 이 열에 월별 요금이 표시됩니다. 
   > 
   > 연결 모니터 단계 중에도 Log Analytics 수집 요금이 적용됩니다.

1. 연결 모니터를 만들 준비가 되었으면 **검토 + 만들기** 탭의 아래쪽에서 **만들기** 를 선택합니다.

연결 모니터는 백그라운드에서 연결 모니터 리소스를 만듭니다.

## <a name="create-test-groups-in-a-connection-monitor"></a>연결 모니터에서 테스트 그룹 만들기

연결 모니터의 각 테스트 그룹에는 네트워크 매개 변수에서 테스트되는 원본 및 대상이 포함됩니다. 실패한 검사 비율 및 테스트 구성의 RTT에 대해 테스트됩니다.

Azure Portal에서 테스트 그룹을 연결 모니터에 만들려면 다음 필드에 대한 값을 지정합니다.

* **테스트 그룹 사용 안 함**: 테스트 그룹에서 지정하는 모든 원본 및 대상에 대한 모니터링을 사용하지 않도록 설정하려면 이 확인란을 선택할 수 있습니다. 이 선택 항목은 기본적으로 선택 취소되어 있습니다.
* **이름**: 테스트 그룹의 이름을 지정합니다.
* **원본**: 에이전트가 설치된 Azure VM과 온-프레미스 컴퓨터를 모두 원본으로 지정할 수 있습니다. 원본에 대한 에이전트를 설치하는 방법에 대한 자세한 내용은 [모니터링 에이전트 설치](./connection-monitor-overview.md#install-monitoring-agents)를 참조하세요.
   * Azure 에이전트를 선택하려면 **Azure 엔드포인트** 탭을 선택합니다. 여기에는 연결 모니터를 만들 때 지정한 지역에 바인딩된 VM만 표시됩니다. 기본적으로 VM은 해당 VM이 속한 구독으로 그룹화됩니다. 이러한 그룹은 접혀 있습니다. 
   
       다음과 같이 **구독** 수준에서 계층 구조의 다른 수준으로 드릴다운할 수 있습니다.

      **구독** > **리소스 그룹** > **VNET** > **서브넷** > **VM(에이전트 포함)**

      또한 **그룹화 기준** 선택기를 변경하여 다른 수준에서 트리를 시작할 수 있습니다. 예를 들어 가상 네트워크를 기준으로 그룹화하는 경우 에이전트가 있는 VM이 **VNET** > **서브넷** > **VM(에이전트 포함)** 계층 구조에 표시됩니다.

       VNET, 서브넷 또는 단일 VM을 선택하면 해당 리소스 ID가 엔드포인트로 설정됩니다. 기본적으로 Azure Network Watcher 확장이 있는 선택한 VNET 또는 서브넷의 모든 VM은 모니터링에 참여합니다. 범위를 좁히려면 특정 서브넷 또는 에이전트를 선택하거나 범위 속성의 값을 변경합니다. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-sources.png" alt-text="연결 모니터의 원본 추가 창 및 Azure 엔드포인트 탭을 보여 주는 스크린샷":::

   * 온-프레미스 에이전트를 선택하려면 **Azure가 아닌 엔드포인트** 탭을 선택합니다. 기본적으로 에이전트는 지역별 작업 영역으로 그룹화되어 있습니다. 이러한 모든 작업 영역에는 네트워크 성능 모니터가 구성되어 있습니다. 
   
       네트워크 성능 모니터를 작업 영역에 추가해야 하는 경우 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/solarwinds.solarwinds-orion-network-performance-monitor?tab=Overview)에서 가져옵니다. 네트워크 성능 모니터를 추가하는 방법에 대한 자세한 내용은 [Azure Monitor의 솔루션 모니터링](../azure-monitor/insights/solutions.md)을 참조하세요. 온-프레미스 컴퓨터에 대한 에이전트를 구성하는 방법에 대한 자세한 내용은 [온-프레미스 컴퓨터에 대한 에이전트](connection-monitor-overview.md#agents-for-on-premises-machines)를 참조하세요.
   
       **연결 모니터 만들기** 아래의 **기본 사항** 탭에는 기본 지역이 선택되어 있습니다. 지역을 변경하면 새 지역의 작업 영역에서 에이전트를 선택할 수 있습니다. 하나 이상의 에이전트 또는 서브넷을 선택할 수 있습니다. **서브넷** 보기에서 모니터링할 특정 IP를 선택할 수 있습니다. 여러 서브넷을 추가하면 **OnPremises_Network_1** 이라는 사용자 지정 온-프레미스 네트워크가 만들어집니다. 또한 **그룹화 기준** 선택기를 변경하여 에이전트별로 그룹화할 수 있습니다.

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-sources.png" alt-text="연결 모니터의 원본 추가 창 및 Azure가 아닌 엔드포인트 탭을 보여 주는 스크린샷":::

   * 최근에 사용한 엔드포인트를 선택하려면 **최근 엔드포인트** 탭을 사용할 수 있습니다. 
   
   * 원본 설정이 완료되면 탭 아래쪽에서 **완료** 를 선택합니다. **테스트 그룹 만들기** 보기에서 엔드포인트를 선택하여 엔드포인트 이름과 같은 기본 속성을 계속 편집할 수 있습니다. 

* **대상**: Azure VM, 온-프레미스 컴퓨터 또는 엔드포인트(공용 IP, URL 또는 FQDN)에 대한 연결을 대상으로 지정하여 모니터링할 수 있습니다. 단일 테스트 그룹에서 Azure VM, 온-프레미스 컴퓨터, Office 365 URL, Dynamics 365 URL 및 사용자 지정 엔드포인트를 추가할 수 있습니다.

    * Azure VM을 대상으로 선택하려면 **Azure 엔드포인트** 탭을 선택합니다. 기본적으로 Azure VM은 **연결 모니터 만들기** 아래의 **기본 사항** 탭에서 선택한 지역에 있는 구독 계층 구조로 그룹화됩니다. 지역을 변경하고, 새 지역에서 Azure VM을 선택할 수 있습니다. 그런 다음, 원본 Azure 엔드포인트를 설정할 때와 마찬가지로 **구독** 수준에서 계층 구조의 다른 수준으로 드릴다운할 수 있습니다.

      원본 Azure 엔드포인트를 설정할 때와 마찬가지로 VNET, 서브넷 또는 단일 VM을 선택할 수 있습니다. VNET, 서브넷 또는 단일 VM을 선택하면 해당 리소스 ID가 엔드포인트로 설정됩니다. 기본적으로 Network Watcher 확장이 있는 선택한 VNET 또는 서브넷의 모든 VM은 모니터링에 참여합니다. 범위를 좁히려면 특정 서브넷 또는 에이전트를 선택하거나 범위 속성의 값을 변경합니다. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests1.png" alt-text="<대상 추가 창 및 Azure 엔드포인트 탭을 보여 주는 스크린샷>":::

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests2.png" alt-text="<구독 수준의 대상 추가 창을 보여 주는 스크린샷>":::
       
    
    * Azure가 아닌 에이전트를 대상으로 선택하려면 **Azure가 아닌 엔드포인트** 탭을 선택합니다. 기본적으로 에이전트는 지역별 작업 영역으로 그룹화되어 있습니다. 이러한 모든 작업 영역에는 네트워크 성능 모니터가 구성되어 있습니다. 
    
      네트워크 성능 모니터를 작업 영역에 추가해야 하는 경우 Azure Marketplace에서 가져옵니다. 네트워크 성능 모니터를 추가하는 방법에 대한 자세한 내용은 [Azure Monitor의 솔루션 모니터링](../azure-monitor/insights/solutions.md)을 참조하세요. 온-프레미스 컴퓨터에 대한 에이전트를 구성하는 방법에 대한 자세한 내용은 [온-프레미스 컴퓨터에 대한 에이전트](connection-monitor-overview.md#agents-for-on-premises-machines)를 참조하세요.

       **연결 모니터 만들기** 아래의  **기본 사항** 탭에는 기본 지역이 선택되어 있습니다. 지역을 변경하면 새 지역의 작업 영역에서 에이전트를 선택할 수 있습니다. 하나 이상의 에이전트 또는 서브넷을 선택할 수 있습니다. **서브넷** 보기에서 모니터링할 특정 IP를 선택할 수 있습니다. 여러 서브넷을 추가하면 **OnPremises_Network_1** 이라는 사용자 지정 온-프레미스 네트워크가 만들어집니다.  

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-dest.png" alt-text="대상 추가 창 및 Azure 엔드포인트 탭을 보여 주는 스크린샷":::
    
    * 퍼블릭 엔드포인트를 대상으로 선택하려면 **외부 주소** 탭을 선택합니다. 엔드포인트 목록에는 이름별로 그룹화된 Office 365 테스트 URL 및 Dynamics 365 테스트 URL이 포함되어 있습니다. 또한 동일한 연결 모니터의 다른 테스트 그룹에서 만든 엔드포인트를 선택할 수 있습니다. 
    
        엔드포인트를 추가하려면 오른쪽 위 모서리에서 **엔드포인트 추가** 를 선택합니다. 그런 다음, 엔드포인트 이름과 URL, IP 또는 FQDN을 제공합니다.

       :::image type="content" source="./media/connection-monitor-2-preview/add-endpoints.png" alt-text="연결 모니터에서 퍼블릭 엔드포인트를 대상으로 추가할 위치를 보여 주는 스크린샷":::

    * 최근에 사용한 엔드포인트를 선택하려면  **최근 엔드포인트** 탭으로 이동합니다.
    * 대상 선택이 완료되면 **완료** 를 선택합니다. **테스트 그룹 만들기** 보기에서 엔드포인트를 선택하여 엔드포인트 이름과 같은 기본 속성을 계속 편집할 수 있습니다. 

* **테스트 구성**: 하나 이상의 테스트 구성을 테스트 그룹에 추가할 수 있습니다. **새 구성** 탭을 사용하여 새 테스트 구성을 만듭니다. 또는 **기존 항목 선택** 탭에서 동일한 연결 모니터에 있는 다른 테스트 그룹의 테스트 구성을 추가합니다.

    * **테스트 구성 이름**: 테스트 구성의 이름을 지정합니다.
    * **프로토콜**:**TCP**, **ICMP** 또는 **HTTP** 를 선택합니다. HTTP를 HTTPS로 변경하려면 프로토콜로 **HTTP** 를 선택한 다음, 포트로 **443** 을 선택합니다.
        * **TCP 테스트 구성 만들기**: 이 확인란은 **프로토콜** 목록에서 **HTTP** 를 선택한 경우에만 표시됩니다. 구성의 다른 위치에서 지정한 것과 동일한 원본 및 대상을 사용하는 다른 테스트 구성을 만들려면 이 확인란을 선택합니다. 새 테스트 구성의 이름은 **\<name of test configuration>_networkTestConfig** 입니다.
        * **경로 추적 사용 안 함**: 프로토콜이 TCP 또는 ICMP인 경우 이 확인란이 적용됩니다. 원본에서 토폴로지 및 홉 단위 RTT를 검색하지 못하게 하려면 이 확인란을 선택합니다.
    * **대상 포트**: 원하는 대상 포트를 제공할 수 있습니다.
        * **수신 대기 포트**: 프로토콜이 TCP인 경우 이 확인란이 적용됩니다. 선택한 TCP 포트가 아직 열려 있지 않은 경우 해당 포트를 열려면 이 확인란을 선택합니다. 
    * **테스트 빈도**: 이 목록에서 지정한 프로토콜 및 포트의 대상을 원본에서 ping하는 빈도를 지정합니다. 30초, 1분, 5분, 15분 또는 30분을 선택할 수 있습니다. 30초~30분의 다른 빈도를 입력하려면 **사용자 지정** 을 선택합니다. 사용자가 선택한 값에 따라 원본에서 대상에 대한 연결을 테스트합니다. 예를 들어 30초를 선택하는 경우 원본에서 대상에 대한 연결을 30초마다 한 번 이상 확인합니다.
    * **성공 임계값**: 다음 네트워크 매개 변수에 대한 임계값을 설정할 수 있습니다.
       * **검사 실패율**: 지정한 기준을 사용하여 원본에서 대상에 대한 연결을 확인할 때 실패할 수 있는 검사 비율을 설정합니다. TCP 또는 ICMP 프로토콜의 경우 검사 실패율은 패킷 손실율과 동일할 수 있습니다. HTTP 프로토콜의 경우 이 값은 응답을 받지 않은 HTTP 요청의 백분율을 나타냅니다.
       * **왕복 시간**: 원본에서 테스트 구성을 통해 대상에 연결하는 데 걸리는 시간에 대한 RTT(밀리초)를 설정합니다.
       
   :::image type="content" source="./media/connection-monitor-2-preview/add-test-config.png" alt-text="연결 모니터에서 테스트 구성을 설정할 위치를 보여 주는 스크린샷":::
       
## <a name="create-alerts-in-connection-monitor"></a>연결 모니터에서 경고 만들기

테스트 구성에 설정된 임계값에 따라 실패한 테스트에 대한 경고를 설정할 수 있습니다.

Azure Portal에서 연결 모니터에 대한 경고를 만들려면 다음 필드에 대한 값을 지정합니다. 

- **경고 만들기**: Azure Monitor에서 메트릭 경고를 만들려면 이 확인란을 선택할 수 있습니다. 이 확인란을 선택하면 다른 필드를 편집할 수 있습니다. [경고에 대한 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)에 따라 경고에 대한 추가 요금이 적용됩니다. 

- **범위** > **리소스** > **계층 구조**: 이러한 값은 **기본 사항** 탭에 지정된 값에 따라 자동으로 채워집니다.

- **조건 이름**: `Test Result(preview)` 메트릭에 대한 경고가 만들어집니다. 연결 모니터 테스트의 결과가 실패 결과이면 경고 규칙이 실행됩니다. 

- **작업 그룹 이름**: 이메일을 직접 입력하거나 작업 그룹을 통해 경고를 만들 수 있습니다. 이메일을 직접 입력하면 **NPM 이메일 작업 그룹** 이라는 작업 그룹이 만들어집니다. 이메일 ID가 해당 작업 그룹에 추가됩니다. 작업 그룹을 사용하도록 선택하는 경우 이전에 만든 작업 그룹을 선택해야 합니다. 작업 그룹을 만드는 방법에 대한 자세한 내용은 [Azure Portal에서 작업 그룹 만들기](../azure-monitor/alerts/action-groups.md)를 참조하세요. 경고가 만들어지면 [경고를 관리](../azure-monitor/alerts/alerts-metric.md#view-and-manage-with-azure-portal)할 수 있습니다. 

- **경고 규칙 이름**: 연결 모니터의 이름입니다.

- **규칙을 만들면 바로 사용**: 조건에 따라 경고 규칙을 사용하도록 설정하려면 이 확인란을 선택합니다. 사용하도록 설정되지 않은 규칙을 만들려면 이 확인란의 선택을 취소합니다. 

:::image type="content" source="./media/connection-monitor-2-preview/create-alert-filled.png" alt-text="연결 모니터의 경고 만들기 탭을 보여 주는 스크린샷":::

## <a name="scale-limits"></a> 확장 한도

연결 모니터에는 다음과 같은 스케일링 제한이 있습니다.

* 지역별 구독당 최대 연결 모니터 수: 100개
* 연결 모니터당 최대 테스트 그룹 수: 20개
* 연결 모니터당 최대 원본 및 대상 수: 100개
* 연결 모니터당 최대 테스트 구성 수: 2개(Azure Portal을 통해)

## <a name="next-steps"></a>다음 단계

* [모니터링 데이터를 분석하고 경고를 설정하는 방법](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)을 알아봅니다.
* [네트워크에서 문제를 진단하는 방법](./connection-monitor-overview.md#diagnose-issues-in-your-network)을 알아봅니다.
