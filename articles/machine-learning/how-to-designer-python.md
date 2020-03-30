---
title: Python
titleSuffix: Azure Machine Learning
description: Azure 기계 학습 디자이너에서 Python을 사용하여 데이터를 변환하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 02/28/2020
ms.openlocfilehash: a2bd9845cd29c7d139e2042f39b4697847639207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455794"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>Azure 기계 학습 디자이너에서 파이썬 코드 실행

이 문서에서는 [Python 스크립트 실행](algorithm-module-reference/execute-python-script.md) 모듈을 사용하여 Azure 기계 학습 디자이너에 사용자 지정 논리를 추가하는 방법을 배웁니다. 다음 방법에서는 팬더 라이브러리를 사용하여 간단한 기능 엔지니어링을 수행합니다.

내장 된 코드 편집기를 사용하여 간단한 파이썬 논리를 빠르게 추가 할 수 있습니다. 더 복잡한 코드를 추가하거나 파이썬 라이브러리를 추가하려면 zip 파일 메서드를 사용해야합니다.

기본 실행 환경은 파이썬의 아나콘다 분포를 사용합니다. 사전 설치된 패키지의 전체 목록은 [파이썬 스크립트 모듈 참조](algorithm-module-reference/execute-python-script.md) 페이지 실행을 참조하십시오.

![파이썬 입력 맵 실행](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>디자이너에 작성된 파이썬 실행

### <a name="add-the-execute-python-script-module"></a>파이썬 스크립트 실행 모듈 추가

1. 디자이너 팔레트에서 **파이썬 스크립트 실행** 모듈을 찾습니다. **파이썬 언어** 섹션에서 찾을 수 있습니다.

1. 모듈을 파이프라인 캔버스에 끌어 놓습니다.

### <a name="connect-input-datasets"></a>입력 데이터 집합 연결

이 문서에서는 샘플 데이터 집합, **자동차 가격 데이터(Raw)를**사용합니다. 

1. 데이터 집합을 파이프라인 캔버스로 끌어놓습니다.

1. 데이터 집합의 출력 포트를 **Python 스크립트 실행** 모듈의 왼쪽 위 입력 포트에 연결합니다. 디자이너는 입력을 진입점 스크립트에 매개 변수로 노출합니다.
    
    올바른 입력 포트는 압축된 파이썬 라이브러리에 예약되어 있습니다.

    ![데이터 집합 연결](media/how-to-designer-python/connect-dataset.png)
        

1. 사용하는 입력 포트를 기록해 둡을 기록합니다. 디자이너는 왼쪽 입력 포트를 변수에 `dataset1` 할당하고 중간 `dataset2`입력 포트를 에 할당합니다. 

**파이썬 스크립트 실행** 모듈에서 직접 데이터를 생성하거나 가져올 수 있으므로 입력 모듈은 선택 사항입니다.

### <a name="write-your-python-code"></a>파이썬 코드 작성

디자이너는 사용자 고유의 Python 코드를 편집하고 입력할 수 있는 초기 진입점 스크립트를 제공합니다. 

이 예제에서는 팬더를 사용하여 자동차 데이터 집합인 **가격** 및 **마력에**있는 두 개의 열을 결합하여 **마력당 달러라는**새 열을 만듭니다. 이 칼럼은 각 마력에 대해 지불하는 금액을 나타내며, 이는 자동차가 돈을 위해 좋은 거래인지 결정하는 데 유용한 기능이 될 수 있습니다. 

1. 파이썬 스크립트 실행 모듈을 **선택합니다.**

1. 캔버스 오른쪽에 나타나는 창에서 **Python 스크립트** 텍스트 상자를 선택합니다.

1. 다음 코드를 복사하여 텍스트 상자에 붙여넣습니다.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    파이프라인은 다음 이미지를 보아야 합니다.
    
    ![파이썬 파이프라인 실행](media/how-to-designer-python/execute-python-pipeline.png)

    진입점 스크립트에는 함수가 `azureml_main`포함되어야 합니다. **파이썬 스크립트** 실행 모듈의 두 입력 포트에 매핑되는 두 가지 함수 매개 변수가 있습니다.

    반환 값은 팬더 데이터 프레임이어야합니다. 최대 2개의 데이터 프레임을 모듈 출력으로 반환할 수 있습니다.
    
1. 파이프라인을 제출합니다.

이제 새로운 **기능인 Dollars/HP가**포함된 데이터 세트가 있어 자동차 추천자를 교육하는 데 유용할 수 있습니다. 이는 기능 추출 및 치수 감소의 예입니다. 

## <a name="next-steps"></a>다음 단계

Azure 기계 학습 디자이너에서 [사용자 고유의 데이터를 가져오는](how-to-designer-import-data.md) 방법을 알아봅니다.