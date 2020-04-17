---
title: 머신 러닝 서비스의 주요 차이점(미리 보기)
description: 이 항목에서는 Azure SQL Database Machine Learning Services(R 포함)와 SQL Server Machine Learning 서비스의 주요 차이점을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 34ba75b6126024c9cd43d6fe474f7c1b62dd990f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453152"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Azure SQL 데이터베이스(미리 보기)의 기계 학습 서비스와 SQL Server 간의 주요 차이점

Azure SQL 데이터베이스 기계 학습 서비스(R 포함)의 기능(미리 보기)은 [SQL Server 기계 학습 서비스와](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)유사합니다. 다음은 몇 가지 주요 차이점입니다.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>언어 지원

SQL Server는 [확장성 프레임워크](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)를 통해 R 및 Python을 지원합니다. SQL Database는 두 언어를 모두 지원하지 않습니다. 주요 차이점은 다음과 같습니다.

- R은 SQL Database에서 유일하게 지원되는 언어입니다. 현재 Python에 대한 다른 지원은 제공되지 않습니다.
- R 버전은 3.4.4입니다.
- `sp_configure`를 통해 `external scripts enabled`를 구성할 필요가 없습니다.

## <a name="package-management"></a>패키지 관리

R 패키지 관리와 설치 작업은 SQL Database와 SQL Server 간에 다릅니다. 차이점은 다음과 같습니다.

- R 패키지는 [sqlmlutils](https://github.com/Microsoft/sqlmlutils) 또는 [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql)를 통해 설치됩니다.
- 패키지는 아웃바운드 네트워크 호출을 수행할 수 없습니다. 이 제한은 SQL Server의 [기계 학습 서비스에 대한 기본 방화벽 규칙과](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) 유사하지만 SQL Database에서는 변경할 수 없습니다.
- 설치나 사용을 위해 OS API에 대한 액세스가 필요하거나 외부 런타임(예: Java)에 의존하는 패키지는 지원되지 않습니다.

## <a name="writing-to-a-temporary-table"></a>임시 테이블에 쓰기

Azure SQL Database에서 RODBC를 사용하는 경우 `sp_execute_external_script` 세션 내부 또는 외부에서 만든 테이블에 쓸 수 없습니다. 해결 방법은 [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) 및 [rxDataStep(덮어쓰기=FALSE](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) 및 추가="행")을 사용하여 `sp_execute_external_script` 쿼리 전에 만든 전역 임시 테이블에 쓰는 것입니다.

## <a name="resource-governance"></a>리소스 거버넌스

[Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) 및 외부 리소스 풀을 통해 R 리소스를 제한할 수 없습니다.

공개 미리 보기 중에 R 리소스는 SQL Database 리소스의 최대 20%로 설정되며 선택한 서비스 계층에 따라 달라집니다. 자세한 내용은 [Azure SQL Database 구매 모델](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)을 참조하세요.
### <a name="insufficient-memory-error"></a>메모리 부족 오류

R에 사용할 수 있는 메모리가 부족하면 오류 메시지가 표시됩니다. 일반적인 오류 메시지는 다음과 같습니다.

- 요청 ID에 대한 'R' 스크립트의 런타임과 통신할 수 없습니다: *******. 'R' 런타임의 요구 사항을 확인하십시오.
- HRESULT 0x80004004를 사용 하 여 'sp_execute_external_script' 실행 하는 동안 'R' 스크립트 오류가 발생 했습니다. ... 외부 스크립트 오류가 발생했습니다. C 함수 'R_AllocStringBuffer'에서 메모리(0Mb)를 할당할 수 없습니다."
- 외부 스크립트 오류가 발생했습니다: 오류: 크기 벡터를 할당할 수 없습니다.

메모리 사용량은 R 스크립트에 사용되는 양과 실행되는 병렬 쿼리 수에 따라 달라집니다. 위의 오류가 발생하면 데이터베이스를 더 높은 서비스 계층으로 확장하여 이 문제를 해결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [R(미리 보기)을 사용하여 개요, Azure SQL 데이터베이스 기계 학습 서비스를](sql-database-machine-learning-services-overview.md)참조하십시오.
- R을 사용하여 Azure SQL 데이터베이스 기계 학습 서비스(미리 보기)를 쿼리하는 방법을 알아보려면 [빠른 시작 가이드를](sql-database-connect-query-r.md)참조하십시오.
- 몇 가지 간단한 R 스크립트를 시작하려면 [Azure SQL Database 기계 학습 서비스(미리 보기)에서 간단한 R 스크립트 만들기 및 실행을](sql-database-quickstart-r-create-script.md)참조하십시오.
