---
title: Azure Migrate - FAQ(질문과 대답) | Microsoft Docs
description: Azure Migrate에 대한 질문과 대답 해결
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: snehaa
ms.openlocfilehash: 08a0312f12b3daab8b7f5e88da118b5bcbeb2f4c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807320"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate - FAQ(질문과 대답)

이 문서는 Azure Migrate에 대한 질문과 대답을 제공합니다. 이 문서를 읽은 후 질문이 있다면 [Azure Migrate 포럼](https://aka.ms/AzureMigrateForum)에 게시해 주세요.

## <a name="general"></a>일반

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Azure Migrate에서 지원되는 Azure 지역은 어디인가요?
Azure Migrate는 현재 다양 한 Azure Migrate 프로젝트를 만들 수 있습니다 하는 지리적 위치를 지원 합니다. 이러한 지역에서 프로젝트를 만들 수 있습니다, 경우에 다른 대상 위치에 대 한 컴퓨터를 여전히 평가할 수 있습니다. 프로젝트 지역은 검색된 메타데이터를 저장하는 데만 사용됩니다.

**지리** | **메타 데이터 저장소 위치** Azure Government | 미국 버지니아 주 정부 아시아 | 동아시아 유럽 또는 동남 아시아 | 남부 유럽 또는 유럽 서 부 United Kingdom | 영국 남부 또는 영국 서 부 미국 | 미국 중서부 또는 미국 서 부 2

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Azure Migrate는 Azure Site Recovery와 어떻게 다른가요?

Azure Migrate 검색, 평가 및 컴퓨터 및 워크 로드를 Azure로 마이그레이션에 도움이 되는 도구를 제공 합니다. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) 재해 복구 솔루션입니다. 두 서비스는 일부 구성 요소를 공유합니다.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Azure Migrate 어플라이언스 (VMware/물리적 서버)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Azure에 Azure Migrate 어플라이언스에 연결 하는 방법

인터넷을 통해 연결 되거나 공용 피어 링이 있는 ExpressRoute를 사용할 수 있습니다.

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Azure Migrate Server 평가 및 마이그레이션에 필요한 네트워크 연결 요구 사항

Azure를 사용 하 여 통신 하도록 Url 및 Azure Migrate에 필요한 포트에 대해 검토 합니다 [VMWare](migrate-support-matrix-vmware.md) 및 [Hyper-v](migrate-support-matrix-hyper-v.md) 행렬을 지원 합니다.

### <a name="can-i-harden-the-appliance-vmware-vm-i-set-up-with-the-ova-template"></a>어플라이언스 OVA 템플릿을 사용 하 여 설정 하는 VMware VM을 강화할 수 있습니다.

추가 구성 요소 (예를 들어 바이러스 백신)을 왼쪽으로 다시 Azure Migrate 어플라이언스에 대 한 필요한 통신 및 방화벽 규칙은 그대로 OVA 템플릿에 추가할 수 있습니다.   

### <a name="to-harden-the-azure-migrate-appliance-what-are-the-recommended-antivirus-av-exclusions"></a>Azure Migrate 어플라이언스를 강화하기 위해 권장되는 AV(바이러스 백신) 예외는 무엇인가요?

어플라이언스의 검색에서 다음 폴더를 제외 해야 합니다.

- Azure Migrate 서비스에 대 한 이진 파일이 포함 된 폴더입니다. 모든 하위 폴더를 제외합니다.
- %ProgramFiles%\ProfilerService  
- Azure Migrate 웹 애플리케이션. 모든 하위 폴더를 제외합니다.
- %SystemDrive%\inetpub\wwwroot
- 데이터베이스 및 로그 파일에 대 한 로컬 캐시입니다. Azure Migrate 서비스를 필요한 읽기/쓰기 액세스가이 폴더에 있습니다.
  - %SystemDrive%\Profiler

### <a name="what-data-is-collected-by-azure-migrate"></a>Azure Migrate에서는 어떤 데이터를 수집하나요?

Azure Migrate 어플라이언스 포함 하는 온-프레미스 Vm에 대 한 메타 데이터를 수집 합니다.

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
- 메모리 사용
- VM에 연결된 각 디스크:
  - 디스크 읽기 처리량
  - 디스크 쓰기 처리량
  - 초당 디스크 읽기 작업
  - 초당 디스크 쓰기 작업
- VM에 연결된 각 네트워크 어댑터:
  - 네트워크 입력
  - 네트워크 출력

