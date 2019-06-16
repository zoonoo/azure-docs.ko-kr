---
title: Azure SQL Database Machine Learning 서비스 (미리 보기)에 대 한 주요 차이점
description: 이 항목에서는 Azure SQL Database Machine Learning Services(R 포함)와 SQL Server Machine Learning 서비스의 주요 차이점을 설명합니다.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 92785015a1ce122b8301b56fa62d122c8d95180c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64725044"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Azure SQL Database (미리 보기)에서 Machine Learning Services 및 SQL Server 간의 주요 차이점

(R)을 사용한 Azure SQL Database Machine Learning 서비스 (미리 보기) 기능은 비슷합니다 [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)합니다. 다음은 몇 가지 주요 차이점입니다.

> [!IMPORTANT]
> Azure SQL Database Machine Learning Services는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="language-support"></a>언어 지원

SQL Server는 [확장성 프레임워크](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)를 통해 R 및 Python을 지원합니다. SQL Database는 두 언어를 모두 지원하지 않습니다. 주요 차이점은 다음과 같습니다.

- R은 SQL Database에서 유일하게 지원되는 언어입니다. 현재 Python에 대한 다른 지원은 제공되지 않습니다.
- R 버전은 3.4.4입니다.
- `sp_configure`를 통해 `external scripts enabled`를 구성할 필요가 없습니다. [등록](sql-database-machine-learning-services-overview.md#signup)하면 SQL 데이터베이스에 대한 기계 학습을 사용할 수 있습니다.

## <a name="package-management"></a>패키지 관리

R 패키지 관리와 설치 작업은 SQL Database와 SQL Server 간에 다릅니다. 차이점은 다음과 같습니다.

- R 패키지는 [sqlmlutils](https://github.com/Microsoft/sqlmlutils) 또는 [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql)를 통해 설치됩니다.
- 패키지는 아웃바운드 네트워크 호출을 수행할 수 없습니다. 이 제한은 비슷합니다는 [Machine Learning 서비스에 대 한 방화벽 규칙을 기본](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) SQL Server에 있지만 SQL Database에서 변경할 수 없습니다.
- 설치나 사용을 위해 OS API에 대한 액세스가 필요하거나 외부 런타임(예: Java)에 의존하는 패키지는 지원되지 않습니다.

## <a name="resource-governance"></a>리소스 관리

[Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) 및 외부 리소스 풀을 통해 R 리소스를 제한할 수 없습니다.

공개 미리 보기 동안 R 리소스 최대 SQL Database 리소스의 20%로 설정 되 고 종속 서비스 계층에서 선택 합니다. 자세한 내용은 [Azure SQL Database 구매 모델](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)을 참조하세요.

### <a name="insufficient-memory-error"></a>메모리 부족 오류

R에 대 한 사용 가능한 메모리가 부족 하 여 있으면 오류 메시지가 표시 됩니다. 일반적인 오류 메시지가 다음과 같습니다.

- 요청 id에 대 한 'R' 스크립트의 런타임과 통신할 수 없습니다: * * * 합니다. 'R' 런타임의 요구 사항을 확인 하세요
- 'Sp_execute_external_script' 0x80004004 HRESULT 사용 하 여 실행 중 'R' 스크립트 오류가 발생 했습니다. ... 외부 스크립트 오류가 발생 했습니다: "... C 함수 'R_AllocStringBuffer' (0mb) 메모리를 할당할 수 없습니다 "
- 외부 스크립트 오류가 발생 했습니다. 오류: 크기의 벡터를 할당할 수 없습니다.

메모리 사용 크기에 따라 달라 집니다 R 스크립트에서 실행 되는 병렬 쿼리 수가 사용 됩니다. 위의 오류가 발생할 경우이 해결 하려면 상위 서비스 계층으로 데이터베이스를 확장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 개요를 참조 하세요 [R (미리 보기)를 사용 하 여 Azure SQL Database Machine Learning Services](sql-database-machine-learning-services-overview.md)합니다.
- Azure SQL Database Machine Learning 서비스 (미리 보기) 쿼리를 R을 사용 하는 방법에 알아보려면 참조를 [빠른 시작 가이드](sql-database-connect-query-r.md)합니다.
- 시작 하기 위한 몇 가지 간단한 R 스크립트를 참조 하세요 [만들기 및 Azure SQL Database Machine Learning 서비스 (미리 보기)에서 간단한 R 스크립트 실행된](sql-database-quickstart-r-create-script.md)합니다.
