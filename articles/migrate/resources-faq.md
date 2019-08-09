---
title: Azure Migrate - FAQ(질문과 대답) | Microsoft Docs
description: Azure Migrate에 대한 질문과 대답 해결
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: snehaa
ms.openlocfilehash: bf591306220b2c8c1e6c8166686836d96432fc7d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856269"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate - FAQ(질문과 대답)

이 문서는 Azure Migrate에 대한 질문과 대답을 제공합니다. 이 문서를 읽은 후 질문이 있다면 [Azure Migrate 포럼](https://aka.ms/AzureMigrateForum)에 게시해 주세요.

## <a name="general"></a>일반

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Azure Migrate에서 지원되는 Azure 지역은 어디인가요?

여기에서 [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) 및 [hyper-v](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)에 대 한 목록을 찾을 수 있습니다.

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Azure Migrate는 Azure Site Recovery와 어떻게 다른가요?

Azure Migrate는 Azure에 대 한 컴퓨터 및 워크 로드의 검색, 평가 및 마이그레이션을 시작, 실행 및 추적 하는 중앙 허브를 제공 합니다. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) 은 재해 복구 솔루션입니다. Azure Migrate 서버 마이그레이션은 백 엔드의 Azure Site Recovery를 활용 하 여 온-프레미스 컴퓨터의 리프트 앤 시프트 마이그레이션에 대 한 마이그레이션 시나리오를 사용 하도록 설정 합니다.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Azure Migrate 어플라이언스 (VMware/물리적 서버)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Azure Migrate 어플라이언스는 Azure에 어떻게 연결 되나요?

인터넷을 통해 연결 하거나 공용/Microsoft 피어 링에서 Express 경로를 사용할 수 있습니다.

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Azure Migrate 서버 평가 및 마이그레이션에 필요한 네트워크 연결 요구 사항

Azure Migrate Azure와 통신 하는 데 필요한 Url 및 포트는 [VMWare](migrate-support-matrix-vmware.md) 및 [hyper-v](migrate-support-matrix-hyper-v.md) 지원 매트릭스를 검토 하세요.

### <a name="can-i-harden-the-appliance-vm-i-set-up-with-the-template"></a>템플릿으로 설정한 어플라이언스 VM을 강화할 수 있나요?

Azure Migrate 어플라이언스에 필요한 통신 및 방화벽 규칙이 그대로 유지 되는 한 추가 구성 요소 (예: 바이러스 백신)를 템플릿에 추가할 수 있습니다.   

### <a name="what-data-is-collected-by-azure-migrate-appliance"></a>Azure Migrate 어플라이언스에서 수집 되는 데이터

Azure Migrate 어플라이언스에서 수집 된 데이터에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware)를 참조 하세요.

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>분석 된 VMware 또는 Hyper-v 환경에 대 한 성능에 영향이 있나요?

성능 데이터의 지속적인 프로 파일링을 통해 Azure Migrate 어플라이언스는 VM 성능 데이터를 측정 하기 위해 온-프레미스 컴퓨터를 프로 파일링 합니다. 이는 vCenter Server 뿐만 아니라 Hyper-v/ESXi 호스트에 대 한 성능에 거의 영향을 주지 않습니다.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>수집된 데이터는 어디에 저장되며 저장 기간은 얼마나 되나요?

Azure Migrate 어플라이언스에 의해 수집 된 데이터는 마이그레이션 프로젝트를 만들 때 지정 하는 Azure 위치에 저장 됩니다. 데이터는 Microsoft 구독에 안전 하 게 저장 되며 Azure Migrate 프로젝트를 삭제 하면 삭제 됩니다.

종속성 시각화의 경우 Vm에 에이전트를 설치 하는 경우 종속성 에이전트에서 수집 하는 데이터는 Azure 구독에서 만든 Log Analytics 작업 영역에 US에 저장 됩니다. 구독에서 Log Analytics 작업 영역을 삭제하면 이 데이터가 삭제됩니다. [자세히 알아보기](concepts-dependency-visualization.md).

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-appliance-during-continuous-profiling"></a>연속 프로 파일링 중 Azure Migrate 어플라이언스에서 업로드 한 데이터 볼륨은 무엇 인가요?

