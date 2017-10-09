---
title: "Azure Machine Learning Data Preparation을 사용한 데이터 흐름 변환 예제 | Microsoft Docs"
description: "이 문서는 Azure ML 데이터 준비로 가능한 사용자 변환 데이터 흐름 변환의 예제 집합을 제공합니다."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f43f65ca89349fc790684e9bd7acd2f19e15abe5
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="sample-of-custom-data-flow-transforms-python"></a>사용자 지정 데이터 흐름 변환 예제(Python) 
메뉴에서 이 변환의 이름은 'Transform Dataflow (스크립트)'입니다. 이 부록을 읽기 전에 [Python 확장성 개요](data-prep-python-extensibility-overview.md)를 읽습니다.

## <a name="transform-frame"></a>프레임 변환
### <a name="create-a-new-column-dynamically"></a>동적으로 새 열 만들기 
동적으로 열(city2)을 만들고 기존 city 열에 맞춰 여러 다른 버전의 샌프란시스코를 조정합니다.
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>새 집계 추가
Risk_category 열로 그룹화된 점수 열에 대해 계산된 첫 번째 및 마지막 집계를 사용해 새 프레임을 만듭니다.
```python
    df = df.groupby(['risk_category'])['Score'].agg(['first','last'])
```
### <a name="winsorize-a-column"></a>열 윈저화 
열의 이상값을 줄이기 위해 수식을 충족하는 데이터를 다시 작성합니다.
```python
    import scipy.stats as stats
    df['Last Order'] = stats.mstats.winsorize(df['Last Order'].values, limits=0.4)
```

## <a name="transform-data-flow"></a>데이터 흐름 변환
### <a name="fill-down"></a>자동 채우기 
자동 채우기를 하려면 두 변환이 필요합니다.
다음과 같은 데이터를 가정합니다.


|state         |City       |
|--------------|-----------|
|워싱턴    |Redmond    |
|              |Bellevue   |
|              |Issaquah   |
|              |시애틀    |
|캘리포니아    |로스앤젤레스|
|              |샌디에이고  |
|              |산호세   |
|텍사스         |댈러스     |
|              |샌안토니오|
|              |휴스턴    |

우선 다음 코드를 포함하는 'Add Column (스크립트)' 변환을 만듭니다.
```python
    row['State'] if len(row['State']) > 0 else None
```
이제 다음 코드를 포함하는 Transform Data Flow (스크립트) 변환을 만듭니다
```python
    df = df.fillna( method='pad')
```

그리고 다음과 같은 데이터를 가정합니다.

|state         |newState         |City       |
|--------------|--------------|-----------|
|워싱턴    |워싱턴    |Redmond    |
|              |워싱턴    |Bellevue   |
|              |워싱턴    |Issaquah   |
|              |워싱턴    |시애틀    |
|캘리포니아    |캘리포니아    |로스앤젤레스|
|              |캘리포니아    |샌디에이고  |
|              |캘리포니아    |산호세   |
|텍사스         |텍사스         |댈러스     |
|              |텍사스         |샌안토니오|
|              |텍사스         |휴스턴    |


