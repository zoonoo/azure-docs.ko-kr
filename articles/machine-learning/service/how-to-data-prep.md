---
title: Python용 Machine Learning Data Prep SDK를 사용하여 데이터 준비 - Azure
description: Python용 Azure Machine Learning Data Prep SDK를 사용하여 다양한 형식의 데이터를 로드하고, 데이터를 더 유용하게 변환하고, 해당 데이터를 모델이 액세스할 위치에 쓰는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 83373f5d6e4a2227bcafdbc4b25b686b0b8d651d
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867190"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 모델링을 위한 데이터 준비
 
데이터 준비는 기계 학습 워크플로의 중요한 부분입니다. 모델은 더 쉽게 이용할 수 있는 형식의 정리된 데이터에 액세스할 수 있는 경우 더 정확하고 효율적입니다. 

[Azure Machine Learning 데이터 준비 SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py)를 사용하여 Python에서 데이터를 준비할 수 있습니다. 

## <a name="data-preparation-pipeline"></a>데이터 준비 파이프라인

주요 데이터 준비 단계는 다음과 같습니다.

1. 다양한 형식의 [데이터 로드](how-to-load-data.md)
2. 더 유용한 구조로 데이터 [변환](how-to-transform-data.md)
3. 모델에 액세스할 수 있는 위치에 해당 데이터 [쓰기](how-to-write-data.md)

![데이터 준비 프로세스](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>다음 단계
Azure Machine Learning Data Prep SDK를 사용하여 데이터 준비의 [예제 노트북](https://github.com/Microsoft/MSDataPrepDocs/blob/master/Scenarios/GettingStarted/getting-started.ipynb)을 검토합니다.