Azure Migrate로 전송 되는 데이터의 볼륨은 여러 매개 변수에 따라 달라 집니다. 지정 된 숫자를 제공 하기 위해 10 대의 컴퓨터가 있는 Azure Migrate 프로젝트 (각각 하나의 디스크와 하나의 NIC)는 하루에 50 MB를 보냅니다. 이 값은 대략적인 값 이며,이 값은 Nic 및 디스크의 데이터 요소 수에 따라 변경 됩니다 (컴퓨터, Nic 또는 디스크 수가 증가 하는 경우 전송 되는 데이터는 선형이 아님).

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>데이터가 미사용 및 전송 중에 암호화 되나요?

둘 다에 대해 예입니다. 메타 데이터는 https를 통해 인터넷을 통해 Azure Migrate 서비스로 안전 하 게 전송 됩니다. 메타 데이터는 [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)에 저장 되 고, Microsoft 구독의 [Azure blob storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 에 저장 되며, 미사용 암호화 됩니다.

종속성 에이전트에서 수집 하는 데이터는 전송 (보안 HTTPS) 에서도 암호화 되며 사용자 구독의 Log Analytics 작업 영역에 저장 됩니다. 또한 미사용 암호화 됩니다.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Azure Migrate 어플라이언스는 vCenter Server 및 Azure Migrate 서비스와 어떻게 통신 하나요?

어플라이언스는 어플라이언스를 설정할 때 제공 된 자격 증명을 사용 하 여 vCenter Server (포트 443)에 연결 됩니다. VMware PowerCLI를 사용 하 여 vCenter Server을 쿼리하여 vCenter Server에서 관리 하는 Vm에 대 한 메타 데이터를 수집 합니다. Vm (코어, 메모리, 디스크, NIC 등)에 대 한 구성 데이터와 지난 달의 각 VM에 대 한 성능 기록을 수집 합니다. 그러면 수집 된 메타 데이터가 평가를 위해 HTTPS를 통해 인터넷을 통해 Azure Migrate 서버 평가로 보내집니다.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>동일한 어플라이언스를 여러 vCenter 서버에 연결할 수 있나요?

아니요. 어플라이언스와 vCenter Server 사이에 일대일 매핑이 있습니다. 여러 vCenter 서버에서 Vm을 검색 해야 하는 경우 여러 어플라이언스를 배포 해야 합니다. 


### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>내 컴퓨터 크기를 변경했습니다. 평가를 다시 실행할 수 있나요?

Azure Migrate 어플라이언스는 온-프레미스 환경에 대 한 정보를 지속적으로 수집 합니다. 그러나 평가는 온-프레미스 Vm의 특정 시점 스냅숏입니다. 평가 하려는 VM의 설정을 변경 하는 경우 ' 다시 계산 ' 옵션을 사용 하 여 평가를 최신 변경 내용으로 업데이트 합니다.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate-server-assessment"></a>Azure Migrate Server 평가에서 다중 테 넌 트 환경을 검색 하려면 어떻게 해야 하나요?

VMware의 경우, 테 넌 트 간에 공유 되는 환경에서 다른 테 넌 트의 구독에 있는 한 테 넌 트의 Vm을 검색 하지 않으려는 경우 검색 하려는 Vm에만 액세스할 수 있는 vCenter Server 자격 증명을 만듭니다. 그런 다음 Azure Migrate 어플라이언스에서 검색을 시작 때 자격 증명을 사용 합니다.

Hyper-v의 경우 검색은 Hyper-v 호스트 자격 증명을 사용 하 고, Vm이 동일한 Hyper-v 호스트를 공유 하는 경우 현재 검색을 분리할 수 있는 방법이 없습니다.  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>단일 마이그레이션 어플라이언스를 사용 하 여 검색할 수 있는 Vm 수는 몇 개입니까?

단일 마이그레이션 어플라이언스를 사용 하 여 최대 1만 VMware Vm 및 최대 5000 Hyper-v Vm을 검색할 수 있습니다.  온-프레미스 환경에 더 많은 컴퓨터가 있는 경우 [hyper-v](scale-hyper-v-assessment.md) 및 [VMware](scale-vmware-assessment.md) 평가를 확장 하는 방법을 알아보세요.

## <a name="azure-migrate-server-assessment"></a>Azure Migrate: Server Assessment

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate: 서버 평가는 물리적 서버에 대 한 평가를 지원 하나요?

아니요, Azure Migrate 현재 물리적 서버의 평가를 지원 하지 않습니다.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate에는 VMware 환경을 검색하기 위해 vCenter Server가 필요한가요?

예, Azure Migrate VMware 환경을 검색 하는 vCenter Server 필요 합니다. VCenter Server으로 관리 되지 않는 ESXi 호스트의 검색은 지원 하지 않습니다.

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate 사용 간의 차이점은 무엇 인가요? 서버 평가 및 맵 도구 키트?

Azure Migrate: 서버 평가는 마이그레이션 준비에 도움을 주는 마이그레이션 평가와 Azure로의 마이그레이션을 위한 워크 로드 평가를 제공 합니다. [Microsoft 평가 및 계획 (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) 에는 최신 버전의 Windows 클라이언트 및 서버 운영 체제에 대 한 마이그레이션 계획 및 소프트웨어 사용 추적 등의 다른 기능이 있습니다. 이러한 시나리오의 경우 MAP Toolkit을 계속 사용합니다.

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>Azure Migrate 방법: 서버 평가가 Azure Site Recovery Deployment Planner 다릅니다.

Azure Migrate: 서버 평가는 마이그레이션 계획 도구입니다. Azure Site Recovery Deployment Planner은 재해 복구 계획 도구입니다.

- **VMware/hyper-v에서 Azure로 마이그레이션**: 온-프레미스 서버를 Azure로 마이그레이션하려면 Azure Migrate를 사용 합니다. 마이그레이션 계획을 위한 서버 평가 도구입니다. 이 도구는 온-프레미스 워크 로드를 평가 하 고 Azure로 마이그레이션하는 데 도움이 되는 지침, 통찰력 및 메커니즘을 제공 합니다. 마이그레이션 계획을 사용할 준비가 되 면 Azure Migrate와 같은 도구를 사용할 수 있습니다. 서버 마이그레이션-컴퓨터를 Azure로 마이그레이션합니다.
- **VMware/Hyper-V에서 Azure로의 재해 복구**: Site Recovery를 사용 하 여 Azure로의 재해 복구를 위해 재해 복구 계획을 위해 Site Recovery Deployment Planner를 사용 합니다. Site Recovery Deployment Planner 온-프레미스 환경에 대 한 심층 Site Recovery 특정 평가를 수행 합니다. 복제와 같은 성공적인 재해 작업 및 Vm 장애 조치 (failover)를 Site Recovery 하는 데 필요한 권장 사항을 제공 합니다.

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>EA (기업계약) 기반 비용 예측을 지원할 Azure Migrate 있나요?

Azure Migrate 현재 [기업계약 제품](https://azure.microsoft.com/offers/enterprise-agreement-support/)의 비용 예측을 지원 하지 않습니다. 해결 방법은 제품으로 종 량 제를 지정 하 고 평가 속성의 ' 할인 ' 필드에서 할인 비율 (구독에 적용 가능)을 수동으로 지정 하는 것입니다.

  ![할인](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>온-프레미스 크기 조정 및 성능 기반 크기 조정의 차이점은 무엇 인가요?

- 온-프레미스 크기 조정으로 Azure Migrate VM 성능 데이터를 고려 하지 않습니다. 온-프레미스 구성에 따라 Vm의 크기를 조정 합니다. -성능 기반 크기 조정에서 크기는 사용률 데이터를 기반으로 합니다.
- 예를 들어 온-프레미스 VM에 4 개의 코어 및 50 50%의 메모리 사용률이 있는 8gb 메모리가 있는 경우 온-프레미스 크기 조정으로 4 개의 코어와 8gb 메모리를 사용 하는 Azure VM SKU를 권장 합니다. 그러나 성능 기반 크기 조정에서는 두 코어의 VM SKU와 4gb를 사용 하는 것이 좋습니다 .이는 사용률 백분율이 고려 되기 때문입니다.
- 마찬가지로 디스크 크기 조정은 두 가지 평가 속성인 크기 조정 기준 및 저장소 유형에 따라 달라 집니다.
= 크기 조정 기준이 성능 기반이 고 저장소 유형이 자동 인 경우 대상 디스크 유형 (Standard 또는 Premium)을 식별 하는 경우 디스크의 IOPS 및 처리량 값을 고려 합니다.
- 크기 조정 기준이 성능 기반이 고 저장소 유형이 프리미엄 인 경우 프리미엄 디스크를 사용 하는 것이 좋습니다. Premium disk SKU는 온-프레미스 디스크의 크기에 따라 선택 됩니다. 크기 조정 기준이 온-프레미스 크기 조정과 저장소 유형이 표준 또는 프리미엄 인 경우 동일한 논리를 사용 하 여 디스크 크기를 조정 합니다.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>성능 기록 및 백분위수 활용률이 크기 추천에 어떤 영향을 주나요?

이러한 속성은 성능 기반 크기 조정에만 적용됩니다.

- Azure Migrate 온-프레미스 컴퓨터의 성능 기록을 수집 하 고이를 사용 하 여 Azure에서 VM 크기 및 디스크 형식을 권장 합니다.
- 어플라이언스는 온-프레미스 환경을 지속적으로 프로 파일링 하 여 20 초 마다 실시간 사용률 데이터를 수집 합니다. 이 어플라이언스는 20초 샘플을 롤업하고 15분마다 데이터 요소를 하나씩 만듭니다. 단일 데이터 요소를 만들기 위해, 이 어플라이언스는 20초 샘플에서 최고 값을 선택하여 Azure로 전송합니다.
- Azure에서 평가를 만들 때 (성능 기간 및 성능 기록 백분위 수 값을 기준으로) Azure Migrate 유효한 사용률 값을 계산 하 고 크기 조정에 사용 합니다.
- 예를 들어 성능 기간을 1 일로 설정 하 고 백분위 수 값을 95 백분위 수로 설정 하는 경우 Azure Migrate는 수집기에서 마지막 날에 보낸 15 분 샘플 요소를 사용 하 고, 오름차순으로 정렬 하 고, 95 번째 백분위 수 값을 유효 사용률.
- 95 번째 백분위 수 값은 99 번째 백분위 수를 사용 하는 경우 발생할 수 있는 이상 값을 무시 하도록 합니다. 해당 기간의 최대 사용량을 선택하고 이상값을 누락하지 않으려면 99번째 백분위 수를 선택해야 합니다.

### <a name="what-is-dependency-visualization"></a>종속성 시각화란?

종속성 시각화를 사용 하면 더 강력한 마이그레이션을 위해 Vm 그룹을 평가할 수 있습니다. 평가를 실행 하기 전에 컴퓨터 종속성을 교차 확인 합니다. 종속성 시각화를 사용 하면 아무것도 유지 하지 않고 Azure로 마이그레이션할 때 예기치 않은 중단을 방지할 수 있습니다. Azure Migrate Azure Monitor 로그의 서비스 맵 솔루션을 활용 하 여 종속성 시각화를 사용 하도록 설정 합니다.

> [!NOTE]
> Azure Government에서는 종속성 시각화 기능을 사용할 수 없습니다.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>종속성 시각화 기능을 사용하면 비용을 지불해야 하나요?

아니요. Azure Migrate 가격 책정에 대해 [자세히 알아봅니다](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>종속성 시각화를 사용하려면 무엇을 설치해야 하나요?

종속성 시각화를 사용하려면 평가할 각 온-프레미스 머신에 에이전트를 다운로드하여 설치해야 합니다.

- 각 머신에 [MMA(Microsoft Monitoring Agent)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)를 설치해야 합니다.
- 각 머신에 [종속성 에이전트](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure)를 설치해야 합니다.
- 또한 인터넷에 연결되지 않은 머신이 있으면 해당 머신에 Log Analytics 게이트웨이를 다운로드하여 설치해야 합니다.

종속성 시각화를 사용 하지 않는 경우 이러한 에이전트가 필요 하지 않습니다.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>종속성 시각화에 기존 작업 영역을 사용할 수 있나요?

예, 기존 작업 영역을 마이그레이션 프로젝트에 연결 하 고 종속성 시각화에 활용할 수 있습니다. [자세히 알아보기](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>종속성 시각화 보고서를 내보낼 수 있나요?

아니요. 종속성 시각화를 내보낼 수 없습니다. 그러나 Azure Migrate에서 종속성 시각화에 서비스 맵을 사용하므로 [서비스 맵 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections)를 사용하여 종속성을 json 형식으로 가져올 수 있습니다.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>MMA(Microsoft Monitoring Agent) 및 종속성 에이전트 설치를 자동화하려면 어떻게 해야 하나요?

[이 스크립트를 사용 하](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) 여 에이전트를 설치 합니다. 명령줄 또는 자동화를 사용 하 여 MMA을 설치 하려면 [다음 지침을 따르세요](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) . MMA의 경우 [이 스크립트](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)를 활용 합니다.

스크립트 외에도 System Center Configuration Manager, [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) 등과 같은 배포 도구를 사용 하 여 에이전트를 배포할 수 있습니다.

### <a name="what-operating-systems-are-supported-by-mma"></a>MMA에서 지원 되는 운영 체제는 무엇 인가요?

- MMA에서 지 원하는 Windows 운영 체제의 목록을 [검토](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) 합니다.
- MMA에서 지 원하는 Linux 운영 체제의 목록을 [검토](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) 합니다.

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>종속성 에이전트에서 지원 되는 운영 체제는 무엇 인가요?

종속성 에이전트에서 지 원하는 Windows 운영 체제를 [검토](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems) 합니다.
종속성 에이전트에서 지 원하는 Linux 운영 체제의 목록을 [검토](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems) 합니다.

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>1 시간 이상 Azure Migrate에서 종속성을 시각화할 수 있나요?
아니요, 최대 1 시간 동안 종속성을 시각화할 수 있습니다. 최근 달까지 기록의 특정 날짜로 다시 이동할 수 있지만 시각화의 최대 기간은 1 시간입니다. 예를 들어 종속성 맵의 기간을 사용 하 여 어제 종속성을 볼 수 있지만 1 시간 동안만 볼 수 있습니다. 그러나 Azure Monitor 로그를 사용 하 여 더 긴 기간 동안 [종속성 데이터를 쿼리할](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) 수 있습니다.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>종속성 시각화가 10대를 초과하는 VM을 사용하는 그룹에 지원되나요?
최대 10 개의 Vm이 있는 [그룹에 대 한 종속성을 시각화할](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) 수 있습니다. 10 개 이상의 Vm이 포함 된 그룹이 있는 경우 그룹을 작은 그룹으로 분할 한 다음 종속성을 시각화 하는 것이 좋습니다.

## <a name="azure-migrate-server-migration"></a>Azure Migrate: 준비

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>Azure Migrate 방법: Azure Site Recovery와 다른 서버 마이그레이션

Azure Migrate: 서버 마이그레이션은 VMware Vm의 에이전트 기반 마이그레이션, Hyper-v Vm 마이그레이션 및 물리적 서버를 Azure로 마이그레이션에 대 한 Site Recovery의 복제 엔진을 활용 합니다. VMware Vm을 마이그레이션하는 에이전트 없는 옵션은 기본적으로 서버 마이그레이션에 내장 되어 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure Migrate 개요](migrate-services-overview.md)를 읽어보세요.
