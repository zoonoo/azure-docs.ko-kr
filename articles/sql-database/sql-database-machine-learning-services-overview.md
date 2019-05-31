---
title: R (미리 보기) 개요를 사용 하 여 azure SQL Database Machine Learning 서비스
description: 이 문서에서는 Azure SQL Database Machine Learning 서비스 (R)을 사용 및 작동 하는 방법을 설명 합니다.
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
ms.openlocfilehash: fe472b8a19b45d7f7b00a8f858c9179d6ee51999
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951586"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>R (미리 보기)를 사용 하 여 azure SQL Database Machine Learning 서비스

Machine Learning Services는 Azure SQL Database의 기능이며, 데이터베이스 내 R 스크립트를 실행하는 데 사용됩니다. 이 기능에는 고성능 예측 분석 및 기계 학습을 위한 Microsoft R 패키지가 포함됩니다. 관계형 데이터는 저장된 프로시저, R 문이 포함된 T-SQL 스크립트 또는 T-SQL이 포함된 R 코드를 통해 R 스크립트에서 사용할 수 있습니다.

> [!IMPORTANT]
> (R)을 사용한 azure SQL Database Machine Learning Services는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
> 공개 미리 보기는 단일 데이터베이스 및 탄력적 풀에서 vCore 기반 구매 모델을 사용 하 여 사용할 수 있습니다는 **범용** 하 고 **중요 한 비즈니스** 서비스 계층입니다. 초기 공개 미리 보기에서는 **하이퍼스케일** 서비스 계층과 **관리되는 인스턴스** 배포 옵션이 지원되지 않습니다. 현재 지원되는 언어는 R뿐입니다. 현재 Python에 대한 다른 지원은 제공되지 않습니다.
>
> 미리 보기는 다음 지역에서 현재 사용할 수 있습니다. 유럽 서 부, 유럽 북부, 미국 서 부 2, 미국 동부, 미국 중남부, 미국 중 북부, 캐나다 중부, 동남 아시아, 인도 남부 및 오스트레일리아 남동부 합니다.
>
> 아래에서 [미리 보기에 등록](#signup)하세요.

## <a name="what-you-can-do-with-r"></a>R에서 수행할 수 있는 작업

강력한 R 언어를 사용하여 데이터베이스에 대한 고급 분석 및 기계 학습을 제공할 수 있습니다. 이 기능은 데이터가 상주하는 위치에서 계산 및 처리를 수행하기 때문에 네트워크를 통해 데이터를 끌어올 필요가 없습니다. 또한 고급 분석을 대규모로 전달할 엔터프라이즈 R 패키지의 기능을 활용할 수 있습니다.

Machine Learning Services는 Microsoft의 엔터프라이즈 R 패키지가 오버레이된 기본 R 배포판을 포함하고 있습니다. Microsoft의 R 함수 및 알고리즘은 규모 및 유용성을 중심으로 엔지니어링되었으며 예측 분석, 통계 모델링, 데이터 시각화 및 첨단 기계 학습 알고리즘을 제공합니다.

### <a name="r-packages"></a>R 패키지

가장 일반적인 오픈 소스 R 패키지에서 Machine Learning 서비스 미리 설치 되어 있습니다. Microsoft의 다음 R 패키지도 포함되어 있습니다.

| R 패키지 | 설명|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open은 Microsoft의 향상된 R 배포판입니다. 통계 분석 및 데이터 과학을 위한 완벽 한 오픈 소스 플랫폼 이며 R을 기반으로 하고 R과 100 % 호환되며 향상된 성능과 재현성을 위한 추가 기능을 포함합니다. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR은 확장성 있는 R의 기본 라이브러리입니다.이 라이브러리의 함수는 가장 널리 사용됩니다. 이 라이브러리에는 데이터 변환 및 조작, 통계 요약, 시각화 및 다양한 형태의 모델링 및 분석이 있습니다. 또한 이 라이브러리의 함수는 계산 엔진에 의해 조정되고 관리되는 데이터 청크로 작업할 수 있는 기능을 사용하여, 병렬 처리를 위해 사용 가능한 코어에 작업 부하를 자동으로 분배합니다. |
| [MicrosoftML(R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML은 텍스트 분석, 이미지 분석 및 정서 분석을 위한 사용자 지정 모델을 만드는 기계 학습 알고리즘을 추가합니다. |

미리 설치 된 패키지 외에도 수 있습니다 [추가 패키지 설치](sql-database-machine-learning-services-add-r-packages.md)합니다.

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>미리 보기 등록

공개 미리 보기에 등록하려면 다음 단계를 따르세요.

1. Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

2. [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com)에서 Microsoft에 이메일을 보내서 공개 미리 보기에 등록할 수 있습니다. SQL Database의 Machine Learning Services(R 포함) 공개 미리 보기는 기본적으로 사용되지 않습니다.

프로그램에 등록 되 면 Microsoft는 온 보 딩 공개 미리 보기와 enable R 기존 또는 새 데이터베이스에 있습니다.

R 사용 하 여 machine Learning Services 공개 미리 보기 동안에 프로덕션 워크 로드에 대 한 권장 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- 참조 된 [SQL Server Machine Learning Services의 주요](sql-database-machine-learning-services-differences.md)합니다.
- Azure SQL Database Machine Learning 서비스 (미리 보기) 쿼리를 R을 사용 하는 방법에 알아보려면 참조를 [빠른 시작 가이드](sql-database-connect-query-r.md)합니다.
- 시작 하기 위한 몇 가지 간단한 R 스크립트를 참조 하세요 [만들기 및 Azure SQL Database Machine Learning 서비스 (미리 보기)에서 간단한 R 스크립트 실행된](sql-database-quickstart-r-create-script.md)합니다.
