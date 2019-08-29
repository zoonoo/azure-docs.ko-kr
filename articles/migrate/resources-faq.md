---
title: Azure Migrate-질문과 대답 (FAQ) | Microsoft Docs
description: Azure Migrate에 대한 질문과 대답 해결
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: snehaa
ms.openlocfilehash: af95ad892b62cb5d8bece554d6026525d9279777
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102883"
---
# <a name="azure-migrate-frequently-asked-questions-faq"></a>Azure Migrate: 질문과 대답(FAQ)

이 문서에서는 Azure Migrate에 대 한 자주 묻는 질문에 답변 합니다. 이 문서를 읽은 후 추가 쿼리가 있는 경우 [Azure Migrate 포럼](https://aka.ms/AzureMigrateForum)에 게시 하세요.

## <a name="general"></a>일반

### <a name="which-azure-geographies-does-azure-migrate-support"></a>어떤 Azure 지역에서 지원 Azure Migrate 있나요?

[VMware 목록과](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) [hyper-v 목록은](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)를 참조 하세요.

### <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Azure Migrate와 Azure Site Recovery의 차이점은 무엇 인가요?

Azure Migrate은 마이그레이션을 시작 하 고, 컴퓨터 및 워크 로드의 검색 및 평가를 실행 하 고 추적 하 고, 컴퓨터 및 워크 로드를 Azure로 마이그레이션하는 작업을 실행 하 고 추적 하는 중앙 허브를 제공 합니다. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) 은 재해 복구 솔루션입니다. Azure Migrate 서버 마이그레이션은 백 엔드에서 Azure Site Recovery를 사용 하 여 온-프레미스 컴퓨터의 리프트 앤 시프트 마이그레이션에 대 한 마이그레이션 시나리오를 사용 하도록 설정 합니다.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Azure Migrate 어플라이언스 (VMware/물리적 서버)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Azure Migrate 어플라이언스는 Azure에 어떻게 연결 되나요?

인터넷을 통해 연결 하거나 공용/Microsoft 피어 링에서 Azure Express 경로를 사용할 수 있습니다.

### <a name="what-are-the-network-connectivity-requirements-for-azure-migrate-server-assessment-and-migration"></a>Azure Migrate 서버 평가 및 마이그레이션에 대 한 네트워크 연결 요구 사항은 무엇 인가요?

Azure Migrate Azure와 통신할 수 있도록 하는 데 필요한 Url 및 포트에 대 한 자세한 내용은 [VMware](migrate-support-matrix-vmware.md) 및 [hyper-v](migrate-support-matrix-hyper-v.md) 지원 매트릭스를 참조 하세요.

### <a name="can-i-harden-the-appliance-vm-that-i-set-up-with-the-template"></a>템플릿으로 설정한 어플라이언스 VM을 강화할 수 있나요?

Azure Migrate 어플라이언스에 필요한 통신 및 방화벽 규칙을 그대로 유지 하는 한 구성 요소 (예: 바이러스 백신)를 템플릿에 추가할 수 있습니다.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Azure Migrate 어플라이언스에서 수집 되는 데이터는 무엇 인가요?

[Azure Migrate 설명서에서](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware)Azure Migrate 어플라이언스에 의해 수집 된 데이터에 대 한 세부 정보를 볼 수 있습니다.

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>분석 된 VMware 또는 Hyper-v 환경에 대 한 성능에 영향이 있나요?

Azure Migrate 어플라이언스는 VM 성능 데이터를 계속 측정 하기 위해 온-프레미스 컴퓨터를 계속 프로 파일링 합니다. 이 프로 파일링은 Hyper-v/ESXi 호스트 또는 vCenter Server의 성능에 거의 영향을 주지 않습니다.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>수집 된 데이터는 어디에 저장 되 고 기간은 얼마 입니까?

Azure Migrate 어플라이언스에 의해 수집 된 데이터는 마이그레이션 프로젝트를 만들 때 선택 하는 Azure 위치에 저장 됩니다. 데이터는 Microsoft 구독에 안전 하 게 저장 되며 Azure Migrate 프로젝트를 삭제 하면 삭제 됩니다.

종속성 시각화의 경우 Vm에 에이전트를 설치 하는 경우 종속성 에이전트에서 수집 하는 데이터는 Azure 구독에서 만든 Log Analytics 작업 영역에 US에 저장 됩니다. 구독에서 Log Analytics 작업 영역을 삭제하면 이 데이터가 삭제됩니다. 자세한 내용은 [종속성 시각화](concepts-dependency-visualization.md)를 참조 하세요.

