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
ms.openlocfilehash: 8825f1dc3b66a5c4981ba25a90813aec63975b1f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65145145"
---
# <a name="algorithm--module-reference-overview"></a>알고리즘 및 모듈 참조 개요

이 참조 콘텐츠 기계 학습 알고리즘 및 Azure Machine Learning 서비스의 시각적 인터페이스 (미리 보기)에서 사용할 수 있는 모듈의 각 기술 배경 정보를 제공 합니다.

각 모듈에는 독립적으로 실행 하 고 기계 학습 작업에 필요한 입력을 지정을 수행할 수 있는 코드의 집합을 나타냅니다. 모듈을 특정 알고리즘을 포함 하거나 기계 학습, 누락 값 대체를 통계 분석 등의 중요 한 작업을 수행 합니다.

> [!TIP]
> 시각적 인터페이스의 모든 실험에서 특정 모듈에 대 한 정보를 가져올 수 있습니다. 모듈을 선택한 다음 선택 합니다 **도움이** 링크를 **빠른 도움말** 창입니다.

## <a name="modules"></a>모듈

모듈 기능으로 구성 되어 있습니다.

| 기능 | 설명 | 모듈 |
| --- |--- | ---- |
| 데이터 형식 변환 | 데이터에 기계 학습에 사용 되는 다양 한 파일 형식 간 변환 | [CSV로 변환](convert-to-csv.md) |
| 데이터 입력 및 출력 | 실험에 클라우드 소스에서 데이터를 이동 합니다. 프로그램 결과 또는 중간 데이터를 Azure Storage, SQL 데이터베이스 또는 Hive, 실험을 실행 하는 동안 쓰거나 실험 간에 데이터를 교환 하는 데 클라우드 저장소를 사용 합니다.  | [데이터 가져오기](import-data.md)<br/>[데이터 내보내기](export-data.md)<br/>[데이터를 수동으로 입력](enter-data-manually.md) |
| 데이터 변환 | 기계 학습, 정규화 등 데이터, 기능 선택 및 차원 감소 범주화에 고유한 데이터에 대해 작업 합니다.| [데이터 집합의 열 선택](select-columns-in-dataset.md) <br/> [메타 데이터 편집](edit-metadata.md) <br/> [누락 데이터 정리](clean-missing-data.md) <br/> [열 추가](add-columns.md) <br/> [행 추가](add-rows.md) <br/> [중복 행 제거](remove-duplicate-rows.md) <br/> [데이터 분할](split-data.md) <br/> [데이터 정규화](normalize-data.md) <br/> [분할 및 샘플링](partition-and-sample.md) |
| Python 모듈 | 코드를 작성 하 고 실험과 함께 Python을 통합 하는 모듈에 포함 해야 합니다. | [Python 스크립트 실행](execute-python-script.md)   <br/> [Python 모델 만들기](create-python-model.md)
|  | **기계 학습 알고리즘**: | |
| 분류 | 클래스를 예측 합니다.  이진 (2 클래스)에서 선택 하거나 다중 클래스 알고리즘입니다.| [다중 클래스 의사 결정 포리스트](multiclass-decision-forest.md) <br/> [다중 클래스 로지스틱 회귀](multiclass-logistic-regression.md)  <br/> [다중 클래스 신경망](multiclass-neural-network.md)  <br/>  [2 클래스 로지스틱 회귀](two-class-logistic-regression.md)  <br/>[2 클래스 평균된 퍼셉트론](two-class-averaged-perceptron.md) <br/> [2 클래스&nbsp;승격&nbsp;의사 결정&nbsp;트리](two-class-boosted-decision-tree.md)  <br/> [2 클래스 의사 결정 포리스트](two-class-decision-forest.md)  <br/> [2 클래스 신경망](two-class-neural-network.md)  <br/> [두&#8209;클래스&nbsp;지원&nbsp;벡터&nbsp;컴퓨터](two-class-support-vector-machine.md) 
| Clustering | 데이터를 그룹화 합니다.| [K-means 클러스터링](k-means-clustering.md)
| 회귀 | 값을 예측 합니다. | [선형 회귀](linear-regression.md)  <br/> [신경망 회귀](neural-network-regression.md)  <br/> [의사 결정 포리스트 회귀](decision-forest-regression.md)  <br/> [향상 된&nbsp;의사 결정&nbsp;트리&nbsp;회귀](boosted-decision-tree-regression.md)
|  | **빌드 및 모델 평가**: | |
| 학습   | 알고리즘을 통해 데이터를 실행 합니다. | [모델 학습](train-model.md)  <br/> [클러스터링 모델 학습](train-clustering-model.md)    |
| 모델 평가 | 학습된 된 모델의 정확도 측정 합니다. |  [모델 평가](evaluate-model.md)
| 점수 | 방금 학습 된 모델에서 예측을 가져옵니다. | [변환 적용](apply-transformation.md)<br/>[할당할&nbsp;데이터&nbsp;에&nbsp;클러스터](assign-data-to-clusters.md) <br/>[모델 점수 매기기](score-model.md)

## <a name="error-messages"></a>오류 메시지

에 대 한 자세한 합니다 [오류 메시지 및 예외 코드](machine-learning-module-error-codes.md) 모듈을 사용 하 여 Azure Machine Learning 서비스의 시각적 인터페이스에서 발생할 수 있습니다.
