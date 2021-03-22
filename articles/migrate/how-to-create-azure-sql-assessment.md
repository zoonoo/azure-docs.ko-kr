---
title: Azure SQL 평가 만들기
description: Azure SQL Managed Instance 및 Azure SQL Database로 마이그레이션하기 위해 SQL 인스턴스를 평가하는 방법을 알아봅니다.
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 02/07/2021
ms.openlocfilehash: 14197516c0669055f756614b9559f1423703c6a8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102053576"
---
# <a name="create-an-azure-sql-assessment"></a>Azure SQL 평가 만들기

Azure로 마이그레이션하는 과정의 일환으로 온-프레미스 워크로드를 평가하여 클라우드 준비 상태를 측정하고, 위험을 식별하고, 비용과 복잡성을 예측합니다.
이 문서에서는 Azure SQL로 마이그레이션하기 위한 준비 작업으로 Azure Migrate: 검색 및 평가 도구를 사용하여 검색된 SQL 인스턴스를 평가하는 방법을 보여줍니다.

> [!Note]
> VMware 환경에서 실행되는 SQL Server 인스턴스 및 데이터베이스를 검색하고 평가하는 기능은 현재 미리 보기로 제공됩니다. 이 기능을 사용해 보려면 [**이 링크**](https://aka.ms/AzureMigrate/SQL)를 사용하여 **오스트레일리아 동부** 지역에서 프로젝트를 만듭니다. 오스트레일리아 동부에 이미 프로젝트가 있고 이 기능을 사용해 보려는 경우 포털에서 이러한 [**필수 구성 요소**](how-to-discover-sql-existing-project.md)를 완료했는지 확인하세요.

## <a name="before-you-start"></a>시작하기 전에

- Azure Migrate 프로젝트를 [생성](./create-manage-projects.md)하고 Azure Migrate: 검색 및 평가 도구를 추가했는지 확인합니다.
- 평가를 만들려면 [VMware](how-to-set-up-appliance-vmware.md)용 Azure Migrate 어플라이언스를 설정해야 합니다. 어플라이언스는 온-프레미스 서버를 검색하고 메타데이터 및 성능 데이터를 Azure Migrate로 보냅니다. [자세히 알아보기](migrate-appliance.md)

## <a name="azure-sql-assessment-overview"></a>Azure SQL 평가 개요
**성능 기반** 으로 크기 조정 기준을 사용하여 Azure SQL 평가를 만들 수 있습니다. 

**크기 조정 기준** | **세부 정보** | **Data**
--- | --- | ---
**성능 기반** | 수집된 성능 데이터를 기반으로 하는 평가 | 권장되는 **Azure SQL 구성** 은 CPU 사용량, 코어 수, 데이터베이스 파일 구성 및 크기, 파일 IO, 초당 일괄 처리 쿼리, 각 데이터베이스의 메모리 크기 및 사용량을 포함하는 SQL 인스턴스 및 데이터베이스의 성능 데이터를 기반으로 합니다.

Azure SQL 평가에 대해 [자세히 알아보세요](concepts-azure-sql-assessment-calculation.md).

## <a name="run-an-assessment"></a>평가 실행
다음과 같이 평가를 실행합니다.
1. **개요** 페이지 > **Windows, Linux 및 SQL Server** 에서 **서버 평가 및 마이그레이션** 을 클릭합니다.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Azure Migrate에 대한 개요 페이지":::
2. **Azure Migrate: 검색 및 평가** 에서 **평가** 를 클릭하고 평가 유형을 **Azure SQL** 로 선택합니다.
    :::image type="content" source="./media/tutorial-assess-sql/assess.png" alt-text="평가 유형을 Azure SQL로 선택하는 드롭다운":::
3. **서버 평가** 에서 **Azure SQL** 로 미리 선택된 평가 유형과 **Azure Migrate 어플라이언스에서 검색된 서버** 로 설정된 검색 원본을 볼 수 있습니다.

4. **편집** 을 클릭하여 평가 속성을 검토합니다.
     :::image type="content" source="./media/tutorial-assess-sql/assess-servers-sql.png" alt-text="평가 속성을 사용자 지정할 수 있는 편집 단추":::
5. 평가 속성 > **대상 속성** 에서 다음을 수행합니다.
    - **대상 지역** 에서 마이그레이션할 Azure 지역을 지정합니다. 
        - Azure SQL 구성 및 비용 권장 사항은 사용자가 지정한 위치를 기반으로 합니다. 
    - **대상 배포 유형** 에서
        - Azure Migrate에서 Azure SQL MI 및 Azure SQL DB로 마이그레이션하기 위한 SQL 인스턴스의 준비 상태를 평가하고 가장 적합한 대상 배포 옵션인 대상 계층, Azure SQL 구성 및 월별 추정치를 추천하려면 **권장** 을 선택합니다. [자세한 내용](concepts-azure-sql-assessment-calculation.md)
        - Azure SQL Database로만 마이그레이션하기 위한 SQL 인스턴스의 준비 상태를 평가하고 대상 계층, Azure SQL 구성 및 월별 추정치를 검토하려면 **Azure SQL DB** 를 선택합니다.
        - Azure SQL Managed Instance로만 마이그레이션하기 위한 SQL 인스턴스의 준비 상태를 평가하고 대상 계층, Azure SQL 구성 및 월별 추정치를 검토하려면 **Azure SQL MI** 를 선택합니다.
    - **예약된 용량** 에서 마이그레이션 후 SQL 서버에 예약된 용량을 사용할지 여부를 지정합니다.
        - 예약된 용량 옵션을 선택하면 "할인(%)"을 지정할 수 없습니다.

6. 평가 속성 > **평가 조건** 에서 다음을 수행합니다.
    - 크기 조정 기준은 기본적으로 **성능 기반** 으로 설정됩니다. 즉, Azure 마이그레이션은 최적의 크기의 Azure SQL Database 및/또는 Azure SQL Managed Instance 구성을 권장하기 위해 SQL 인스턴스 및 SQL 인스턴스에서 관리되는 데이터베이스와 관련된 성능 메트릭을 수집합니다. 다음을 지정할 수 있습니다.
        - **성능 기록**: 평가 기준으로 사용하려는 데이터 기간을 나타냅니다. (기본값은 1일입니다.)
        - **백분위수 활용률**: 성능 샘플에 사용할 백분위수 값을 나타냅니다. (기본값은 95번째 백분위수입니다.)
    - **쾌적 인자** 에서 평가 중에 사용할 버퍼를 표시합니다. 이는 계절별 사용량, 성능 기록 부족, 향후 사용량 증가와 같은 문제를 고려합니다. 예를 들어 2의 쾌적 인자를 사용하는 경우 다음과 같습니다. 
        
        **구성 요소** | **유효 사용률** | **쾌적 인자(2.0) 추가**
        --- | --- | ---
        코어 | 2  | 4
        메모리 | 8GB | 16GB
   
7. **가격 책정** 에서 다음을 수행합니다.
    - **제품/라이선스 프로그램** 에서 등록하는 경우 Azure 제품을 지정합니다. 현재 종량제 및 종량제 개발/테스트에서만 선택할 수 있습니다. 
        - 종량된 제품외에 예약된 용량과 Azure 하이브리드 혜택을 적용하여 추가 할인을 받을 수 있습니다. 
        - 종량제 개발/테스트 외에 Azure 하이브리드 혜택을 적용할 수 있습니다. 평가는 현재 종량제 개발/테스트 제품 외에 예약된 용량을 적용하는 것을 지원하지 않습니다.
    - **서비스 계층** 에서 Azure SQL Database 및/또는 Azure SQL Managed Instance로 마이그레이션하기 위한 비즈니스 요구 사항을 충족하는 가장 적절한 서비스 계층 옵션을 선택합니다. 
        - Azure Migrate에서 서버에 가장 적합한 서비스 계층을 추천하려면 **권장** 을 선택합니다. 권장 제품은 범용 또는 중요 비즈니스용 제품입니다. 자세한 정보
        - 예산 기반 워크로드에 맞게 Azure SQL 구성을 디자인하려면 **범용** 을 선택합니다.
        - 복원력이 높고 장애 조치(failover)에 대한 대기 시간이 짧은 워크로드에 맞게 디자인된 Azure SQL 구성을 원하는 경우 **중요 비즈니스용** 을 선택합니다.
    - **할인(%)** 에서 Azure 제품에 적용되는 구독별 할인을 추가합니다. 기본 설정은 0%입니다.
    - **통화** 에서 계정의 청구 통화를 선택합니다.
    - **Azure 하이브리드 혜택** 에서 SQL Server 라이선스가 이미 있는지 여부를 지정합니다. 이러한 작업을 수행하고 SQL Server 구독의 활성 소프트웨어 보증이 적용되는 경우 Azure에 라이선스를 가져올 때 Azure 하이브리드 혜택을 적용할 수 있습니다.
    - 변경하는 경우 저장을 클릭합니다.
     :::image type="content" source="./media/tutorial-assess-sql/view-all.png" alt-text="평가 속성의 저장 단추":::
8. **서버 평가** 에서 [다음]을 클릭합니다.
9.  **평가할 서버 선택** > **평가 이름** 에서 평가 이름을 지정합니다.
10. **그룹 선택 또는 만들기** 에서 **새로 만들기** 를 선택하고 그룹 이름을 지정합니다.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-add-servers.png" alt-text="새 그룹 단추의 위치":::
11. 어플라이언스를 선택하고 그룹에 추가할 서버를 선택합니다. 그런 후 "다음"을 클릭합니다.
12. **검토 + 평가 만들기** 에서 평가 세부 정보를 검토하고, 평가 만들기를 클릭하여 그룹을 만들고 평가를 실행합니다.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-create.png" alt-text="검토 및 평가 만들기 단추의 위치.":::
13. 평가를 만든 후에는 **Windows, Linux 및 SQL Server** > **Azure Migrate: 검색 및 평가** 타일로 이동하고 Azure SQL 평가 옆에 있는 숫자를 클릭합니다.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-navigation.png" alt-text="만든 평가로 이동":::
15. 보려는 평가 이름을 클릭합니다.

> [!NOTE]
> Azure SQL 평가는 성능 기반 평가이므로 검색을 시작한 후 적어도 하루 이상 기다렸다가 평가를 만드는 것이 좋습니다. 이는 더 높은 신뢰도로 성능 데이터를 수집할 시간을 제공합니다. 검색이 계속 진행 중인 경우 SQL 인스턴스의 준비 상태가 **알 수 없음** 으로 표시됩니다. 검색을 시작한 후 **지정한 성능 기간(일/주/월)을 기다렸다** 가 신뢰가 높은 평가를 만들거나 다시 계산하는 것이 가장 좋습니다. 

## <a name="review-an-assessment"></a>평가 검토

**평가를 보려면 다음을 수행합니다.**

1. **Windows, Linux 및 SQL Server** > **Azure Migrate: 검색 및 평가** > Azure SQL 평가 옆에 있는 숫자를 클릭합니다.
2. 보려는 평가 이름을 클릭합니다. 예(예제에만 해당되는 예측 및 비용): :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-summary.png" alt-text="SQL 평가 개요":::
3. 평가 요약을 검토합니다. 평가 속성을 편집하거나 평가를 다시 계산할 수도 있습니다.

#### <a name="discovered-items"></a>검색된 항목

이 평가에서 평가된 SQL 서버, 인스턴스 및 데이터베이스의 수를 나타냅니다.
    
#### <a name="azure-readiness"></a>Azure 준비 상태

평가된 SQL 인스턴스의 분포를 나타냅니다. 
    
**대상 배포 유형(평가 속성에서)** | **준비 상태**   
--- | --- |
**권장됨** |  Azure SQL Database 준비, Azure SQL Managed Instance 준비, 잠재적 Azure VM 준비, 준비 상태 알수 없음(검색이 진행 중이거나 수정해야 할 검색 문제가 있는 경우)
**Azure SQL DB** 또는 **Azure SQL MI** | Azure SQL Database, Azure SQL Managed Instance 준비, Azure SQL Database 및 Azure SQL Managed Instance 준비 안 됨, 준비 상태 알수 없음(검색이 진행 중이거나 수정해야 할 검색 문제가 있는 경우)
     
드릴다운하여 Azure SQL로 마이그레이션하기 전에 수정할 수 있는 마이그레이션 문제/경고에 대한 세부 정보를 파악할 수 있습니다. [자세히 알아보기](concepts-azure-sql-assessment-calculation.md) Azure SQL 데이터베이스 및/또는 Managed Instances로 마이그레이션하기 위해 권장되는 Azure SQL 구성을 검토할 수도 있습니다.
    
#### <a name="azure-sql-database-and-managed-instance-cost-details"></a>Azure SQL Database 및 Managed Instance 비용 세부 정보

월별 예상 비용에는 권장되는 Azure SQL Database 및/또는 Azure SQL Managed Instance 배포 유형에 해당하는 Azure SQL 구성에 대한 컴퓨팅 및 스토리지 비용이 포함됩니다. [자세한 내용](concepts-azure-sql-assessment-calculation.md#calculate-monthly-costs)


### <a name="review-readiness"></a>준비 상태 검토

1. **Azure SQL 준비 상태** 를 클릭합니다.
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-readiness.png" alt-text="Azure SQL 준비 상태 세부 정보":::
1. Azure SQL 준비 상태에서 평가된 SQL 인스턴스에 대한 **Azure SQL DB 준비 상태** 및 **Azure SQL MI 준비 상태** 를 검토합니다.
    - **준비**: 마이그레이션 문제나 경고 없이 인스턴스를 Azure SQL DB/MI로 마이그레이션할 준비가 되었습니다. 
        - 준비(하이퍼링크 및 파란색 정보 아이콘): 인스턴스를 마이그레이션 문제 없이 Azure SQL DB/MI로 마이그레이션할 준비가 되었지만 검토해야 하는 마이그레이션 경고가 있습니다. 하이퍼링크를 클릭하여 마이그레이션 경고 및 권장 수정 지침: :::image type="content" source="./media/tutorial-assess-sql/assess-ready.png" alt-text="준비 상태 평가":::를 검토할 수 있습니다.       
    - **준비되지 않음**: 인스턴스를 Azure SQL DB/MI로 마이그레이션하는 데 문제가 하나 이상 있습니다. 하이퍼링크를 클릭하고 마이그레이션 문제 및 권장 수정 지침을 검토할 수 있습니다.
    - **알 수 없음**: 검색이 진행 중이거나 검색 중에 알림 블레이드에서 수정해야 하는 문제가 있기 때문에 Azure Migrate는 준비를 평가할 수 없습니다. 문제가 지속되면 Microsoft 지원에 문의하세요. 
1. 아래 행렬에 따라 결정되는 SQL 인스턴스에 대한 권장 배포 유형을 검토합니다.

    - **대상 배포 유형**(평가 속성에서 선택한 대로): **권장**

        **Azure SQL DB 준비 상태** | **Azure SQL MI 준비 상태** | **권장 배포 유형** | **Azure SQL 구성 및 예상 비용을 계산했나요?**
         --- | --- | --- | --- |
        준비 | 준비 | Azure SQL DB 또는 Azure SQL MI [자세한 정보](concepts-azure-sql-assessment-calculation.md#recommended-deployment-type) | 예
        준비 | 준비되지 않았거나 알 수 없음 | Azure SQL DB | 예
        준비되지 않았거나 알 수 없음 | 준비 | Azure SQL MI | 예
        준비 안 됨 | 준비 안 됨 | Azure VM에 대한 잠재적 준비 [자세한 정보](concepts-azure-sql-assessment-calculation.md#potentially-ready-for-azure-vm) | No
        준비되지 않았거나 알 수 없음 | 준비되지 않았거나 알 수 없음 | 알 수 없음 | 아니요
    
    - **대상 배포 유형**(평가 속성에서 선택한 대로): **Azure SQL DB**
    
        **Azure SQL DB 준비 상태** | **Azure SQL 구성 및 예상 비용을 계산했나요?**
        --- | --- |
        준비 | 예
        준비 안 됨 | 예
        알 수 없음 | 아니요
    
    - **대상 배포 유형**(평가 속성에서 선택한 대로): **Azure SQL MI**
    
        **Azure SQL MI 준비 상태** | **Azure SQL 구성 및 예상 비용을 계산했나요?**
         --- | --- |
        준비 | 예
        준비 안 됨 | 예
        알 수 없음 | 아니요

4. 인스턴스 이름 드릴다운을 클릭하여 사용자 데이터베이스 수, 인스턴스 속성, 컴퓨팅(인스턴스 범위) 및 원본 데이터베이스 스토리지 정보를 비롯한 인스턴스 정보를 확인합니다.
5. 사용자 데이터베이스 수를 클릭하여 데이터베이스 목록과 세부 정보를 검토합니다. 예(예제에만 해당되는 예측 및 비용): :::image type="content" source="./media/tutorial-assess-sql/assessment-db.png" alt-text="SQL 인스턴스 세부 정보":::
5. 마이그레이션 문제 열에서 세부 정보 검토를 클릭하여 특정 대상 배포 유형에 대한 마이그레이션 문제 및 경고를 검토합니다. 
    :::image type="content" source="./media/tutorial-assess-sql/assessment-db-issues.png" alt-text="DB 마이그레이션 문제 및 경고":::

### <a name="review-cost-estimates"></a>예상 비용 검토
평가 요약은 권장되는 Azure SQL 데이터베이스 및/또는 Managed Instances 배포 유형에 해당하는 Azure SQL 구성의 예상 월별 컴퓨팅 및 스토리지 비용을 보여줍니다.

1. 월별 총 비용을 검토합니다. 비용은 평가된 그룹의 모든 SQL서버 인스턴스에 대해 집계됩니다.
    - 예상 비용은 인스턴스에 대해 권장되는 Azure SQL 구성을 기반으로 합니다. 
    - 컴퓨팅 및 스토리지의 월간 예상 비용이 표시됩니다. 예를 들면(예제에만 해당되는 예측 및 비용) 다음과 같습니다.
    
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-cost.png" alt-text="비용 세부 정보":::

1. 인스턴스 수준에서 드릴다운하여 인스턴스 수준에서 Azure SQL 구성 및 예상 비용을 확인할 수 있습니다.  
1. Azure SQL Database 구성을 권장하는 경우 데이터베이스 목록으로 드릴다운하여 Azure SQL 구성 및 데이터베이스당 비용 예측을 검토할 수도 있습니다.

### <a name="review-confidence-rating"></a>신뢰 등급 검토
Azure Migrate는 평가된 모든 SQL 인스턴스 및 데이터베이스에 대한 평가를 계산하는 데 필요한 성능/사용률 데이터 요소의 가용성에 따라 모든 Azure SQL 평가에 신뢰 등급을 할당합니다. 등급은 별 1개(가장 낮음)부터 별 5개(가장 높음)까지입니다.
 
신뢰 등급은 평가에서 크기 추천 사항의 안정성을 예측하는 데 도움이 됩니다.  신뢰 등급은 다음과 같습니다.

**데이터 요소 가용성** | **신뢰 등급**
--- | ---
0%-20% | 별 1개
21%-40% | 별 2개
41%-60% | 별 3개
61%-80% | 별 4개
81%-100% | 별 5개

신뢰 등급에 대해 [자세히 알아보세요](concepts-azure-sql-assessment-calculation.md#confidence-ratings).

## <a name="next-steps"></a>다음 단계

- Azure SQL 평가를 계산하는 방법에 대해 [자세히 알아보세요](concepts-azure-sql-assessment-calculation.md).
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)를 사용하여 SQL 인스턴스 및 데이터베이스 마이그레이션을 시작하세요.
