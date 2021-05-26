---
title: 관리형 온라인 엔드포인트 VM SKU 목록(미리 보기)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 관리형 온라인 엔드포인트(미리 보기)에 사용할 수 있는 VM SKU를 나열합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: laobri
ms.author: seramasu
author: rsethur
ms.date: 05/10/2021
ms.openlocfilehash: 8cdc688af7a9ce57200b817576fe9746793ff19c
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110466619"
---
# <a name="managed-online-endpoints-sku-list-preview"></a>관리형 온라인 엔드포인트 SKU 목록(미리 보기)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

이 표에서는 Azure Machine Learning 관리형 온라인 엔드포인트(미리 보기)에 지원되는 VM SKU를 보여줍니다.

* 배포에 사용되는 `instance_type` 특성은 "Standard_F4s_v2" 형식으로 지정해야 합니다. 아래 표에는 인스턴스 이름(예: F2s v2)이 나열되어 있습니다. 이러한 이름은 배포를 만들고 업데이트하기 위해 Azure CLI 또는 ARM 템플릿(Azure Resource Manager 템플릿) 요청에 대해 지정된 형식(`Standard_{name}`)에 배치해야 합니다. 

* CPU 및 RAM과 같은 구성 세부 사항에 대한 자세한 내용은 [Azure Machine Learning 가격 책정](https://azure.microsoft.com/pricing/details/machine-learning/)을 참조하세요.

| 크기 | 범용 | 컴퓨팅 최적화 | 메모리 최적화 | GPU |
| --- | --- | --- | --- | --- | 
| V.Small | DS2 v2 | F2s v2 | E2 v3 | NC4as_T4_v3 |
| Small | DS3 v2 | F4s v2 | E4 v3 | NC6s v2 <br/> NC6s v3 <br/> NC8as_T4_v3 |
| 중간 | DS4 v2 | F8s v2 | E8 v3 | NC12s v2 <br/> NC12s v3 <br/> NC16as_T4_v3 |
| 대형 | DS5 v2 | F16s v2 |E16 v3 | NC24s v2 <br/> NC24s v3 <br/> NC64as_T4_v3 |
| X-Large| - | F32s v2 <br/> F48s v2 <br/> F64s v2 <br/> F72s v2 | - | - |


