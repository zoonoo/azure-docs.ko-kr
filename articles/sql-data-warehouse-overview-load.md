<properties
   pageTitle="SQL 데이터 웨어하우스에 데이터 로드 | Microsoft Azure"
   description="SQL 데이터 웨어하우스에서 데이터 로드를 위한 일반적인 시나리오에 대해 알아봅니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/21/2015"
   ms.author="mausher;barbkess"/>

# SQL 데이터 웨어하우스에 데이터 로드
SQL 데이터 웨어하우스는 다양한 시나리오에 대해 데이터를 로드하기 위한 다양한 옵션을 제공합니다. 예를 들어 다음과 같은 항목을 로드할 수 있습니다.

- 큰 일괄 처리는 하루에 한 번
- 작은 일괄 처리는 하루 종일 또는
- 작은 (차원) 테이블에 간단한 업데이트

각 시나리오의 요구 사항은 SQL 데이터 웨어하우스에 로드하려는 데이터 형식에 따라 달라 집니다. 이 문서에서는 데이터 로딩에 제공되는 일부 선택 사항을 표시합니다.

## 기술
SQL 데이터 웨어하우스는 다음 표준 데이터 로드 도구를 지원합니다.

- Azure 데이터 팩터리
- bcp 명령줄 유틸리티
- PolyBase
- SQL Server 통합 서비스(SSIS)
- 타사 데이터 로드 도구

### Azure 데이터 팩터리(ADF)
ADF는 데이터 저장소, 데이터 처리 및 데이터 이동 서비스를 효율적이고 확장 가능하며 안정적인 데이터 프로덕션 파이프라인으로 구성하기 위해 완전히 관리되는 서비스입니다. SQL 데이터 웨어하우스는 ADF [복사 작업][]에 대해 [지원되는 원본/싱크][]입니다.

### bcp 명령줄 유틸리티
**bcp** 명령줄 실행 파일은 SQL Server, SQL 데이터베이스 및 SQL 데이터 웨어하우스로부터 데이터 로드 및 추출을 허용하는 Microsoft 유틸리티입니다. 시작하려면 [bcp 사용하여 데이터를 로드][] 자습서를 따라 하십시오.

### PolyBase
PolyBase는 모두 표준 TRANSACT-SQL을 통해, 그리고 MapReduce를 사용하지 않고 Hadoop 및 Azure 저장소 blob 저장소를 쿼리하는 방법을 제공함으로써 데이터 분석을 간소화하는 Microsoft 기술입니다. PolyBase는 또한 Azure blob 저장소에서 SQL 데이터 웨어하우스로 데이터를 로드할 수도 있습니다. 시작하려면 [PolyBase를 사용하여 로드][] 자습서를 따라하십시오.

### SQL Server 통합 서비스(SSIS)
[SSIS][]는 엔터프라이즈 수준의 데이터 통합 및 변환 솔루션을 구축하기 위한 플랫폼입니다. SQL 데이터 웨어하우스에 연결되는 패키지를 작성하려면 표준 [SQL Server 대상 어댑터][]를 사용하십시오.

### 타사 도구
SQL 데이터 웨어하우스는 데이터 로드를 위해 선도적인 업계 솔루션을 지원합니다. 자세한 내용은 [솔루션 파트너][] 목록을 참조하세요.

## 다음 단계
더 많은 개발 팁은 [개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[bcp 사용하여 데이터를 로드]: sql-data-warehouse-load-with-bcp.md
[PolyBase를 사용하여 로드]: sql-data-warehouse-load-with-polybase.md
[솔루션 파트너]: sql-data-warehouse-solution-partners.md
[개발 개요]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[지원되는 원본/싱크]: https://msdn.microsoft.com/library/dn894007.aspx
[복사 작업]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server 대상 어댑터]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!--Other Web references-->

<!---HONumber=July15_HO1-->