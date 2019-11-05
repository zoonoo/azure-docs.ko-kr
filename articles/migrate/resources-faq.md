---
title: Azure Migrate에 대 한 일반적인 질문
description: Azure Migrate에 대 한 일반적인 질문과 대답을 다룹니다.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: snehaa
ms.openlocfilehash: 1663e1d418610bcfa2ada28770944bfc8cbf908f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73480063"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: 일반적인 질문

이 문서에서는 Azure Migrate에 대 한 일반적인 질문에 답변 합니다. 이 문서를 읽은 후 추가 쿼리가 있는 경우 [Azure Migrate 포럼](https://aka.ms/AzureMigrateForum)에 게시 하세요.

## <a name="general"></a>일반

### <a name="which-azure-geographies-are-supported"></a>지원 되는 Azure 지역

[VMWARE vm](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) 및 [hyper-v vm](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)에 대해 지원 되는 Azure Migrate 지역을 검토 합니다.

### <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Azure Migrate와 Site Recovery의 차이점은 무엇 인가요?

Azure Migrate는 Azure에 대 한 온-프레미스 컴퓨터 및 워크 로드의 검색, 평가 및 마이그레이션을 관리 하 고 추적 하는 중앙 허브를 제공 합니다. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) 은 재해 복구 솔루션입니다. Azure Migrate: 서버 마이그레이션 도구는 일부 온-프레미스 컴퓨터의 리프트 앤 시프트 마이그레이션에 백 엔드 Site Recovery 기능을 사용 합니다.

### <a name="how-do-i-delete-an-azure-migrate-project"></a>어떻게 할까요? Azure Migrate 프로젝트를 삭제 하 시겠습니까?

