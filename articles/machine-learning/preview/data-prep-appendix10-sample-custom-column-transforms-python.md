---
title: "Azure Machine Learning 데이터 준비에서 새 열을 파생하는 샘플 Python | Microsoft Docs"
description: "이 문서에서는 Azure ML 데이터 준비에서 새 열을 만드는 Python 코드 예제를 제공합니다."
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
ms.date: 09/12/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 9a6e331e622b007232a62b34c2220d60d1d050ca
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="sample-of-custom-column-transforms-python"></a>사용자 지정 열 변환 샘플(Python) 
메뉴에서 이 변환의 이름은 '열 추가(스크립트)'입니다.

이 부록을 읽기 전에 [Python 확장성 개요](data-prep-python-extensibility-overview.md)를 참조하세요.

## <a name="test-equivalence-and-replace-values"></a>등가 테스트 및 값 바꾸기 
Col1의 값이 4보다 작은 경우 새 열의 값은 1이어야 하고 그렇지 않은 경우에는 2 

```python
    1 if row["Col1"] < 4 else 2
```
## <a name="current-date-and-time"></a>현재 날짜 및 시간 

```python
    datetime.datetime.now()
```
## <a name="typecasting"></a>형식 캐스팅 
```python
    float(row["Col1"]) / float(row["Col2"] - 1)
```
## <a name="evaluate-for-nullness"></a>Null 여부 평가 
Col1에 Null이 있는 경우 새 열을 Bad로 표시하고 그렇지 않은 경우 Good으로 표시 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>새 계산 열 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Epoch 계산 
Unix Epoch 이후의 시간(초)(Col1을 날짜로 가정) 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```






