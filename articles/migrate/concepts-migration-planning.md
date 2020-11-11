---
title: Azure Migrate를 사용 하 여 마이그레이션 계획 빌드 Microsoft Docs
description: Azure Migrate로 마이그레이션 계획을 구축 하는 방법에 대 한 지침을 제공 합니다.
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: rajosh
ms.openlocfilehash: 8f37814e29ce0089c26e235123768296efc2c0b0
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94504927"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Azure Migrate를 사용하여 마이그레이션 계획 빌드

이 문서에 따라 [Azure Migrate](migrate-services-overview.md)를 사용 하 여 Azure에 마이그레이션 계획을 구축 합니다. 

## <a name="define-cloud-migration-goals"></a>클라우드 마이그레이션 목표 정의

시작 하기 전에 클라우드로의 이동에 대 한 [동기](/azure/cloud-adoption-framework/strategy/motivations) 를 이해 하 고 평가 하는 것은 성공적인 비즈니스 결과에 기여할 수 있습니다. [클라우드 채택 프레임 워크](/azure/cloud-adoption-framework)에 설명 된 것 처럼 다양 한 트리거와 결과가 있습니다.   

**비즈니스 이벤트** | **마이그레이션 결과**
--- | ---
데이터 센터 종료 | 비용 
병합기, 취득 또는 divestiture | 공급 업체/기술적 복잡성 감소
자본 지출 절감 | 내부 작업의 최적화
업무상 중요한 기술 지원 종료 | 비즈니스 민첩성 향상
규정 준수 변경에 대 한 응답 | 새로운 기술 역량 준비
새 데이터 주권 요구 사항 | 시장 수요를 충족 하도록 크기 조정
장애 감소 및 IT 안정성 개선 | 지리적 요구에 맞게 크기 조정