Tn 추가 종속성 매핑을 배포 하는 경우 종속성 매핑을 에이전트 FQDN 컴퓨터, 운영 체제, IP 주소, MAC 주소를 VM 및 VM에 대 한 들어오는/나가는 TCP 연결 내에서 실행 되는 프로세스를 포함 한 정보를 수집 합니다. 이 검색은 검색에 대 한 종속성 매핑을 사용 하는 경우에 사용 하는 선택 사항입니다.

### <a name="is-there-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>성능 영향 분석된 ESXi 호스트 환경에 있나요?

연속 프로 파일링으로 성능 데이터를 Azure Migrate 어플라이언스 프로필 온-프레미스 VM 성능 데이터를 측정 하는 컴퓨터. 이 vCenter Server와 ESXi 호스트에 0 거의 성능 영향을 미칩니다.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>수집된 데이터는 어디에 저장되며 저장 기간은 얼마나 되나요?

Azure Migrate 어플라이언스에 의해 수집 된 데이터는 마이그레이션 프로젝트를 만들 때 지정 하는 Azure 위치에 저장 됩니다. 데이터는 Microsoft 구독에 안전 하 게 저장 하 고 Azure Migrate 프로젝트를 삭제 하면 삭제 됩니다.

종속성 시각화를 Vm에 에이전트를 설치 하는 경우 종속성 에이전트에서 수집 된 데이터는 Azure 구독에서 만들어진 Log Analytics 작업 영역에서 미국에 저장 됩니다. 구독에서 Log Analytics 작업 영역을 삭제하면 이 데이터가 삭제됩니다. [자세히 알아보기](concepts-dependency-visualization.md).

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-during-continuous-profiling"></a>연속 프로 파일링 하는 동안 Azure Migrate에서 업로드 된 데이터의 볼륨은 어떻게 되나요?

Azure Migrate로 전송 된 데이터 양이 매개 변수 몇 개에 따라 다릅니다. (각각 하나의 디스크 및 NIC 1 개), 10 개의 컴퓨터를 사용 하 여 Azure Migrate 프로젝트를 나타낼 수 있도록 하루 약 50MB를 보냅니다. Nic에 대 한 데이터 요소의 수에 따라 변경 하는 대략적인 값와 디스크 (전송 된 데이터는 비선형 컴퓨터, Nic 또는 디스크 수가 증가 하는 경우)입니다.

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>데이터를 암호화 된 미사용 및 전송 중 인가요?

모두 예입니다. 메타 데이터를 https 통해 인터넷을 통해 Azure Migrate 서비스에 안전 하 게 전송 됩니다. 메타 데이터에 저장 됩니다는 [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest), 및 [Azure blob 저장소](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) Microsoft 구독에서 암호화 된 미사용 및 합니다.

종속성 에이전트에서 수집 된 데이터 (보안 HTTPS), 전송 중에 암호화 되며 사용자 구독에서 Log Analytics 작업 영역에 저장 됩니다. 미사용 암호화 이기도합니다.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>VCenter Server 및 Azure Migrate 서비스를 사용 하 여 Azure Migrate 어플라이언스 통신 하는 방법

VCenter Server (포트 443)에 연결 하는 기기 어플라이언스를 설정 하는 경우 제공 된 자격 증명을 사용 합니다. VCenter Server VMware PowerCLI를 사용 하 여 vCenter Server에서 관리 되는 Vm에 대 한 메타 데이터를 수집 하도록 쿼리 합니다. 지난 달의 각 VM의 성능 기록을 뿐만 아니라 Vm (코어, 메모리, 디스크, NIC 등)에 대 한 구성 데이터를 수집합니다. 수집 된 메타 데이터 평가 대 한 (인터넷을 통해 HTTPS 통해) Azure Migrate Server 평가에 전송 됩니다. 

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>여러 vCenter 서버에는 동일한 어플라이언스에 연결할 수 있습니까?

예, 여러 vCenter 서버를 검색 하는 단일 Azure Migrate 어플라이언스를 사용할 수 있습니다 하지만 동시에 없습니다. 한 번에 하나씩 검색을 실행 해야 합니다.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Site Recovery에서 사용되는 OVA 템플릿이 Azure Migrate에서 사용되는 OVA와 통합되나요?

현재는 통합이 없습니다. Site Recovery의 .OVA 템플릿은 VMware VM/물리적 서버 복제를 위해 Site Recovery 구성 서버를 설정하는 데 사용됩니다. 합니다. Azure Migrate 사용해 OVA 평가 및 마이그레이션을 위해 vCenter 서버에서 관리 되는 VMware Vm을 검색 하는 데 사용 됩니다.

### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>내 컴퓨터 크기를 변경했습니다. 평가 다시 실행할 수 있습니까?
Azure Migrate 어플라이언스는 온-프레미스 환경에 대 한 정보를 지속적으로 수집합니다. 그러나 평가 온-프레미스 Vm의 지정 시간 스냅숏입니다. 평가 하려는 VM에 대 한 설정을 변경 하면 '다시 계산' 옵션을 사용 하 여 최신 변경 내용으로 평가 업데이트 합니다.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Azure Migrate에서 다중 테넌트 환경을 검색하려면 어떻게 해야 하나요?

VMware에 대 한 테 넌 트 간에 공유 되는 환경이 있고 다른 테 넌 트의 구독에 대 한 테 넌 트의 Vm을 검색 하지 않으려는 경우 vCenter Server 자격 증명 만들기만 검색 하려는 해당 Vm에 대 한 액세스를 사용 하 여 합니다. Azure Migrate 어플라이언스의 검색을 시작 하는 경우에 다음 자격 증명을 사용 합니다.

Hyper-v에 대 한 검색 사용 하 여 Hyper-v 호스트의 자격 증명을 Vm이 동일한 Hyper-v 호스트를 공유 하는 경우 현재 있는지 검색을 분리할 수 없습니다.  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>마이그레이션은 어플라이언스를 사용 하 여 얼마나 많은 Vm은 검색할 수 있습니다?

최대 10,000 VMware Vm 및 최대 단일 마이그레이션 어플라이언스를 사용 하 여 5,000 Hyper-v Vm을 검색할 수 있습니다.  더 많은 컴퓨터를 온-프레미스 환경의 경우 크기를 조정 하는 방법을 알아봅니다 [하이퍼-V](scale-hyper-v-assessment.md) 하 고 [VMware](scale-vmware-assessment.md) 평가 합니다.


## <a name="azure-migrate-server-assessment"></a>Azure로 마이그레이션합니다. 서버 평가

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate는: 서버 평가 물리적 서버에 대 한 평가 지원 하나요?

아니요, Azure Migrate 현재 지원 하지 않습니다 물리적 서버에 대 한 평가 합니다. 

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate에는 VMware 환경을 검색하기 위해 vCenter Server가 필요한가요?

예, Azure Migrate vCenter 서버를 VMware 환경을 검색 해야 합니다. VCenter Server에서 관리 되지 않는 ESXi 호스트의 검색을 지원 하지 않습니다.

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate를 사용 하 여 간의 차이점은 무엇입니까: 서버 평가 및 Map Toolkit?

Azure로 마이그레이션합니다. 서버 평가 마이그레이션 준비 상태 및 Azure로 마이그레이션에 대 한 워크 로드의 평가 도움이 되는 마이그레이션 평가 제공 합니다. [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) 에 최신 버전의 Windows 클라이언트 및 서버 운영 체제 및 소프트웨어 사용 현황 추적에 대 한 계획 된 마이그레이션 등의 다른 기능을 합니다. 이러한 시나리오의 경우 MAP Toolkit을 계속 사용합니다.

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>Azure Migrate는 방법입니다. Azure Site Recovery Deployment Planner에서 다른 서버 평가?

Azure로 마이그레이션합니다. 서버 평가 마이그레이션 계획 도구입니다. Azure Site Recovery Deployment Planner는 재해 복구 계획 도구입니다.

- **VMware/Hyper-v에서 Azure로 마이그레이션**: 온-프레미스 서버를 Azure로 마이그레이션 하려는 경우 Azure Migrate를 사용 합니다. 마이그레이션 계획을 위한 서버 평가 도구입니다. 도구는 온-프레미스 워크 로드를 평가 하 고 지침, insights 및 Azure로 마이그레이션하는 데 도움이 하는 메커니즘을 제공 합니다. 마이그레이션 계획을 사용 하 여 준비가 되 면 Azure Migrate와 같은 도구를 사용할 수 있습니다. 컴퓨터를 Azure로 마이그레이션할 서버 마이그레이션
- **VMware/Hyper-V에서 Azure로의 재해 복구**: Site Recovery를 사용 하 여 Azure에 재해 복구에 대 한 재해 복구 계획에 대 한 Site Recovery Deployment Planner를 사용 합니다. Site Recovery Deployment Planner는 온-프레미스 환경의 심층, 사이트 복구 관련 평가 수행합니다. 복제 및 장애 조치 vm 등의 성공적인 재해 작업에 대 한 Site Recovery에 필요한 권장 사항을 제공 합니다. 

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Azure Migrate를 EA 기업 계약 기반 비용 예측 지원 하나요?

