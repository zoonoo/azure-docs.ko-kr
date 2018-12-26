---
title: Azure Migrate - FAQ(질문과 대답) | Microsoft Docs
description: Azure Migrate에 대한 질문과 대답 해결
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: snehaa
ms.openlocfilehash: 2f04fe103d010a64a77b7d80730cf80007c3c126
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256378"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate - FAQ(질문과 대답)

이 문서는 Azure Migrate에 대한 질문과 대답을 제공합니다. 이 문서를 읽은 후 질문이 있다면 [Azure Migrate 포럼](https://aka.ms/AzureMigrateForum)에 게시해 주세요.

## <a name="general"></a>일반

### <a name="does-azure-migrate-support-assessment-of-only-vmware-workloads"></a>Azure Migrate가 VMware 워크로드의 평가만 지원하나요?

예. Azure Migrate는 현재, VMware 워크로드의 평가만 지원합니다. Hyper-V 및 물리적 서버에 대한 지원은 향후 제공될 예정입니다.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate에는 VMware 환경을 검색하기 위해 vCenter Server가 필요한가요?

네, Azure Migrate에는 VMware 환경을 검색하기 위한 vCenter Server가 필요합니다. vCenter Server에서 관리되지 않는 ESXi 호스트의 검색은 지원되지 않습니다.

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Azure Migrate는 Azure Site Recovery와 어떻게 다른가요?

Azure Migrate는 온-프레미스 워크로드를 검색하고 Azure로 마이그레이션을 계획할 때 도움이 되는 평가 서비스입니다. 재해 복구 솔루션과 함께 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure)를 사용하면 온-프레미스 워크로드를 Azure의 IaaS VM으로 마이그레이션하는 데 유용합니다.

### <a name="whats-the-difference-between-using-azure-migrate-for-assessments-and-the-map-toolkit"></a>평가를 위해 Azure Migrate를 사용할 때와 Map Toolkit을 사용할 때 어떤 차이가 있나요?

[Azure Migrate](migrate-overview.md)는 온-프레미스 워크로드를 Azure로 마이그레이션하기 위한 준비 및 평가에 도움이 되는 마이그레이션 평가를 제공합니다. [MAP(Microsoft Assessment and Planning) Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=7826)에는 다른 기능이 있습니다. 예를 들어, 최신 버전의 Windows 클라이언트 및 서버 운영 체제에 대한 마이그레이션 계획, 소프트웨어 사용 현황 추적 등이 있습니다. 이러한 시나리오의 경우 MAP Toolkit을 계속 사용합니다.


### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>Azure Migrate는 Azure Site Recovery Deployment Planner와 어떻게 다른가요?

Azure Migrate는 마이그레이션 계획 도구이며 Azure Site Recovery Deployment Planner는 DR(재해 복구) 계획 도구입니다.

**VMware에서 Azure로의 마이그레이션**: 온-프레미스 워크로드를 Azure로 마이그레이션하려는 경우 마이그레이션 계획에 Azure Migrate를 사용합니다. Azure Migrate는 온-프레미스 워크로드를 평가하고 Azure로 마이그레이션하는 데 유용한 지침, 통찰력 및 메커니즘을 제공합니다. 마이그레이션 계획이 준비되면 Azure Site Recovery 및 Azure Database Migration Service 같은 서비스를 사용하여 컴퓨터를 Azure로 마이그레이션할 수 있습니다.

**Hyper-V에서 Azure로의 마이그레이션**: Azure Migrate는 현재 Azure로의 마이그레이션에 대해 VMware 가상 머신에 대한 평가만 지원합니다. Hyper-V에 대한 지원은 Azure Migrate에 대해 준비 중입니다. 그때까지는 Site Recovery Deployment Planner를 사용할 수 있습니다. Azure Migrate에서 Hyper-V 지원을 사용할 수 있게 되면 Hyper-V 워크로드의 마이그레이션 계획에 Azure Migrate를 사용할 수 있습니다.

**VMware/Hyper-V에서 Azure로의 재해 복구**: Azure Site Recovery(Site Recovery)를 사용하여 Azure에서 DR(재해 복구)을 수행하려는 경우 DR 계획에 Site Recovery Deployment Planner를 사용합니다. Site Recovery Deployment Planner는 온-프레미스 환경에 대한 심도 있는 ASR 특정 평가를 수행합니다. 가상 머신의 복제, 장애 조치(failover) 등 성공적인 DR 작업을 위해 Site Recovery에 필요한 권장 사항을 제공합니다.  

### <a name="which-azure-regions-are-supported-by-azure-migrate"></a>Azure Migrate에서 지원되는 Azure 지역은 어디인가요?

