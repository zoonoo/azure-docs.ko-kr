---
title: Azure Migrate의 검색, 평가 및 종속성 분석에 대 한 질문
description: Azure Migrate에서 검색, 평가 및 종속성 분석에 대 한 일반적인 질문에 대 한 답변을 얻습니다.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 6c4dfed27a105fad951ae12ca053b6d86772717a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032571"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>검색, 평가 및 종속성 분석-일반적인 질문

이 문서에서는 Azure Migrate의 검색, 평가 및 종속성 분석에 대 한 일반적인 질문에 답변 합니다. 다른 질문이 있는 경우 다음 리소스를 확인 하세요.

- Azure Migrate에 대 한 [일반적인 질문](resources-faq.md)
- [Azure Migrate 어플라이언스](common-questions-appliance.md) 에 대 한 질문
- [서버 마이그레이션](common-questions-server-migration.md) 에 대 한 질문
- [Azure Migrate 포럼](https://aka.ms/AzureMigrateForum) 에서 질문에 대 한 답변 받기


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Azure Migrate에서 검색 및 평가에 대해 지원 되는 지역은 무엇 인가요?

[퍼블릭](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드](migrate-support-matrix.md#supported-geographies-azure-government)에 대해 지원되는 지역을 검토합니다.


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>어플라이언스로 검색할 수 있는 Vm 수는 몇 개입니까?

단일 어플라이언스를 사용 하 여 최대 1만 VMware Vm, 최대 5000 Hyper-v Vm 및 최대 1000 개의 물리적 서버를 검색할 수 있습니다. 컴퓨터가 더 많은 경우 [hyper-v 평가를 확장](scale-hyper-v-assessment.md)하거나 [VMware 평가를 확장](scale-vmware-assessment.md)하거나 [물리적 서버 평가](scale-physical-assessment.md)를 확장 하는 방법을 참조 하세요.

## <a name="how-do-i-choose-the-assessment-type"></a>평가 유형을 선택하려면 어떻게 해야 하나요?

- Azure vm으로 마이그레이션하기 위한 온-프레미스 [VMware vm](how-to-set-up-appliance-vmware.md), [hyper-v vm](how-to-set-up-appliance-hyper-v.md)및 [물리적 서버](how-to-set-up-appliance-physical.md) 를 평가 하려는 경우 **azure vm 평가** 를 사용 합니다. [자세한 내용](concepts-assessment-calculation.md)

- Azure sql Managed Instance Azure SQL Database으로 마이그레이션하기 위해 VMware 환경에서 온-프레미스 SQL Server를 평가 하려는 경우 평가 유형 **AZURE sql** 을 사용 합니다. [자세한 내용](concepts-assessment-calculation.md)

    > [!Note]
    > VMware 환경에서 실행 중인 SQL Server 인스턴스 및 데이터베이스의 검색 및 평가는 현재 미리 보기로 제공 됩니다. 이 기능을 사용해 보려면 [**이 링크**](https://aka.ms/AzureMigrate/SQL)를 사용하여 **오스트레일리아 동부** 지역에서 프로젝트를 만듭니다. 오스트레일리아 동부에 이미 프로젝트가 있고 이 기능을 사용해 보려는 경우 포털에서 이러한 [**필수 구성 요소**](how-to-discover-sql-existing-project.md)를 완료했는지 확인하세요.

- 이 평가 유형을 사용 하 여 [Azure Vmware 솔루션 (avs)](../azure-vmware/introduction.md) 으로 마이그레이션하기 위한 온-프레미스 [VMware vm](how-to-set-up-appliance-vmware.md) 을 평가 하려는 경우에는 **azure vmware solution (avs)** 평가를 사용 합니다. [자세히 알아보기](concepts-azure-vmware-solution-assessment-calculation.md)

- 두 가지 유형의 평가를 실행하는 경우에만 VMware 컴퓨터에서 공통 그룹을 사용할 수 있습니다. Azure Migrate에서 AVS 평가를 처음 실행하는 경우 새 VMware 컴퓨터 그룹을 만드는 것이 좋습니다.
 

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>Azure VM 및/또는 AVS 평가 보고서의 일부/모든 서버에 대 한 성능 데이터가 누락 된 이유는 무엇 인가요?

"성능 기반" 평가의 경우 Azure Migrate 어플라이언스에서 온-프레미스 VM에 대한 성능 데이터를 수집할 수 없는 경우 평가 보고서 내보내기에 'PercentageOfCoresUtilizedMissing' 또는 'PercentageOfMemoryUtilizedMissing'이 표시됩니다. 다음을 확인하세요.

- 평가를 만드는 동안 VM의 전원이 켜져 있는지 확인합니다.
- 메모리 카운터만 없고 Hyper-v Vm을 평가 하려고 하는 경우 이 시나리오에서는 Vm에서 동적 메모리를 사용 하도록 설정 하 고 평가를 ' 다시 계산 ' 하 여 최신 변경 내용을 반영 하세요. VM이 동적 메모리를 사용 하도록 설정 된 경우에만 어플라이언스에서 Hyper-v Vm에 대 한 메모리 사용률 값을 수집할 수 있습니다.

- 모든 성능 카운터가 누락 된 경우 포트 443 (HTTPS)의 아웃 바운드 연결이 허용 되는지 확인 합니다.

    > [!Note]
    > 성능 카운터가 누락 된 경우 Azure Migrate: 서버 평가는 온-프레미스에서 할당 된 코어/메모리로 대체 되 고 그에 따라 VM 크기를 권장 합니다.


## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>Azure SQL 평가에서 일부/모든 SQL 인스턴스/데이터베이스에 대 한 성능 데이터가 누락 된 이유는 무엇 인가요?

성능 데이터가 수집되는지 확인하려면 다음을 확인하세요.

- 평가를 생성 하는 기간 동안 SQL Server의 전원이 켜 지는 경우
- Azure Migrate SQL 에이전트의 연결 상태가 ' 연결 됨 ' 인 경우 마지막 하트 비트를 확인 합니다. 
- 검색 된 SQL 인스턴스 블레이드의 모든 SQL 인스턴스에 대 한 연결 상태 Azure Migrate ' 연결 됨 ' 인 경우
- 모든 성능 카운터가 누락된 경우 포트 443(HTTPS)에서 아웃바운드 연결이 허용되는지 확인합니다.

성능 카운터가 없는 경우 Azure SQL 평가는 해당 인스턴스/데이터베이스에 대해 가장 작은 Azure SQL 구성을 권장 합니다.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>내 평가의 신뢰 등급이 낮은 이유는 무엇인가요?

신뢰 등급은 평가를 계산하는 데 필요한 [사용 가능한 데이터 요소](./concepts-assessment-calculation.md#ratings)의 백분율을 기준으로 "성능 기반" 평가에 대해 계산됩니다. 아래에는 평가의 신뢰 등급이 낮아질 수 있는 이유가 나와 있습니다.

- 평가를 작성하는 기간 동안 환경을 프로파일링하지 않았습니다. 예를 들어 성능 기간을 일주일로 설정하여 평가를 만드는 경우 검색 시작 후 일주일 이상 기다려야 데이터 요소가 수집됩니다. 이 기간 동안 기다릴 수 없으면 성능 기간을 더 작은 기간으로 변경 하 고 평가를 **다시 계산** 하십시오.
 
- 평가는 일부 또는 모든 서버에 대한 성능 데이터를 평가 기간에 수집할 수 없습니다. 높은 신뢰 등급의 경우 다음을 확인 하세요. 
    - 평가 기간 동안 서버가 켜 집니다.
    - 포트 443에 대 한 아웃 바운드 연결이 허용 됩니다.
    - Hyper-v 서버의 경우 동적 메모리를 사용할 수 있습니다. 
    - Azure Migrate 에이전트의 연결 상태는 ' 연결 됨 '이 고 마지막 하트 비트를 확인 합니다.
    - Azure SQL 평가의 경우 검색 된 SQL 인스턴스 블레이드의 모든 SQL 인스턴스에 대 한 연결 상태가 "연결 됨" Azure Migrate.

    신뢰 등급의 최신 변경 내용을 반영하려면 평가를 **다시 계산** 하세요.

- Azure VM 및 AVS 평가의 경우 검색을 시작한 후 소수의 서버가 생성 되었습니다. 예를 들어 지난 한 달의 성능 기록에 대 한 평가를 만드는 중이지만 환경에 몇 개의 서버가 몇 주 전에만 생성 된 경우를 예로 들 수 있습니다. 이 경우 새 서버에 대 한 성능 데이터는 전체 기간 동안 사용할 수 없으며 신뢰 등급이 낮습니다. [자세히 알아보기](./concepts-assessment-calculation.md#confidence-ratings-performance-based)

- Azure SQL 평가의 경우 검색을 시작한 후 일부 SQL 인스턴스 또는 데이터베이스가 만들어졌습니다. 예를 들어 지난 한 달의 성능 기록에 대 한 평가를 만드는 경우 몇 주 전에 환경에서 몇 개의 SQL 인스턴스 또는 데이터베이스가 생성 된 것입니다. 이 경우 새 서버에 대 한 성능 데이터는 전체 기간 동안 사용할 수 없으며 신뢰 등급이 낮습니다. [자세히 알아보기](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="i-want-to-try-out-the-new-azure-sql-assessment-feature-in-azure-migrate"></a>Azure Migrate에서 새로운 Azure SQL 평가 기능을 사용하려고 합니다.
이 기능을 사용해 보려면 [이 링크](https://go.microsoft.com/fwlink/?linkid=2155668L)를 통해 **오스트레일리아 동부** 지역의 프로젝트를 만드세요.
- 시작하려면 [검색](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware) 및 [평가](https://docs.microsoft.com/azure/migrate/tutorial-assess-sql) 자습서를 참조하세요.
- VMware 환경에서 실행 중인 SQL Server 인스턴스 및 데이터베이스의 검색 및 평가는 현재 미리 보기로 제공됩니다.

## <a name="i-cant-see-some-servers-when-i-am-creating-an-azure-sql-assessment"></a>Azure SQL 평가를 만들 때 일부 서버를 볼 수 없습니다.

- Azure SQL 평가는 SQL 인스턴스가 검색된 서버에서만 실행할 수 있습니다. 평가할 서버와 SQL 인스턴스가 표시되지 않는 경우 검색이 완료될 때까지 기다렸다가 평가를 만드세요. 
- 평가를 만드는 동안 이전에 만든 그룹을 볼 수 없는 경우에는 해당 그룹에서 SQL 인스턴스를 사용 하지 않는 VMware 서버 또는 서버를 모두 제거 하십시오.
- Azure Migrate에서 Azure SQL 평가를 처음 실행하는 경우 새 서버 그룹을 만드는 것이 좋습니다.

## <a name="i-want-to-understand-how-was-the-readiness-for-my-instance-computed"></a>내 인스턴스의 준비 상태를 어떻게 알 수 있나요?
대상 Azure SQL 배포 형식(Azure SQL Database 또는 Azure SQL Managed Instance)을 사용하여 기능 호환성 검사를 수행한 후에 SQL 인스턴스에 대한 준비 상태가 계산되었습니다. [자세히 알아보기](./concepts-azure-sql-assessment-calculation.md#calculate-readiness)

## <a name="why-is-the-readiness-for-all-my-sql-instances-marked-as-unknown"></a>모든 SQL 인스턴스가 알 수 없는 것으로 표시 되는 이유는 무엇 인가요?
최근에 검색을 시작 하 여 계속 진행 중인 경우 일부 또는 모든 SQL 인스턴스에 대 한 준비 상태가 알 수 없음으로 표시 될 수 있습니다. 어플라이언스가 환경을 프로파일링하도록 어느 정도 기다린 후 평가를 다시 계산하는 것이 좋습니다.
SQL 검색은 24 시간 마다 한 번씩 수행 되며, 최신 구성 변경 내용이 반영 되도록 하루 중에 대기 해야 할 수도 있습니다. 

## <a name="why-is-the-readiness-for-some-of-my-sql-instances-marked-as-unknown"></a>일부 SQL 인스턴스를 알 수 없는 것으로 표시 하는 이유는 무엇 인가요?
이 문제는 다음과 같은 경우에 발생할 수 있습니다. 
- 검색이 아직 진행 중입니다. 어플라이언스가 환경을 프로파일링하도록 어느 정도 기다린 후 평가를 다시 계산하는 것이 좋습니다.
- 오류 및 알림 블레이드에서 수정해야 하는 몇 가지 검색 문제가 있습니다.

SQL 검색은 24 시간 마다 한 번씩 수행 되며, 최신 구성 변경 내용이 반영 되도록 하루 중에 대기 해야 할 수도 있습니다.

## <a name="my-assessment-is-in-outdated-state"></a>내 평가가 오래됨 상태입니다.

### <a name="azure-vmavs-assessment"></a>Azure VM/AVS 평가
평가 된 그룹에 있는 Vm에 대 한 온-프레미스 변경 내용이 있는 경우 평가는 오래 된 것으로 표시 됩니다. 아래 속성의 변경 내용 중 하나 이상으로 인해 평가를 "오래 된" 것으로 표시할 수 있습니다.
- 프로세서 코어 수
- 할당 된 메모리
- 부팅 유형 또는 펌웨어
- 운영 체제 이름, 버전 및 아키텍처
- 디스크 수
- 네트워크 어댑터 수
- 디스크 크기 변경 (할당 된 GB)
- Nic 속성 업데이트. 예: Mac 주소 변경, IP 주소 추가 등

평가의 최신 변경 내용을 반영 하기 위해 평가를 **다시 계산** 하세요.

### <a name="azure-sql-assessment"></a>Azure SQL 평가
평가된 그룹에 있는 온-프레미스 SQL 인스턴스 및 데이터베이스에 변경 내용이 있는 경우 평가는 **오래됨** 상태로 표시됩니다.
- SQL 인스턴스가 추가되었거나 서버에서 제거되었습니다.
- SQL 데이터베이스가 추가되었거나 SQL 인스턴스에서 제거되었습니다.
- 20%를 초과하여 변경된 SQL 인스턴스의 총 데이터베이스 크기
- 프로세서 코어 수 및/또는 할당 된 메모리 변경

평가의 최신 변경 내용을 반영 하기 위해 평가를 **다시 계산** 하세요.

## <a name="why-was-i-recommended-a-particular-target-deployment-type"></a>특정 대상 배포 유형을 권장하는 이유는 무엇인가요?
Azure Migrate는 SQL 인스턴스와 호환되는 특정 Azure SQL 배포 유형을 권장합니다. Microsoft 권장 대상으로 마이그레이션하면 전반적인 마이그레이션 노력이 줄어듭니다. 이 Azure SQL 구성(SKU)은 SQL 인스턴스 및 관리하는 데이터베이스의 성능 특성을 고려한 후 권장되었습니다. 여러 Azure SQL 구성이 적합한 경우에는 가장 비용 효율적인 것이 좋습니다. [자세히 알아보기](./concepts-azure-sql-assessment-calculation.md#calculate-sizing)

## <a name="what-deployment-target-should-i-choose-if-my-sql-instance-is-ready-for-azure-sql-db-and-azure-sql-mi"></a>SQL 인스턴스가 Azure SQL DB 및 Azure SQL MI에 대해 준비된 경우 어떤 배포 대상을 선택해야 하나요? 
인스턴스가 Azure SQL DB 및 Azure SQL MI 둘 다에 대해 준비된 경우 Azure SQL 구성의 예상 비용이 낮은 대상 배포 유형을 선택하는 것이 좋습니다.

## <a name="why-is-my-instance-marked-as-potentially-ready-for-azure-vm-in-my-azure-sql-assessment"></a>내 인스턴스가 azure SQL 평가에서 Azure VM에 대해 잠재적으로 준비 된 것으로 표시 되는 이유는 무엇 인가요?
이는 평가 속성에서 선택한 대상 배포 유형이 **권장됨** 이고 SQL 인스턴스가 Azure SQL Database 및 Azure SQL Managed Instance에 대해 준비되지 않은 경우에 발생할 수 있습니다. 사용자는 Azure Migrate에서 **Azure VM** 평가 유형으로 평가를 만들어 인스턴스가 실행되는 서버가 Azure VM으로 마이그레이션할 준비가 되었는지 확인하는 것이 좋습니다.
사용자는 평가 유형을 **AZURE vm** 으로 사용 하 여 Azure Migrate 평가를 만들어 인스턴스가 실행 되 고 있는 서버를 azure vm으로 마이그레이션할 준비가 되었는지 확인 하는 것이 좋습니다.
- Azure Migrate의 azure VM 평가는 현재까지 진행 되 고 있으며 Azure 가상 머신에서 SQL 인스턴스 및 데이터베이스를 실행 하는 특정 성능 메트릭을 고려 하지 않습니다. 
- 서버에서 Azure VM 평가를 실행하는 경우 서버에서 실행되는 모든 인스턴스에 대해 권장되는 크기 및 예상 비용을 계산하고 서버 마이그레이션 도구를 사용하여 Azure VM으로 마이그레이션할 수 있습니다. 마이그레이션하기 전에 Azure Virtual Machines의 SQL Server에 대한 [성능 지침을 검토](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices)합니다.

## <a name="i-cant-see-some-databases-in-my-assessment-even-though-the-instance-is-part-of-the-assessment"></a>인스턴스가 평가의 일부인 경우에도 내 평가에서 일부 데이터베이스를 볼 수 없습니다.

Azure SQL 평가는 온라인 상태에 있는 데이터베이스만 포함합니다. 데이터베이스가 다른 상태에 있는 경우 평가는 이러한 데이터베이스에 대한 준비, 크기 조정 및 비용 계산을 무시합니다. 이러한 데이터베이스를 평가하려는 경우에는 데이터베이스의 상태를 변경하고 잠시 후 평가를 다시 계산하세요.

## <a name="i-want-to-compare-costs-for-running-my-sql-instances-on-azure-vm-vs-azure-sql-databaseazure-sql-managed-instance"></a>Azure VM에서의 SQL 인스턴스 실행에 대 한 비용과 Azure SQL Database/Azure SQL Managed Instance를 비교 하려고 합니다.

**Azure SQL** 평가에 사용된 것과 동일한 그룹에서 **Azure VM** 유형을 사용하여 평가를 만들 수 있습니다. 그런 다음 두 보고서를 나란히 비교할 수 있습니다. 그러나 Azure Migrate의 Azure VM 평가는 현재 리프트 앤 시프트에 초점을 맞춘 후 Azure 가상 머신에서 SQL 인스턴스 및 데이터베이스를 실행하는 특정 성능 메트릭을 고려하지 않습니다. 서버에서 Azure VM 평가를 실행하는 경우 서버에서 실행되는 모든 인스턴스에 대해 권장되는 크기 및 예상 비용을 계산하고 서버 마이그레이션 도구를 사용하여 Azure VM으로 마이그레이션할 수 있습니다. 마이그레이션하기 전에 Azure Virtual Machines의 SQL Server에 대한 [성능 지침을 검토](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices)합니다.

## <a name="the-storage-cost-in-my-azure-sql-assessment-is-zero"></a>Azure SQL 평가의 저장소 비용은 0입니다.
Azure SQL Managed Instance의 경우 첫 번째 32 g b/인스턴스/월 저장소에 대 한 저장소 비용이 추가 되지 않으며 저장소에 대 한 추가 저장소 비용이 32GB 증분으로 추가 됩니다. [자세한 내용](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>Azure VMware 솔루션 (AVS) 평가를 만들 때 일부 그룹을 볼 수 없습니다.

- VMware 컴퓨터만 있는 그룹에서 AVS 평가를 수행할 수 있습니다. AVS 평가를 수행하려는 경우 그룹에서 VMware가 아닌 컴퓨터를 모두 제거하세요.
- Azure Migrate에서 AVS 평가를 처음 실행하는 경우 새 VMware 컴퓨터 그룹을 만드는 것이 좋습니다.

## <a name="i-cant-see-some-vm-types-in-azure-government"></a>Azure Government에서 일부 VM 유형을 볼 수 없습니다.

평가 및 마이그레이션에 지원 되는 VM 유형은 Azure Government 위치의 가용성에 따라 달라 집니다. Azure Government에서 VM 유형을 [검토 하 고 비교할](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) 수 있습니다.

## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>VM의 크기가 변경 되었습니다. 평가를 다시 실행할 수 있나요?

Azure Migrate 어플라이언스는 온-프레미스 환경에 대 한 정보를 지속적으로 수집 합니다.  평가는 온-프레미스 Vm의 지정 시간 스냅숏입니다. 평가 하려는 VM의 설정을 변경 하는 경우 다시 계산 옵션을 사용 하 여 평가를 최신 변경 내용으로 업데이트 합니다.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>다중 테 넌 트 환경에서 Vm을 검색 어떻게 할까요??

- **VMware**: 환경이 테 넌 트 간에 공유 되 고 다른 테 넌 트의 구독에서 테 넌 트의 vm을 검색 하지 않으려는 경우 검색 하려는 vm에만 액세스할 수 있는 VMware vCenter Server 자격 증명을 만듭니다. 그런 다음 Azure Migrate 어플라이언스에서 검색을 시작할 때 해당 자격 증명을 사용 합니다.
- **Hyper-v**: 검색에서 hyper-v 호스트 자격 증명을 사용 합니다. Vm이 동일한 Hyper-v 호스트를 공유 하는 경우 현재 검색을 분리할 수 있는 방법이 없습니다.  

## <a name="do-i-need-vcenter-server"></a>VCenter Server 필요 한가요?

예, Azure Migrate는 VMware 환경에서 vCenter Server를 사용 하 여 검색을 수행 해야 합니다. Azure Migrate는 vCenter Server으로 관리 되지 않는 ESXi 호스트의 검색을 지원 하지 않습니다.

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Azure VM 평가의 크기 조정 옵션은 무엇 인가요?

온-프레미스 크기 조정으로 Azure Migrate는 평가를 위해 VM 성능 데이터를 고려 하지 않습니다. Azure Migrate은 온-프레미스 구성에 따라 VM 크기를 평가 합니다. 성능 기반 크기 조정을 사용 하 여 크기는 사용률 데이터를 기반으로 합니다.

예를 들어 온-프레미스 VM에 4 개의 코어와 8gb의 메모리가 50% CPU 사용률, 50%의 메모리 사용률을 포함 하는 경우:
- 온-프레미스 크기 조정에서는 4 개의 코어와 8gb의 메모리가 있는 Azure VM SKU를 권장 합니다.
- 성능 기반 크기 조정에서는 두 개의 코어와 4gb의 메모리가 있는 VM SKU를 사용 하는 것이 좋습니다.

마찬가지로 디스크 크기 조정은 크기 조정 조건 및 저장소 유형에 따라 달라 집니다.
- 크기 조정 기준이 성능 기반이 고 저장소 유형이 자동 인 경우, Azure Migrate 대상 디스크 유형 (Standard 또는 Premium)을 식별할 때 디스크의 IOPS 및 처리량 값을 고려 합니다.
- 크기 조정 기준이 성능 기반이 고 저장소 유형이 프리미엄 인 경우 Azure Migrate 온-프레미스 디스크의 크기에 따라 프리미엄 디스크 SKU를 권장 합니다. 크기 조정이 온-프레미스이 고 저장소 유형이 Standard 또는 Premium 인 경우 동일한 논리가 디스크 크기 조정에 적용 됩니다.

## <a name="does-performance-history-and-utilization-affect-sizing-in-an-azure-vm-assessment"></a>성능 기록 및 사용률이 Azure VM 평가의 크기 조정에 영향을 미칩니까?

예, 성능 기록 및 사용률이 Azure VM 평가의 크기 조정에 영향을 줍니다.

### <a name="performance-history"></a>성능 기록

성능 기반 크기 조정의 경우에만 Azure Migrate 온-프레미스 컴퓨터의 성능 기록을 수집 하 고이를 사용 하 여 Azure에서 VM 크기 및 디스크 형식을 권장 합니다.

1. 어플라이언스는 온-프레미스 환경을 지속적으로 프로 파일링 하 여 20 초 마다 실시간 사용률 데이터를 수집 합니다.
2. 어플라이언스는 수집 된 20 초 샘플을 롤업 하 고이를 사용 하 여 15 분 마다 단일 데이터 요소를 만듭니다.
3. 어플라이언스는 데이터 요소를 만들기 위해 20 초의 모든 샘플에서 최고 값을 선택 합니다.
4. 어플라이언스는 데이터 요소를 Azure로 전송 합니다.

### <a name="utilization"></a>사용률

Azure에서 평가를 만들 때 성능 기간 및 성능 기록 백분위 수 값에 따라 Azure Migrate는 유효한 사용률 값을 계산 하 여 크기 조정에 사용 합니다.

예를 들어, 성능 기간을 1 일로 설정 하 고 백분위 수 값을 95 번째 백분위 수로 설정 하는 경우 Azure Migrate는 수집기에서 지난 날짜의 오름차순으로 보낸 15 분 샘플 지점을 정렬 합니다. 95 번째 백분위 수 값을 유효 사용률으로 선택 합니다.

95 번째 백분위 수 값을 사용 하면 이상 값이 무시 됩니다. Azure Migrate에서 99 번째 백분위 수를 사용 하는 경우 이상 값이 포함 될 수 있습니다. 이상 값이 누락 되지 않은 기간에 대 한 피크 사용량을 선택 하려면 99 번째 백분위 수를 사용 하도록 Azure Migrate를 설정 합니다.


## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>가져오기 기반 평가는 검색 원본을 어플라이언스로 사용 하는 평가와 어떻게 다르며?

가져오기 기반 Azure VM 평가는 CSV 파일을 사용 하 여 Azure Migrate로 가져온 컴퓨터로 생성 된 평가입니다. 서버 이름, 코어, 메모리 및 운영 체제 중에서 4 개의 필드만을 가져와야 합니다. 유의 해야 할 몇 가지 사항은 다음과 같습니다. 
 - 부팅 형식 매개 변수에 대 한 가져오기 기반 평가에서 준비 기준이 더 엄격 하지 않습니다. 부팅 유형을 제공 하지 않으면 컴퓨터에 BIOS 부팅 유형이 있고 컴퓨터가 **조건부로 준비** 된 것으로 표시 되어 있지 않다고 가정 합니다. 검색 원본을 어플라이언스로 평가 하는 경우 부팅 유형이 없는 경우 준비 상태는 조건에 따라 **준비** 로 표시 됩니다. 준비 계산의 이러한 차이점은 사용자에 게 가져오기 기반 평가를 수행 하는 경우 마이그레이션 계획의 초기 단계에서 컴퓨터에 대 한 모든 정보가 없을 수 있기 때문입니다. 
 - 성능 기반 가져오기 평가에서는 사용자가 올바른 크기 조정 계산에 제공 하는 사용률 값을 사용 합니다. 사용률 값은 사용자가 제공 하므로 평가 속성에서 **성능 기록** 및 **백분위 수 사용률** 옵션을 사용할 수 없습니다. 검색 원본을 어플라이언스로 평가에서 선택 된 백분위 수 값은 어플라이언스에서 수집 된 성능 데이터에서 선택 됩니다.

## <a name="why-is-the-suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>가져오기 기반 AVS 평가에서 제안 된 마이그레이션 도구가 알 수 없음으로 표시 되는 이유는 무엇 인가요?

CSV 파일을 통해 가져온 컴퓨터의 경우에는 AVS 평가의 기본 마이그레이션 도구를 알 수 없습니다. 그러나 VMware 컴퓨터의 경우에는 HCX (VMware 하이브리드 클라우드 확장) 솔루션을 사용 하는 것이 좋습니다. [자세한 정보](../azure-vmware/tutorial-deploy-vmware-hcx.md).


## <a name="what-is-dependency-visualization"></a>종속성 시각화란?

종속성 시각화를 사용 하면 더 강력 하 게 마이그레이션할 Vm 그룹을 평가할 수 있습니다. 종속성 시각화는 평가를 실행 하기 전에 컴퓨터 종속성을 교차 확인 합니다. 이 기능을 사용 하면 아무것도 유지 되지 않고 Azure로 마이그레이션할 때 예기치 않은 중단을 방지할 수 있습니다. Azure Migrate는 Azure Monitor의 서비스 맵 솔루션을 사용하여 종속성 시각화를 사용하도록 설정합니다. [자세한 정보를 알아보세요](concepts-dependency-visualization.md).

> [!NOTE]
> 에이전트 기반 종속성 분석은 Azure Government에서 사용할 수 없습니다. 에이전트 없는 종속성 분석을 사용할 수 있습니다.

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>에이전트 기반 및 에이전트 없는의 차이점은 무엇 인가요?

에이전트 없는 시각화와 에이전트 기반 시각화 간의 차이점은 표에 요약 되어 있습니다.

**요구 사항** | **에이전트 없음** | **에이전트 기반**
--- | --- | ---
지원 | 이 옵션은 현재 미리 보기 상태 이며 VMware Vm에 대해서만 사용할 수 있습니다. 지원 되는 운영 체제를 [검토](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) 합니다. | GA (일반 공급)를 사용 합니다.
에이전트 | 교차 확인 하려는 컴퓨터에 에이전트를 설치할 필요가 없습니다. | [MMA (Microsoft Monitoring agent)](../azure-monitor/agents/agent-windows.md)및 [종속성 에이전트](../azure-monitor/agents/agents-overview.md#dependency-agent)를 분석 하려는 각 온-프레미스 컴퓨터에 설치 되는 에이전트입니다. 
필수 구성 요소 | 필수 구성 요소 및 배포 요구 사항을 [검토](concepts-dependency-visualization.md#agentless-analysis) 합니다. | 필수 구성 요소 및 배포 요구 사항을 [검토](concepts-dependency-visualization.md#agent-based-analysis) 합니다.
Log Analytics | 필수 아님. | Azure Migrate는 종속성 시각화에 대한 [Azure Monitor 로그](../azure-monitor/logs/log-query-overview.md)의 [서비스 맵](../azure-monitor/vm/service-map.md) 솔루션을 사용합니다. [자세한 정보를 알아보세요](concepts-dependency-visualization.md#agent-based-analysis).
작동 방법 | 종속성 시각화에 사용 되는 컴퓨터에서 TCP 연결 데이터를 캡처합니다. 검색 후 5 분 간격으로 데이터를 수집 합니다. | 컴퓨터에 설치 된 서비스 맵 에이전트는 각 프로세스에 대 한 TCP 프로세스 및 인바운드/아웃 바운드 연결에 대 한 데이터를 수집 합니다.
데이터 | 원본 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름입니다.<br/><br/> 대상 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름 및 포트입니다. | 원본 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름입니다.<br/><br/> 대상 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름 및 포트입니다.<br/><br/> 연결 수, 대기 시간 및 데이터 전송 정보를 수집 하 고 Log Analytics 쿼리에 사용할 수 있습니다. 
시각화 | 단일 서버에 대 한 종속성 맵은 1 시간에서 30 일 동안 볼 수 있습니다. | 단일 서버의 종속성 맵입니다.<br/><br/> 지도는 한 시간에 한 해 볼 수 있습니다.<br/><br/> 서버 그룹의 종속성 맵입니다.<br/><br/> 지도 보기에서 그룹의 서버를 추가 하 고 제거 합니다.
데이터 내보내기 | 지난 30 일간의 데이터는 CSV 형식으로 다운로드할 수 있습니다. | Log Analytics를 사용 하 여 데이터를 쿼리할 수 있습니다.


## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>에이전트 없는 종속성 분석용 어플라이언스를 배포 해야 하나요?

예, [Azure Migrate 어플라이언스](migrate-appliance.md) 를 배포 해야 합니다.

## <a name="do-i-pay-for-dependency-visualization"></a>종속성 시각화에 대해 비용을 지불 하나요?

아니요. [Azure Migrate 가격 책정](https://azure.microsoft.com/pricing/details/azure-migrate/)에 대해 자세히 알아보세요.

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>에이전트 기반 종속성 시각화에는 무엇을 설치 해야 하나요?

에이전트 기반 종속성 시각화를 사용 하려면 평가 하려는 각 온-프레미스 컴퓨터에 에이전트를 다운로드 하 여 설치 합니다.

- [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)
- [종속성 에이전트](../azure-monitor/agents/agents-overview.md#dependency-agent)
- 인터넷에 연결 되지 않은 컴퓨터가 있는 경우 해당 컴퓨터에 Log Analytics 게이트웨이를 다운로드 하 여 설치 합니다.

에이전트 기반 종속성 시각화를 사용 하는 경우에만 이러한 에이전트가 필요 합니다.

## <a name="can-i-use-an-existing-workspace"></a>기존 작업 영역을 사용해도 되나요?

예, 에이전트 기반 종속성 시각화의 경우 기존 작업 영역을 마이그레이션 프로젝트에 연결 하 고 종속성 시각화에 사용할 수 있습니다. 

## <a name="can-i-export-the-dependency-visualization-report"></a>종속성 시각화 보고서를 내보낼 수 있나요?

아니요. 에이전트 기반 시각화에서 종속성 시각화 보고서를 내보낼 수 없습니다. 그러나 Azure Migrate 서비스 맵를 사용 하 고 [서비스 맵 REST API](/rest/api/servicemap/machines/listconnections) 를 사용 하 여 종속성을 JSON 형식으로 검색할 수 있습니다.

## <a name="can-i-automate-agent-installation"></a>에이전트 설치를 자동화할 수 있나요?

에이전트 기반 종속성 시각화의 경우:

- 스크립트를 사용 [하 여 종속성 에이전트를 설치](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent)합니다.
- MMA의 경우 [명령줄 또는 자동화를 사용](../azure-monitor/agents/log-analytics-agent.md#installation-options)하거나 [스크립트](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)를 사용 합니다.
- 스크립트 외에도 Microsoft Endpoint Configuration Manager 및 [Intigua](https://www.intigua.com/intigua-for-azure-migration) 와 같은 배포 도구를 사용 하 여 에이전트를 배포할 수 있습니다.

## <a name="what-operating-systems-does-mma-support"></a>MMA에서 지 원하는 운영 체제는 무엇 인가요?

- [MMA에서 지 원하는 Windows 운영 체제](../azure-monitor/agents/log-analytics-agent.md#installation-options)목록을 봅니다.
- [MMA에서 지 원하는 Linux 운영 체제](../azure-monitor/agents/log-analytics-agent.md#installation-options)목록을 봅니다.

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>1 시간 이상 종속성을 시각화할 수 있나요?

에이전트 기반 시각화의 경우 최대 1 시간 동안 종속성을 시각화할 수 있습니다. 기록의 특정 날짜까지 한 달 뒤로 이동할 수 있지만 시각화의 최대 기간은 1 시간입니다. 예를 들어, 종속성 맵의 기간을 사용 하 여 어제 종속성을 볼 수 있지만 1 시간 동안만 종속성을 볼 수 있습니다. 그러나 Azure Monitor 로그를 사용 하 여 더 긴 기간 동안 [종속성 데이터를 쿼리할](./how-to-create-group-machine-dependencies.md) 수 있습니다.

에이전트 없는 시각화의 경우 1 시간에서 30 일 사이에 단일 서버의 종속성 맵을 볼 수 있습니다.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>10 개 이상의 Vm 그룹에 대 한 종속성을 시각화할 수 있나요?

Vm을 최대 10 개 포함 하는 그룹에 대 한 [종속성을 시각화할](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) 수 있습니다. 10 개 이상의 Vm이 있는 그룹이 있는 경우 그룹을 더 작은 그룹으로 분할 한 다음 종속성을 시각화 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

[Azure Migrate 개요](migrate-services-overview.md)를 참조 하세요.