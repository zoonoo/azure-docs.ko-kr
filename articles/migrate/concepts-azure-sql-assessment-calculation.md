---
title: Azure Migrate 검색 및 평가 도구의 Azure SQL 평가
description: Azure Migrate 검색 및 평가 도구의 Azure SQL 평가에 대 한 자세한 정보
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 02/07/2021
ms.openlocfilehash: d1ea328575cf07a22ce39549c34d5cd21e916427
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054764"
---
# <a name="assessment-overview-migrate-to-azure-sql"></a>평가 개요 (Azure SQL로 마이그레이션)

이 문서에서는 [Azure Migrate: 검색 및 평가 도구](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-assessment-tool)를 사용 하 여 VMware 환경에서 Azure SQL Database 또는 관리 되는 인스턴스로 온-프레미스 SQL Server 인스턴스를 마이그레이션하기 위한 평가의 개요를 제공 합니다.

> [!Note]
> VMware 환경에서 실행 중인 SQL Server 인스턴스 및 데이터베이스의 검색 및 평가는 현재 미리 보기로 제공 됩니다. 이 기능을 사용해 보려면 [**이 링크**](https://aka.ms/AzureMigrate/SQL)를 사용하여 **오스트레일리아 동부** 지역에서 프로젝트를 만듭니다. 오스트레일리아 동부에 이미 프로젝트가 있고 이 기능을 사용해 보려는 경우 포털에서 이러한 [**필수 구성 요소**](how-to-discover-sql-existing-project.md)를 완료했는지 확인하세요.

## <a name="whats-an-assessment"></a>평가는 무엇 인가요?
검색 및 평가 도구를 사용한 평가는 데이터의 특정 시점 스냅숏으로, 준비를 측정 하 고 온-프레미스 서버를 Azure로 마이그레이션하는 효과를 예측 합니다.

## <a name="types-of-assessments"></a>평가 유형

Azure Migrate: 검색 및 평가 도구를 사용 하 여 만들 수 있는 평가에는 세 가지 유형이 있습니다.

**평가 유형** | **세부 정보**
--- | --- 
**Azure VM** | 온-프레미스 서버를 Azure 가상 머신으로 마이그레이션하기 위한 평가. [VMware](how-to-set-up-appliance-vmware.md) 및 [hyper-v](how-to-set-up-appliance-hyper-v.md) 환경에서 온-프레미스 서버를 평가 하 고,이 평가 유형을 사용 하 여 Azure vm으로 마이그레이션하기 위한 [물리적 서버](how-to-set-up-appliance-physical.md) 를 평가할 수 있습니다.
**Azure SQL** | 온-프레미스 SQL server를 VMware 환경에서 Azure SQL Database 또는 Azure SQL Managed Instance로 마이그레이션하기 위한 평가.
**AVS(Azure VMware 솔루션)** | 온-프레미스 서버를 [AVS(Azure VMware 솔루션)](../azure-vmware/introduction.md)로 마이그레이션하기 위한 평가. 이 평가 유형을 사용하여 AVS(Azure VMware 솔루션)로 마이그레이션하기 위한 온-프레미스 [VMware VM](how-to-set-up-appliance-vmware.md)을 평가할 수 있습니다. [자세히 알아보기](concepts-azure-vmware-solution-assessment-calculation.md)

Azure SQL 평가는 다음과 같은 하나의 크기 조정 기준을 제공 합니다.

**크기 조정 기준** | **세부 정보** | **Data**
--- | --- | ---
**성능 기반** | 수집된 성능 데이터를 기반으로 권장 사항을 만드는 평가 | Azure SQL 구성은 CPU 사용률, 메모리 사용률, IOPS (데이터 및 로그 파일), IO 작업의 처리량 및 대기 시간을 포함 하는 SQL 인스턴스 및 데이터베이스의 성능 데이터를 기반으로 합니다.

## <a name="how-do-i-assess-my-on-premises-sql-servers"></a>온-프레미스 SQL server를 평가 어떻게 할까요??

경량 Azure Migrate 어플라이언스에서 수집한 구성 및 사용률 데이터를 사용 하 여 온-프레미스 SQL Server 인스턴스를 평가할 수 있습니다. 어플라이언스는 온-프레미스 SQL server 인스턴스 및 데이터베이스를 검색 하 여 Azure Migrate 구성 및 성능 데이터를 전송 합니다. [자세한 내용](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>어플라이언스를 사용 하 여 어떻게 할까요? 평가 하 시겠습니까?
온-프레미스 서버를 검색 하기 위해 Azure Migrate 어플라이언스를 배포 하는 경우 다음 단계를 수행 합니다.
1.  Azure Migrate와 함께 작동 하도록 Azure 및 온-프레미스 환경을 설정 합니다.
2.  첫 번째 평가의 경우 Azure Migrate 프로젝트를 만들고이 프로젝트에 Azure Migrate: 검색 및 평가 도구를 추가 합니다.
3.  경량 Azure Migrate 어플라이언스를 배포 합니다. 어플라이언스는 온-프레미스 서버를 지속적으로 검색 하 여 Azure Migrate 구성 및 성능 데이터를 전송 합니다. 어플라이언스를 VM 또는 물리적 서버로 배포 합니다. 평가 하려는 서버에는 아무것도 설치할 필요가 없습니다.

기기가 검색을 시작한 후에는 평가 하려는 서버를 그룹으로 수집 하 고 평가 유형이 **AZURE SQL** 인 그룹에 대 한 평가를 실행할 수 있습니다.

[SQL Server 인스턴스](tutorial-assess-sql.md) 를 평가 하 여 이러한 단계를 시험해 보려면 자습서를 따르세요.

## <a name="how-does-the-appliance-calculate-performance-data-for-sql-instances-and-databases"></a>어플라이언스는 SQL 인스턴스 및 데이터베이스의 성능 데이터를 계산 하는 방법

어플라이언스는 다음 단계에 따라 계산 설정의 성능 데이터를 수집 합니다.
1. 어플라이언스는 실시간 샘플 지점을 수집 합니다. SQL server의 경우 샘플 지점은 30 초 마다 수집 됩니다.
2. 어플라이언스는 10 분 동안 30 초 마다 수집 된 샘플 데이터 요소를 집계 합니다. 데이터 요소를 만들기 위해 어플라이언스는 모든 샘플에서 최고 값을 선택 합니다. 각 카운터의 최대, 평균 및 분산을 Azure로 보냅니다.
3. Azure Migrate는 지난 달의 모든 10 분 데이터 요소를 저장 합니다.
4. 평가를 만들 때 Azure Migrate는 rightsizing 조정에 사용할 적절 한 데이터 요소를 식별 합니다. 식별은 성능 기록 및 백분위 수 사용률에 대 한 백분위 수 값을 기준으로 합니다.
    - 예를 들어 성능 기록이 1 주이 고 백분위 수 사용률이 95 번째 백분위 수 인 경우 평가는 지난 주에 대 한 10 분 샘플 요소를 정렬 합니다. 오름차순으로 정렬 하 고 오른쪽 크기 조정의 95 번째 백분위 수 값을 선택 합니다.
    - 95 번째 백분위 수 값을 사용 하면 99 번째 백분위 수를 선택한 경우 포함 될 수 있는 이상 값을 무시 합니다.
    - 기간에 대 한 피크 사용을 선택 하 고 이상 값을 누락 하지 않으려면 백분위 수 사용률에 대해 99 번째 백분위 수를 선택 합니다.
5. 이 값에는 어플라이언스에서 수집 하는 이러한 메트릭에 대 한 효과적인 성능 사용률 데이터를 얻기 위해 편안 하 게 해당 하는 요소가 곱해집니다.
    - CPU 사용률(%)
    - 메모리 사용률 (%)
    - 읽기 IO/s 및 쓰기 IO/s (데이터 및 로그 파일)
    - 읽기 m b/초 및 쓰기 MB/s (처리량)
    - IO 작업의 대기 시간

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Azure SQL 평가를 만들고 사용자 지정 하는 데 사용 되는 속성은 무엇 인가요?

Azure SQL 평가 속성에 포함 된 같습니다는 다음과 같습니다.

**속성** | **세부 정보**
--- | ---
**대상 위치** | 마이그레이션할 Azure 지역입니다. Azure SQL 구성 및 비용 권장 사항은 사용자가 지정 하는 위치에 따라 달라 집니다.
**대상 배포 유형** | 평가를 실행할 대상 배포 유형: Azure SQL MI 및 Azure SQL DB로 마이그레이션하기 위한 SQL server의 준비 상태를 평가 하 고 가장 적합 한 대상 배포 옵션인 대상 계층, Azure SQL 구성 및 월별 추정치를 추천 하려면 **권장** 을 선택 하 고 Azure Migrate 합니다. Azure sql Database로만 마이그레이션하기 위한 SQL server를 평가 하 고 대상 계층, Azure SQL DB 구성 및 월별 추정치를 검토 하려면 **AZURE SQL db** 를 선택 합니다. Azure sql MI로 마이그레이션할 SQL server를 평가 하 고 대상 계층, Azure SQL MI 구성 및 월별 추정치를 검토 하려면 **AZURE sql mi** 를 선택 합니다.
**예약 용량** | 평가에 대 한 비용 예측이 고려 되도록 예약 된 용량을 지정 합니다. 예약 된 용량 옵션을 선택 하는 경우 "할인 (%)"을 지정할 수 없습니다.
**크기 조정 기준** | 이 속성은 Azure SQL 구성의 크기를 적절 하 게 조정 하는 데 사용 됩니다. 기본적으로 **성능 기반** 으로 설정 됩니다. 즉, 평가에서 SQL Server 인스턴스 및 데이터베이스 성능 메트릭을 수집 하 여 최적의 크기의 Azure SQL Managed Instance 및/또는 Azure SQL Database 계층/구성 권장 구성을 권장 합니다.
**성능 기록** | 성능 기록은 성능 데이터를 평가할 때 사용 되는 기간을 지정 합니다.
**백분위 수 사용률** | 백분위 수 사용률은 rightsizing 조정에 사용 되는 성능 샘플의 백분위 수 값을 지정 합니다.
**쾌적 인자** | 평가 중에 사용 되는 버퍼입니다. 이는 계절 사용량, 짧은 성능 기록 및 향후 사용에 대 한 성능 향상 등의 문제를 해결 합니다. 예를 들어 20% 사용률이 있는 10 코어 인스턴스는 일반적으로 2 개 코어 인스턴스를 생성 합니다. 편안 하 게 2.0를 사용 하 여 결과는 4 코어 인스턴스입니다.
**제품/라이선스 프로그램** | 등록 하는 [Azure 제품](https://azure.microsoft.com/support/legal/offer-details/) 입니다. 현재 종 량 제 및 종 량 제 개발/테스트 중 에서만 선택할 수 있습니다. 예약 된 용량을 적용 하 고 종 량 제 제안 위에 Azure 하이브리드 혜택 하 여 추가 할인을 사용할 수 있습니다.
**서비스 계층** | Azure SQL Database 및/또는 Azure SQL Managed Instance로의 마이그레이션을 위한 비즈니스 요구를 수용 하는 가장 적절 한 서비스 계층 옵션: 서버에 가장 적합 한 서비스 계층을 권장 하는 Azure Migrate 하려면 **권장** 을 선택 합니다. 이는 범용 이거나 업무상 중요할 수 있습니다. 예산 기반 작업에 대해 Azure SQL 구성을 디자인 하려면 **일반 용도** 를 선택 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/azure-sql/database/service-tier-general-purpose) 복원 력이 높고 장애 조치 (failover)에 대 한 대기 시간이 짧은 워크 로드를 위해 설계 된 Azure SQL 구성을 원하는 경우 **중요 비즈니스용** 를 선택 합니다. [자세한 내용](https://docs.microsoft.com/azure/azure-sql/database/service-tier-business-critical)
**통화** | 계정에 대 한 청구 통화입니다.
**할인(%)** | Azure 제품을 기반으로 받는 모든 구독 관련 할인 기본 설정은 0%입니다.
**Azure 하이브리드 혜택** | SQL Server 라이선스가 이미 있는지 여부를 지정 합니다. 이러한 작업을 수행 하 고 SQL Server 구독의 활성 소프트웨어 보증이 적용 되는 경우 Azure에 라이선스를 제공할 때 Azure 하이브리드 혜택에 대해를 적용할 수 있습니다.

Azure Migrate를 사용 하 여 평가를 만드는 방법에 대 한 [모범 사례를 검토](best-practices-assessment.md) 합니다.

## <a name="calculate-readiness"></a>준비 상태 계산

> [!NOTE]
> 평가에는 온라인 상태의 데이터베이스만 포함 됩니다. 데이터베이스가 다른 상태에 있는 경우 평가는 이러한 데이터베이스에 대한 준비, 크기 조정 및 비용 계산을 무시합니다. 이러한 데이터베이스를 평가하려는 경우에는 데이터베이스의 상태를 변경하고 잠시 후 평가를 다시 계산하세요.

### <a name="azure-sql-readiness"></a>Azure SQL 준비

SQL 인스턴스 및 데이터베이스에 대 한 Azure SQL 준비는 Azure SQL Database 및 Azure SQL Managed Instance의 기능 호환성 검사를 기반으로 합니다.
- Azure SQL 평가는 현재 원본 SQL Server 작업 (SQL 에이전트 작업, 연결 된 서버 등)에서 사용 하는 SQL Server 인스턴스 기능 및 사용자 데이터베이스 스키마 (테이블, 뷰, 트리거, 저장 프로시저 등)를 고려 하 여 호환성 문제를 식별 합니다.
- 호환성 문제가 발견 되지 않은 경우 대상 배포 유형 (Azure SQL Database 또는 Azure SQL Managed Instance)에 대 한 준비 상태가 **준비** 로 표시 됩니다.
- 특정 대상 배포 유형으로의 마이그레이션을 차단 하지 않는 성능이 저하 되거나 지원 되지 않는 기능과 같은 중요 하지 않은 호환성 문제가 있는 경우 준비는 **경고** 세부 정보 및 권장 되는 수정 지침을 포함 하는 **준비** (하이퍼링크 및 파랑 정보 아이콘)로 표시 됩니다.
- 특정 대상 배포 유형으로의 마이그레이션을 차단할 수 있는 호환성 문제가 있는 경우 준비 상태가 **문제** 세부 정보 및 권장 되는 수정 지침과 함께 **준비 되지 않음** 으로 표시 됩니다.
    - SQL 인스턴스에서 특정 대상 배포 유형에 대해 준비 되지 않은 데이터베이스가 하나 이상 있는 경우 해당 인스턴스는 해당 배포 유형에 대해 **준비 되지 않음** 으로 표시 됩니다.
- 검색이 아직 진행 중이거나 SQL 인스턴스 또는 데이터베이스에 대 한 검색 문제가 있는 경우 평가에서 해당 SQL 인스턴스의 준비 상태를 계산할 수 없기 때문에 준비 상태가 **알 수 없음** 으로 표시 됩니다.

### <a name="recommended-deployment-type"></a>권장 배포 유형

Azure SQL 평가 속성에서 **권장** 되는 대상 배포 유형을 선택 하는 경우 sql 인스턴스와 호환 되는 azure sql 배포 유형을 권장 Azure Migrate. Microsoft에서 권장 하는 대상으로 마이그레이션하면 전반적인 마이그레이션 노력이 줄어듭니다. 

#### <a name="recommended-deployment-type-based-on-azure-sql-readiness"></a>Azure SQL 준비를 기준으로 권장 되는 배포 유형

 **Azure SQL DB 준비** | **Azure SQL MI 준비** | **권장 배포 유형** | **Azure SQL 구성 및 예상 비용을 계산 했습니까?**
 --- | --- | --- | --- |
 준비 | 준비 | Azure SQL DB 또는 Azure SQL MI | 예
 준비 | 준비 되지 않음 또는 알 수 없음 | Azure SQL DB | 예
 준비 되지 않음 또는 알 수 없음 | 준비 | Azure SQL MI | 예
 준비 안 됨 | 준비 안 됨 | 잠재적으로 Azure VM에 대해 준비 | 아니요
 준비 되지 않음 또는 알 수 없음 | 준비 되지 않음 또는 알 수 없음 | 알 수 없음 | 아니요

> [!NOTE]
> 평가 **속성에서 권장 되는** 배포 유형을 선택 하 고 원본 SQL Server가 AZURE sql DB 단일 데이터베이스와 azure sql Managed Instance에 모두 적합 한 경우 평가에서는 비용을 최적화 하 고 크기 및 성능 경계 내에 맞추기 위한 특정 옵션을 권장 합니다.

#### <a name="potentially-ready-for-azure-vm"></a>잠재적으로 Azure VM에 대해 준비

SQL 인스턴스가 Azure SQL Database 및 Azure SQL Managed Instance에 대해 준비 되지 않은 경우 권장 되는 배포 유형은 *AZURE VM에 대해 잠재적으로 준비* 된 것으로 표시 됩니다.
- 사용자는 "Azure VM"으로 평가 유형을 사용 하 여 Azure Migrate 평가를 만들어 인스턴스가 실행 되는 서버를 Azure VM으로 마이그레이션할 준비가 되었는지 확인 하는 것이 좋습니다. 다음 사항에 유의합니다.
    - Azure Migrate의 azure VM 평가는 현재 리프트 앤 시프트 이며 Azure 가상 머신에서 SQL 인스턴스 및 데이터베이스를 실행 하는 특정 성능 메트릭을 고려 하지 않습니다. 
    - 서버에서 Azure VM 평가를 실행하는 경우 서버에서 실행되는 모든 인스턴스에 대해 권장되는 크기 및 예상 비용을 계산하고 서버 마이그레이션 도구를 사용하여 Azure VM으로 마이그레이션할 수 있습니다. 마이그레이션하기 전에 Azure Virtual Machines의 SQL Server에 대한 [성능 지침을 검토](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices)합니다.


## <a name="calculate-sizing"></a>크기 계산 계산

### <a name="azure-sql-configuration"></a>Azure SQL 구성

평가가 준비 및 권장 되는 Azure SQL 배포 유형을 결정 한 후에는 온-프레미스 SQL 인스턴스 성능을 충족 하거나 초과할 수 있는 특정 서비스 계층 및 Azure SQL 구성 (SKU 크기)을 계산 합니다.
1. 검색 프로세스 중에 Azure Migrate은 다음을 포함 하는 SQL 인스턴스 구성 및 성능을 수집 합니다.
    - vCores (할당 된) 및 CPU 사용률 (%)
        - SQL 인스턴스의 CPU 사용률은 SQL server의 인스턴스에서 사용 하는 할당 된 CPU의 백분율입니다.
        - 데이터베이스에 대 한 CPU 사용률은 SQL 인스턴스의 데이터베이스에서 사용 하는 할당 된 CPU의 백분율입니다.
    - 메모리 (할당 됨) 및 메모리 사용률 (%)
    - 읽기 IO/s 및 쓰기 IO/s (데이터 및 로그 파일)
        - 읽기 io/s 및 쓰기 io/s는 해당 인스턴스에서 검색 된 모든 데이터베이스의 읽기 IO/s와 쓰기 io/s를 추가 하 여 계산 됩니다.
    - 읽기 m b/초 및 쓰기 MB/s (처리량)
    - IO 작업의 대기 시간
    - 총 DB 크기 및 데이터베이스 파일 조직
        - 데이터베이스 크기는 모든 데이터 및 로그 파일을 추가 하 여 계산 됩니다.
1. 평가는 모든 구성 및 성능 데이터를 집계 하 고 다양 한 Azure SQL 서비스 계층 및 구성에서 가장 일치 하는 항목을 찾으려고 시도 하며, SQL 인스턴스 성능 요구 사항을 충족 하거나 초과 하 여 비용을 최적화할 수 있는 구성을 선택 합니다.

### <a name="confidence-ratings"></a>신뢰 등급 
각 Azure SQL 평가는 신뢰도 등급과 연결 됩니다. 등급의 범위는 1 (최저)에서 5 (가장 높음) 사이입니다. 신뢰 등급은 Azure Migrate에서 제공 하는 크기 권장 사항의 안정성을 예측 하는 데 도움이 됩니다.
- 신뢰 등급이 평가에 할당 됩니다. 등급은 평가를 계산 하는 데 필요한 데이터 요소의 가용성을 기준으로 합니다.
- 성능 기반 크기 조정의 경우 평가는 다음을 포함 하 여 모든 SQL 인스턴스 및 데이터베이스의 성능 데이터를 수집 합니다.
    - CPU 사용률(%)
    - 메모리 사용률 (%)
    - 읽기 IO/s 및 쓰기 IO/s (데이터 및 로그 파일)
    - 읽기 m b/초 및 쓰기 MB/s (처리량)
    - IO 작업의 대기 시간
    
이러한 사용률을 사용할 수 없는 경우에는 크기 권장 사항이 불안정 해질 수 있습니다.
다음 표에서는 사용 가능한 데이터 요소의 백분율에 따라 평가 신뢰 등급을 보여 줍니다.

**데이터 요소 가용성** | **신뢰 등급**
--- | ---
0%-20% | 별 1개
21%-40% | 별 2개
41%-60% | 별 3개
61%-80% | 별 4개
81%-100% | 별 5개

#### <a name="low-confidence-ratings"></a>낮은 신뢰 등급
평가가 낮은 신뢰 등급을 얻을 수 있는 몇 가지 이유는 다음과 같습니다.
- 평가를 생성 하는 기간에 대 한 환경을 프로 파일링 하지 않았습니다. 예를 들어 성능 기간이 1 일로 설정 된 평가를 만드는 경우 수집 되는 모든 데이터 요소에 대 한 검색을 시작한 후 적어도 하루 이상 기다려야 합니다.
- 평가는 일부 또는 모든 서버에 대한 성능 데이터를 평가 기간에 수집할 수 없습니다. 높은 신뢰 등급의 경우 다음을 확인 하세요.
    - 평가 기간 동안 서버가 켜 집니다.
    - 포트 443에 대 한 아웃 바운드 연결이 허용 됩니다.
    - Azure Migrate에 있는 SQL 에이전트의 연결 상태가 ' 연결 됨 ' 인 경우 마지막 하트 비트를 확인 Azure Migrate 
    - 검색된 SQL 인스턴스 블레이드에서 모든 SQL 인스턴스에 대한 Azure Migrate 연결 상태가 "연결됨"인 경우

    신뢰 등급의 최신 변경 내용을 반영하려면 평가를 '다시 계산'하세요.
- 평가가 계산 된 기간 동안 일부 데이터베이스 또는 인스턴스가 생성 되었습니다. 예를 들어 지난 달의 성능 기록에 대 한 평가를 만들었지만 일부 데이터베이스 또는 인스턴스가 일주일 전에만 생성 되었다고 가정 합니다. 이 경우 새 서버에 대 한 성능 데이터는 전체 기간 동안 사용할 수 없으며 신뢰 등급이 낮습니다.

> [!NOTE]
> Azure SQL 평가는 성능 기반 평가 이므로 평가의 신뢰 등급이 별 5 개 미만이 면 어플라이언스에서 환경을 프로 파일링 하 고 평가를 다시 계산 하는 데 하루 이상 기다려야 하는 것이 좋습니다. 그렇지 않으면 성능 기반 크기 조정이 불안정 해질 수 있습니다.

## <a name="calculate-monthly-costs"></a>월별 비용 계산
크기 조정 권장 사항이 완료 된 후 Azure SQL 평가는 내부 가격 책정 API를 사용 하 여 권장 되는 Azure SQL 구성에 대 한 계산 및 저장소 비용을 계산 합니다. 모든 인스턴스에 대해 계산 및 저장소 비용을 집계 하 여 월별 총 계산 비용을 계산 합니다. 
### <a name="compute-cost"></a>계산 비용
- Azure SQL 구성의 계산 비용을 계산 하기 위해 평가는 다음 속성을 고려 합니다.
    - SQL 라이선스에 대 한 Azure 하이브리드 혜택
    - 예약 용량
    - Azure 대상 위치
    - 통화
    - 제품/라이선스 프로그램
    - 할인(%)

### <a name="storage-cost"></a>스토리지 비용
- 저장소 비용은 데이터 파일만 포함 하 고 로그 파일은 포함 하지 않습니다. 
- Azure SQL 구성에 대 한 저장소 비용을 계산 하기 위해 평가는 다음 속성을 고려 합니다.
    - Azure 대상 위치
    - 통화
    - 제품/라이선스 프로그램
    - 할인(%)
- 백업 저장소 비용은 평가에 포함 되지 않습니다.
- **Azure SQL Database**
    - 최소 5GB 저장소 비용은 비용 예상에 추가 되 고 저장소에 대 한 추가 저장소 비용은 1GB 단위로 추가 됩니다. [자세한 내용](https://azure.microsoft.com/pricing/details/sql-database/single/)
- **Azure SQL Managed Instance**
    - 처음 32 g b/인스턴스/월 저장소에는 저장소 비용이 추가 되지 않으며 저장소에 대 한 추가 저장소 비용이 32GB 증분 단위로 추가 됩니다. [자세한 내용](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)
        
## <a name="next-steps"></a>다음 단계
- 평가를 만드는 모범 사례를 [검토합니다](best-practices-assessment.md). 
- [AZURE SQL 평가](how-to-create-azure-sql-assessment.md)를 실행 하는 방법을 알아봅니다.
