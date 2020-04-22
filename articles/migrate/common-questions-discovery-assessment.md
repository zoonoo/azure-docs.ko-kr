---
title: Azure 마이그레이션에서 검색, 평가 및 종속성 분석에 대한 질문
description: Azure 마이그레이션에서 검색, 평가 및 종속성 분석에 대한 일반적인 질문에 대한 답변을 가져옵니다.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: b4b2a50bc88768d46c82f6bce73447dc901e5dfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681915"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>검색, 평가 및 종속성 분석 - 일반적인 질문

이 문서에서는 Azure 마이그레이션에서 검색, 평가 및 종속성 분석에 대한 일반적인 질문에 대해 설명합니다. 다른 질문이 있는 경우 다음 리소스를 확인하십시오.

- Azure 마이그레이션에 대한 [일반적인 질문](resources-faq.md)
- [Azure 마이그레이션 어플라이언스에](common-questions-appliance.md) 대한 질문
- 서버 [마이그레이션에](common-questions-server-migration.md) 대한 질문
- [Azure 마이그레이션 포럼에서](https://aka.ms/AzureMigrateForum) 질문에 대한 답변 받기


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Azure 마이그레이션을 통해 검색 및 평가에 지원되는 지역은 무엇입니까?

[지원되는](migrate-support-matrix.md#supported-geographies-public-cloud) 지역을 공용 및 정부 클라우드에 대해 [검토합니다.](migrate-support-matrix.md#supported-geographies-azure-government)


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>어플라이언스를 통해 몇 개의 VM을 검색할 수 있습니까?

단일 어플라이언스를 사용하여 최대 10,000개의 VM웨어 VM, 최대 5,000개의 Hyper-V VM 및 최대 250개의 물리적 서버를 검색할 수 있습니다. 컴퓨터가 더 많은 경우 [Hyper-V 평가 크기 조정,](scale-hyper-v-assessment.md) [VMware 평가 크기 조정](scale-vmware-assessment.md)또는 [실제 서버 평가 확장에](scale-physical-assessment.md)대해 읽어보십시오.

## <a name="i-cant-see-some-vm-types-in-azure-government"></a>Azure 정부에서 일부 VM 형식을 볼 수 없습니다.

평가 및 마이그레이션에 지원되는 VM 유형은 Azure 정부 위치의 가용성에 따라 다릅니다. Azure 정부에서 VM 형식을 [검토하고 비교할](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) 수 있습니다.


## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>VM의 크기가 변경되었습니다. 평가를 다시 실행할 수 있습니까?

Azure 마이그레이션 어플라이언스는 온-프레미스 환경에 대한 정보를 지속적으로 수집합니다.  평가는 온-프레미스 VM의 정시 스냅샷입니다. 평가하려는 VM의 설정을 변경하는 경우 다시 계산 옵션을 사용하여 최신 변경 내용으로 평가를 업데이트합니다.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>다중 테넌트 환경에서 VM을 검색하려면 어떻게 해야 합니까?

- **VMware**: 환경이 테넌트 간에 공유되고 다른 테넌트의 구독에서 테넌트의 VM을 검색하지 않으려면 검색하려는 VM만 액세스할 수 있는 VMware vCenter Server 자격 증명을 만듭니다. 그런 다음 Azure 마이그레이션 어플라이언스에서 검색을 시작할 때 해당 자격 증명을 사용합니다.
- **하이퍼-V**: 디스커버리는 하이퍼-V 호스트 자격 증명을 사용합니다. VM이 동일한 Hyper-V 호스트를 공유하는 경우 현재 검색을 분리할 수 있는 방법이 없습니다.  

## <a name="do-i-need-vcenter-server"></a>vCenter 서버가 필요합니까?

예. Azure 마이그레이션에서는 검색을 수행하기 위해 VMware 환경에서 vCenter 서버가 필요합니다. Azure 마이그레이션은 vCenter Server에서 관리하지 않는 ESXi 호스트의 검색을 지원하지 않습니다.

## <a name="what-are-the-sizing-options"></a>크기 조정 옵션은 무엇입니까?

온-프레미스 크기 조정을 통해 Azure Migrate는 평가를 위해 VM 성능 데이터를 고려하지 않습니다. Azure 마이그레이션은 온-프레미스 구성을 기반으로 VM 크기를 평가합니다. 성능 기반 크기 조정을 사용하면 크기 조정이 사용률 데이터를 기반으로 합니다.

예를 들어 온-프레미스 VM에 50%의 CPU 사용률과 50%의 메모리 사용률에서 4개의 코어와 8GB의 메모리가 있는 경우:
- 온-프레미스 크기 조정은 4개의 코어와 8GB의 메모리가 있는 Azure VM SKU를 권장합니다.
- 성능 기반 크기 조정은 사용률을 고려하기 때문에 두 개의 코어와 4GB의 메모리가 있는 VM SKU를 권장합니다.

마찬가지로 디스크 크기 조정은 크기 조정 기준 및 저장소 유형에 따라 다릅니다.
- 크기 조정 기준이 성능 기반이고 저장소 유형이 자동인 경우 Azure Migrate는 대상 디스크 유형(표준 또는 Premium)을 식별할 때 디스크의 IOPS 및 처리량 값을 고려합니다.
- 크기 조정 기준이 성능 기반이고 저장소 유형이 프리미엄인 경우 Azure Migrate는 온-프레미스 디스크의 크기에 따라 프리미엄 디스크 SKU를 권장합니다. 크기 조정이 온-프레미스이고 저장소 유형이 표준 또는 Premium인 경우 디스크 크기 조정에 동일한 논리가 적용됩니다.

## <a name="does-performance-history-and-utilization-affect-sizing"></a>성능 기록 및 사용률이 크기 조정에 영향을 미칩니까?

예. 성능 기록 및 사용률은 Azure 마이그레이션의 크기 조정에 영향을 미칩니다.

### <a name="performance-history"></a>성능 기록

성능 기반 크기 조정에만 사용할 수 있도록 Azure Migrate는 온-프레미스 시스템의 성능 기록을 수집한 다음 이를 사용하여 Azure에서 VM 크기 및 디스크 유형을 권장합니다.

1. 어플라이언스는 온-프레미스 환경을 지속적으로 프로파일화하여 20초마다 실시간 사용률 데이터를 수집합니다.
1. 어플라이언스는 수집된 20초 샘플을 롤업하고 이를 사용하여 15분마다 단일 데이터 포인트를 만듭니다.
1. 데이터 점을 만들기 위해 어플라이언스는 모든 20초 샘플에서 피크 값을 선택합니다.
1. 어플라이언스는 데이터 포인트를 Azure로 보냅니다.

### <a name="utilization"></a>사용률

Azure에서 평가를 만들 때 성능 기간 및 설정된 성능 기록 백분위수 값에 따라 Azure Migrate는 유효 사용률 값을 계산한 다음 크기 조정에 사용합니다.

예를 들어 성능 기간을 1일로 설정하고 백분위수 값을 95번째 백분위수로 설정하면 Azure Migrate는 수집기에서 보낸 15분 샘플 포인트를 오름차순으로 정렬합니다. 95번째 백분위수 값을 유효 사용률로 선택합니다.

95번째 백분위수 값을 사용하면 이상값을 무시합니다. Azure Migrate에서 99번째 백분위수를 사용하는 경우 이상값이 포함될 수 있습니다. 이상값을 놓치지 않고 해당 기간의 최대 사용량을 선택하려면 Azure Migrate를 설정하여 99번째 백분위수를 사용하도록 설정합니다.

## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>검색 소스를 어플라이언스로 사용하여 가져오기 기반 평가는 평가와 어떻게 다른가요?

가져오기 기반 평가는 CSV 파일을 사용하여 Azure Migrate로 가져온 컴퓨터로 만든 평가입니다. 서버 이름, 코어, 메모리 및 운영 체제의 네 가지 필드만 가져와야 합니다. 다음은 주의해야 할 몇 가지 사항입니다. 
 - 준비 기준은 부팅 유형 매개 변수에 대한 가져오기 기반 평가에서 덜 엄격합니다. 부팅 유형이 제공되지 않으면 컴퓨터가 BIOS 부팅 유형이 있고 컴퓨터가 **조건부 준비로**표시되지 않은 것으로 가정합니다. 검색 소스를 어플라이언스로 평가할 때 부팅 유형이 없는 경우 준비 상태가 **조건부 준비됨으로** 표시됩니다. 이러한 준비 성 계산의 차이는 사용자가 가져오기 기반 평가가 수행될 때 마이그레이션 계획의 초기 단계에서 컴퓨터에 대한 모든 정보를 갖지 못할 수 있기 때문입니다. 
 - 성능 기반 가져오기 평가는 올바른 크기 조정 계산을 위해 사용자가 제공한 사용률 값을 사용합니다. 사용률 값은 사용자가 제공하므로 **평가** 속성에서 성능 기록 및 **백분위수 사용률이** 비활성화됩니다. 검색 소스를 어플라이언스로 사용하는 평가에서 선택한 백분위수 값은 어플라이언스에서 수집한 성능 데이터에서 선택됩니다.

## <a name="what-is-dependency-visualization"></a>종속성 시각화란?

종속성 시각화를 사용하면 VM 그룹을 평가하여 보다 안심하고 마이그레이션할 수 있습니다. 종속성 시각화는 평가를 실행하기 전에 컴퓨터 종속성을 교차 검사합니다. 아무 것도 남지 않도록 하고 Azure로 마이그레이션할 때 예기치 않은 중단을 방지하는 데 도움이 됩니다. Azure Migrate는 Azure Monitor의 서비스 맵 솔루션을 사용하여 종속성 시각화를 사용하도록 설정합니다. [자세히 알아보기](concepts-dependency-visualization.md).

> [!NOTE]
> Azure 정부에서는 에이전트 기반 종속성 분석을 사용할 수 없습니다. 에이전트 없는 종속성 분석을 사용할 수 있습니다.

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>에이전트 기반과 에이전트리스의 차이점은 무엇입니까?

에이전트 없는 시각화와 에이전트 기반 시각화 간의 차이점이 표에 요약되어 있습니다.

**요구 사항** | **에이전트 없음** | **에이전트 기반**
--- | --- | ---
지원 | 이 옵션은 현재 미리 보기 중이며 VMware VM에서만 사용할 수 있습니다. 지원되는 운영 체제를 [검토합니다.](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) | 일반 공급 (GA).
에이전트 | 교차 확인하려는 컴퓨터에 에이전트를 설치할 필요가 없습니다. | 분석하려는 각 온-프레미스 컴퓨터에 설치할 에이전트: [MMA(Microsoft 모니터링 에이전트)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)및 [종속성 에이전트](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
사전 요구 사항 | 필수 구성 조건 및 배포 요구 사항을 [검토합니다.](concepts-dependency-visualization.md#agentless-analysis) | 필수 구성 조건 및 배포 요구 사항을 [검토합니다.](concepts-dependency-visualization.md#agent-based-analysis)
Log Analytics | 필요하지 않습니다. | Azure Migrate는 종속성 시각화를 위해 [Azure Monitor 로그의](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) [서비스 맵](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) 솔루션을 사용합니다. [자세히 알아보기](concepts-dependency-visualization.md#agent-based-analysis).
작동 방법 | 종속성 시각화에 사용하도록 설정된 컴퓨터에서 TCP 연결 데이터를 캡처합니다. 검색 후 5분 간격으로 데이터를 수집합니다. | 컴퓨터에 설치된 서비스 맵 에이전트는 각 프로세스에 대한 TCP 프로세스 및 인바운드/아웃바운드 연결에 대한 데이터를 수집합니다.
데이터 | 소스 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름입니다.<br/><br/> 대상 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름 및 포트입니다. | 소스 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름입니다.<br/><br/> 대상 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름 및 포트입니다.<br/><br/> 연결 수, 대기 시간 및 데이터 전송 정보가 수집되어 Log Analytics 쿼리에 사용할 수 있습니다. 
시각화 | 단일 서버의 종속성 맵은 1시간에서 30일 동안 볼 수 있습니다. | 단일 서버의 종속성 맵입니다.<br/><br/> 지도는 한 시간 동안만 볼 수 있습니다.<br/><br/> 서버 그룹의 종속성 맵입니다.<br/><br/> 맵 보기에서 그룹의 서버를 추가 및 제거합니다.
데이터 내보내기 | 현재 테이블 형식으로 다운로드할 수 없습니다. | 로그 분석을 통해 데이터를 쿼리할 수 있습니다.

## <a name="do-i-pay-for-dependency-visualization"></a>종속성 시각화에 대한 비용을 지불합니까?

아니요. [Azure 마이그레이션 가격에](https://azure.microsoft.com/pricing/details/azure-migrate/)대해 자세히 알아봅니다.

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>에이전트 기반 종속성 시각화를 위해 무엇을 설치합니까?

에이전트 기반 종속성 시각화를 사용하려면 평가하려는 각 온-프레미스 컴퓨터에서 에이전트를 다운로드하고 설치합니다.

- [마이크로소프트 모니터링 에이전트 (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)
- [종속성 에이전트](../azure-monitor/platform/agents-overview.md#dependency-agent)
- 인터넷에 연결되지 않은 컴퓨터가 있는 경우 Log Analytics 게이트웨이를 다운로드하여 설치합니다.

에이전트 기반 종속성 시각화를 사용하는 경우에만 이러한 에이전트가 필요합니다.

## <a name="can-i-use-an-existing-workspace"></a>기존 작업 영역을 사용해도 되나요?

예. 에이전트 기반 종속성 시각화의 경우 기존 작업 영역을 마이그레이션 프로젝트에 연결하고 종속성 시각화에 사용할 수 있습니다. 

## <a name="can-i-export-the-dependency-visualization-report"></a>종속성 시각화 보고서를 내보낼 수 있나요?

아니요, 에이전트 기반 시각화의 종속성 시각화 보고서를 내보낼 수 없습니다. 그러나 Azure Migrate는 서비스 맵을 사용하며 [서비스 맵 REST API를](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) 사용하여 JSON 형식으로 종속성을 검색할 수 있습니다.

## <a name="can-i-automate-agent-installation"></a>에이전트 설치를 자동화할 수 있습니까?

에이전트 기반 종속성 시각화의 경우:

- 스크립트를 사용하여 [종속성 에이전트를 설치합니다.](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)
- MMA의 경우 [명령줄 또는 자동화를 사용하거나](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) [스크립트를](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)사용합니다.
- 스크립트 외에도 Microsoft 엔드포인트 구성 관리자 및 [Intigua와](https://www.intigua.com/getting-started-intigua-for-azure-migration) 같은 배포 도구를 사용하여 에이전트를 배포할 수 있습니다.

## <a name="what-operating-systems-does-mma-support"></a>MMA는 어떤 운영 체제를 지원합니까?

- [MMA가 지원하는 Windows 운영 체제](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)목록을 봅니다.
- [MMA가 지원하는 Linux 운영 체제](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)목록을 봅니다.

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>한 시간 이상 종속성을 시각화할 수 있습니까?

에이전트 기반 시각화의 경우 최대 1시간 동안 종속성을 시각화할 수 있습니다. 기록의 특정 날짜로 한 달까지 돌아갈 수 있지만 시각화의 최대 기간은 1시간입니다. 예를 들어 종속성 맵의 시간 기간을 사용하여 어제의 종속성을 볼 수 있지만 한 시간 동안만 종속성을 볼 수 있습니다. 그러나 Azure Monitor 로그를 사용하여 [종속성 데이터를](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) 더 긴 기간 동안 쿼리할 수 있습니다.

에이전트 없는 시각화의 경우 1시간에서 30일 사이의 기간 동안 단일 서버의 종속성 맵을 볼 수 있습니다.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>10개 이상의 VM 그룹에 대한 종속성을 시각화할 수 있습니까?

최대 10개의 VM이 있는 그룹에 대한 [종속성을 시각화할](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) 수 있습니다. VM이 10개 이상인 그룹이 있는 경우 그룹을 더 작은 그룹으로 분할한 다음 종속성을 시각화하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

Azure [마이그레이션 개요를](migrate-services-overview.md)읽습니다.
