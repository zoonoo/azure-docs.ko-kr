---
title: Azure Migrate를 사용 하 여 마이그레이션 계획 빌드
description: Azure Migrate로 마이그레이션 계획을 작성 하는 방법에 대 한 지침을 제공 합니다.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: 1c2be47060004d464003c00cbbddb3b58a136e3c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871149"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Azure Migrate를 사용하여 마이그레이션 계획 빌드

이 문서에 따라 [Azure Migrate](migrate-services-overview.md)를 사용 하 여 Azure에 마이그레이션 계획을 구축 합니다. 

## <a name="define-cloud-migration-goals"></a>클라우드 마이그레이션 목표 정의

시작 하기 전에 클라우드로의 이동에 대 한 [동기](/azure/cloud-adoption-framework/strategy/motivations) 를 이해 하 고 평가 하는 것은 성공적인 비즈니스 결과에 기여할 수 있습니다. [클라우드 채택 프레임 워크](/azure/cloud-adoption-framework)에 설명 된 것 처럼 다양 한 트리거와 결과가 있습니다.   

**비즈니스 이벤트** | **마이그레이션 결과**
--- | ---
데이터 센터 종료 | Cost 
합병, 인수 또는 분할 | 공급 업체/기술적 복잡성 감소
자본 비용 절감 | 내부 작업의 최적화
업무상 중요한 기술 지원 종료 | 비즈니스 민첩성 향상
규정 준수 변경에 대응 | 새로운 기술 역량 준비
새로운 데이터 주권 요구 사항 | 시장 수요에 맞게 스케일링
장애 감소 및 IT 안정성 개선 | 지리적 요구에 맞게 스케일링

동기를 식별 하면 전략적 마이그레이션 목표를 고정 하는 데 도움이 됩니다. 다음 단계는 워크 로드에 맞게 조정 된 마이그레이션 경로를 식별 하 고 계획 하는 것입니다. [Azure Migrate: 검색 및 평가](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) 도구를 사용 하 여 온-프레미스 워크 로드를 평가 하 고 마이그레이션에 도움이 되는 지침 및 도구를 제공 합니다.

## <a name="understand-your-digital-estate"></a>디지털 자산 이해

먼저 온-프레미스 인프라, 애플리케이션 및 종속성을 식별합니다. 이렇게 하면 Azure로 마이그레이션할 워크로드를 식별하고 비용 예측을 최적화할 수 있습니다. 검색 및 평가 도구를 사용 하 여 사용 중인 작업, 워크 로드 간 종속성 및 워크 로드 최적화를 식별할 수 있습니다.

### <a name="workloads-in-use"></a>사용 중인 워크로드

Azure Migrate는 경량 Azure Migrate 어플라이언스를 사용 하 여 온-프레미스 VMware Vm, Hyper-v Vm, 기타 가상화 된 서버 및 물리적 서버에 대 한 에이전트 없는 검색을 수행 합니다. 연속 검색은 응용 프로그램 데이터 뿐만 아니라 서버 구성 정보 및 성능 메타 데이터를 수집 합니다. 다음은 기기가 온-프레미스 서버에서 수집 하는 것입니다. 

- 서버, 디스크 및 NIC 메타 데이터.

- 설치된 애플리케이션, 역할 및 기능

- CPU 및 메모리 사용률, 디스크 IOPS, 처리량을 포함하는 성능 데이터

데이터를 수집한 후 응용 프로그램 인벤토리 목록을 내보내 서버에서 실행 되는 앱 및 SQL Server 인스턴스를 찾을 수 있습니다. Azure Migrate: 데이터베이스 평가 도구를 사용하여 SQL Server 준비 상태를 파악할 수 있습니다.

 ![포털에 대한 애플리케이션 인벤토리](./media/concepts-migration-planning/application-inventory-portal.png)

 ![애플리케이션 인벤토리 내보내기](./media/concepts-migration-planning/application-inventory-export.png)

검색 및 평가 도구를 사용 하 여 검색 된 데이터와 함께 CMDB (구성 관리 데이터베이스) 데이터를 사용 하 여 서버 및 데이터베이스 공간 보기를 작성 하 고, 서버가 비즈니스 단위, 응용 프로그램 소유자, 지역 등에 걸쳐 분산 되는 방식을 이해할 수 있습니다. 이렇게 하면 마이그레이션을 위해 우선 순위를 정할 작업을 결정할 수 있습니다. 

