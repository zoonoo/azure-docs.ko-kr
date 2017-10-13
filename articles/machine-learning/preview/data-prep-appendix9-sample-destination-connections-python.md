---
title: "Azure Machine Learning Data Preparation로 가능한 예제 대상/출력 | Microsoft Docs"
description: "이 문서는 Azure ML 데이터 준비를 사용하여 사용자 지정 데이터 대상/출력 예제 집합을 제공합니다."
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
ms.openlocfilehash: e85ab5d52c2265536f72fa7301c0a25fa3a112cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="sample-of-destination-connections-python"></a>대상 연결 샘플(Python) 
이 부록을 읽기 전에 [Python 확장성 개요](data-prep-python-extensibility-overview.md)를 참조하세요.


### <a name="write-to-excel"></a>Excel에 쓰기 


Excel에 쓰려면 추가 라이브러리가 필요하며, 새 라이브러리 추가에 관해서는 확장성 개요에 문서화되어 있습니다. `openpyxl`은 추가되어야 할 라이브러리입니다.

쓰기 전에 일부 다른 변경이 필요할 수도 있습니다. Data Prep에서 사용된 일부 데이터 형식은 일부 대상 형식에서 지원되지 않습니다. 예를 들어 "Error" 개체가 존재할 경우 이들 개체는 Excel로 정확하게 직렬화하지 않습니다. 따라서 Excel에 쓰기를 시도하기 전에 모든 열에서 오류를 제거하는 "오류 값 대체 " 변환이 필요합니다.

위의 작업이 모두 완료되었다고 가정한다면 다음 줄은 데이터 표를 Excel 문서의 단일 시트에 씁니다. Write DataFlow(스크립트) 변환을 추가하고 식 섹션에 다음 코드를 입력합니다.


#### <a name="on-windows"></a>Windows  
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

#### <a name="on-macosos-x"></a>MacOS/OS X에서 ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
