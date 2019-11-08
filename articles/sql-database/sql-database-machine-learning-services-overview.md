---
title: R을 사용 하 여 Machine Learning Services (미리 보기)
description: 이 문서에서는 Azure SQL Database Machine Learning Services (R 사용)에 대해 설명 하 고 작동 방식을 설명 합니다.
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
ms.date: 03/01/2019
ms.openlocfilehash: a2af1fdd1ee461e3b3db613ff4a575649da2dfdc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827426"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>R을 사용 하 여 Machine Learning Services Azure SQL Database (미리 보기)

Machine Learning Services는 Azure SQL Database의 기능이며, 데이터베이스 내 R 스크립트를 실행하는 데 사용됩니다. 이 기능에는 고성능 예측 분석 및 기계 학습을 위한 Microsoft R 패키지가 포함됩니다. 관계형 데이터는 저장된 프로시저, R 문이 포함된 T-SQL 스크립트 또는 T-SQL이 포함된 R 코드를 통해 R 스크립트에서 사용할 수 있습니다.

> [!IMPORTANT]
> Azure SQL Database Machine Learning Services (R)는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
> 공개 미리 보기는 **범용** 및 **중요 비즈니스용** 서비스 계층에서 vcore 기반 구매 모델을 사용 하 여 단일 데이터베이스 및 탄력적 풀에 사용할 수 있습니다. 초기 공개 미리 보기에서는 **하이퍼스케일** 서비스 계층과 **관리되는 인스턴스** 배포 옵션이 지원되지 않습니다. 현재 지원되는 언어는 R뿐입니다. 현재 Python에 대한 다른 지원은 제공되지 않습니다.
>
> 미리 보기는 현재 유럽 서부, 서유럽, 미국 서 부 2, 미국 동부, 미국 중부, 미국 중 북부, 캐나다 중부, 동남 아시아, 인도 남부 및 오스트레일리아 남동쪽 지역에서 사용할 수 있습니다.
>
> 아래에서 [미리 보기에 등록](#signup)하세요.

## <a name="what-you-can-do-with-r"></a>R에서 수행할 수 있는 작업

강력한 R 언어를 사용하여 데이터베이스에 대한 고급 분석 및 기계 학습을 제공합니다. 이 기능은 데이터가 상주하는 위치에서 계산 및 처리를 수행하기 때문에 네트워크를 통해 데이터를 끌어올 필요가 없습니다. 또한 엔터프라이즈 R 패키지의 기능을 활용 하 여 고급 분석을 대규모로 제공할 수 있습니다.

Machine Learning Services는 Microsoft의 엔터프라이즈 R 패키지가 오버레이된 기본 R 배포판을 포함하고 있습니다. Microsoft의 R 함수 및 알고리즘은 규모 및 유용성을 중심으로 엔지니어링되었으며 예측 분석, 통계 모델링, 데이터 시각화 및 첨단 기계 학습 알고리즘을 제공합니다.

### <a name="r-packages"></a>R 패키지

가장 일반적인 오픈 소스 R 패키지는 Machine Learning Services에 미리 설치 되어 있습니다. Microsoft의 다음 R 패키지도 포함되어 있습니다.

| R 패키지 | 설명|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open은 Microsoft의 향상된 R 배포판입니다. 통계 분석 및 데이터 과학을 위한 완전 한 오픈 소스 플랫폼입니다. R을 기반으로 하고 R과 100 % 호환되며 향상된 성능과 재현성을 위한 추가 기능을 포함합니다. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR은 확장성 있는 R의 기본 라이브러리입니다.이 라이브러리의 함수는 가장 널리 사용됩니다. 이 라이브러리에는 데이터 변환 및 조작, 통계 요약, 시각화 및 다양한 형태의 모델링 및 분석이 있습니다. 또한 이 라이브러리의 함수는 계산 엔진에 의해 조정되고 관리되는 데이터 청크로 작업할 수 있는 기능을 사용하여, 병렬 처리를 위해 사용 가능한 코어에 작업 부하를 자동으로 분배합니다. |
| [MicrosoftML(R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML은 텍스트 분석, 이미지 분석 및 정서 분석을 위한 사용자 지정 모델을 만드는 기계 학습 알고리즘을 추가합니다. |

사전 설치 된 패키지 외에 [추가 패키지를 설치할](sql-database-machine-learning-services-add-r-packages.md)수도 있습니다.

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>미리 보기 등록

공개 미리 보기에 등록하려면 다음 단계를 따르세요.

1. Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

2. [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com)에서 Microsoft에 이메일을 보내서 공개 미리 보기에 등록할 수 있습니다. SQL Database의 Machine Learning Services(R 포함) 공개 미리 보기는 기본적으로 사용되지 않습니다.

프로그램에 등록 하면 Microsoft는 공개 미리 보기에 등록 하 고 기존 또는 새 데이터베이스에 R을 사용 하도록 설정 합니다.

공개 미리 보기 동안 프로덕션 작업에는 R을 사용 하지 않는 것이 좋습니다. Machine Learning Services

## <a name="next-steps"></a>다음 단계

- [SQL Server Machine Learning Services의 주요 차이점](sql-database-machine-learning-services-differences.md)을 참조 하세요.
- R을 사용 하 여 Azure SQL Database Machine Learning Services (미리 보기)를 쿼리 하는 방법에 대 한 자세한 내용은 [빠른 시작 가이드](sql-database-connect-query-r.md)를 참조 하세요.
- 몇 가지 간단한 R 스크립트를 시작 하려면 [Azure SQL Database Machine Learning Services (미리 보기)에서 간단한 r 스크립트 만들기 및 실행](sql-database-quickstart-r-create-script.md)을 참조 하세요.