Azure Migrate 현재 지원 하지 않습니다에 대 한 비용 예상 [기업 계약 제품](https://azure.microsoft.com/offers/enterprise-agreement-support/)합니다. 해결 방법은 종 량 제 제품을 지정 하 고 수동으로 평가 속성의 '할인' 필드에 할인율 (구독에 적용)을 지정 하는 것입니다.

  ![할인](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>으로 온-프레미스 크기 조정 및 성능 기반 크기 조정을 간의 차이 무엇입니까?

- Azure Migrate 온-프레미스 크기 조정 VM 성능 데이터 고려 되지 않습니다. 온-프레미스 구성에 따라 Vm 크기를 조절 합니다. 성능 기반 크기 조정에서 크기 조정은 사용률 데이터를 기반으로 합니다.
- 예를 들어 경우 온-프레미스 VM에 4 개 코어와 8GB 메모리 50 %cpu 및 메모리 사용률이 50%를 사용 하 여 온-프레미스로 크기 조정 권장 4 개 코어와 8GB 메모리를 사용 하 여 Azure VM SKU를 합니다. 하지만 사용률을 사용 하는 것으로 간주 되므로 VM SKU를 2 개의 코어 및 4GB를 권장 성능 기반 크기 조정, 합니다.
- 마찬가지로, 디스크 크기 조정 조건 및 저장소 형식 크기 조정-두 평가 속성에 따라 달라 집니다.
= 크기 조정 기준이 성능 기반을 저장소 유형이 자동 인 경우 대상 디스크 유형 (표준 또는 프리미엄)을 식별 하는 경우 디스크의 IOPS 및 처리량 값 간주 됩니다.
- 크기 조정 기준이 성능 기반은 저장소 유형은 프리미엄, 프리미엄 디스크는 것이 좋습니다. SKU 선택은 프리미엄 디스크 온-프레미스 디스크의 크기를 기반으로 합니다. 동일한 논리는 온-프레미스 크기 조정으로 크기 조정 기준이 되 고 저장소 형식이 표준 또는 프리미엄 디스크 크기 조정 하는 데 사용 됩니다.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>성능 기록 및 백분위수 활용률이 크기 추천에 어떤 영향을 주나요?

이러한 속성은 성능 기반 크기 조정에만 적용됩니다.

- Azure Migrate는 온-프레미스 컴퓨터의 성능 기록을 수집 하 고 Azure에서 VM 크기 및 디스크 형식을 추천 하는 데 사용 합니다.
- 어플라이언스 20 초 마다 실시간 사용률 데이터를 수집 하도록 온-프레미스 환경을 지속적으로 프로필을 만듭니다. 이 어플라이언스는 20초 샘플을 롤업하고 15분마다 데이터 요소를 하나씩 만듭니다. 단일 데이터 요소를 만들기 위해, 이 어플라이언스는 20초 샘플에서 최고 값을 선택하여 Azure로 전송합니다.
- Azure (성능 기간 및 성능 기록 백분위 수 값에 따라)에서 평가 만들 때 Azure Migrate 효과적인 사용률 값을 계산한 크기 조정을 위해 사용 합니다.
- 성능 기간 1 일을 설정 하는 경우 95 백 분 위 수, Azure Migrate를 백분위 수 값은 마지막 날에 대 한 수집기에 의해 전송 15 분 샘플 포인트를 사용 하 여 오름차순으로 정렬 하 고으로 95 번째 백분위 수 값을 선택 합니다. 예를 들어 합니다 효율적으로 사용 합니다.
- 95 번째 백분위 수 값 99 번째 백분위 수를 사용 하는 경우에 발생할 수 있는 모든 이상 값을 무시 하 고 있는지 확인 합니다. 해당 기간의 최대 사용량을 선택하고 이상값을 누락하지 않으려면 99번째 백분위 수를 선택해야 합니다.

### <a name="what-is-dependency-visualization"></a>종속성 시각화란?

종속성 시각화를 사용 하면 더 자신 있게 마이그레이션에 대 한 Vm의 그룹을 평가할 수 있습니다. 해당 간 검사 컴퓨터 종속성 평가 실행 하기 전에 합니다. 종속성 시각화에는 하나도 남겨두지를 확인 하 고 Azure로 마이그레이션할 때 예기치 않은 중단을 방지할 수 있습니다. Azure Migrate는 종속성 시각화를 사용 하도록 설정 하려면 Azure Monitor 로그에서 서비스 맵 솔루션을 활용 합니다.

> [!NOTE]
> Azure Government에서는 종속성 시각화 기능을 사용할 수 없습니다.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>종속성 시각화 기능을 사용하면 비용을 지불해야 하나요?

아니요. Azure Migrate 가격 책정에 대해 [자세히 알아봅니다](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>종속성 시각화를 사용하려면 무엇을 설치해야 하나요?

종속성 시각화를 사용하려면 평가할 각 온-프레미스 머신에 에이전트를 다운로드하여 설치해야 합니다.

- 각 머신에 [MMA(Microsoft Monitoring Agent)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)를 설치해야 합니다.
- 각 머신에 [종속성 에이전트](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure)를 설치해야 합니다.
- 또한 인터넷에 연결되지 않은 머신이 있으면 해당 머신에 Log Analytics 게이트웨이를 다운로드하여 설치해야 합니다.

종속성 시각화를 사용 하지 않으면 이러한 에이전트를 필요 하지 않습니다.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>종속성 시각화에 기존 작업 영역을 사용할 수 있나요?

예, 마이그레이션 프로젝트에 기존 작업 영역을 연결 하 고 종속성 시각화를 위해 활용할 수 있습니다. [자세히 알아보기](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>종속성 시각화 보고서를 내보낼 수 있나요?

아니요, 종속성 시각화를 내보낼 수 없습니다. 그러나 Azure Migrate에서 종속성 시각화에 서비스 맵을 사용하므로 [서비스 맵 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections)를 사용하여 종속성을 json 형식으로 가져올 수 있습니다.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>MMA(Microsoft Monitoring Agent) 및 종속성 에이전트 설치를 자동화하려면 어떻게 해야 하나요?

[이 스크립트를 사용 하 여](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) 에이전트의 설치에 대 한 합니다. [다음이 지침에 따라](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) 명령줄 또는 자동화를 사용 하 여 MMA를 설치 합니다. MMA 활용 [이 스크립트](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)합니다.

스크립트 외에도 System Center Configuration Manager와 같은 배포 도구를 사용할 수 있습니다 [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) 에이전트를 배포 하는 등입니다.

### <a name="what-operating-systems-are-supported-by-mma"></a>MMA 어떤 운영 체제를 지원 하나요?

- [검토](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) MMA에서 지 원하는 Windows 운영 체제의 목록입니다.
- [검토] https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) MMA에서 지 원하는 Linux 운영 체제의 목록입니다.

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>종속성 에이전트에서 지 원하는 운영 체제는 무엇입니까?

[검토](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems) 종속성 에이전트에서 지 원하는 Windows 운영 체제입니다.
[검토](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems) 종속성 에이전트에서 지 원하는 Linux 운영 체제의 목록입니다.

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>1 시간 이상에 대 한 Azure Migrate의 종속성을 시각화할 수 있나요?
아니요, 최대 한 시간에 대 한 종속성을 시각화할 수 있습니다. 지난 달에 최대 기록의 특정 날짜 돌아갈 수 있지만 시각화에 대 한 최대 기간은 1 시간입니다. 예를 들어 기간, 어제에 대 한 종속성을 보려면 종속성 맵에 사용할 수 있지만 1 시간 창에 대 한 보기만 할 수 있습니다. 그러나 Azure Monitor의 로그를 사용할 수 있습니다 [종속성 데이터를 쿼리](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) 더 긴 기간 동안.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-ten-vms"></a>종속성 시각화가 10 개가 넘는 Vm 사용 하 여 그룹에 대 한 지원 되나요?
할 수 있습니다 [그룹에 대 한 종속성을 시각화](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) 최대 10 개의 Vm을 사용 하 여 합니다. 10 개가 넘는 Vm 사용 하 여 그룹을 설정한 경우 더 작은 그룹에 그룹을 분할 하는 것이 좋습니다 하 고 종속성을 시각화 합니다.

## <a name="azure-migrate-server-migration"></a>Azure로 마이그레이션합니다. 서버 마이그레이션

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>Azure Migrate는 방법입니다. Azure Site Recovery에서 다른 서버 마이그레이션?

Azure로 마이그레이션합니다. 서버 마이그레이션 에이전트 기반 서버에서 Azure로 마이그레이션에 대 한 Site Recovery 복제 엔진을 활용합니다.
## <a name="next-steps"></a>다음 단계
[Azure Migrate 개요](migrate-services-overview.md)를 읽어보세요.
