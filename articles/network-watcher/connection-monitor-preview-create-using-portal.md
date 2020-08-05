---
title: 연결 모니터 미리 보기 만들기-Azure Portal
titleSuffix: Azure Network Watcher
description: Azure Portal를 사용 하 여 연결 모니터 (미리 보기)를 만드는 방법에 대해 알아봅니다.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: 4e7067e537ce8fb6faf82198f7863957f79ffb22
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567931"
---
# <a name="create-a-connection-monitor-preview-using-the-azure-portal"></a>Azure Portal를 사용 하 여 연결 모니터 (미리 보기) 만들기

Azure Portal를 사용 하 여 리소스 간의 통신을 모니터링 하는 연결 모니터 (미리 보기)를 만드는 방법에 대해 알아봅니다. 하이브리드 및 Azure 클라우드 배포를 지원 합니다.

## <a name="before-you-begin"></a>시작하기 전에 

연결 모니터 (미리 보기)에서 만든 연결 모니터에서 온-프레미스 컴퓨터와 Azure Vm을 모두 원본으로 추가할 수 있습니다. 이러한 연결 모니터는 끝점에 대 한 연결을 모니터링할 수도 있습니다. 끝점은 Azure 또는 다른 모든 URL 또는 IP에 있을 수 있습니다.

연결 모니터 (미리 보기)에는 다음 엔터티가 포함 됩니다.