### <a name="dependencies-between-workloads"></a>워크로드 간 종속성

서버 검색 후 [종속성을 분석](concepts-dependency-visualization.md)하여 서버 간 종속성을 시각화 및 식별할 수 있으며, 상호 의존적인 서버를 Azure로 이동하기 위한 최적화 전략을 확인할 수 있습니다. 시각화를 사용 하면 특정 서버가 사용 중인지 또는 마이그레이션 대신 서비스를 해제할 수 있는지를 파악할 수 있습니다.  종속성을 분석하면 마이그레이션을 진행하는 동안 누락 또는 갑작스러운 중단을 방지할 수 있습니다. 애플리케이션 인벤토리 및 종속성 분석이 완료되면 높은 신뢰도의 서버 그룹을 만들고 평가를 시작할 수 있습니다.

 ![종속성 매핑](./media/concepts-migration-planning/expand-client-group.png)

### <a name="optimization-and-sizing"></a>최적화 및 크기 조정

Azure는 클라우드 용량을 조정할 수 있는 유연성을 제공하며, 마이그레이션은 서버에 할당된 CPU 및 메모리 리소스를 최적화할 수 있는 기회를 제공합니다. ID가 있는 서버에 대한 평가를 만들면 워크로드 성능 기록을 이해하는 데 도움이 됩니다. 이는 Azure에서 Azure VM SKU를 올바른 크기로 조정하고 디스크 권장 사항을 적용하는 데 중요합니다.

## <a name="assess-migration-readiness"></a>마이그레이션 준비 상태 평가


### <a name="readinesssuitability-analysis"></a>준비 상태/적합성 분석

평가 보고서를 내보내고 다음 범주를 기준으로 필터링하여 Azure 준비 상태를 파악할 수 있습니다.

- **Azure 준비**: 서버를 변경 하지 않고 azure에 그대로 마이그레이션할 수 있습니다. 
- **조건적으로 azure에 대해 준비** 됨: 서버를 azure로 마이그레이션할 수 있지만 평가에 제공 된 수정 지침에 따라 사소한 변경이 필요 합니다.
- **Azure에 대해 준비 되지 않음**: 서버를 있는 그대로 azure로 마이그레이션할 수 없습니다. 마이그레이션하기 전에 수정 지침에 따라 문제를 해결해야 합니다. 
- **준비 상태 알 수 없음**: Azure Migrate 메타 데이터가 부족 하 여 서버 준비 상태를 확인할 수 없습니다.

