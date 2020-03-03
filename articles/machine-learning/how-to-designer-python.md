---
title: Python
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너에서 Python을 사용 하 여 데이터를 변환 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 02/28/2020
ms.openlocfilehash: b0b0bb5eefde9e744b1f30109d60ded91d3b44e8
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228685"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>Azure Machine Learning 디자이너에서 Python 코드 실행

이 문서에서는 [Python 스크립트 실행](algorithm-module-reference/execute-python-script.md) 모듈을 사용 하 여 Azure Machine Learning 디자이너에 사용자 지정 논리를 추가 하는 방법에 대해 알아봅니다. 다음 방법에서는 Pandas 라이브러리를 사용 하 여 간단한 기능 엔지니어링을 수행 합니다.

빌드된 코드 편집기를 사용 하 여 간단한 Python 논리를 빠르게 추가할 수 있습니다. 더 복잡 한 코드를 추가 하거나 추가 Python 라이브러리를 업로드 하려면 zip 파일 메서드를 사용 해야 합니다.

기본 실행 환경에서는 Python의 Anacondas 배포를 사용 합니다. 사전 설치 된 패키지의 전체 목록은 [Python 스크립트 모듈 참조 실행](algorithm-module-reference/execute-python-script.md) 페이지를 참조 하세요.

![Python 입력 맵 실행](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>디자이너에서 작성 한 Python 실행

### <a name="add-the-execute-python-script-module"></a>Python 스크립트 실행 모듈을 추가 합니다.

1. 디자이너 색상표에서 **Python 스크립트 실행** 모듈을 찾습니다. **Python 언어** 섹션에서 찾을 수 있습니다.

1. 모듈을 파이프라인 캔버스로 끌어다 놓습니다.

### <a name="connect-input-datasets"></a>입력 데이터 집합 연결

이 아티클에서는 샘플 데이터 집합 **자동차 가격 데이터 (원시)** 를 사용 합니다. 

1. 데이터 집합을 파이프라인 캔버스로 끌어다 놓습니다.

1. 데이터 집합의 출력 포트를 **Python 스크립트 실행** 모듈의 왼쪽 위에 있는 입력 포트에 연결 합니다. 디자이너는 입력을 진입점 스크립트에 매개 변수로 노출 합니다.
    
    오른쪽 입력 포트는 압축 된 python 라이브러리에 대해 예약 되어 있습니다.

    ![데이터 집합 연결](media/how-to-designer-python/connect-dataset.png)
        

1. 사용할 입력 포트를 적어 둡니다. 디자이너는 `dataset1` 변수에 왼쪽 입력 포트를 할당 하 고 가운데 입력 포트를 `dataset2`에 할당 합니다. 

**Python 스크립트 실행** 모듈에서 직접 데이터를 생성 하거나 가져올 수 있으므로 입력 모듈은 선택 사항입니다.

### <a name="write-your-python-code"></a>Python 코드 작성

디자이너는 사용자 고유의 Python 코드를 편집 하 고 입력할 수 있는 초기 진입점 스크립트를 제공 합니다. 

이 예에서는 Pandas를 사용 하 여 자동차 데이터 집합에 있는 두 개의 열, **가격** 및 **마**트를 결합 하 여 새 열, **마 력 당 달러**를 만듭니다. 이 열은 각 마 력에 대 한 비용을 나타냅니다 .이 열은 자동차가 우수한 금액 인지 여부를 결정 하는 데 유용한 기능입니다. 

1. **Python 스크립트 실행** 모듈을 선택 합니다.

1. 캔버스 오른쪽에 표시 되는 창에서 **Python 스크립트** 텍스트 상자를 선택 합니다.

1. 다음 코드를 복사 하 여 입력란에 붙여 넣습니다.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    파이프라인이 다음 이미지를 살펴봐야 합니다.
    
    ![Python 파이프라인 실행](media/how-to-designer-python/execute-python-pipeline.png)

    진입점 스크립트는 `azureml_main`함수를 포함 해야 합니다. **Python 스크립트 실행** 모듈에 대 한 두 개의 입력 포트에 매핑되는 두 개의 함수 매개 변수가 있습니다.

    반환 값은 Pandas 데이터 프레임 여야 합니다. 모듈 출력으로 최대 2 개의 데이터 프레임를 반환할 수 있습니다.
    
1. 파이프라인을 실행합니다.

이제 자동차 추천를 학습 하는 데 유용할 수 있는 새로운 기능 **달러/HP**가 포함 된 데이터 집합이 있습니다. 이는 기능 추출 및 차원 감소의 한 예입니다. 

## <a name="next-steps"></a>다음 단계

Azure Machine Learning designer에서 사용자 [고유의 데이터를 가져오는](how-to-designer-import-data.md) 방법에 대해 알아봅니다.