* **연결 모니터 리소스** – 지역별 Azure 리소스입니다. 다음 엔터티는 모두 연결 모니터 리소스의 속성입니다.
* **끝점** – 연결 확인에 참여 하는 원본 또는 대상입니다. 끝점의 예로는 Azure Vm, 온-프레미스 에이전트, Url 및 Ip가 있습니다.
* **테스트 구성** – 테스트에 대 한 프로토콜 관련 구성입니다. 선택한 프로토콜에 따라 포트, 임계값, 테스트 빈도 및 기타 매개 변수를 정의할 수 있습니다.
* **테스트 그룹** – 소스 끝점, 대상 끝점 및 테스트 구성이 포함 된 그룹입니다. 연결 모니터에는 두 개 이상의 테스트 그룹이 포함 될 수 있습니다.
* **Test** – 소스 끝점, 대상 끝점 및 테스트 구성의 조합입니다. 테스트는 모니터링 데이터를 사용할 수 있는 가장 세분화 된 수준입니다. 모니터링 데이터는 실패 한 검사 비율과 RTT (왕복 시간)를 포함 합니다.

    ![테스트 그룹 및 테스트 간의 관계를 정의 하는 연결 모니터를 보여 주는 다이어그램](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create"></a>만드는 단계

Azure Portal를 사용 하 여 연결 모니터 (미리 보기)를 만들려면 아래 단계를 수행 합니다.

1. Azure Portal 홈 페이지에서 **Network Watcher**으로 이동 합니다.
1. 왼쪽의 **모니터링** 섹션에서 **연결 모니터 (미리 보기)** 를 선택 합니다.
1. 연결 모니터 (미리 보기)에서 생성 된 모든 연결 모니터가 표시 됩니다. 연결 모니터의 클래식 환경에서 만든 연결 모니터를 보려면 **연결 모니터** 탭으로 이동 합니다.

    ![연결 모니터 (미리 보기)에서 만든 연결 모니터를 보여 주는 스크린샷](./media/connection-monitor-2-preview/cm-resource-view.png)   
    
1. **연결 모니터 (미리 보기)** 대시보드의 왼쪽 위 모서리에서 **만들기**를 선택 합니다.
1. **기본 사항** 탭에서 연결 모니터에 대 한 정보를 입력 합니다.
   * **연결 모니터 이름** – 연결 모니터의 이름을 추가 합니다. Azure 리소스에 대 한 표준 명명 규칙을 사용 합니다.
   * **구독** – 연결 모니터에 대 한 구독을 선택 합니다.
   * **지역** – 연결 모니터에 사용할 지역을 선택 합니다. 이 지역에 생성 된 원본 Vm만 선택할 수 있습니다.
   * **작업 영역 구성** -작업 영역에 모니터링 데이터를 저장 합니다. 사용자 지정 작업 영역 또는 기본 작업 영역을 사용할 수 있습니다. 
       * 기본 작업 영역을 사용 하려면 확인란을 선택 합니다. 
       * 사용자 지정 작업 영역을 선택 하려면 확인란의 선택을 취소 합니다. 그런 다음 사용자 지정 작업 영역에 대 한 구독 및 지역을 선택 합니다. 
1. 탭의 아래쪽에서 **다음: 테스트 그룹**을 선택 합니다.

   ![연결 모니터의 기본 사항 탭을 보여 주는 스크린샷](./media/connection-monitor-2-preview/create-cm-basics.png)

1. **테스트 그룹** 탭에서 **+ 테스트 그룹**을 선택 합니다. 테스트 그룹을 설정 하려면 [연결 모니터에서 테스트 그룹 만들기](#create-test-groups-in-a-connection-monitor)를 참조 하세요. 
1. 탭의 아래쪽에서 **다음: 검토 + 만들기** 를 선택 하 여 연결 모니터를 검토 합니다.

   ![테스트 그룹 탭 및 테스트 그룹 정보를 추가 하는 창을 보여 주는 스크린샷](./media/connection-monitor-2-preview/create-tg.png)

1. **검토 + 만들기** 탭에서 연결 모니터를 만들기 전에 기본 정보 및 테스트 그룹을 검토 합니다. 연결 모니터를 편집 해야 하는 경우 다음을 수행 합니다.
   * 기본 정보를 편집 하려면 연필 아이콘을 선택 합니다.
   * 테스트 그룹을 편집 하려면 해당 그룹을 선택 합니다.

   > [!NOTE] 
   > **검토 + 만들기** 탭은 연결 모니터 미리 보기 단계에서 월별 비용을 보여 줍니다. 현재 **비용** 열에는 요금이 부과 되지 않습니다. 일반적으로 연결 모니터를 사용할 수 있게 되 면이 열에는 월별 요금이 표시 됩니다. 
   > 
   > 연결 모니터 미리 보기 단계 에서도 Log Analytics 수집 요금이 적용 됩니다.

1. 연결 모니터를 만들 준비가 되 면 **검토 + 만들기** 탭의 맨 아래에서 **만들기**를 선택 합니다.

   ![검토 + 만들기 탭을 표시 하는 연결 모니터의 스크린샷](./media/connection-monitor-2-preview/review-create-cm.png)

연결 모니터 (미리 보기)는 백그라운드에서 연결 모니터 리소스를 만듭니다.

## <a name="create-test-groups-in-a-connection-monitor"></a>연결 모니터에서 테스트 그룹 만들기

연결 모니터의 각 테스트 그룹에는 네트워크 매개 변수에서 테스트를 수행 하는 원본 및 대상이 포함 되어 있습니다. 실패 한 검사와 테스트 구성에 대 한 RTT의 비율을 테스트 합니다.

Azure Portal에서 연결 모니터에 테스트 그룹을 만들려면 다음 필드에 대 한 값을 지정 합니다.

* **테스트 그룹 사용 안 함** –이 필드를 선택 하 여 테스트 그룹에서 지정 하는 모든 원본 및 대상에 대해 모니터링을 사용 하지 않도록 설정할 수 있습니다. 이 선택 항목은 기본적으로 선택 되어 있지 않습니다.
* **이름** – 테스트 그룹의 이름을로 합니다.
* **원본** -에이전트가 설치 되어 있는 경우 Azure vm과 온-프레미스 컴퓨터를 모두 원본으로 지정할 수 있습니다. 원본에 대 한 에이전트를 설치 하려면 [모니터링 에이전트 설치](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents)를 참조 하세요.
   * Azure 에이전트를 선택 하려면 **Azure 에이전트** 탭을 선택 합니다. 여기서는 연결 모니터를 만들 때 지정한 지역에 바인딩된 Vm만 표시 됩니다. 기본적으로 Vm은 속한 구독으로 그룹화 됩니다. 이러한 그룹은 축소 됩니다. 
   
       구독 수준에서 계층의 다른 수준으로 드릴 다운할 수 있습니다.

      **구독**  >  **리소스 그룹**  >  **Vnet**  >  **서브넷**  >  **에이전트를 사용 하는 vm**

      **묶는** 방법 필드의 값을 변경 하 여 다른 수준에서 트리를 시작할 수도 있습니다. 예를 들어 가상 네트워크를 기준으로 그룹화 하는 경우 에이전트를 사용 하 여 **vnet**  >  **서브넷**  >  **vm**계층 구조에 에이전트가 있는 vm이 표시 됩니다.

      ![원본 추가 패널 및 Azure 에이전트 탭을 표시 하는 연결 모니터의 스크린샷](./media/connection-monitor-2-preview/add-azure-sources.png)

   * 온-프레미스 에이전트를 선택 하려면 **비 Azure 에이전트** 탭을 선택 합니다. 기본적으로 에이전트는 지역별로 작업 영역으로 그룹화 됩니다. 이러한 모든 작업 영역에는 구성 된 네트워크 성능 모니터 솔루션이 있습니다. 
   
       작업 영역에 네트워크 성능 모니터를 추가 해야 하는 경우 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)에서 가져옵니다. 네트워크 성능 모니터를 추가 하는 방법에 대 한 자세한 내용은 [Azure Monitor에서 솔루션 모니터링](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)을 참조 하세요. 
   
       **연결 모니터 만들기** 보기의 기본 **사항** 탭에서 기본 지역이 선택 되어 있습니다. 영역을 변경 하는 경우 새 지역의 작업 영역에서 에이전트를 선택할 수 있습니다. **Group** by 필드의 값을 group by 서브넷으로 변경할 수도 있습니다.

      ![원본 추가 패널 및 비 Azure 에이전트 탭을 표시 하는 연결 모니터의 스크린샷](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * 선택한 Azure 및 비 Azure 에이전트를 검토 하려면 **검토** 탭으로 이동 합니다.

      ![소스 추가 패널 및 검토 탭을 표시 하는 연결 모니터의 스크린샷](./media/connection-monitor-2-preview/review-sources.png)

   * 원본 설정이 끝나면 **원본 추가** 패널의 맨 아래에 있는 **완료**를 선택 합니다.

* **대상** – 대상으로 지정 하 여 Azure vm 또는 끝점 (공용 IP, URL 또는 FQDN)에 대 한 연결을 모니터링할 수 있습니다. 단일 테스트 그룹에서 Azure Vm, Office 365 Url, Dynamics 365 Url 및 사용자 지정 끝점을 추가할 수 있습니다.

    * Azure Vm을 대상으로 선택 하려면 **Azure vm** 탭을 선택 합니다. 기본적으로 Azure Vm은 **기본 탭의** **연결 모니터 만들기** 보기에서 선택한 것과 동일한 지역에 있는 구독 계층 구조로 그룹화 됩니다. 영역을 변경 하 고 새로 선택한 지역에서 Azure Vm을 선택할 수 있습니다. 그런 다음 구독 수준에서 계층의 다른 수준 (예: Azure 에이전트 수준)으로 드릴 다운할 수 있습니다.

       ![Azure Vm 탭을 표시 하는 대상 추가 창의 스크린샷](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![구독 수준을 표시 하는 대상 추가 창의 스크린샷](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * 끝점을 대상으로 선택 하려면 **끝점** 탭을 선택 합니다. 끝점 목록에는 이름으로 그룹화 된 Office 365 테스트 Url 및 Dynamics 365 테스트 Url이 포함 되어 있습니다. 이러한 끝점 외에도 동일한 연결 모니터의 다른 테스트 그룹에서 만든 끝점을 선택할 수 있습니다. 
    
        새 끝점을 추가 하려면 오른쪽 위 모서리에서 **+ 끝점**을 선택 합니다. 그런 다음 끝점 이름과 URL, IP 또는 FQDN을 제공 합니다.

       ![연결 모니터에서 끝점을 대상으로 추가 하는 위치를 보여 주는 스크린샷](./media/connection-monitor-2-preview/add-endpoints.png)

    * 선택한 Azure Vm 및 끝점을 검토 하려면 **검토** 탭을 선택 합니다.
    * 대상 선택을 마치면 **완료**를 선택 합니다.

* 테스트 **구성** – 테스트 구성의 테스트 구성에 연결할 수 있습니다. Azure Portal는 테스트 그룹당 하나의 테스트 구성만 허용 하지만 ARMClient을 사용 하 여 추가를 추가할 수 있습니다.

    * **이름** – 테스트 구성의 이름을로 설정 합니다.
    * **프로토콜** – TCP, ICMP 또는 HTTP를 선택 합니다. HTTP를 HTTPS로 변경 하려면 **http** 를 프로토콜로 선택 하 고 포트를 **443** 로 선택 합니다.
        * **네트워크 테스트 구성 만들기** –이 확인란은 **프로토콜** 필드에서 **HTTP** 를 선택한 경우에만 표시 됩니다. 구성의 다른 곳에서 지정한 것과 동일한 소스와 대상을 사용 하는 다른 테스트 구성을 만들려면이 상자를 선택 합니다. 새로 만든 테스트 구성의 이름은 `<the name of your test configuration>_networkTestConfig` 입니다.
        * **경로 추적 사용 안 함** –이 필드는 프로토콜이 TCP 또는 ICMP 인 테스트 그룹에 적용 됩니다. 원본에서 토폴로지 및 홉 단위 RTT를 검색 하지 못하게 하려면이 상자를 선택 합니다.
    * **대상 포트** – 선택한 대상 포트를 사용 하 여이 필드를 사용자 지정할 수 있습니다.
    * **테스트 빈도** –이 필드를 사용 하 여 원본에서 지정한 프로토콜 및 포트의 대상을 ping 하는 빈도를 선택할 수 있습니다. 30 초, 1 분, 5 분, 15 분 또는 30 분을 선택할 수 있습니다. 원본은 사용자가 선택한 값을 기준으로 대상에 대 한 연결을 테스트 합니다.  예를 들어 30 초를 선택 하면 소스는 30 초 동안 한 번 이상 대상에 대 한 연결을 확인 합니다.
    * **성공 임계값** – 다음 네트워크 매개 변수에 대 한 임계값을 설정할 수 있습니다.
       * **검사 실패** – 원본에서 지정한 조건을 사용 하 여 대상에 대 한 연결을 확인할 때 실패할 수 있는 검사 비율을 설정 합니다. TCP 또는 ICMP 프로토콜의 경우 실패 한 검사의 백분율이 패킷 손실의 백분율에 동일시 수 있습니다. HTTP 프로토콜의 경우이 필드는 응답을 받지 못한 HTTP 요청 비율을 나타냅니다.
       * **라운드트립 시간** – 소스에서 테스트 구성을 통해 대상에 연결 하는 데 걸리는 시간 (초)을 설정 합니다.
    
       ![연결 모니터에서 테스트 구성을 설정할 위치를 보여 주는 스크린샷](./media/connection-monitor-2-preview/add-test-config.png)


## <a name="scale-limits"></a>크기 제한

연결 모니터의 크기 제한은 다음과 같습니다.

* 지역별 구독 당 최대 연결 모니터 수: 100
* 연결당 최대 테스트 그룹 수 모니터: 20
* 연결 당 최대 원본 및 대상 모니터: 100
* 연결 당 최대 테스트 구성 모니터: 2 Azure Portal 통해

## <a name="next-steps"></a>다음 단계

* [모니터링 데이터를 분석 하 고 경고를 설정 하는 방법](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts) 알아보기
* [네트워크에서 문제를 진단 하는 방법을](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network) 알아봅니다.
