---
title: Azure SQL Database(미리 보기)의 Machine Learning Services(R 포함)에 대한 주요 차이점 개요
description: 이 항목에서는 Azure SQL Database Machine Learning Services(R 포함)와 SQL Server Machine Learning 서비스의 주요 차이점을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning-services
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 01/31/2019
ms.openlocfilehash: adc303e9b375aaa6f37e9e79ea5434675c75523d
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55825030"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-and-sql-server"></a>Azure SQL Database와 SQL Server의 Machine Learning Services에 대한 주요 차이점

Azure SQL Database의 Machine Learning Services(R 포함) 기능은 [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)와 비슷합니다. 이 두 가지의 주요 차이점은 아래와 같습니다.

## <a name="language-support"></a>언어 지원

SQL Server는 [확장성 프레임워크](https://docs.microsoft.com/en-us/sql/advanced-analytics/concepts/extensibility-framework)를 통해 R 및 Python을 지원합니다. SQL Database는 두 언어를 모두 지원하지 않습니다. 주요 차이점은 다음과 같습니다.

- R은 SQL Database에서 유일하게 지원되는 언어입니다. 현재 Python에 대한 다른 지원은 제공되지 않습니다.
- R 버전은 3.4.4입니다.
- `sp_configure`를 통해 `external scripts enabled`를 구성할 필요가 없습니다. [등록](sql-database-machine-learning-services-overview.md#signup)하면 SQL 데이터베이스에 대한 기계 학습을 사용할 수 있습니다.

## <a name="package-management"></a>패키지 관리

R 패키지 관리와 설치 작업은 SQL Database와 SQL Server 간에 다릅니다. 차이점은 다음과 같습니다.

- R 패키지는 [sqlmlutils](https://github.com/Microsoft/sqlmlutils) 또는 [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql)를 통해 설치됩니다.
- 패키지는 아웃바운드 네트워크 호출을 수행할 수 없습니다. 이 제한은 SQL Server의 [Machine Learning Services에 대한 기본 방화벽 규칙](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration)과 유사하지만 SQL Database에서는 변경할 수 없습니다.
- 설치나 사용을 위해 OS API에 대한 액세스가 필요하거나 외부 런타임(예: Java)에 의존하는 패키지는 지원되지 않습니다.

## <a name="resource-governance"></a>리소스 관리

[Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) 및 외부 리소스 풀을 통해 R 리소스를 제한할 수 없습니다. R 리소스는 SQL Database 리소스의 백분율이며 선택한 서비스 계층에 따라 달라집니다. 자세한 내용은 [Azure SQL Database 구매 모델](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)을 참조하세요.

## <a name="security-isolation"></a>보안 격리

Azure SQL Database에서 SQLPAL(SQL 플랫폼 추상화 계층)은 외부 프로세스에 대한 격리를 제공합니다. 이러한 격리는 R 스크립트를 실행하는 추가 보안 계층을 제공합니다.

## <a name="next-steps"></a>다음 단계

- 일반 정보는 [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics) 설명서를 참조하세요.
- Azure SQL Database에서 Machine Learning Services(R 포함)를 사용하는 방법을 알아보려면 [빠른 시작 가이드](sql-database-connect-query-r.md)를 참조하세요.
- 자세한 내용은 [SQL Server R 언어 자습서](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)를 참조하세요.