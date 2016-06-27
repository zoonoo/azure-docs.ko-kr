<properties
   pageTitle="Azure SQL 데이터베이스 Web 및 Business 버전 지원 종료 FAQ | Microsoft Azure"
   description="Azure SQL Web 및 Business 데이터베이스가 사용되지 않는 시기를 확인하고 새 서비스 계층의 특징 및 기능에 대해 알아보십시오."
   services="sql-database"
   documentationCenter="na"
   authors="stevestein"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="05/09/2016"
   ms.author="sstein" />

# Web 및 Business 버전 지원 종료 FAQ

Azure SQL Web 및 Business 데이터베이스는 이제 사용되지 않습니다. Basic, Standard, Premium 및 Elastic 계층이 사용되지 않는 Web 및 Business 데이터베이스를 대체합니다.

Web 및 Business 데이터베이스 업그레이드를 지원하기 위해 SQL 데이터베이스 서비스는 기록 워크로드를 기반으로 각 데이터베이스에 적절한 서비스 계층 및 성능 수준(가격 책정 계층)을 권장합니다.

**가격 책정 계층 권장 사항 가져오기:**

- [Azure 포털을 사용하여 SQL 데이터베이스 V12로 업그레이드](sql-database-upgrade-server-portal.md)
- [PowerShell을 사용하여 SQL 데이터베이스 V12로 업그레이드 준비](sql-database-upgrade-server-powershell.md)
- [Web 또는 Business 데이터베이스의 가격 책정 계층 변경](sql-database-service-tier-advisor.md)



## Azure 포털이 내 Web 및 Business edition 데이터베이스를 사용되지 않음으로 표시하는 이유가 무엇입니까?

Web 및 Business 버전 데이터베이스는 2015 년 9 월 후 사용할 수 없으므로 포털은 Web 및 Business 데이터베이스를 사용되지 않음으로 표시합니다. 또한 사용되지 않는 레이블은 Web 및 Business 데이터베이스를 Standard, Basic 또는 Premium으로 업그레이드 해야 한다는 알림을 제공합니다. 기존 Web 또는 Business 데이터베이스를 새 서비스 계층으로 업그레이드에 대한 자세한 내용은 [Azure SQL 데이터베이스 V12로 업그레이드](sql-database-upgrade-server-portal.md)를 참조하세요.

## 내 기존 Web 또는 Business 데이터베이스를 업그레이드하기 위한 새 서비스 계층은 무엇입니까?

기존 Web 또는 Business 데이터베이스에 적절한 새 서비스 계층 및 성능의 선택은 응용 프로그램에 대한 특정 기능과 성능에 따라 달라집니다.

위에서 설명한 가격 책정 계층 권장 사항을 사용하거나 적절한 새 서비스 계층 선택을 지원하기 위한 상세 정보는 [Azure SQL 데이터베이스 V12로 업그레이드](sql-database-upgrade-server-portal.md)를 참조하세요.

## Microsoft가 새 서비스 계층을 도입하는 이유는 무엇입니까?

고객의 의견에 따라 Azure SQL 데이터베이스는 관계형 데이터베이스 워크로드를 더 쉽게 지원할 수 있도록 새 서비스 계층을 도입하고 있습니다. 새 계층은 경량급에서 중량급 트랜잭션 응용 프로그램 요구에 대한 7단계 스펙트럼에 걸쳐 예측 가능한 성능을 제공하기 위한 것입니다. 또한 새 계층은 광범위한 비즈니스 연속성 기능, 더욱 강력한 가동 시간 SLA, 적은 비용으로 더 큰 데이터베이스 크기 및 개선된 청구 환경을 제공합니다.

## 새 서비스 계층에 대해 더 자세한 내용을 어디서 알아볼 수 있습니까?