데이터베이스 평가를 사용하여 Azure SQL Database 또는 Azure SQL Managed Instances로 마이그레이션하기 위한 SQL Server 데이터 자산의 준비 상태를 평가할 수 있습니다. 평가는 각 SQL Server 인스턴스에 대한 마이그레이션 준비 상태를 백분율로 보여 줍니다. 또한 각 인스턴스에 대해 Azure에서 권장되는 대상, 잠재적인 마이그레이션 방해 요인, 호환성이 손상되는 변경 수, Azure SQL DB 또는 Azure SQL VM에 대한 준비 상태, 호환성 수준을 볼 수 있습니다. 마이그레이션 방해 요인의 영향 및 수정을 위한 권장 사항을 이해하기 위해 더 자세히 알아볼 수 있습니다.

 ![데이터베이스 평가](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>크기 조정 권장 사항

서버가 Azure 준비 완료로 표시 되 면 검색 및 평가에서 서버에 대 한 Azure VM SKU 및 디스크 유형을 식별 하는 크기 조정 권장 사항을 만듭니다. 성능 기록 (마이그레이션하는 동안 리소스 최적화) 또는 온-프레미스 서버 설정 (성능 기록 없음)에 따라 크기 조정 권장 사항을 가져올 수 있습니다. 데이터베이스 평가에서는 데이터베이스 SKU, 가격 책정 계층 및 컴퓨팅 수준에 대한 권장 사항을 볼 수 있습니다.  

### <a name="get-compute-costs"></a>컴퓨팅 비용 가져오기

Azure Migrate 평가의 성능 기반 크기 조정 옵션은 VM을 적절한 크기로 조정하는 데 도움이 되며 Azure에서 워크로드를 최적화하는 모범 사례로 사용해야 합니다. 올바른 크기 조정 외에도 Azure 비용을 절감하는 데 도움이 되는 몇 가지 다른 옵션이 있습니다. 

- **예약 인스턴스**: [RI(예약 인스턴스)](https://azure.microsoft.com/pricing/reserved-vm-instances/)를 사용하면 [종량제 가격 책정](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)에 비해 비용을 크게 절감할 수 있습니다.
- **Azure 하이브리드 혜택**: [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)을 사용하면 활성 Software Assurance가 포함된 온-프레미스 Windows Server 라이선스 또는 Linux 구독을 Azure로 가져오고 예약 인스턴스 옵션과 결합할 수 있습니다.
- **기업계약**: Azure [EA(기업계약)](../cost-management-billing/manage/ea-portal-agreements.md)는 Azure 구독 및 서비스 비용 절감을 제공할 수 있습니다.
- **제안**: 다양한 [Azure 제안](https://azure.microsoft.com/support/legal/offer-details/)이 있습니다. 예를 들어 [종량제 개발/테스트](https://azure.microsoft.com/pricing/dev-test/) 또는 [Enterprise 개발/테스트 제안](https://azure.microsoft.com/offers/ms-azr-0148p/)은 개발/테스트 VM에 대해 더 낮은 요금을 제공합니다.
- **VM 작동 시간**: Azure VM이 실행되는 월간 일수 및 일중 시간을 검토할 수 있습니다. 서버를 사용 하지 않는 경우 서버를 종료 하면 비용을 줄일 수 있습니다 (RIs에는 적용 되지 않음).
- **대상 지역**: 여러 지역에서 평가를 작성하여 특정 지역으로 마이그레이션이 더 비용 효율적인지 파악할 수 있습니다. 

### <a name="visualize-data"></a>데이터 시각화

포털에서 Azure 준비 정보 및 월별 비용 배포를 사용 하 여 검색 및 평가 보고서를 볼 수 있습니다. 또한 평가를 내보내고 추가 시각화를 사용하여 마이그레이션 계획을 보강할 수 있습니다. 다양한 조합의 속성을 사용하여 여러 평가를 작성하고 비즈니스에 가장 적합한 속성 집합을 선택할 수 있습니다.  

 ![평가 개요](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gapsblockers"></a>격차/방해 요인 평가

마이그레이션하려는 앱 및 워크로드를 파악하고, 가동 중지 시간 제약 조건을 확인하고, 앱과 기본 인프라 간의 작동 종속성을 찾습니다. 이 분석을 통해 RTO(복구 시간 목표)를 충족하는 마이그레이션을 계획하고 데이터 손실을 최소화할 수 있습니다. 마이그레이션하기 전에 서버/SQL 데이터베이스 마이그레이션을 방해할 수 있는 호환성 문제 또는 지원되지 않는 기능을 검토하고 완화하는 것이 좋습니다. Azure Migrate 검색 및 평가 보고서와 Azure Migrate 데이터베이스 평가를 통해이를 지원할 수 있습니다. 

### <a name="prioritize-workloads"></a>워크로드 우선 순위 지정

인벤토리 정보를 수집한 후에는 먼저 마이그레이션할 앱 및 워크로드를 식별할 수 있습니다. 본격적인 마이그레이션을 시작하기 전에 문제를 해결할 수 있도록 체계적이고 제어 가능한 방식으로 앱을 마이그레이션하는 '적용 및 학습' 접근 방법을 개발합니다.

마이그레이션 우선 순위를 지정하기 위해 복잡성, 마이그레이션 시간, 비즈니스 긴급도, 프로덕션/비프로덕션 고려 사항, 규정 준수, 보안 요구 사항, 애플리케이션 지식 등의 전략적 요소를 사용할 수 있습니다. 

다음은 몇 가지 권장 사항입니다.

- **즉각적 성과를 기준으로 우선 순위 지정**: 평가 보고서를 사용하여 완벽하게 준비되고 Azure로 마이그레이션하는 데 최소한의 노력만 필요한 서버 및 데이터베이스 등 즉각적으로 성과를 거둘 수 있는 항목을 식별합니다. 표에는 이 작업을 수행하는 몇 가지 방법이 요약되어 있습니다.

    **State** | **작업**
    --- | ---
    **Azure 준비 VM** | 평가 보고서를 내보내고 *Azure에 대 한 상태가 준비* 된 모든 서버를 필터링 합니다. 이는 [Azure Migrate: 서버 마이그레이션](migrate-services-overview.md#azure-migrate-server-migration-tool) 도구를 사용 하 여 Azure로 리프트 하 고 이동 하는 첫 번째 서버 그룹 일 수 있습니다.
    **지원 종료 운영 체제** | 평가 보고서를 내보내고 Windows Server 2008 R2/Windows Server 2008를 실행 하는 모든 서버를 필터링 합니다. 이러한 운영 체제는 지원 종료 상태이며 Azure로 마이그레이션하는 경우에만 Azure가 3년간의 보안 업데이트를 무료로 제공합니다. Azure 하이브리드 혜택을 결합하고 RI를 사용하는 경우 절감액은 훨씬 더 높아질 수 있습니다.
    **SQL Server 마이그레이션** | 데이터베이스 평가 권장 사항을 사용하여 Azure SQL Database에 대해 준비가 된 데이터베이스를 Azure Migrate: 데이터베이스 마이그레이션 도구를 사용하여 마이그레이션합니다. Azure SQL VM에 대해 준비된 데이터베이스를 Azure Migrate: 서버 마이그레이션 도구를 사용하여 마이그레이션합니다.
    **지원 종료 소프트웨어** | 애플리케이션 인벤토리를 내보내고 지원 종료에 도달할 수 있는 모든 소프트웨어/확장을 필터링합니다. 이러한 애플리케이션의 마이그레이션 우선 순위를 지정합니다.
    **프로 비전 된 서버** | 평가 보고서를 내보내고 CPU 사용률이 낮은 서버 (%)를 필터링 합니다. 및 메모리 사용률 (%).  올바른 크기의 Azure VM으로 마이그레이션하고 미달 사용 리소스에 대한 비용을 절감합니다.
    **과도 프로 비전 된 서버** | 평가 보고서를 내보내고 CPU 사용률이 높은 서버에 대해 필터링 (%) 및 메모리 사용률 (%).  용량 제약 조건을 해결 하 고, 과도 하 게 변형 된 서버가 중단 되지 않도록 하 고, 이러한 서버를 Azure로 마이그레이션하여 성능을 향상 시킵니다. Azure에서 자동 크기 조정 기능을 사용하여 수요를 충족합니다.<br/><br/> 평가 보고서를 분석하여 스토리지 제약 조건을 조사합니다. 디스크 IOPS 및 처리량 그리고 권장 디스크 유형을 분석합니다.

- **소규모로 시작한 후 대규모로 확대**: 최소한의 위험 및 복잡성을 나타내는 앱 및 워크로드부터 이동하여 마이그레이션 전략에 대한 확신을 높입니다. CMDB 리포지토리와 함께 Azure Migrate 평가 권장 사항을 분석하여 파일럿 마이그레이션의 후보가 될 수 있는 개발/테스트 워크로드를 찾고 마이그레이션합니다. 파일럿 마이그레이션에서 얻은 피드백 및 교훈은 프로덕션 워크로드 마이그레이션을 시작할 때 도움이 될 수 있습니다.  
- **규정 준수**: 제품의 범위 및 전문성 측면에서 Azure는 업계 최대 규모의 규정 준수 포트폴리오를 유지합니다. 규정 준수 요구 사항을 사용하여 앱 및 워크로드가 국가, 지역, 산업별 표준 및 법률을 준수하도록 마이그레이션 우선 순위를 지정합니다. 이는 비즈니스에 중요한 프로세스를 처리하거나 중요한 정보를 보유하거나 규제가 심한 업종에 속하는 조직에서 특히 그렇습니다. 이러한 종류의 조직에는 표준 및 규정이 많고 자주 변경되어 대처하기가 어렵습니다.  

## <a name="finalize-the-migration-plan"></a>마이그레이션 계획 확정

마이그레이션 계획을 확정하기 전에 다음과 같이 다른 잠재적 방해 요인을 고려하고 완화해야 합니다. 

- **네트워크 요구 사항**: 네트워크 대역폭 및 대기 시간 제약 조건을 평가합니다. 이로 인해 예기치 않게 마이그레이션 복제가 지연 및 중단될 수 있습니다.
- **테스트/마이그레이션 후 조정**: 마이그레이션된 앱에 대한 성능 및 사용자 수용 테스트를 수행하거나 데이터베이스 연결 문자열 업데이트, 웹 서버 구성, 전환/정리 등의 마이그레이션 후 앱 구성/조정 작업을 수행할 수 있는 완충 기간을 운영합니다.
- **권한**: 권장되는 Azure 권한 및 마이그레이션에 필요한 서버/데이터베이스 액세스 역할 및 권한을 검토합니다.
- **교육**: 디지털 변환을 위해 조직을 준비시킵니다. 성공적인 조직 변경에는 충실한 교육이라는 토대가 중요합니다. Azure 기본 사항, 솔루션 아키텍처, 보안에 대한 과정 등 [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF)에서 제공하는 무료 교육을 확인하세요. 팀이  [Azure 인증](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)을 탐색하도록 장려합니다.  
- **구현 지원**: 필요한 경우 구현에 대한 지원을 받습니다. 대부분의 조직은 클라우드 마이그레이션을 도울 외부 지원을 선택합니다. 맞춤형 지원을 통해 신속하고 안전하게 Azure로 마이그레이션하려면  [Azure Expert 관리형 서비스 공급자](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF) 또는  [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF)를 고려하세요.  


마이그레이션할 앱, 앱/데이터베이스 가용성, 가동 중지 시간 제약 조건 및 마이그레이션 마일스톤에 대한 세부 정보를 포함하는 효과적인 클라우드 마이그레이션 계획을 수립합니다. 이 계획은 데이터 복사에 소요되는 시간을 고려하고 마이그레이션 후 테스트 및 전환 활동에 대한 현실적인 버퍼를 포함합니다. 

마이그레이션 후 테스트 계획은 기능, 통합, 보안, 성능 테스트 및 사용 사례를 포함하여 마이그레이션된 앱이 예상대로 작동하고 모든 데이터베이스 개체 및 데이터 관계가 성공적으로 클라우드로 전송되는지 확인해야 합니다.  

마이그레이션 로드맵을 작성하고 유지 관리 기간을 선언하여 제로 또는 최소한의 가동 중지 시간으로 애플리케이션 및 데이터베이스를 마이그레이션하고 마이그레이션 중에 잠재적 운영 및 비즈니스 영향을 제한합니다.  

## <a name="migrate"></a>마이그레이션

본격적인 마이그레이션을 시작하기 전에 Azure Migrate에서 테스트 마이그레이션을 실행하는 것이 좋습니다. 테스트 마이그레이션을 사용하면 관련된 시간을 예상하고 마이그레이션 계획을 조정할 수 있습니다. 본격적인 마이그레이션을 진행하기 전에 잠재적인 문제를 발견하고 해결할 수 있는 기회를 제공합니다.

마이그레이션을 수행할 준비가 되 면 Azure Migrate: 서버 마이그레이션 도구 및 Azure Data Migration Service (DMS)를 사용 하 여 원활한 통합 마이그레이션 환경을 제공 하 고 종단 간 추적을 사용 하세요.

- 서버 마이그레이션 도구를 사용 하 여 온-프레미스 Vm과 서버 또는 다른 사설 또는 공용 클라우드 (AWS, GCP 포함)에 있는 Vm을 가동 중지 시간이 0 인 상태로 마이그레이션할 수 있습니다.
- Azure DMS는 가동 중지 시간을 최소화 하면서 여러 데이터베이스 소스에서 Azure 데이터 플랫폼으로 원활 하 게 마이그레이션할 수 있도록 설계 된 완전히 관리 되는 서비스를 제공 합니다.  

## <a name="next-steps"></a>다음 단계

-  [](/azure/architecture/cloud-adoption/getting-started/migrate)   Azure 클라우드 도입 프레임 워크의 클라우드 마이그레이션 경험을 조사 하세요.
- Azure Migrate에 대 한 [간략 한 개요](migrate-services-overview.md) 를 확인 하 고 [시작 비디오](https://youtu.be/wFfq3YPxYHE)를 시청 하세요.
- [Azure vm](concepts-assessment-calculation.md)으로 마이그레이션하기 위해 vm을 평가 하는 방법에 대해 자세히 알아보세요.
