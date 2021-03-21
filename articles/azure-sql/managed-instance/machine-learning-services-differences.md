---
title: Machine Learning Services의 주요 차이점
description: 이 문서에서는 Azure SQL Managed Instance Machine Learning Services와 SQL Server Machine Learning Services 간의 주요 차이점을 설명 합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: b5ad439a8e10fa9aa44e477ca35f45d65ae40803
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599547"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Azure SQL Managed Instance와 SQL Server의 Machine Learning Services에 대한 주요 차이점

이 문서에서는 [AZURE SQL Managed Instance의 Machine Learning Services](machine-learning-services-overview.md) 와 [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)간의 기능에서 몇 가지 주요 차이점을 설명 합니다.

## <a name="language-support"></a>언어 지원

SQL Managed Instance에서 Machine Learning Services 하 고 SQL Server Python 및 R [확장성 프레임 워크](/sql/machine-learning/concepts/extensibility-framework)를 지원 합니다. SQL Managed Instance의 주요 차이점은 다음과 같습니다.

- Python 및 R만 지원됩니다. Java와 같은 외부 언어를 추가할 수 없습니다.

- Python 및 R의 초기 버전은 다릅니다.

  | 플랫폼                   | Python 런타임 버전           | R 런타임 버전                   |
  |----------------------------|----------------------------------|--------------------------------------|
  | Azure SQL Managed Instance | 3.7.2                            | 3.5.2                                |
  | SQL Server 2019            | 3.7.1                            | 3.5.2                                |
  | SQL Server 2017            | 3.5.2 및 3.7.2 (CU22 이상) | 3.3.3 및 3.5.2 (CU22 이상)     |
  | SQL Server 2016            | 사용할 수 없음                    | 3.2.2 및 3.5.2 (SP2 CU14 이상) |

## <a name="python-and-r-packages"></a>Python 및 R 패키지

외부 런타임 (예: Java)에 의존 하거나 설치 또는 사용을 위해 OS Api에 액세스 해야 하는 패키지에 대해서는 SQL Managed Instance 지원 되지 않습니다.

Python 및 R 패키지를 관리 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

- [Python 패키지 정보 가져오기](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [R 패키지 정보 가져오기](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>리소스 거버넌스

SQL Managed Instance에서는 [Resource Governor](/sql/relational-databases/resource-governor/resource-governor?view=azuresqldb-mi-current&preserve-view=true)를 통해 R 리소스를 제한할 수 없으며 외부 리소스 풀은 지원 되지 않습니다.

확장성을 사용 하는 경우 기본적으로 R 리소스는 사용 가능한 SQL Managed Instance 리소스의 최대 20%로 설정 됩니다. 이 기본 백분율을 변경 하려면에서 Azure 지원 티켓을 만듭니다 [https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/) .

다음 SQL 명령을 사용 하 여 확장성을 사용할 수 있습니다 (SQL Managed Instance를 다시 시작 하 고 몇 초 동안 사용할 수 없음).

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

확장성을 사용 하지 않도록 설정 하 고 메모리 및 CPU 리소스의 100%를 SQL Server으로 복원 하려면 다음 명령을 사용 합니다.

```sql
sp_configure 'external scripts enabled', 0;
RECONFIGURE WITH OVERRIDE;
```

SQL Managed Instance에서 사용할 수 있는 총 리소스는 선택한 서비스 계층에 따라 달라 집니다. 자세한 내용은 [Azure SQL Database 구매 모델](/azure/sql-database/sql-database-service-tiers)을 참조하세요.

### <a name="insufficient-memory-error"></a>메모리 부족 오류

메모리 사용량은 R 스크립트에서 사용되는 양 및 실행되는 병렬 쿼리 수에 따라 달라집니다. R에 사용할 수 있는 메모리가 충분 하지 않으면 오류 메시지가 표시 됩니다. 일반적인 오류 메시지는 다음과 같습니다.

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

이러한 오류 중 하나를 수신 하는 경우 데이터베이스를 더 높은 서비스 계층으로 확장 하 여 해결할 수 있습니다.

## <a name="sql-managed-instance-pools"></a>SQL Managed Instance 풀

Machine Learning Services 현재 [AZURE SQL Managed Instance 풀 (미리 보기)](instance-pools-overview.md)에서 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [AZURE SQL Managed Instance의](machine-learning-services-overview.md)개요, Machine Learning Services를 참조 하세요.
- Machine Learning Services에서 Python을 사용 하는 방법에 대 한 자세한 내용은 [python 스크립트 실행](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)을 참조 하세요.
- Machine Learning Services에서 R을 사용 하는 방법에 대 한 자세한 내용은 [r 스크립트 실행](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)을 참조 하세요.