새 서비스 계층 및 성능 모델에 대한 자세한 내용은 [서비스 계층](sql-database-service-tiers.md)을 참조하세요. 새 서비스 계층에 대한 자세한 가격 책정 정보는 [SQL 데이터베이스 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

## Basic, Standard 및 Premium에서 제공되지 않는 특징 또는 기능은 무엇입니까?

Web 및 Business 버전에서 페더레이션 기능이 사용되지 않습니다. 데이터베이스를 규모 확장해야 하는 고객은 분할된 데이터베이스를 사용하는 응용 프로그램 만들기 및 관리를 단순화하는 [Azure SQL 데이터베이스](sql-database-elastic-scale-get-started.md)에 대한 [탄력적인 데이터베이스 도구](sql-database-elastic-scale-get-started.md)를 대신 사용하거나 이 도구로 마이그레이션하는 것이 좋습니다. .NET 클라이언트 라이브러리를 사용하면 응용 프로그램에서 데이터를 분할하도록 매핑하고 OLTP 요청을 적절한 데이터베이로 경로 설정하는 방법을 정의할 수 있습니다. 데이터가 분할된 데이터베이스 사이에 분배되는 방법을 다시 구성하는 관리 작업을 지원하기 위해 자신의 Azure 구독에서 호스팅할 수 있는 Azure 클라우드 서비스 템플릿이 포함되어 있습니다. [탄력적인 데이터베이스 도구](sql-database-elastic-scale-get-started.md) 외에도 Microsoft는 심층 고객 참여에서 얻은 교훈을 바탕으로 [사용자 지정 분할된 데이터베이스 패턴 및 방법 지침](https://msdn.microsoft.com/library/azure/dn764977.aspx)을 계속 만들고 게시합니다. 규모 확장 기능이 필요한 새 고객은 [탄력적인 데이터베이스 도구](sql-database-elastic-scale-get-started.md)를 확인하거나 Microsoft 지원 부서에 문의하여 옵션을 평가해야 합니다.

또한 Microsoft는 Premium 데이터베이스를 사용하여 데이터베이스 복사본 환경을 변경하고 있습니다. 이전에는 프리미엄 데이터베이스 할당량이 제한되었으므로, T-SQL의 CREATE DATABASE … AS A COPY OF는 Business 데이터베이스와 동일한 요율이 부과되는 예비 자원 없이 Suspended Premium 데이터베이스를 만들었습니다. Premium 할당량은 이제 더 자유롭게 사용할 수 있으므로 Suspended Premium은 더이상 지원되지 않습니다. 데이터베이스 복사본은 이제 원본과 동일한 버전 및 성능 수준을 사용하여 생성되며 그에 따라 요금이 청구됩니다. 고객은 원할 경우 비용을 줄이기 위해 복사된 데이터베이스를 다른 서비스 계층 또는 성능 수준으로 다운그레이드할 수 있습니다. 기존 Suspended Premium 데이터베이스는 이 릴리스의 일부로 Business 버전으로 변환됩니다. [특정 시점 복원](sql-database-point-in-time-restore.md)의 도입으로 데이터베이스의 백업 복사본을 만들 필요성이 감소할 것으로 예상됩니다.

## Basic, Standard 및 Premium은 내 요금 청구 경험을 어떻게 개선합니까?

기본, 표준 및 Premium Azure SQL 데이터베이스에서는 시간 단위로 요금이 청구되며 24시간 동안 각 데이터베이스를 4번 확장 및 축소할 수 있습니다. 매 시간 선택하는 최고 서비스 계층과 성능 수준을 기반으로 고정된 요금이 청구됩니다. 또한 청구서에서 성능 수준이 분할되므로(Basic, S1 및 P2) 각 성능 수준에서 한 달에 발생한 데이터베이스 일/시간 수를 더 쉽게 볼 수 있습니다. Web 및 Business 데이터베이스는 계속해서 데이터베이스 크기를 기반으로 Database Units를 사용하여 요금이 청구됩니다. 가격 책정 및 새 서비스 계층 사이의 차이점에 대해 더 자세히 알아보려면 [SQL 데이터베이스 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)를 방문하세요.


## 참고 항목

[Azure SQL 데이터베이스](https://azure.microsoft.com/documentation/services/sql-database/)

[Web 및 Business 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/web-business/)

[서비스 계층](sql-database-service-tiers.md)

[Azure SQL 데이터베이스 V12로 업그레이드](sql-database-upgrade-server-portal.md)

<!---HONumber=AcomDC_0615_2016-->