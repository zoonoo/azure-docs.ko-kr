---
title: Azure SQL Database(미리 보기)의 Machine Learning Services(R 포함) 개요
description: 이 항목에서는 Azure SQL Database Machine Learning Services(R 포함)에 대해 설명하고 작동 방법을 설명합니다.
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
ms.date: 02/06/2019
ms.openlocfilehash: b50fd21e4d3325875134d2e2e9caeed9f8db75d0
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875606"
---
# <a name="machine-learning-services-with-r-in-azure-sql-database-preview"></a>Azure SQL Database(미리 보기)의 Machine Learning Services(R 포함)

Machine Learning Services는 Azure SQL Database의 기능이며, 데이터베이스 내 R 스크립트를 실행하는 데 사용됩니다. 이 기능에는 고성능 예측 분석 및 기계 학습을 위한 Microsoft R 패키지가 포함됩니다. 관계형 데이터는 저장된 프로시저, R 문이 포함된 T-SQL 스크립트 또는 T-SQL이 포함된 R 코드를 통해 R 스크립트에서 사용할 수 있습니다.

> [!IMPORTANT]
> Azure SQL Database의 Machine Learning Services(R 포함)는 현재 **범용** 및 **중요 비즈니스용** 서비스 계층의 vCore 기반 구매 모델을 사용하는 단일 데이터베이스 및 탄력적 풀에서 공개 미리 보기로 제공됩니다. 초기 공개 미리 보기에서는 **하이퍼스케일** 서비스 계층과 **관리되는 인스턴스** 배포 옵션이 지원되지 않습니다. 현재 지원되는 언어는 R뿐입니다. 현재 Python에 대한 다른 지원은 제공되지 않습니다. 
>
> 미리 보기는 현재 유럽 서부, 북유럽, 미국 서부 2, 미국 동부, 미국 중남부, 미국 중북부, 캐나다 중부, 동남 아시아, 인도 남부 및 오스트레일리아 남동부 지역에서 사용할 수 있습니다. 
>
> 아래에서 [미리 보기에 등록](#signup)하세요.

## <a name="what-you-can-do-with-r"></a>R에서 수행할 수 있는 작업

강력한 R 언어를 사용하여 데이터베이스에 대한 고급 분석 및 기계 학습을 제공할 수 있습니다. 이 기능은 데이터가 상주하는 위치에서 계산 및 처리를 수행하기 때문에 네트워크를 통해 데이터를 끌어올 필요가 없습니다. 또한 강력한 엔터프라이즈 R 패키지를 활용하여 고급 분석을 대규모로 제공합니다.

Machine Learning Services는 Microsoft의 엔터프라이즈 R 패키지가 오버레이된 기본 R 배포판을 포함하고 있습니다. Microsoft의 R 함수 및 알고리즘은 규모 및 유용성을 중심으로 엔지니어링되었으며 예측 분석, 통계 모델링, 데이터 시각화 및 첨단 기계 학습 알고리즘을 제공합니다.

### <a name="r-packages"></a>R 패키지

가장 일반적인 오픈 소스 R 패키지는 Machine Learning Services에 미리 설치되어 있습니다. Microsoft의 다음 R 패키지도 포함되어 있습니다.

| R 패키지 | 설명|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open은 Microsoft의 향상된 R 배포판입니다. 통계 분석 및 데이터 과학을 위한 완벽한 오픈 소스 플랫폼입니다. R을 기반으로 하고 R과 100 % 호환되며 향상된 성능과 재현성을 위한 추가 기능을 포함합니다. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR은 확장성 있는 R의 기본 라이브러리입니다.이 라이브러리의 함수는 가장 널리 사용됩니다. 이 라이브러리에는 데이터 변환 및 조작, 통계 요약, 시각화 및 다양한 형태의 모델링 및 분석이 있습니다. 또한 이 라이브러리의 함수는 계산 엔진에 의해 조정되고 관리되는 데이터 청크로 작업할 수 있는 기능을 사용하여, 병렬 처리를 위해 사용 가능한 코어에 작업 부하를 자동으로 분배합니다. |
| [MicrosoftML(R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML은 텍스트 분석, 이미지 분석 및 정서 분석을 위한 사용자 지정 모델을 만드는 기계 학습 알고리즘을 추가합니다. |

미리 설치된 패키지 외에 [추가 패키지를 설치](sql-database-connect-query-r.md#add-package)할 수 있습니다.

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>미리 보기 등록

공개 미리 보기에 등록하려면 다음 단계를 따르세요.

1. Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

2. [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com)에서 Microsoft에 이메일을 보내서 공개 미리 보기에 등록할 수 있습니다. SQL Database의 Machine Learning Services(R 포함) 공개 미리 보기는 기본적으로 사용되지 않습니다.

프로그램에 등록되면 Microsoft에서 사용자가 공개 미리 보기에 온보딩되고 기존 또는 새 데이터베이스에 대해 R을 사용하도록 설정됩니다.

공개 미리 보기 기간에는 Machine Learning Services(R 포함)를 프로덕션 워크로드에 사용하지 마십시오.

## <a name="next-steps"></a>다음 단계

- [SQL Server Machine Learning Services와의 주요 차이](sql-database-machine-learning-services-differences.md)를 참조하세요.
- Azure SQL Database에서 Machine Learning Services(R 포함)를 사용하는 방법을 알아보려면 [빠른 시작 가이드](sql-database-connect-query-r.md)를 참조하세요.
- 자세한 내용은 [SQL Server R 언어 자습서](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)를 참조하세요.
