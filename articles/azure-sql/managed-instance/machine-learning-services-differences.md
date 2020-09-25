---
title: Machine Learning Services (미리 보기)의 주요 차이점
description: 이 항목에서는 Azure SQL Managed Instance Machine Learning Services와 SQL Server Machine Learning Services 간의 주요 차이점에 대해 설명 합니다.
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
ms.date: 05/27/2020
ms.openlocfilehash: 9ff2de18042c466bdd8fa6c71194fff4286c820d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325099"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Azure SQL Managed Instance와 SQL Server의 Machine Learning Services에 대한 주요 차이점

[AZURE SQL Managed Instance (미리 보기)의 Machine Learning Services](machine-learning-services-overview.md) 기능은 [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)와 거의 동일 합니다. 다음은 몇 가지 주요 차이점입니다.

> [!IMPORTANT]
> Azure SQL Managed Instance의 Machine Learning Services은 현재 공개 미리 보기로 제공 됩니다. 등록 하려면 [미리 보기 등록](machine-learning-services-overview.md#signup)을 참조 하세요.

## <a name="preview-limitations"></a>미리 보기 제한 사항

미리 보기 동안 서비스에는 다음과 같은 제한 사항이 있습니다.

- 루프백 연결은 작동 하지 않습니다 ( [Python 또는 R 스크립트에서 SQL Server에 대 한 루프백 연결](/sql/machine-learning/connect/loopback-connection)참조).
- 외부 리소스 풀은 지원되지 않습니다.
- Python 및 R만 지원됩니다. Java와 같은 외부 언어를 추가할 수 없습니다.
- MPI ( [메시지 전달 인터페이스](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) )를 사용 하는 시나리오는 지원 되지 않습니다.

SLO(서비스 수준 목표) 업데이트의 경우 SLO를 업데이트하고 지원 티켓을 제기하여 R/Python의 전용 리소스 제한을 다시 사용하도록 설정하세요.

## <a name="language-support"></a>언어 지원

SQL Managed Instance에서 Machine Learning Services 하 고 Python 및 R [확장성 프레임 워크](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)를 모두 지원 SQL Server. 주요 차이점은 다음과 같습니다.

- Python 및 R의 초기 버전은 SQL Managed Instance 및 SQL Server에서 Machine Learning Services 서로 다릅니다.

  | 시스템               | Python | R     |
  |----------------------|--------|-------|
  | SQL Managed Instance | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- `sp_configure`를 통해 `external scripts enabled`를 구성할 필요가 없습니다. 미리 보기에 [등록](machine-learning-services-overview.md#signup) 한 후에는 Azure SQL Managed Instance에 대해 기계 학습을 사용할 수 있습니다.

## <a name="packages"></a>패키지

Python 및 R 패키지 관리는 SQL Managed Instance와 SQL Server 간에 다르게 작동 합니다. 차이점은 다음과 같습니다.

- 패키지는 아웃바운드 네트워크 호출을 수행할 수 없습니다. 이 제한은 SQL Server의 [Machine Learning Services에 대 한 기본 방화벽 규칙과](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) 유사 하지만 SQL Managed Instance에서 변경할 수는 없습니다.
- 설치나 사용을 위해 OS API에 대한 액세스가 필요하거나 외부 런타임(예: Java)에 의존하는 패키지는 지원되지 않습니다.

Python 및 R 패키지를 관리 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

- [Python 패키지 정보 가져오기](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)
- [R 패키지 정보 가져오기](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)

## <a name="resource-governance"></a>리소스 거버넌스

[Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) 및 외부 리소스 풀을 통해 R 리소스를 제한할 수 없습니다.

공개 미리 보기 중에 R 리소스는 SQL Managed Instance 리소스의 최대 20%로 설정되며, 선택한 서비스 계층에 따라 달라집니다. 자세한 내용은 [Azure SQL Database 구매 모델](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)을 참조하세요.

### <a name="insufficient-memory-error"></a>메모리 부족 오류

R에 사용할 수 있는 메모리가 충분하지 않으면 오류 메시지가 표시됩니다. 일반적인 오류 메시지는 다음과 같습니다.

- 요청 id: * * * * * * *에 대 한 ' R ' 스크립트의 런타임과 통신할 수 없습니다. ‘R’ 런타임의 요구 사항을 확인하세요.
- 'sp_execute_external_script'를 실행하는 동안 HRESULT 0x80004004와 함께 ‘R’ 스크립트 오류가 발생했습니다. ... 외부 스크립트 오류가 발생했습니다. "C 함수 'R_AllocStringBuffer'에서 메모리(0MB)를 할당할 수 없습니다."
- 외부 스크립트 오류가 발생 했습니다. 오류: 크기의 벡터를 할당할 수 없습니다.

메모리 사용량은 R 스크립트에서 사용되는 양 및 실행되는 병렬 쿼리 수에 따라 달라집니다. 위의 오류가 표시되면 데이터베이스를 더 높은 서비스 계층으로 확장하여 이 문제를 해결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [AZURE SQL Managed Instance의](machine-learning-services-overview.md)개요, Machine Learning Services를 참조 하세요.
- Machine Learning Services에서 Python을 사용 하는 방법에 대 한 자세한 내용은 [python 스크립트 실행](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)을 참조 하세요.
- Machine Learning Services에서 R을 사용 하는 방법에 대 한 자세한 내용은 [r 스크립트 실행](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)을 참조 하세요.
