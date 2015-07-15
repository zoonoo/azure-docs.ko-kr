<properties
   pageTitle="SQL 데이터 웨어하우스에서 비즈니스 연속성 계획 | Microsoft Azure"
   description="SQL 데이터 웨어하우스에서 비즈니스 연속성 개요"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/25/2015"
   ms.author="sahajs"/>


# SQL 데이터 웨어하우스에서 비즈니스 연속성 계획

이 문서는 SQL 데이터 웨어하우스를 사용한 비즈니스 연속성 및 재해 복구 계획의 기본 사항을 안내합니다. 데이터 웨어하우스는 기업에 대한 정보의 중앙 저장소이며 조직의 모든 수준에서의 비즈니스 인텔리전스 및 분석을 위한 일상적인 작업에 있어 중요한 역할을 담당합니다. 따라서 필수적으로 데이터 웨어하우스가 안정적이며 복구 기능 및 연속 작업을 수행할 수 있어야 합니다. 특히,이 문서에서는 SQL 데이터 웨어하우스에서 지정 시간 복원 및 지역 복원을 사용하여 사용자 오류 및 재해에서 복구할 수 있는 방법을 보여줍니다.

## 개념

비즈니스 연속성 및 재해 복구 계획은 다음을 최적화하기 위해 필요합니다.

**복구 시간 목표(RTO):** 실패 중 복구 가용성 손실과 같은 중단 이벤트 후 데이터베이스가 완벽하게 복구되기 전의 최대 허용 가능한 시간입니다.

**복구 지점 목표(RTO):** 실패 중 최대 데이터 손실과 같은 중단 이벤트에서 데이터베이스가 복구될 때 손실된 업데이트의 최대 허용 가능한 시간입니다.

**예상 복구 시간(ERT):** 복원 요청 이후 데이터베이스가 완전하게 사용 가능하게 될 때까지의 예상 기간입니다.

## 비즈니스 연속성 시나리오

**인프라 오류로부터 복구:** 디스크 오류 등의 인프라 문제를 복구하는 데 이 시나리오를 참조합니다. 고객은 내결함성이 있는 비즈니스 연속성 및 고가용성 인프라를 확인하고자 합니다.

**사용자 오류에서 복구:** 이 시나리오에서는 의도 하지 않거나 부수적 데이터 손상 또는 삭제로부터의 복구를 가리킵니다. 사용자가 실수로 또는 우연하게 데이터를 수정하거나 삭제한 경우, 고객은 이전 시점으로 데이터베이스를 복원하여 비즈니스 연속성을 보장하고자 합니다.

**재해에서 복구(DR):** 이 시나리오는 주요 재해로부터의 복구를 가리킵니다. 자연 재해 또는 지역 가동 중단과 같은 중단 이벤트로 데이터베이스를 사용할 수 없는 시나리오에서는 고객이 비즈니스 작업을 계속하기 위해 다른 지역에서 데이터베이스를 복구하려고 합니다.


## 비즈니스 연속성 기능

SQL 데이터 웨어하우스가 데이터베이스의 안정성을 개선하고 앞에서 언급한 시나리오에서의 복구 기능 및 연속 작업을 허용하는 방법을 살펴보겠습니다.


### 데이터 중복

SQL 데이터 웨어하우스는 계산과 저장소를 분리하기 때문에, 모든 데이터는 지리적 중복 Azure 저장소(RA-GRS)로 직접 기록됩니다. 지역 중복 저장소는 기본 지역에서 수백 마일 떨어져 있는 보조 영역에 데이터를 복제합니다. 기본 및 보조 지역에서 데이터는 별도 오류 도메인 및 업그레이드 도메인에 걸쳐 각각 세 번씩 복제됩니다. 이렇게 하면 영역 중 하나를 사용할 수 없게 하는 완전한 지역 가동 중단 또는 재해 경우더라도 데이터를 지속적으로 사용할 수 있습니다. 읽기 액세스 지역 중복 저장소에 대한 자세한 정보는 [Azure 저장소 중복 옵션][]을 참조하세요.

### 데이터베이스 복원

데이터베이스 복원은 데이터베이스를 이전 시점으로 복원하도록 설계되었습니다. Azure SQL 데이터 웨어하우스 서비스는 8시간마다 자동 저장소 스냅숏을 사용하여 모든 데이터베이스를 보호하고 7일 동안 복원 지점의 불연속 집합 제공을 유지합니다. 이러한 백업은 RA-GRS Azure 저장소에 저장되므로 기본적으로 지리적으로 중복됩니다. 자동 백업 및 복원 기능은 추가 비용 없이 제공되며 실수로 손상 또는 삭제되지 않도록 데이터베이스를 보호하는 데 비용이 들지 않으며 별도의 관리 방법도 없습니다. 데이터베이스를 복원하는 방법에 대한 자세한 내용은 [사용자 오류로부터 복구][]를 참조하세요.

### 지역 복원

지역 복원은 중단 이벤트로 인해 사용할 수 없는 경우 데이터베이스를 복구하도록 설계되었습니다. 새 Azure 지역에서 새 데이터베이스를 만들려면 지역 중복 백업에서 데이터베이스를 복원하도록 지원을 요청할 수 있습니다. 백업이 지리적으로 중복되기 때문에 가동 중단으로 인해 데이터베이스에 액세스할 수 없는 경우더라도 데이터베이스를 복구하는 데 사용할 수 있습니다. 지역 복원 기능은 추가 비용 없이 제공됩니다.


## 다음 단계
다른 SQL 데이터베이스 버전의 비즈니스 연속성 기능에 대해 알아보려면 [SQL 데이터베이스 비즈니스 연속성 개요][]를 읽으세요.

<!--Image references-->

<!--Article references-->
[business continuity overview]: sql-database-business-continuity.md
[Finalize a recovered database]: sql-database-recovered-finalize.md
[Azure 저장소 중복 옵션]: storage-redundancy/#read-access-geo-redundant-storage-ra-grs.md
[SQL 데이터베이스 비즈니스 연속성 개요]: sql-database-business-continuity.md
[사용자 오류로부터 복구]: sql-data-warehouse-business-continuity-recover-from-user-error.md

<!--MSDN references-->
[Create database restore request]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: http://msdn.microsoft.com/library/azure/dn509562.aspx

<!--Other Web references-->

<!---HONumber=July15_HO1-->