프로젝트를 삭제 하려면 [다음 지침](how-to-delete-project.md) 을 따르세요. Azure Migrate 프로젝트에서 컴퓨터와 워크 로드를 검색 하 고, 평가 하 고, 마이그레이션할 때 생성 된 [리소스를 검토](how-to-delete-project.md#created-resources) 합니다.


## <a name="azure-migrate-appliance"></a>Azure Migrate 어플라이언스

### <a name="how-does-the-appliance-connect-to-azure"></a>어플라이언스는 Azure에 어떻게 연결 되나요?

인터넷을 통해 연결 하거나 공용/Microsoft 피어 링에서 Azure Express 경로를 사용할 수 있습니다.

### <a name="what-network-connectivity-is-needed-for-azure-migrate-server-assessment-and-migration"></a>Azure Migrate 서버 평가 및 마이그레이션에 필요한 네트워크 연결은 무엇 인가요?

Azure Migrate Azure와 통신 하는 데 필요한 Url 및 포트에 대 한 자세한 내용은 [VMware](migrate-support-matrix-vmware.md) 및 [hyper-v](migrate-support-matrix-hyper-v.md) 지원 매트릭스를 검토 하세요.

### <a name="can-i-harden-the-appliance-vm-created-with-the-template"></a>템플릿으로 만든 어플라이언스 VM을 강화할 수 있나요?

Azure Migrate 어플라이언스에 필요한 통신 및 방화벽 규칙을 그대로 유지 하는 한 구성 요소 (예: 바이러스 백신)를 템플릿에 추가할 수 있습니다.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Azure Migrate 어플라이언스에서 수집 되는 데이터는 무엇 인가요?

다음과 같이 어플라이언스에 의해 수집 된 데이터를 검토 합니다.
- VMware Vm에 대 한 [성능 데이터](migrate-appliance.md#collected-performance-data-vmware) 및 [메타 데이터](migrate-appliance.md#collected-metadata-vmware)
- Hyper-v Vm에 대 한 [성능 데이터](migrate-appliance.md#collected-performance-data-hyper-v) 및 [메타 데이터](migrate-appliance.md#collected-metadata-hyper-v)


### <a name="does-appliance-analysis-impact-performance"></a>어플라이언스 분석이 성능에 영향을 미칩니까?

Azure Migrate 어플라이언스는 VM 성능 데이터를 계속 측정 하기 위해 온-프레미스 컴퓨터를 계속 프로 파일링 합니다. 이 프로 파일링은 Hyper-v/ESXi 호스트 또는 vCenter Server의 성능에 거의 영향을 주지 않습니다.

### <a name="where-and-how-long-is-collected-data-stored"></a>수집 된 데이터는 어디에 저장 되나요?

Azure Migrate 어플라이언스에 의해 수집 된 데이터는 마이그레이션 프로젝트를 만들 때 선택 하는 Azure 위치에 저장 됩니다. 데이터는 Microsoft 구독에 안전 하 게 저장 되며 Azure Migrate 프로젝트를 삭제 하면 삭제 됩니다.

종속성 시각화의 경우 수집 된 데이터는 미국에 Azure 구독에서 만든 Log Analytics 작업 영역에 저장 됩니다. 구독에서 Log Analytics 작업 영역을 삭제하면 이 데이터가 삭제됩니다. 종속성 시각화에 [대해 자세히 알아보세요](concepts-dependency-visualization.md) .

### <a name="what-volume-of-data-is-uploaded-during-continuous-profiling"></a>연속 프로 파일링 중에 업로드 되는 데이터 볼륨은 무엇 인가요?

Azure Migrate로 전송 되는 데이터의 볼륨은 여러 매개 변수에 따라 달라 집니다. 볼륨을 파악 하기 위해 10 대의 컴퓨터가 있는 Azure Migrate 프로젝트 (각각 하나의 디스크와 하나의 NIC)는 하루에 50 MB를 보냅니다. 이 값은 근사값 이며, Nic 및 디스크에 대 한 데이터 요소 수에 따라 변경 됩니다. 컴퓨터, Nic 또는 디스크 수가 늘어나면 전송 되는 데이터의 증가는 비선형입니다.

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>데이터가 미사용 및 전송 중에 암호화 되나요?

예, 둘 다 있습니다.
- 메타 데이터는 HTTPS를 통해 인터넷을 통해 Azure Migrate 서비스로 안전 하 게 전송 됩니다.
- 메타 데이터는 [Azure Cosmos database](../cosmos-db/database-encryption-at-rest.md) 데이터베이스 및 Microsoft 구독의 [azure Blob storage](../storage/common/storage-service-encryption.md) 에 저장 됩니다. 메타 데이터는 미사용 암호화 됩니다.
- 종속성 분석에 대 한 데이터는 전송 중에도 암호화 됩니다 (보안 HTTPS). 구독의 Log Analytics 작업 영역에 저장 됩니다. 또한 미사용 암호화 됩니다.

### <a name="how-does-the-appliance-communicate-with-vcenter-server-and-azure-migrate"></a>어플라이언스는 vCenter Server 및 Azure Migrate와 어떻게 통신 하나요?

1. 어플라이언스는 어플라이언스를 설정할 때 제공한 자격 증명을 사용 하 여 vCenter Server (포트 443)에 연결 됩니다.
2. 어플라이언스는 VMware PowerCLI를 사용 하 여 vCenter Server를 쿼리하고 vCenter Server에서 관리 하는 Vm에 대 한 메타 데이터를 수집 합니다. Vm (코어, 메모리, 디스크, Nic) 및 지난 달의 각 VM에 대 한 성능 기록에 대 한 구성 데이터를 수집 합니다.
3. 그런 다음 수집 된 메타 데이터는 평가를 위해 HTTPS를 통해 인터넷을 통해 서버 평가로 Azure Migrate 전송 됩니다.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>동일한 어플라이언스를 여러 vCenter Server 인스턴스에 연결할 수 있나요?

아니요. 어플라이언스와 vCenter Server 사이에 일대일 매핑이 있습니다. 여러 vCenter Server 인스턴스에서 Vm을 검색 하려면 여러 어플라이언스를 배포 해야 합니다.


### <a name="machine-size-changed-can-i-run-the-assessment-again"></a>컴퓨터 크기가 변경 되었습니다. 평가를 다시 실행할 수 있나요?

Azure Migrate 어플라이언스는 온-프레미스 환경에 대 한 정보를 지속적으로 수집 합니다. 하지만 평가는 온-프레미스 Vm의 특정 시점 스냅숏입니다. 평가 하려는 VM의 설정을 변경 하는 경우 다시 계산 옵션을 사용 하 여 평가를 최신 변경 내용으로 업데이트 합니다.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment"></a>다중 테 넌 트 환경에서 검색을 수행 하려면 어떻게 해야 하나요?

- VMware의 경우 사용자 환경이 테 넌 트 간에 공유 되 고 다른 테 넌 트의 구독에 있는 한 테 넌 트의 Vm을 검색 하지 않으려는 경우 검색 하려는 Vm에만 액세스할 수 있는 vCenter Server 자격 증명을 만듭니다. 그런 다음 Azure Migrate 어플라이언스에서 검색을 시작할 때 해당 자격 증명을 사용 합니다.
- Hyper-v의 경우 검색에서 Hyper-v 호스트 자격 증명을 사용 합니다. Vm이 동일한 Hyper-v 호스트를 공유 하는 경우 현재 검색을 분리할 수 있는 방법이 없습니다.  

### <a name="how-many-vms-can-i-discover-with-a-single-appliance"></a>단일 어플라이언스로 검색할 수 있는 Vm은 몇 개입니까?

단일 마이그레이션 어플라이언스를 사용 하 여 최대 1만 VMware Vm 및 최대 5000 Hyper-v Vm을 검색할 수 있습니다. 온-프레미스 환경에 더 많은 컴퓨터가 있는 경우 [hyper-v](scale-hyper-v-assessment.md) 및 [VMware](scale-vmware-assessment.md) 평가를 확장 하는 방법을 알아보세요.

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>프로젝트에서 Azure Migrate 어플라이언스를 삭제할 수 있나요?

프로젝트에서 현재 어플라이언스 삭제가 지원 되지 않습니다.

- 어플라이언스를 삭제 하는 유일한 방법은 기기와 연결 된 Azure Migrate 프로젝트를 포함 하는 리소스 그룹을 삭제 하는 것입니다.
- 그러나 리소스 그룹을 삭제 하면 리소스 그룹의 프로젝트와 연결 된 다른 등록 된 어플라이언스, 검색 된 인벤토리, 평가 및 기타 모든 Azure 구성 요소도 삭제 됩니다.

## <a name="azure-migrate-server-assessment"></a>Azure Migrate 서버 평가


### <a name="does-azure-migrate-need-vcenter-server-for-vmware-vm-discovery"></a>VMWare VM 검색에 대 한 vCenter Server 필요 Azure Migrate?

예, Azure Migrate VMware 환경에서 검색을 수행 하는 vCenter Server 필요 합니다. VCenter Server으로 관리 되지 않는 ESXi 호스트의 검색은 지원 하지 않습니다.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate 서버 평가와 맵 도구 키트 간의 차이점은 무엇 인가요?

서버 평가는 마이그레이션 준비에 도움을 주는 평가와 Azure로 마이그레이션하기 위한 워크 로드 평가를 제공 합니다. [Microsoft 평가 및 계획 (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) 은 최신 버전의 Windows 클라이언트/서버 운영 체제에 대 한 마이그레이션 계획 및 소프트웨어 사용 추적을 비롯 하 여 다른 작업에 도움이 됩니다. 이러한 시나리오에서는 계속 해 서 MAP Toolkit을 사용 합니다.

### <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>서버 평가와 Site Recovery Deployment Planner의 차이점은 무엇 인가요?

서버 평가는 마이그레이션 계획 도구입니다. Site Recovery Deployment Planner은 재해 복구 계획 도구입니다. 

- **Azure로 온-프레미스 마이그레이션 계획**: 온-프레미스 서버를 azure로 마이그레이션하려면 마이그레이션 계획에 대 한 서버 평가를 사용 합니다. 온-프레미스 워크 로드를 평가 하 고 마이그레이션에 도움이 되는 지침 및 도구를 제공 합니다. 마이그레이션 계획을 적용 한 후에는 Azure Migrate 서버 마이그레이션을 비롯 한 도구를 사용 하 여 컴퓨터를 Azure로 마이그레이션할 수 있습니다.
- **Azure로 재해 복구 계획**: Site Recovery을 사용 하 여 온-프레미스에서 azure로 재해 복구를 설정 하려면 Site Recovery Deployment Planner를 사용 합니다. Deployment Planner은 재해 복구를 위해 온-프레미스 환경에 대 한 심층 Site Recovery 관련 평가를 제공 합니다. 복제 및 장애 조치 (failover)와 같은 재해 복구에 대 한 권장 사항을 제공 합니다.

### <a name="does-azure-migrate-estimate-costs-for-the-enterprise-agreement-ea-program"></a>EA (기업계약) 프로그램에 대 한 예상 비용을 Azure Migrate 합니까?

Azure Migrate 서버 평가는 현재 [기업계약 프로그램](https://azure.microsoft.com/offers/enterprise-agreement-support/)에 대 한 비용 추정치를 지원 하지 않습니다. 해결 방법으로 평가를 만들 때 **제품**으로 **종 량** 제를 지정 하 고 평가 **속성에서 할인으로 할인 비율** (구독에 적용 됨)을 수동으로 추가할 수 있습니다.

  ![평가 속성](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-and-performance-based-sizing"></a>온-프레미스와 성능 기반 크기 조정의 차이점은 무엇 인가요?

온-프레미스 크기 조정으로 Azure Migrate는 평가를 위해 VM 성능 데이터를 고려 하지 않습니다. 온-프레미스 구성에 따라 VM 크기를 평가 합니다. 성능 기반 크기 조정을 사용 하 여 크기는 사용률 데이터를 기반으로 합니다.

- 예를 들어 온-프레미스 VM에 4 개의 코어와 8 GB의 메모리가 50% CPU 사용률 및 50% 메모리 사용률에 있으면 다음이 발생 합니다.
    - 온-프레미스 크기 조정에서는 4 개의 코어와 8gb의 메모리가 있는 Azure VM SKU를 권장 합니다.
    - 성능 기반 크기 조정에서는 두 개의 코어와 4gb의 메모리가 포함 된 VM SKU를 사용 하는 것이 좋습니다 .이는 사용률 비율이 고려 되기 때문입니다.

- 마찬가지로 디스크 크기 조정은 두 가지 평가 속성인 크기 조정 조건 및 저장소 유형에 따라 달라 집니다.
    - 크기 조정 기준이 성능 기반이 고 저장소 유형이 자동 인 경우, Azure Migrate 대상 디스크 유형 (standard 또는 premium)을 식별할 때 디스크의 IOPS 및 처리량 값을 고려 합니다.
    - 크기 조정 기준이 성능 기반이 고 저장소 유형이 프리미엄 인 경우 Azure Migrate 온-프레미스 디스크의 크기에 따라 프리미엄 디스크 SKU를 권장 합니다. 크기 조정이 온-프레미스이 고 저장소 유형이 표준 또는 프리미엄 인 경우 동일한 논리가 디스크 크기 조정에 적용 됩니다.

### <a name="does-performance-history-and-utilization-percentile-impact-size-recommendations"></a>성능 기록 및 사용률 백분위 수는 크기 권장 사항에 영향을 미칩니까?

이러한 속성은 성능 기반 크기 조정에만 적용됩니다.

- Azure Migrate 온-프레미스 컴퓨터의 성능 기록을 수집 하 고이를 사용 하 여 Azure에서 VM 크기 및 디스크 형식을 권장 합니다.
- 어플라이언스는 온-프레미스 환경을 지속적으로 프로 파일링 하 여 20 초 마다 실시간 사용률 데이터를 수집 합니다.
- 어플라이언스는 20 초 샘플을 롤업 하 고 15 분 마다 단일 데이터 요소를 만듭니다.
- 어플라이언스는 데이터 요소를 만들기 위해 20 초의 모든 샘플에서 최고 값을 선택 합니다.
- 어플라이언스는 Azure에이 데이터 요소를 보냅니다.

성능 기간 및 성능 기록 백분위 수 값을 기준으로 Azure에서 평가를 만들 때 Azure Migrate는 유효한 사용률 값을 계산 하 고 크기 조정에 사용 합니다.

- 예를 들어 성능 기간을 1 일로 설정 하 고 백분위 수 값을 95 백분위 수로 설정 하는 경우, Azure Migrate는 수집기에서 지난 날짜의 15 분 샘플 지점을 오름차순으로 정렬 하 고 95 번째 백분위 수 값을 유효 하 게 선택 합니다. 효율.
- 95 번째 백분위 수 값을 사용 하면 이상 값이 무시 됩니다. 99 번째 백분위 수를 사용 하는 경우 이상 값이 포함 될 수 있습니다. 이상 값을 누락 하지 않고 기간의 피크 사용을 선택 하려면 99 번째 백분위 수를 선택 합니다.

## <a name="server-assessment---dependency-visualization"></a>서버 평가-종속성 시각화


### <a name="what-is-dependency-visualization"></a>종속성 시각화란?

종속성 시각화를 사용 하 여 마이그레이션을 위한 Vm 그룹을 보다 확실 하 게 평가 합니다. 종속성 시각화는 평가를 실행 하기 전에 컴퓨터 종속성을 교차 확인 합니다. 이 기능을 사용 하면 아무것도 유지 되지 않으므로 Azure로 마이그레이션할 때 예기치 않은 중단을 방지할 수 있습니다. Azure Migrate는 Azure Monitor의 서비스 맵 솔루션을 사용하여 종속성 시각화를 사용하도록 설정합니다. [자세히 알아보세요](concepts-dependency-visualization.md).

> [!NOTE]
> Azure Government에서 종속성 시각화를 사용할 수 없습니다.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>종속성 시각화를 사용 하기 위해 비용을 지불 해야 하나요?
아니요. Azure Migrate 가격 책정에 대해 [자세히 알아봅니다](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>종속성 시각화를 사용하려면 무엇을 설치해야 하나요?

종속성 시각화를 사용하려면 평가할 각 온-프레미스 머신에 에이전트를 다운로드하여 설치해야 합니다.

각 컴퓨터에 다음 에이전트를 설치 해야 합니다.
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [종속성 에이전트](../azure-monitor/platform/agents-overview.md#dependency-agent).
- 인터넷에 연결 되지 않은 컴퓨터가 있는 경우 Log Analytics 게이트웨이를 다운로드 하 여 설치 해야 합니다.

종속성 시각화를 사용 하지 않는 경우 이러한 에이전트가 필요 하지 않습니다.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>종속성 시각화에 기존 작업 영역을 사용할 수 있나요?

예, 기존 작업 영역을 마이그레이션 프로젝트에 연결 하 고 종속성 시각화에 사용할 수 있습니다. [자세히 알아봅니다](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>종속성 시각화 보고서를 내보낼 수 있나요?

아니요. 종속성 시각화를 내보낼 수 없습니다. 그러나 Azure Migrate 서비스 맵를 사용 하 고 [서비스 맵 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) 를 사용 하 여 종속성을 JSON 형식으로 검색할 수 있습니다.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Microsoft Monitoring Agent (MMA) 및 종속성 에이전트의 설치를 자동화 하려면 어떻게 해야 하나요?

이 [스크립트를 사용 하 여 종속성 에이전트를 설치](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)합니다. 명령줄 또는 자동화를 사용 하 여 [MMA을 설치 하려면 다음 지침을](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) 따르세요. MMA의 경우 [이 스크립트](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)를 사용 합니다.

스크립트 외에도 System Center Configuration Manager 및 [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) 와 같은 배포 도구를 사용 하 여 에이전트를 배포할 수 있습니다.


### <a name="what-operating-systems-are-supported-by-mma"></a>MMA에서 지원 되는 운영 체제는 무엇 인가요?

- [MMA에서 지 원하는 Windows 운영 체제](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)목록을 봅니다.
- [MMA에서 지 원하는 Linux 운영 체제](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)목록을 봅니다.

### <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>1 시간 이상 종속성을 시각화할 수 있나요?
아니요. 최대 1 시간 동안 종속성을 시각화할 수 있습니다. 기록의 특정 날짜로 다시 이동할 수 있습니다 .이 날짜는 한 달에 다시 표시 되지만 시각화의 최대 기간은 1 시간입니다. 예를 들어 종속성 맵의 기간을 사용 하 여 어제 종속성을 볼 수 있지만 1 시간 동안만 종속성을 볼 수 있습니다. 그러나 Azure Monitor 로그를 사용 하 여 더 긴 기간 동안 [종속성 데이터를 쿼리할](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) 수 있습니다.

### <a name="can-i-use-dependency-visualization-for-groups-of-more-than-10-vms"></a>10 개 이상의 Vm 그룹에 종속성 시각화를 사용할 수 있나요?
최대 10 개의 Vm을 포함 하는 그룹에 대 한 [종속성을 시각화할](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) 수 있습니다. 10 개 이상의 Vm이 포함 된 그룹이 있는 경우 그룹을 더 작은 그룹으로 분할 한 다음 종속성을 시각화 하는 것이 좋습니다.

## <a name="azure-migrate-server-migration"></a>Azure Migrate 서버 마이그레이션

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-site-recovery"></a>Azure Migrate 서버 마이그레이션과 Site Recovery 간의 차이점은 무엇 인가요?

- 온-프레미스 VMware Vm의 에이전트 없는 마이그레이션은 Azure Migrate Server 마이그레이션 내에서 고유 합니다.
- Hyper-v Vm, 물리적 서버 및 에이전트 기반 VMware Vm을 마이그레이션하는 경우 Azure Migrate Server 마이그레이션은 Azure Site Recovery 복제 엔진을 사용 합니다.


## <a name="next-steps"></a>다음 단계
[Azure Migrate 개요](migrate-services-overview.md)를 참조 하세요.