### <a name="what-volume-of-data-is-uploaded-by-the-azure-migrate-appliance-during-continuous-profiling"></a>연속 프로 파일링 중 Azure Migrate 어플라이언스에서 업로드 되는 데이터 볼륨은 무엇 인가요?

Azure Migrate로 전송 되는 데이터의 볼륨은 여러 매개 변수에 따라 달라 집니다. 볼륨을 이해 하려면: 10 대의 컴퓨터가 있는 Azure Migrate 프로젝트 (각각 하나의 디스크와 하나의 NIC)가 하루에 50 MB를 보냅니다. 이 값은 근사값입니다. Nic 및 디스크에 대 한 데이터 요소 수에 따라 변경 됩니다. (컴퓨터, Nic 또는 디스크 수가 증가 하는 경우 전송 되는 데이터의 증가는 비선형입니다.)

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>데이터는 미사용 및 전송 중에 암호화 되나요?

예, 둘 다 있습니다. 메타 데이터는 HTTPS를 통해 인터넷을 통해 Azure Migrate 서비스로 안전 하 게 전송 됩니다. 메타 데이터는 [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) 데이터베이스 및 Microsoft 구독의 [Azure Blob storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 에 저장 됩니다. 메타 데이터는 미사용 시 암호화 됩니다.

종속성 에이전트에서 수집 하는 데이터도 전송 중에 암호화 됩니다 (보안 HTTPS). 구독의 Log Analytics 작업 영역에 저장 됩니다. 또한 미사용에 암호화 됩니다.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-vcenter-server-and-the-azure-migrate-service"></a>Azure Migrate 어플라이언스는 vCenter Server 및 Azure Migrate 서비스와 어떻게 통신 하나요?

어플라이언스는 어플라이언스를 설정할 때 제공한 자격 증명을 사용 하 여 vCenter Server (포트 443)에 연결 됩니다. VMware PowerCLI를 사용 하 여 vCenter Server에서 관리 하는 Vm에 대 한 메타 데이터를 수집 vCenter Server를 쿼리 합니다. Vm (코어, 메모리, 디스크, Nic 등)에 대 한 구성 데이터와 지난 달의 각 VM에 대 한 성능 기록을 수집 합니다. 그러면 수집 된 메타 데이터는 평가를 위해 HTTPS를 통해 인터넷을 통해 Azure Migrate 서버 평가에 전송 됩니다.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>동일한 어플라이언스를 여러 vCenter Server 인스턴스에 연결할 수 있나요?

아니요. 어플라이언스와 vCenter Server 사이에 일대일 매핑이 있습니다. 여러 vCenter Server 인스턴스에서 Vm을 검색 해야 하는 경우 여러 어플라이언스를 배포 해야 합니다.


### <a name="i-changed-my-machine-size-can-i-run-the-assessment-again"></a>내 컴퓨터 크기를 변경했습니다. 평가를 다시 실행할 수 있나요?

Azure Migrate 어플라이언스는 온-프레미스 환경에 대 한 정보를 지속적으로 수집 합니다. 하지만 평가는 온-프레미스 Vm의 특정 시점 스냅숏입니다. 평가 하려는 VM의 설정을 변경 하는 경우 다시 계산 옵션을 사용 하 여 평가를 최신 변경 내용으로 업데이트 합니다.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment-in-azure-migrate-server-assessment"></a>Azure Migrate Server 평가에서 다중 테 넌 트 환경에서 검색을 수행 하려면 어떻게 해야 하나요?

VMware의 경우, 테 넌 트 간에 공유 되는 환경에서 다른 테 넌 트의 구독에 있는 한 테 넌 트의 Vm을 검색 하지 않으려는 경우 검색 하려는 Vm에만 액세스할 수 있는 vCenter Server 자격 증명을 만듭니다. 그런 다음 Azure Migrate 어플라이언스에서 검색을 시작할 때 이러한 자격 증명을 사용 합니다.

Hyper-v의 경우 검색에서 Hyper-v 호스트 자격 증명을 사용 합니다. Vm이 동일한 Hyper-v 호스트를 공유 하는 경우 현재 검색을 분리할 수 있는 방법이 없습니다.  

### <a name="how-many-vms-can-i-discover-with-a-single-migration-appliance"></a>단일 마이그레이션 어플라이언스로 검색할 수 있는 Vm은 몇 개입니까?

단일 마이그레이션 어플라이언스를 사용 하 여 최대 1만 VMware Vm 및 최대 5000 Hyper-v Vm을 검색할 수 있습니다. 온-프레미스 환경에 더 많은 컴퓨터가 있는 경우 [hyper-v](scale-hyper-v-assessment.md) 및 [VMware](scale-vmware-assessment.md) 평가를 확장 하는 방법을 알아보세요.

## <a name="azure-migrate-server-assessment"></a>Azure Migrate 서버 평가

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>실제 서버에 대 한 서버 평가 지원 평가를 Azure Migrate 합니까?

아니요, Azure Migrate는 현재 물리적 서버의 평가를 지원 하지 않습니다.

### <a name="does-azure-migrate-need-vcenter-server-to-perform-discovery-in-a-vmware-environment"></a>VMware 환경에서 검색을 수행 하려면 vCenter Server 필요 Azure Migrate?

예, Azure Migrate VMware 환경에서 검색을 수행 하는 vCenter Server 필요 합니다. VCenter Server으로 관리 되지 않는 ESXi 호스트의 검색은 지원 하지 않습니다.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate 서버 평가와 맵 도구 키트 간의 차이점은 무엇 인가요?

Azure Migrate Server 평가는 마이그레이션 준비에 도움을 주는 마이그레이션 평가와 Azure로 마이그레이션하기 위한 워크 로드 평가를 제공 합니다. [Microsoft 평가 및 계획 (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) 은 최신 버전의 Windows 클라이언트 및 서버 운영 체제 및 소프트웨어 사용 추적을 위한 마이그레이션 계획과 같은 다른 작업에 도움이 됩니다. 이러한 시나리오의 경우 MAP Toolkit을 계속 사용합니다.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-azure-site-recovery-deployment-planner"></a>Azure Migrate Server 평가와 Azure Site Recovery Deployment Planner 간의 차이점은 무엇 인가요?

Azure Migrate Server 평가는 마이그레이션 계획 도구입니다. Azure Site Recovery Deployment Planner은 재해 복구 계획 도구입니다.

**VMware/hyper-v에서 Azure로 마이그레이션**: 온-프레미스 서버를 Azure로 마이그레이션하려는 경우 마이그레이션 계획에 대 한 Azure Migrate 서버 평가를 사용 합니다. 온-프레미스 워크 로드를 평가 하 고 서버를 Azure로 마이그레이션하는 데 도움이 되는 지침, 통찰력 및 도구를 제공 합니다. 마이그레이션 계획을 완료 한 후에는 Azure Migrate 서버 마이그레이션과 같은 도구를 사용 하 여 컴퓨터를 Azure로 마이그레이션할 수 있습니다.

**VMware/hyper-v에서 Azure로 재해 복구**: Site Recovery를 통해 Azure로의 재해 복구를 위해 계획을 위해 Site Recovery Deployment Planner를 사용 합니다. Site Recovery Deployment Planner 온-프레미스 환경에 대 한 심층 Site Recovery 특정 평가를 수행 합니다. Vm의 복제 및 장애 조치 (failover)와 같은 성공적인 재해 작업을 Site Recovery 하는 데 필요한 권장 사항을 제공 합니다.

### <a name="does-azure-migrate-support-cost-estimation-for-the-enterprise-agreement-ea-program"></a>EA (기업계약) 프로그램에 대 한 비용 예측을 지원 Azure Migrate 있나요?

Azure Migrate 현재 [기업계약 프로그램](https://azure.microsoft.com/offers/enterprise-agreement-support/)의 비용 예측을 지원 하지 않습니다. 해결 방법은 **제품** 으로 **종 량** 제을 지정 하 고 평가 속성의 **할인** 상자에서 할인 비율 (구독에 적용 가능)을 수동으로 지정 하는 것입니다.

  ![평가 속성](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>온-프레미스 크기 조정 및 성능 기반 크기 조정의 차이점은 무엇 인가요?

온-프레미스 크기 조정을 사용 하 Azure Migrate VM 성능 데이터를 고려 하지 않습니다. 온-프레미스 구성에 따라 Vm의 크기를 조정 합니다. 성능 기반 크기 조정을 사용 하 여 크기는 사용률 데이터를 기반으로 합니다.

예를 들어 CPU 사용률이 50%이 고 50%의 메모리 사용률이 4 개 코어와 8gb의 메모리가 있는 온-프레미스 VM을 가정해 보겠습니다. 이 VM의 경우 온-프레미스 크기 조정으로 4 개의 코어와 8gb의 메모리가 있는 Azure VM SKU를 권장 합니다. 성능 기반 크기 조정에서는 두 개의 코어와 4gb의 메모리가 포함 된 VM SKU를 사용 하는 것이 좋습니다 .이는 사용률 비율이 고려 되기 때문입니다.

마찬가지로 디스크 크기 조정은 두 가지 평가 속성인 크기 조정 조건 및 저장소 유형에 따라 달라 집니다. 크기 조정 기준이 성능 기반이 고 저장소 유형이 자동 인 경우, Azure Migrate 대상 디스크 유형 (standard 또는 premium)을 식별할 때 디스크의 IOPS 및 처리량 값을 고려 합니다.

크기 조정 기준이 성능 기반이 고 저장소 유형이 프리미엄 인 경우 Azure Migrate 프리미엄 디스크를 권장 합니다. Premium disk SKU는 온-프레미스 디스크의 크기에 따라 선택 됩니다. 크기 조정 기준이 온-프레미스로 크기 조정 되 고 저장소 유형이 standard 또는 premium 인 경우 동일한 논리를 사용 하 여 디스크 크기를 조정 합니다.

### <a name="what-impact-does-performance-history-and-utilization-percentile-have-on-size-recommendations"></a>성능 기록 및 사용률 백분위 수에 미치는 영향은 크기 권장 사항은 무엇 인가요?

이러한 속성은 성능 기반 크기 조정에만 적용됩니다.

Azure Migrate 온-프레미스 컴퓨터의 성능 기록을 수집 하 고이를 사용 하 여 Azure에서 VM 크기 및 디스크 형식을 권장 합니다.

어플라이언스는 온-프레미스 환경을 지속적으로 프로 파일링 하 여 20 초 마다 실시간 사용률 데이터를 수집 합니다. 어플라이언스는 20 초 샘플을 롤업 하 고 15 분 마다 단일 데이터 요소를 만듭니다. 어플라이언스는 데이터 요소를 만들기 위해 20 초의 모든 샘플에서 최고 값을 선택 합니다. 어플라이언스는 Azure에이 데이터 요소를 보냅니다.

Azure에서 평가를 만들 때 (성능 기간 및 성능 기록 백분위 수 값을 기준으로) Azure Migrate 유효한 사용률 값을 계산 하 고 크기 조정에 사용 합니다.

예를 들어, 성능 기간을 1 일로 설정 하 고 백분위 수 값을 95 백분위 수로 설정 했다고 가정 합니다. Azure Migrate 지난 날짜에 수집기에서 보낸 15 분 샘플 지점을 오름차순으로 정렬 하 고 95 번째 백분위 수 값을 유효 사용률으로 선택 합니다.

95 번째 백분위 수 값은 이상 값을 무시 하는 것을 보장 합니다. 99 번째 백분위 수를 사용 하는 경우 이상 값이 포함 될 수 있습니다. 기간에 대 한 피크 사용을 선택 하 고 이상 값을 누락 하지 않으려면 99 번째 백분위 수를 선택 합니다.

### <a name="what-is-dependency-visualization"></a>종속성 시각화란?

종속성 시각화를 사용 하면 더 강력한 마이그레이션을 위해 Vm 그룹을 평가할 수 있습니다. 평가를 실행 하기 전에 컴퓨터 종속성을 교차 확인 합니다. 종속성 시각화를 사용 하면 Azure로 마이그레이션할 때 예기치 않은 중단을 방지 하기 위해 아무것도 남겨 두지 않습니다. Azure Migrate Azure Monitor 로그의 서비스 맵 솔루션을 사용 하 여 종속성 시각화를 사용 하도록 설정 합니다.

> [!NOTE]
> Azure Government에서 종속성 시각화를 사용할 수 없습니다.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>종속성 시각화를 사용 하기 위해 비용을 지불 해야 하나요?

아니요. 자세한 내용은 [Azure Migrate 가격 책정](https://azure.microsoft.com/pricing/details/azure-migrate/)을 참조 하세요.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>종속성 시각화를 사용하려면 무엇을 설치해야 하나요?

종속성 시각화를 사용하려면 평가할 각 온-프레미스 머신에 에이전트를 다운로드하여 설치해야 합니다.

각 컴퓨터에 다음 에이전트를 설치 해야 합니다.
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [종속성 에이전트](../azure-monitor/platform/agents-overview.md#dependency-agent).
- 인터넷에 연결 되지 않은 컴퓨터가 있는 경우 Log Analytics 게이트웨이를 다운로드 하 여 설치 해야 합니다.

종속성 시각화를 사용 하지 않는 경우 이러한 에이전트가 필요 하지 않습니다.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>종속성 시각화에 기존 작업 영역을 사용할 수 있나요?

예, 기존 작업 영역을 마이그레이션 프로젝트에 연결 하 고 종속성 시각화에 사용할 수 있습니다. 자세한 내용은 [종속성 시각화](concepts-dependency-visualization.md#how-does-it-work) 문서에서 "작동 방법"을 참조 하세요.

### <a name="can-i-export-the-dependency-visualization-report"></a>종속성 시각화 보고서를 내보낼 수 있나요?

아니요. 종속성 시각화를 내보낼 수 없습니다. 그러나 Azure Migrate에서 종속성 시각화를 위해 서비스 맵를 사용 하기 때문에 [서비스 맵 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) 를 사용 하 여 종속성을 JSON 형식으로 가져올 수 있습니다.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Microsoft Monitoring Agent (MMA) 및 종속성 에이전트의 설치를 자동화 하려면 어떻게 해야 하나요?

이 [스크립트를 사용 하 여 에이전트를 설치](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples)합니다. 명령줄 또는 자동화를 사용 하 여 [MMA을 설치 하려면 다음 지침을](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) 따르세요. MMA의 경우 [이 스크립트](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)를 사용 합니다.

스크립트 외에도 System Center Configuration Manager 및 [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) 와 같은 배포 도구를 사용 하 여 에이전트를 배포할 수 있습니다.

### <a name="what-operating-systems-are-supported-by-mma"></a>MMA에서 지원 되는 운영 체제는 무엇 인가요?

- [MMA에서 지 원하는 Windows 운영 체제](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)목록을 봅니다.
- [MMA에서 지 원하는 Linux 운영 체제](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)목록을 봅니다.

### <a name="what-operating-systems-are-supported-by-the-dependency-agent"></a>종속성 에이전트에서 지원 되는 운영 체제는 무엇 인가요?

- [종속성 에이전트에서 지 원하는 Windows 운영 체제](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)목록을 봅니다.
- [종속성 에이전트에서 지 원하는 Linux 운영 체제](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)목록을 봅니다.

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>1 시간 이상 Azure Migrate에서 종속성을 시각화할 수 있나요?
아니요. 최대 1 시간 동안 종속성을 시각화할 수 있습니다. 기록의 특정 날짜로 다시 이동할 수 있습니다 .이 날짜는 한 달에 다시 표시 되지만 시각화의 최대 기간은 1 시간입니다. 예를 들어, 종속성 맵의 기간을 사용 하 여 어제 종속성을 볼 수 있지만 1 시간 동안만 볼 수 있습니다. 그러나 Azure Monitor 로그를 사용 하 여 더 긴 기간 동안 [종속성 데이터를 쿼리할](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) 수 있습니다.

### <a name="can-i-use-dependency-visualization-for-groups-that-contain-more-than-10-vms"></a>Vm을 10 개 이상 포함 하는 그룹에 대해 종속성 시각화를 사용할 수 있나요?
최대 10 개의 Vm을 포함 하는 [그룹에 대 한 종속성을 시각화할](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) 수 있습니다. 10 개 이상의 Vm이 포함 된 그룹이 있는 경우 그룹을 더 작은 그룹으로 분할 한 다음 종속성을 시각화 하는 것이 좋습니다.

## <a name="azure-migrate-server-migration"></a>Azure Migrate 서버 마이그레이션

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-azure-site-recovery"></a>Azure Migrate 서버 마이그레이션과 Azure Site Recovery 간의 차이점은 무엇 인가요?

Azure Migrate 서버 마이그레이션은 VMware Vm의 에이전트 기반 마이그레이션, Hyper-v Vm 마이그레이션 및 물리적 서버를 Azure로 마이그레이션하는 데 Site Recovery 복제 엔진을 사용 합니다. VMware Vm을 마이그레이션하는 에이전트 없는 옵션은 기본적으로 서버 마이그레이션에 내장 되어 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure Migrate 개요](migrate-services-overview.md)를 참조 하세요.