Azure Migrate는 현재 마이그레이션 프로젝트 위치로 미국 동부 및 미국 중서부를 지원합니다. 미국 중서부 및 미국 동부에서만 마이그레이션 프로젝트를 만들 수 있더라도 [여러 대상 위치](https://docs.microsoft.com/azure/migrate/how-to-modify-assessment#edit-assessment-properties)에 대해 여전히 컴퓨터를 평가할 수 있습니다. 프로젝트 위치는 검색된 데이터를 저장하는 데에만 사용됩니다.

### <a name="how-does-the-on-premises-site-connect-to-azure-migrate"></a>온-프레미스 사이트에서 Azure Migrate로 연결하려면 어떻게 하나요?

이 연결은 인터넷을 통해 진행되거나 공용 피어링을 통해 ExpressRoute를 사용할 수 있습니다.

### <a name="can-i-harden-the-vm-set-up-with-the-ova-template"></a>OVA 템플릿으로 VM 설정을 강화할 수 있나요?

Azure Migrate 어플라이언스가 작동하는 데 필요한 통신 및 방화벽 규칙이 그대로 유지되기만 하면 추가 구성 요소(예: 바이러스 백신)를 OVA 템플릿에 추가할 수 있습니다.   

## <a name="discovery"></a>검색

### <a name="what-data-is-collected-by-azure-migrate"></a>Azure Migrate에서는 어떤 데이터를 수집하나요?

Azure Migrate는 어플라이언스 기반 검색 및 에이전트 기반 검색의 두 종류 검색을 지원합니다.
어플라이언스 기반 검색은 온-프레미스 VM에 대한 메타데이터를 수집하며, 어플라이언스에 의해 수집된 메타데이터의 전체 목록은 다음과 같습니다.

**VM의 구성 데이터**
- VM 표시 이름(vCenter에서)
- VM 인벤토리 경로(vCenter에서 host/cluster/folder)
- IP 주소
- MAC 주소
- 운영 체제
- 코어, 디스크, NIC 수
- 메모리 크기, 디스크 크기

**VM의 성능 데이터**
- CPU 사용량
- 메모리 사용량
- VM에 연결된 각 디스크:
  - 디스크 읽기 처리량
  - 디스크 쓰기 처리량
  - 초당 디스크 읽기 작업
  - 초당 디스크 쓰기 작업
- VM에 연결된 각 네트워크 어댑터:
  - 네트워크 입력
  - 네트워크 출력

에이전트 기반 검색은 어플라이언스 기반 검색의 최상위에서 사용할 수 있는 옵션으로, 고객이 온-프레미스 VM의 [종속성을 시각화](how-to-create-group-machine-dependencies.md)하는 데 유용합니다. 종속성 에이전트는 FQDN, OS, IP 주소, MAC 주소, VM 내부에서 실행되는 프로세스 및 VM에서 들어오거나 나가는 TCP 연결과 같은 세부 사항을 수집합니다. 에이전트 기반 검색은 선택 사항으로, VM의 종속성을 시각화하지 않을 경우에는 에이전트를 설치하지 않도록 선택할 수 있습니다.

### <a name="would-there-be-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>분석되는 ESXi 호스트 환경의 성능에 대한 영향

[일회성 검색 방식](https://docs.microsoft.com/azure/migrate/concepts-collector#discovery-methods)의 경우 성능 데이터를 수집하려면 vCenter Server의 통계 수준을 3으로 설정해야 합니다. 통계를 이 수준으로 설정하면 대량의 문제 해결 데이터가 수집되어 vCenter Server 데이터베이스에 저장됩니다. 따라서 vCenter Server에서 몇 가지 성능 문제가 발생할 수 있습니다. 하지만 ESXi 호스트에 대한 영향은 미미합니다.

성능 데이터 연속 프로파일링 기능(미리 보기)이 새롭게 도입되었습니다. 연속 프로파일링 사용 시에는 성능 기반 평가를 실행하기 위해 vCenter Server 통계 수준을 더 이상 변경할 필요가 없습니다. 이제 수집기 어플라이언스는 온-프레미스 컴퓨터를 프로파일링하여 가상 머신의 성능 데이터를 측정합니다. 그러므로 ESXi 호스트와 vCenter Server의 성능에 대한 영향은 거의 없습니다.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>수집된 데이터는 어디에 저장되며 저장 기간은 얼마나 되나요?

수집기 어플라이언스에서 수집한 데이터는 마이그레이션 프로젝트를 만들면서 사용자가 지정한 Azure 위치에 저장됩니다. 데이터는 Microsoft 구독에 안전하게 저장되며, 사용자가 Azure Migrate 프로젝트를 삭제하는 경우 삭제됩니다.

종속성 시각화의 경우 VM에 에이전트를 설치하면 종속성 에이전트에서 수집된 데이터는 사용자의 구독에서 만든 Log Analytics 작업 영역 내 미국에 저장됩니다. 구독에서 Log Analytics 작업 영역을 삭제하면 이 데이터가 삭제됩니다. [자세히 알아보기](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization).

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>데이터는 미사용 및 전송 중에 암호화되나요?

예, 수집된 데이터는 미사용 및 전송 중 모두에서 암호화됩니다. 어플라이언스에서 수집된 메타데이터는 Azure Migrate 서비스로 https를 통해 인터넷으로 안전하게 전송됩니다. 수집된 메타데이터는 [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) 및 Microsoft 구독의 [Azure blob 저장소](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)에 저장되고 미사용 암호화됩니다.

종속성 에이전트에서 수집된 데이터도 전송 중에 암호화(보안 https 채널)되며, 사용자의 구독에서 Log Analytics 작업 영역에 저장됩니다. 또한 미사용 시에도 암호화됩니다.

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>수집기가 vCenter Server 및 Azure Migrate 서비스와 통신하는 방법은 어떻게 되나요?

수집기 어플라이언스는 어플라이언스의 사용자가 제공한 자격 증명을 사용하여 vCenter Server(포트 443)에 연결합니다. vCenter Server에서 관리되는 VM에 대한 메타데이터를 수집하도록 VMware PowerCLI를 사용하여 vCenter Server를 쿼리합니다. vCenter Server에서 지난 한 달 동안의 각 VM에 대한 성능 기록뿐 아니라 VM에 대한 두 구성 데이터(코어, 메모리, 디스크, NIC 등)를 수집합니다. 그런 다음, 평가를 위해 수집된 메타데이터가 Azure Migrate 서비스로 전송됩니다(https를 통해 인터넷으로). [자세히 알아보기](concepts-collector.md)

### <a name="can-i-connect-the-same-collector-appliance-to-multiple-vcenter-servers"></a>여러 vCenter 서버에 동일한 수집기 어플라이언스를 연결할 수 있나요?

예. 여러 vCenter 서버를 검색하는 데 단일 수집기 어플라이언스를 사용할 수 있지만 동시 검색은 가능하지 않습니다. 검색을 차례대로 실행해야 합니다.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Site Recovery에서 사용되는 OVA 템플릿이 Azure Migrate에서 사용되는 OVA와 통합되나요?

현재는 통합이 없습니다. Site Recovery의 .OVA 템플릿은 VMware VM/물리적 서버 복제를 위해 Site Recovery 구성 서버를 설정하는 데 사용됩니다. Azure Migrate에서 사용되는 .OVA는 마이그레이션 평가를 위해 vCenter 서버에서 관리되는 VMware VM을 검색하는 데 사용됩니다.

### <a name="i-changed-my-machine-size-can-i-rerun-the-assessment"></a>내 컴퓨터 크기를 변경했습니다. 평가를 다시 실행할 수 있나요?

평가하려는 VM에서 설정을 변경하는 경우 수집기 어플라이언스를 사용하여 검색을 다시 트리거합니다. 어플라이언스에서 이 작업을 수행하려면 **수집 다시 시작** 옵션을 사용합니다. 컬렉션 완료된 후 업데이트된 평가 결과를 얻으려면 포털에서 평가에 대한 **다시 계산** 옵션을 선택합니다.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Azure Migrate에서 다중 테넌트 환경을 검색하려면 어떻게 해야 하나요?

테넌트 간에 공유되는 환경을 사용하고 있는데 다른 테넌트의 구독에서 한 테넌트의 VM을 검색하지 않으려는 경우 수집기 어플라이언스에서 범위 필드를 사용하여 검색의 범위를 지정할 수 있습니다. 테넌트가 호스트를 공유하는 경우 특정 테넌트에 속한 VM만 읽기 전용 액세스 권한이 있는 자격 증명을 만든 다음, 수집기 어플라이언스에서 이 자격 증명을 사용하고 검색을 수행하는 호스트로 범위를 지정합니다. 또는 공유 호스트에서 vCenter Server에 폴더를 만들 수도 있습니다(예: tenant1에 대한 folder1 및 tenant2에 대한 folder2). tenant1에 대한 VM을 folder1로 옮기고 tenant2에 대한 VM을 folder2로 옮긴 다음, 적절한 폴더를 지정하여 그에 따라 수집기에서 검색 범위를 지정합니다.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>단일 마이그레이션 프로젝트에서 검색할 수 있는 가상 머신의 수는 얼마인가요?

단일 마이그레이션 프로젝트에서는 1500대의 가상 머신을 검색할 수 있습니다. 온-프레미스 환경에 더 많은 컴퓨터가 있는 경우 Azure Migrate에서 대규모 환경을 검색하는 방법에 대해 [자세히 알아보세요](how-to-scale-assessment.md).

## <a name="assessment"></a>평가

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Azure Migrate가 EA(기업 계약) 기반 예측 비용을 지원하나요?

Azure Migrate는 현재 [기업 계약 제품](https://azure.microsoft.com/offers/enterprise-agreement-support/)에 대해 비용 예측을 지원하지 않습니다. 해결 방법은 종량제를 제품에 지정하고 평가 속성의 ‘할인’ 필드에 할인율(구독에 적용)을 수동으로 지정하는 것입니다.

  ![할인](./media/resources-faq/discount.png)
  

## <a name="dependency-visualization"></a>종속성 시각화

### <a name="what-is-dependency-visualization"></a>종속성 시각화란?

종속성 시각화를 사용하면 평가를 실행하기 전에 머신 종속성을 교차 확인하여 마이그레이션할 VM 그룹을 보다 확실하게 평가할 수 있습니다. 종속성 시각화를 사용하면 하나도 놓치지 않고 모두 처리하여 Azure로 마이그레이션할 때 예기치 않은 작동 중단이 발생하지 않도록 도와줍니다. Azure Migrate는 Log Analytics의 서비스 맵 솔루션을 활용하여 종속성 시각화를 지원합니다.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>종속성 시각화 기능을 사용하면 비용을 지불해야 하나요?

아니요. [여기](https://azure.microsoft.com/pricing/details/azure-migrate/)에서 Azure Migrate 가격 책정에 대해 자세히 알아보세요.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>종속성 시각화를 사용하려면 무엇을 설치해야 하나요?

종속성 시각화를 사용하려면 평가할 각 온-프레미스 머신에 에이전트를 다운로드하여 설치해야 합니다. 

- 각 머신에 [MMA(Microsoft Monitoring Agent)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)를 설치해야 합니다.
- 각 머신에 [종속성 에이전트](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure)를 설치해야 합니다.
- 또한 인터넷에 연결되지 않은 머신이 있으면 해당 머신에 Log Analytics 게이트웨이를 다운로드하여 설치해야 합니다.

종속성 시각화를 사용하지 않는다면 평가하려는 머신에 이러한 에이전트가 필요하지 않습니다.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>종속성 시각화에 기존 작업 영역을 사용할 수 있나요?

예. 이제 Azure Migrate에서는 기존 작업 영역을 마이그레이션 프로젝트에 연결하여 종속성 시각화에 활용할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>종속성 시각화 보고서를 내보낼 수 있나요?

아니요, 종속성 시각화를 내보낼 수 없습니다. 그러나 Azure Migrate에서 종속성 시각화에 서비스 맵을 사용하므로 [서비스 맵 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections)를 사용하여 종속성을 json 형식으로 가져올 수 있습니다.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>MMA(Microsoft Monitoring Agent) 및 종속성 에이전트 설치를 자동화하려면 어떻게 해야 하나요?

종속성 에이전트 설치에 사용할 수 있는 스크립트는 [여기](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples)에 나와 있습니다. MMA의 경우에는 TechNet의 [이 페이지](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)에서 제공되는 스크립트를 활용할 수 있습니다.

스크립트 외에도 SCCM(System Center Configuration Manager), [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) 등의 배포 도구를 활용하여 에이전트를 배포할 수도 있습니다.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>MMA에서는 어떤 운영 체제가 지원되나요?

MMA에서 지원하는 Windows 운영 체제 목록은 [여기](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)에 나와 있습니다.
MMA에서 지원하는 Linux 운영 체제 목록은 [여기](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)에 나와 있습니다.

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>종속성 에이전트는 어떤 운영 체제를 지원하나요?

종속성 에이전트가 지원하는 Windows 운영 체제 목록은 [여기](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)에 나와 있습니다.
종속성 에이전트가 지원하는 Linux 운영 체제 목록은 [여기](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)에 나와 있습니다.

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>1시간이 넘는 기간에 대해 Azure Migrate의 종속성을 시각화할 수 있나요?
아니요. Azure Migrate에서는 최대 1시간 동안의 종속성을 시각화할 수 있습니다. 하지만 Azure Migrate에서는 최대 1개월 전의 특정 날짜로 돌아가 종속성을 시각화할 수 있습니다. 종속성을 시각화할 수 있는 최대 기간은 1시간입니다. 예를 들어 종속성 맵의 기간 기능을 사용해 어제의 종속성을 확인할 수는 있지만, 종속성을 확인할 수 있는 시간은 1시간입니다.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>종속성 시각화가 10대를 초과하는 VM을 사용하는 그룹에 지원되나요?
[그룹의 종속성 시각화](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) 시에 허용되는 최대 VM 수는 10개입니다. VM이 10개보다 많은 그룹이 있다면 더 작은 그룹 여러 개로 분할한 다음 종속성을 시각화하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

- [Azure Migrate 개요](migrate-overview.md)를 읽어보세요.
- VMware 환경을 [검색하고 평가](tutorial-assessment-vmware.md)하는 방법을 알아봅니다.
