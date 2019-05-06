---
title: 알고리즘 및 모듈 참조
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 시각적 인터페이스에서 사용할 수 있는 모듈에 알아봅니다
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 6602eb4bacdc3b6382c1b6873a465cdfc0632693
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029342"
---
# <a name="algorithm--module-reference-overview"></a>알고리즘 및 모듈 참조 개요

이 참조 콘텐츠 기계 학습 알고리즘 및 Azure Machine Learning 서비스의 시각적 인터페이스 (미리 보기)에서 사용할 수 있는 모듈의 각 기술 배경 정보를 제공 합니다. 

각 모듈에는 독립적으로 실행 하 고 기계 학습 작업에 필요한 입력을 지정을 수행할 수 있는 코드의 집합을 나타냅니다. 모듈을 특정 알고리즘을 포함 하거나 기계 학습, 누락 값 대체를 통계 분석 등의 중요 한 작업을 수행 합니다. 

> [!TIP]
> 시각적 인터페이스의 모든 실험에서 특정 모듈에 대 한 정보를 가져올 수 있습니다. 모듈을 선택한 다음 선택 합니다 **도움이** 링크를 **빠른 도움말** 창입니다.

모듈 기능으로 구성 되어 있습니다.

**데이터 형식 변환**

  + [CSV로 변환 ](convert-to-csv.md)

**입력 및 출력 데이터 모듈** 실험에 클라우드 소스에서 데이터를 이동 하는 작업을 수행 합니다. 실험을 실행 하는 동안 Azure Storage, SQL 데이터베이스 또는 Hive에 프로그램 결과 또는 중간 데이터를 쓸 수도 있고 클라우드 저장소를 사용 하 여 실험 간에 데이터를 교환할 수 있습니다.  

  + [데이터 가져오기](import-data.md)

  + [데이터 내보내기](export-data.md)

  + [데이터를 수동으로 입력](enter-data-manually.md)


**데이터 변환 모듈** 기계 학습, 정규화 등 데이터, 기능 선택 및 차원 감소 범주화에 고유한 데이터에 대 한 작업을 지원 합니다.

  + [데이터 집합의 열 선택](select-columns-in-dataset.md)

  + [메타 데이터 편집](edit-metadata.md)

  + [누락 데이터 정리](clean-missing-data.md)

  + [열 추가](add-columns.md)

  + [행 추가](add-rows.md)

  + [중복 행 제거](remove-duplicate-rows.md)

  + [데이터 분할](split-data.md)

  + [데이터 정규화](normalize-data.md)

  + [분할 및 샘플링](partition-and-sample.md)


**기계 학습 알고리즘** 예: 클러스터링, 지원 벡터 컴퓨터 또는 신경망을 적절 한 매개 변수를 사용 하 여 기계 학습 작업을 사용자 지정할 수 있는 개별 모듈 내에서 사용할 수 있습니다.  
  + [모델 점수 매기기](score-model.md)

  + [클러스터에 데이터 할당 ](assign-data-to-clusters.md)

  + [모델 학습](train-model.md)

  + [클러스터링 모델 학습](train-clustering-model.md)

  + [모델 평가](evaluate-model.md)

  + [변환 적용](apply-transformation.md)

  + [선형 회귀](linear-regression.md)

  + [신경망 회귀](neural-network-regression.md)

  + [의사 결정 포리스트 회귀](decision-forest-regression.md)

  + [승격 된 의사 결정 트리 회귀](boosted-decision-tree-regression.md)

  + [2 클래스 승격 된 의사 결정 트리](two-class-boosted-decision-tree.md)

  + [2 클래스 로지스틱 회귀](two-class-logistic-regression.md)

  + [다중 클래스 로지스틱 회귀](multiclass-logistic-regression.md)

  + [다중 클래스 신경망](multiclass-neural-network.md)

  + [다중 클래스 의사 결정 포리스트](multiclass-decision-forest.md)

  + [2 클래스 평균된 퍼셉트론](two-class-averaged-perceptron.md)

  + [2 클래스 의사 결정 포리스트](two-class-decision-forest.md)

  + [2 클래스 신경망](two-class-neural-network.md)

  + [2 클래스 지원 벡터 컴퓨터](two-class-support-vector-machine.md)
  
  + [K-means 클러스터링](k-means-clustering.md)


**Python 모듈** 쉽게 사용자 지정 함수를 실행할 수 있습니다. 코드를 작성 하는 실험 서비스를 사용 하 여 Python을 통합 하는 모듈에 포함 합니다.
  + [Python 스크립트 실행](execute-python-script.md)

  + [Python 모델 만들기](create-python-model.md)


