---
title: 행을 추가 합니다. 모듈 참조
titleSuffix: Azure Machine Learning service
description: 두 데이터 집합을 연결할 Azure Machine Learning 서비스에서 행 추가 모듈을 사용 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ffd693ea3452ef48dc3e05e7bc4a6d3988a487b0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028757"
---
# <a name="add-rows-module"></a>행 모듈 추가

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 두 개의 데이터 집합을 연결 합니다. 연결에서 두 번째 데이터 집합의 행은 첫 번째 데이터 집합의 끝에 추가 됩니다.  
  
행 연결은 다음과 같은 시나리오에서 유용합니다.  
  
+ 일련의 평가 통계를 생성 하 고 보다 쉬운 보고에 대 한 테이블 하나로 결합 하려는 합니다.  
  
+ 서로 다른 데이터 집합을 사용 하 여 작업할 및 최종 데이터 집합을 만들려면 데이터 집합을 결합 하려는 합니다.  

## <a name="how-to-use-add-rows"></a>행 추가 사용 하는 방법  

두 데이터 집합의 행을 연결 하려면 행에 동일한 스키마를 정확 하 게 있어야 합니다. 즉, 같은 수의 열, 및 열에 있는 데이터의 종류입니다.

1.  끌어서 합니다 **행 추가** 실험에 모듈을 찾을 수 아래 **데이터 변환**의 **조작** 범주입니다.

2. 두 입력된 포트에 데이터 집합을 연결 합니다. 추가 하려는 데이터 집합의 두 번째 (오른쪽) 포트에 연결 되어야 합니다. 
  
3.  실험을 실행합니다. 출력 데이터 집합의 행 수가 두 입력된 데이터 집합의 행의 합계와 같아야 합니다.

    두 입력에 동일한 데이터 집합을 추가 하는 경우는 **행 추가** 모듈을 데이터 집합에서 중복 됩니다. 

## <a name="next-steps"></a>다음 단계

참조를 [사용할 수 있는 모듈 집합](module-reference.md) Azure Machine Learning 서비스입니다. 