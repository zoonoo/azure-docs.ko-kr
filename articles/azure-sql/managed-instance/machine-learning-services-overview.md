---
title: Azure SQL Managed Instance의 Machine Learning Services
description: 이 문서에서는 Azure SQL Managed Instance에서 개요 또는 Machine Learning Services을 제공 합니다.
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
ms.openlocfilehash: 94495144c64b3770995a5f67e9129b3ba86e741e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599564"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance의 Machine Learning Services

Machine Learning Services은 Python 및 R 스크립트를 모두 지 원하는 데이터베이스 내 기계 학습 기능을 제공 하는 Azure SQL Managed Instance의 기능입니다. 이 기능에는 고성능 예측 분석 및 기계 학습을 위한 Microsoft Python 및 R 패키지가 포함 됩니다. 관계형 데이터는 저장 프로시저, Python 또는 R 문을 포함 하는 T-sql 스크립트, T-sql을 포함 하는 Python 또는 R 코드를 통해 스크립트에서 사용할 수 있습니다.

## <a name="what-is-machine-learning-services"></a>Machine Learning Services란?

Azure SQL Managed Instance에서 Machine Learning Services를 사용 하 여 데이터베이스 내에서 Python 및 R 스크립트를 실행할 수 있습니다. 이를 사용하여 데이터를 준비 및 정리하고, 기능 엔지니어링을 수행하고, 데이터베이스 내에서 기계 학습 모델을 학습, 평가 및 배포할 수 있습니다. 이 기능은 데이터가 상주하는 스크립트를 실행하고 네트워크를 통해 다른 서버에 데이터를 전송하는 작업을 제거합니다.

Azure SQL Managed Instance에서 R/Python 지원과 함께 Machine Learning Services를 사용 하 여 다음을 수행 합니다.

- **R 및 python 스크립트를 실행 하 여 데이터 준비 및 범용 데이터 처리를 수행** 합니다. 이제 r 및 python 스크립트를 실행 하기 위해 데이터를 다른 서버로 이동 하는 대신 데이터를 저장 하는 Azure SQL Managed Instance로 r/python 스크립트를 가져올 수 있습니다. 데이터 이동 및 대기 시간, 보안 및 규정 준수와 관련 된 문제에 대 한 필요성을 없앨 수 있습니다.

- **데이터베이스에서 기계 학습 모델 학습** -모든 오픈 소스 알고리즘을 사용 하 여 모델을 학습 시킬 수 있습니다. 데이터베이스에서 가져온 샘플 데이터 집합에 의존 하지 않고 전체 데이터 집합에 대 한 학습을 쉽게 확장할 수 있습니다.

- **저장 프로시저에서 프로덕션에 모델 및 스크립트 배포** -스크립트 및 학습 된 모델은 단순히 t-sql 저장 프로시저에 포함 하 여 조작 가능한 수 있습니다. Azure SQL Managed Instance에 연결 하는 앱은 저장 프로시저를 호출 하 여 이러한 모델에서 예측 및 인텔리전스의 이점을 누릴 수 있습니다. 또한 네이티브 T-sql PREDICT 함수를 사용 하 여 매우 동시 실시간 점수 매기기 시나리오에서 빠른 점수 매기기를 위한 모델을 운영 수 있습니다.

Python 및 R의 기본 배포판은 Machine Learning Services에 포함되어 있습니다. Microsoft 패키지 [revoscalepy](/sql/machine-learning/python/ref-py-revoscalepy) 및 [microsoftml](/sql/machine-learning/python/ref-py-microsoftml)(Python용), [RevoScaleR](/sql/machine-learning/r/ref-r-revoscaler), [MicrosoftML](/sql/machine-learning/r/ref-r-microsoftml), [olapR](/sql/machine-learning/r/ref-r-olapr) 및 [sqlrutils](/sql/machine-learning/r/ref-r-sqlrutils)(R용) 외에 PyTorch, TensorFlow 및 scikit-learn과 같은 오픈 소스 패키지 및 프레임워크를 설치하고 사용할 수 있습니다.

## <a name="how-to-enable-machine-learning-services"></a>Machine Learning Services를 사용 하도록 설정 하는 방법

다음 SQL 명령을 사용 하 여 확장성을 사용 하도록 설정 하 여 Azure SQL Managed Instance에서 Machine Learning Services를 사용 하도록 설정할 수 있습니다 (SQL Managed Instance 다시 시작 되 고 몇 초 동안 사용할 수 없음).

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

이 명령이 SQL Managed Instance 리소스에 영향을 주는 방법에 대 한 자세한 내용은 [리소스 관리](machine-learning-services-differences.md#resource-governance)를 참조 하세요.

### <a name="enable-machine-learning-services-in-a-failover-group"></a>장애 조치 (failover) 그룹에서 Machine Learning Services 사용

[장애 조치 (failover) 그룹](failover-group-add-instance-tutorial.md)에서 시스템 데이터베이스는 보조 인스턴스에 복제 되지 않습니다. 자세한 내용은 [장애 조치 (failover) 그룹의 제한 사항](../database/auto-failover-group-overview.md#limitations-of-failover-groups) 을 참조 하세요.

사용 중인 Managed Instance 장애 조치 (failover) 그룹의 일부인 경우 다음을 수행 합니다.

- `sp_configure` `RECONFIGURE` 장애 조치 (failover) 그룹의 각 인스턴스에 대해 및 명령을 실행 하 여 Machine Learning Services를 사용 하도록 설정 합니다.

- Master 데이터베이스가 아니라 사용자 데이터베이스에 R/Python 라이브러리를 설치 합니다.

## <a name="next-steps"></a>다음 단계

- [SQL Server Machine Learning Services의 주요 차이점](machine-learning-services-differences.md)을 참조 하세요.
- Machine Learning Services에서 Python을 사용 하는 방법에 대 한 자세한 내용은 [python 스크립트 실행](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)을 참조 하세요.
- Machine Learning Services에서 R을 사용 하는 방법에 대 한 자세한 내용은 [r 스크립트 실행](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)을 참조 하세요.
- 다른 SQL 플랫폼의 기계 학습에 대 한 자세한 내용은 [sql machine learning 설명서](/sql/machine-learning/index)를 참조 하세요.