동기를 식별 하면 전략적 마이그레이션 목표를 고정 하는 데 도움이 됩니다. 다음 단계는 워크 로드에 맞게 조정 된 마이그레이션 경로를 식별 하 고 계획 하는 것입니다. [Azure Migrate: 서버 평가](migrate-services-overview.md#azure-migrate-server-assessment-tool) 도구를 사용 하 여 온-프레미스 워크 로드를 평가 하 고 마이그레이션에 도움이 되는 지침 및 도구를 제공 합니다.

## <a name="understand-your-digital-estate"></a>디지털 공간 이해

먼저 온-프레미스 인프라, 응용 프로그램 및 종속성을 식별 합니다. 이렇게 하면 Azure로의 마이그레이션 작업을 식별 하 고 최적화 된 비용 예측을 수집할 수 있습니다. 서버 평가 도구를 사용 하 여 사용 중인 작업, 워크 로드 간 종속성 및 워크 로드 최적화를 식별할 수 있습니다.

### <a name="workloads-in-use"></a>사용 중인 워크 로드

Azure Migrate 경량 Azure Migrate 어플라이언스를 사용 하 여 온-프레미스 VMware Vm, Hyper-v Vm, 기타 가상화 된 컴퓨터 및 물리적 서버에 대 한 에이전트 없는 검색을 수행 합니다. 연속 검색은 응용 프로그램 데이터 뿐만 아니라 컴퓨터 구성 정보 및 성능 메타 데이터를 수집 합니다. 다음은 기기가 온-프레미스 컴퓨터에서 수집 하는 것입니다. 

- 컴퓨터, 디스크 및 NIC 메타 데이터입니다.

- 설치 된 응용 프로그램, 역할 및 기능.

- CPU 및 메모리 사용률, 디스크 IOPS 및 처리량을 포함 하는 성능 데이터

데이터를 수집한 후 응용 프로그램 인벤토리 목록을 내보내 앱을 찾고 컴퓨터에서 실행 되는 SQL Server 인스턴스를 찾을 수 있습니다. Azure Migrate: 데이터베이스 평가 도구를 사용 하 여 SQL Server 준비 상태를 파악할 수 있습니다.

 ![포털의 응용 프로그램 인벤토리](./media/concepts-migration-planning/application-inventory-portal.png)

 ![응용 프로그램 인벤토리 내보내기](./media/concepts-migration-planning/application-inventory-export.png)

서버 평가 도구를 사용 하 여 검색 된 데이터와 함께 CMDB (구성 관리 데이터베이스) 데이터를 사용 하 여 서버 및 데이터베이스 공간 보기를 작성 하 고, 서버가 비즈니스 단위, 응용 프로그램 소유자, 지역 등에 걸쳐 분산 되는 방식을 이해할 수 있습니다. 이렇게 하면 마이그레이션을 위해 우선 순위를 정할 작업을 결정할 수 있습니다. 

### <a name="dependencies-between-workloads"></a>작업 간 종속성

서버 검색 후 [종속성을 분석](concepts-dependency-visualization.md)하 여 서버 간 종속성을 시각화 하 고 식별할 수 있으며, 상호 의존적인 서버를 Azure로 이동 하기 위한 최적화 전략을 확인할 수 있습니다. 시각화를 사용 하면 특정 컴퓨터가 사용 중인지 또는 마이그레이션 대신 서비스를 해제할 수 있는지를 파악할 수 있습니다.  종속성을 분석 하면 마이그레이션을 진행 하는 동안 아무것도 유지 되지 않고 중단 되지 않습니다. 응용 프로그램 인벤토리 및 종속성 분석이 완료 되 면 높은 신뢰도의 서버 그룹을 만들고 평가를 시작할 수 있습니다.

 ![종속성 매핑](./media/concepts-migration-planning/expand-client-group.png)

### <a name="optimization-and-sizing"></a>최적화 및 크기 조정

Azure는 시간에 따라 클라우드 용량 크기를 조정할 수 있는 유연성을 제공 하며, 마이그레이션은 서버에 할당 된 CPU 및 메모리 리소스를 최적화할 수 있는 기회를 제공 합니다. Id가 있는 서버에 대 한 평가를 만들면 워크 로드 성능 기록을 이해 하는 데 도움이 됩니다. Azure VM Sku를 오른쪽으로 크기 조정 하 고 Azure에서 디스크 권장 사항을 적용 하는 데 중요 합니다.

## <a name="assess-migration-readiness"></a>마이그레이션 준비 상태 평가


### <a name="readinesssuitability-analysis"></a>준비/적합성 분석

평가 보고서를 내보내고 다음 범주에 대해 필터링 하 여 Azure 준비 상태를 이해할 수 있습니다.

- **Azure 준비** : 컴퓨터를 변경 하지 않고 azure에 그대로 마이그레이션할 수 있습니다. 
- **조건적으로 azure에 대해 준비** 됨: 컴퓨터를 azure로 마이그레이션할 수 있지만 평가에 제공 된 수정 지침에 따라 사소한 변경이 필요 합니다.
- **Azure에 대해 준비 되지 않음** : 컴퓨터를 azure로 그대로 마이그레이션할 수 없습니다. 마이그레이션을 수행 하기 전에 수정 지침에 따라 문제를 해결 해야 합니다. 
- **준비 상태 알 수 없음** : Azure Migrate 메타 데이터가 부족 하 여 컴퓨터 준비 상태를 확인할 수 없습니다.

데이터베이스 평가를 사용 하 여 Azure SQL Database 또는 Azure SQL 관리 되는 인스턴스로 마이그레이션하기 위해 SQL Server 데이터 공간 준비 상태를 평가할 수 있습니다. 평가는 각 SQL server 인스턴스에 대 한 마이그레이션 준비 상태 비율을 보여 줍니다. 또한 각 인스턴스에 대해 Azure에서 권장 되는 대상, 잠재적인 마이그레이션 차단기, 주요 변경 내용 수, Azure SQL DB 또는 Azure SQL VM에 대 한 준비 및 호환성 수준을 볼 수 있습니다. 마이그레이션 블 로커의 영향 및 수정에 대 한 권장 사항을 이해 하는 데 더 자세히 알아볼 수 있습니다.

 ![데이터베이스 평가](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>크기 조정 권장 사항

컴퓨터가 Azure 준비 완료로 표시 되 면 서버 평가에서 컴퓨터의 Azure VM SKU 및 디스크 유형을 식별 하는 크기 조정 권장 사항을 만듭니다. 성능 기록 (마이그레이션하는 동안 리소스 최적화) 또는 성능 기록 없이 온-프레미스 컴퓨터 설정에 따라 크기 조정 권장 사항을 가져올 수 있습니다. 데이터베이스 평가에서는 데이터베이스 SKU, 가격 책정 계층 및 계산 수준에 대 한 권장 사항을 볼 수 있습니다.  

### <a name="get-compute-costs"></a>계산 비용 가져오기

Azure Migrate 평가의 성능 기반 크기 조정 옵션을 사용 하면 Vm을 적절 하 게 크기를 조정할 수 있으며 Azure에서 작업을 최적화 하는 모범 사례로 사용 해야 합니다. 오른쪽 크기 조정 외에도 Azure 비용을 절감 하는 데 도움이 되는 몇 가지 다른 옵션이 있습니다. 

- **예약 인스턴스** : [예약 인스턴스 (RI)](https://azure.microsoft.com/pricing/reserved-vm-instances/)를 사용 하면 [종 량 제 가격 책정](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)에 비해 비용을 크게 줄일 수 있습니다.
- **Azure 하이브리드 혜택** : [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)를 사용 하면 활성 소프트웨어 보증 또는 Linux 구독과 함께 온-프레미스 Windows Server 라이선스를 Azure로 가져오고, 예약 된 인스턴스 옵션과 결합할 수 있습니다.
- **기업계약** : azure [EA (기업 계약)](../cost-management-billing/manage/ea-portal-agreements.md) 는 azure 구독 및 서비스에 대 한 절감을 제공할 수 있습니다.
- **제공** : 여러 [Azure 제품을 제공](https://azure.microsoft.com/support/legal/offer-details/)합니다. 예를 들어 개발/테스트 Vm에 대 한 낮은 요금을 제공 하기 위해 [종량제 개발/테스트](https://azure.microsoft.com/pricing/dev-test/)또는 [Enterprise 개발/테스트 제품](https://azure.microsoft.com/offers/ms-azr-0148p/)
- **VM 작동 시간** : Azure vm이 실행 되는 월간 일 수 및 시간별 시간을 검토할 수 있습니다. 사용 중이 아닌 컴퓨터를 종료 하면 비용을 줄일 수 있습니다 (RIs에는 적용 되지 않음).
- **대상 지역** : 다른 지역에서 평가를 만들어 특정 지역으로의 마이그레이션이 더 비용 효율적일 수 있는지 파악할 수 있습니다. 

### <a name="visualize-data"></a>데이터 시각화

포털에서 서버 평가 보고서 (Azure 준비 정보 및 월별 비용 배포를 사용 하 여)를 볼 수 있습니다. 또한 평가를 내보내고 추가 시각화를 사용 하 여 마이그레이션 계획을 보강할 수 있습니다. 서로 다른 속성 조합을 사용 하 여 여러 평가를 만들고 비즈니스에 가장 적합 한 속성 집합을 선택할 수 있습니다.  

 ![평가 개요](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gapsblockers"></a>간격/차단 평가

마이그레이션하려는 앱 및 워크 로드를 파악 하 고, 가동 중지 시간 제약 조건을 확인 하 고, 앱과 기본 인프라 간의 작동 종속성을 찾습니다. 이 분석을 통해 RTO (복구 시간 목표)를 충족 하는 마이그레이션을 계획 하 고 데이터 손실을 최소화할 수 있습니다. 마이그레이션하기 전에 서버/s q s 데이터베이스 마이그레이션을 차단할 수 있는 호환성 문제 또는 지원 되지 않는 기능을 검토 하 고 완화 하는 것이 좋습니다. Azure Migrate Server 평가 보고서 및 Azure Migrate 데이터베이스 평가를 통해이를 지원할 수 있습니다. 

### <a name="prioritize-workloads"></a>워크 로드 우선 순위 지정

인벤토리에 대 한 정보를 수집한 후에는 먼저 마이그레이션할 앱 및 워크 로드를 식별할 수 있습니다. 완전 한 마이그레이션을 시작 하기 전에 결함을 낼 수 있도록 체계적이 고 제어 가능한 방식으로 앱을 마이그레이션하기 위한 "적용 및 학습" 접근 방법을 개발 합니다.

마이그레이션 순서의 우선 순위를 지정 하기 위해 복잡성, 마이그레이션 시간, 비즈니스 긴급도, 프로덕션/비프로덕션 고려 사항, 규정 준수, 보안 요구 사항, 응용 프로그램 지식 등의 전략적 요인을 사용할 수 있습니다. 

몇 가지 권장 사항:

- **빠른 Wins 우선 순위** 지정: 평가 보고서를 사용 하 여 완전히 준비 된 서버와 데이터베이스를 포함 하 여 낮은 수준의 과일을 식별 하 고 Azure로 마이그레이션하기 위한 노력을 최소화 합니다. 테이블에는이 작업을 수행 하는 몇 가지 방법이 요약 되어 있습니다.

    **State** | **작업**
    --- | ---
    **Azure 준비 Vm** | 평가 보고서를 내보내고 *Azure에 대 한 상태가 준비* 된 모든 컴퓨터를 필터링 합니다. 이는 [Azure Migrate: 서버 마이그레이션](migrate-services-overview.md#azure-migrate-server-migration-tool) 도구를 사용 하 여 Azure로 리프트 하 고 이동 하는 첫 번째 컴퓨터 그룹 일 수 있습니다.
    **지원 종료 운영 체제** | 평가 보고서를 내보내고 Windows Server 2008 R2/Windows Server 2008를 실행 하는 모든 컴퓨터를 필터링 합니다. 이러한 운영 체제는 지원이 끝난 후 azure로 마이그레이션할 때 3 년간의 보안 업데이트를 무료로 제공 합니다. Azure 하이브리드 혜택 결합 하 고 RIs를 사용 하는 경우 절감 액은 훨씬 더 높아질 수 있습니다.
    **마이그레이션 SQL Server** | 데이터베이스 평가 권장 사항을 사용 하 여 Azure Migrate: 데이터베이스 마이그레이션 도구를 사용 하 여 Azure SQL Database 준비가 된 데이터베이스를 마이그레이션합니다. Azure Migrate: 서버 마이그레이션 도구를 사용 하 여 Azure SQL VM에 대해 준비 된 데이터베이스를 마이그레이션합니다.
    **지원 종료 소프트웨어** | 응용 프로그램 인벤토리를 내보내고 지원 종료에 도달할 수 있는 모든 소프트웨어/확장을 필터링 합니다. 마이그레이션을 위해 이러한 응용 프로그램의 우선 순위를 지정 합니다.
    **프로 비전 된 컴퓨터** | 평가 보고서를 내보내고 CPU 사용률이 낮은 컴퓨터 (%)를 필터링 합니다. 및 메모리 사용률 (%).  올바른 크기의 Azure VM으로 마이그레이션하고 미달 사용 리소스에 대 한 비용을 절감 합니다.
    **과도 하 게 프로 비전 된 컴퓨터** | 평가 보고서를 내보내고 CPU 사용률이 높은 컴퓨터 (%)를 필터링 합니다. 및 메모리 사용률 (%).  용량 제약 조건을 해결 하 고, 과도 하 게 변형 된 컴퓨터가 중단 되지 않도록 하 고, 이러한 컴퓨터를 Azure로 마이그레이션하여 성능을 향상 시킵니다. Azure에서 자동 크기 조정 기능을 사용 하 여 수요를 충족 합니다.<br/><br/> 평가 보고서를 분석 하 여 저장소 제약 조건을 조사 합니다. 디스크 IOPS 및 처리량을 분석 하 고 권장 디스크 유형을 분석 합니다.

- **먼저 작은** 위험 및 복잡성을 나타내는 앱 및 워크 로드를 이동 하 여 마이그레이션 전략에 대 한 확신을 구축 하는 것부터 시작 하세요. CMDB 리포지토리와 함께 Azure Migrate 평가 권장 사항을 분석 하 여 파일럿 마이그레이션의 후보가 될 수 있는 개발/테스트 워크 로드를 찾고 마이그레이션합니다. 파일럿 마이그레이션의 피드백 및 학습는 프로덕션 워크 로드 마이그레이션을 시작 하는 데 도움이 될 수 있습니다.  
- **준수** : Azure는 제품의 폭과 깊이를 기준으로 업계에서 가장 큰 규정 준수 포트폴리오를 유지 관리 합니다. 규정 준수 요구 사항을 사용 하 여 앱과 워크 로드가 국가, 지역 및 산업별 표준 및 법률을 준수 하도록 마이그레이션 우선 순위를 지정 합니다. 이는 비즈니스에 중요 한 프로세스를 처리 하 고 중요 한 정보를 보유 하거나 많이 규제 된 업계에서 특히 그렇습니다. 이러한 종류의 조직에서 표준 및 규정 abound는 자주 변경 될 수 있으며,이는 계속 진행 하기 어렵습니다.  

## <a name="finalize-the-migration-plan"></a>마이그레이션 계획 완료

마이그레이션 계획을 완료 하기 전에 다음과 같이 다른 잠재적 블 로커를 고려 하 고 완화 해야 합니다. 

- **네트워크 요구 사항** : 네트워크 대역폭 및 대기 시간 제약 조건을 평가 합니다 .이로 인해 예기치 않은 지연이 발생 하 고 마이그레이션 복제 속도가 저하 될 수 있습니다.
- **테스트/마이그레이션 후** 조정: 시간 버퍼가 마이그레이션된 앱에 대 한 성능 및 사용자 수용 테스트를 수행할 수 있도록 하거나, 데이터베이스 연결 문자열 업데이트, 웹 서버 구성, failover)가/정리 등의 응용 프로그램 마이그레이션 후 구성/조정 작업을 수행할 수 있습니다.
- **사용 권한** : 권장 되는 Azure 권한 및 마이그레이션에 필요한 서버/데이터베이스 액세스 역할 및 사용 권한을 검토 합니다.
- **교육** : 디지털 변환을 위해 조직을 준비 합니다. 성공적인 조직 변경에는 견고한 학습 기초가 중요 합니다. Azure 기본 사항, 솔루션 아키텍처 및 보안에 대 한 과정을 포함 하 여 [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF)에 대 한 무료 교육을 확인 하세요. 팀에서 [Azure 인증](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)을 탐색 하는 것이 좋습니다.  
- **구현 지원** : 필요한 경우 구현에 대 한 지원을 받습니다. 대부분의 조직에서는 클라우드 마이그레이션을 지원 하기 위해 외부 지원을 옵트인 합니다. 개인화 된 지원을 통해 신속 하 고 안전 하 게 Azure로 이동 하려면 [Azure 전문가 관리 서비스 공급자](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)를 고려 하거나 [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF)합니다.  


마이그레이션할 앱, 앱/데이터베이스 가용성, 가동 중지 시간 제약 조건 및 마이그레이션 마일스 톤에 대 한 자세한 정보를 포함 하는 효과적인 클라우드 마이그레이션 계획을 만듭니다. 이 계획은 데이터 복사에 소요 되는 시간을 고려 하 고 마이그레이션 후 테스트 및 중단 활동에 대 한 현실적인 버퍼를 포함 합니다. 

마이그레이션 후 테스트 계획은 기능, 통합, 보안 및 성능 테스트 및 사용 사례를 포함 하 여 마이그레이션된 앱이 예상 대로 작동 하 고 모든 데이터베이스 개체 및 데이터 관계가 성공적으로 클라우드로 전송 되는지 확인 해야 합니다.  

마이그레이션 로드맵을 작성 하 고 유지 관리 기간을 선언 하 여 가동 중지 시간을 최소화 하 고 응용 프로그램 및 데이터베이스를 마이그레이션할 수 있으며, 마이그레이션 중에 잠재적 운영 및 비즈니스 영향을 제한 합니다.  

## <a name="migrate"></a>Migrate

전체 마이그레이션을 시작 하기 전에 Azure Migrate에서 테스트 마이그레이션을 실행 하는 것이 좋습니다. 테스트 마이그레이션을 사용 하면 관련 된 시간을 예측 하 고 마이그레이션 계획을 조정할 수 있습니다. 모든 잠재적인 문제를 검색 하 고 전체 마이그레이션 전에 해결할 수 있는 기회를 제공 합니다.

마이그레이션을 수행할 준비가 되 면 Azure Migrate: 서버 마이그레이션 도구 및 Azure Data Migration Service (DMS)를 사용 하 여 원활한 통합 마이그레이션 환경을 제공 하 고 종단 간 추적을 사용 하세요.

- 서버 마이그레이션 도구를 사용 하 여 온-프레미스 Vm과 서버 또는 다른 사설 또는 공용 클라우드 (AWS, GCP 포함)에 있는 Vm을 가동 중지 시간이 0 인 상태로 마이그레이션할 수 있습니다.
- Azure DMS는 가동 중지 시간을 최소화 하면서 여러 데이터베이스 소스에서 Azure 데이터 플랫폼으로 원활 하 게 마이그레이션할 수 있도록 설계 된 완전히 관리 되는 서비스를 제공 합니다.  

## <a name="next-steps"></a>다음 단계

-  [cloud migration journey](/azure/architecture/cloud-adoption/getting-started/migrate)   Azure 클라우드 도입 프레임 워크의 클라우드 마이그레이션 경험을 조사 하세요.
- Azure Migrate에 대 한 [간략 한 개요](migrate-services-overview.md) 를 확인 하 고 [시작 비디오](https://youtu.be/wFfq3YPxYHE)를 시청 하세요.
- [Azure vm](concepts-assessment-calculation.md)으로 마이그레이션하기 위해 vm을 평가 하는 방법에 대해 자세히 알아보세요.
