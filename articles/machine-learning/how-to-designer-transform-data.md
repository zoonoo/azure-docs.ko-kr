---
title: 데이터 변환
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너에서 데이터를 변환 하 여 고유한 데이터 집합을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 05/04/2020
ms.openlocfilehash: 5296ac54cab403ef78b3e8bd32fe5ebe6ea43119
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82842878"
---
# <a name="transform-data-in-azure-machine-learning-designer-preview"></a>Azure Machine Learning 디자이너에서 데이터 변환 (미리 보기)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

이 문서에서는 기계 학습을 위해 사용자 고유의 데이터를 준비할 수 있도록 Azure Machine Learning 디자이너에서 데이터 집합을 변환 하 고 저장 하는 방법에 대해 알아봅니다.

샘플 [성인 인구 조사 소득 이진 분류](sample-designer-datasets.md) 데이터 집합을 사용 하 여 두 데이터 집합을 준비 합니다. 한 데이터 집합은 미국 에서만 성인 인구 조사 정보를 포함 하 고 다른 데이터 집합은 미국의 인구 조사 정보를 포함 하는 데이터 집합입니다.

이 문서에서는 다음 방법을 설명합니다.

1. 데이터 집합을 변환 하 여 학습을 준비 합니다.
1. 결과 데이터 집합을 데이터 저장소로 내보냅니다.
1. 결과를 봅니다.

이 방법은 [다시 학습 designer 모델](how-to-retrain-designer.md) 문서에 대 한 필수 구성 요소입니다. 이 문서에서는 변환 된 데이터 집합을 사용 하 여 파이프라인 매개 변수를 사용 하 여 여러 모델을 학습 하는 방법을 배웁니다.

## <a name="transform-a-dataset"></a>데이터 집합 변환

이 섹션에서는 샘플 데이터 집합을 가져오고 데이터를 미국 및 미국 이외의 데이터 집합으로 분할 하는 방법에 대해 알아봅니다. 사용자 고유의 데이터를 디자이너로 가져오는 방법에 대 한 자세한 내용은 [데이터를 가져오는 방법](how-to-designer-import-data.md)을 참조 하세요.

### <a name="import-data"></a>데이터 가져오기

다음 단계를 사용 하 여 샘플 데이터 집합을 가져옵니다.

1. <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>에 로그인하고, 사용하려는 작업 영역을 선택합니다.

1. 디자이너로 이동 합니다. **사용 하기 쉬운 fakes.prebuild 라고 하겠습니다 모듈** 을 선택 하 여 새 파이프라인을 만듭니다.

1. 파이프라인을 실행할 기본 계산 대상을 선택 합니다.

1. 파이프라인 캔버스의 왼쪽에는 데이터 세트와 모듈로 구성된 팔레트가 있습니다. **데이터 세트**를 선택합니다. 그런 다음 **샘플** 섹션을 봅니다.

1. **성인 인구 조사 소득 이진 분류** 데이터 집합을 캔버스에 끌어다 놓습니다.

1. **성인 인구 조사 소득** 데이터 집합 모듈을 선택 합니다.

1. 캔버스 오른쪽에 표시 되는 세부 정보 창에서 **출력**을 선택 합니다.

1. 시각화 아이콘을 선택 합니다. ![시각화 아이콘](media/how-to-designer-transform-data/visualize-icon.png).

1. 데이터 미리 보기 창을 사용 하 여 데이터 집합을 탐색할 수 있습니다. "기본 국가" 열 값에 대 한 특별 한 메모를 작성 합니다.

### <a name="split-the-data"></a>데이터 분할

이 섹션에서는 [데이터 분할 모듈](algorithm-module-reference/split-data.md) 을 사용 하 여 "기본 국가" 열에 "미국의"가 포함 된 행을 식별 하 고 분할 합니다. 

1. 캔버스 왼쪽의 모듈 팔레트에서 **데이터 변환** 섹션을 확장 하 고 **분할 데이터** 모듈을 찾습니다.

1. **데이터 분할** 모듈을 캔버스로 끌어 놓은 다음 모듈을 데이터 집합 모듈 아래에 놓습니다.

1. 데이터 집합 모듈을 **분할 데이터** 모듈에 연결 합니다.

1. **데이터** 분할 모듈을 선택합니다.

1. 캔버스 오른쪽의 모듈 세부 정보 창에서 **분할 모드** 를 **정규식**으로 설정 합니다.

1. **정규식**을 입력 `\"native-country" United-States`합니다.

    **정규식** 모드는 단일 열에 값을 테스트 합니다. 데이터 분할 모듈에 대 한 자세한 내용은 관련 [알고리즘 모듈 참조 페이지](algorithm-module-reference/split-data.md)를 참조 하세요.

파이프라인은 다음과 같습니다.

![파이프라인 및 분할 데이터 모듈을 구성 하는 방법을 보여 주는 스크린샷](media/how-to-designer-transform-data/split-data.png).


## <a name="save-the-datasets"></a>데이터 집합 저장

이제 파이프라인이 데이터를 분할 하도록 설정 되었으므로 데이터 집합을 보관할 위치를 지정 해야 합니다. 이 예에서는 **데이터 내보내기** 모듈을 사용 하 여 데이터 집합을 데이터 저장소에 저장 합니다. Datastores에 대 한 자세한 내용은 [Azure storage 서비스에 연결](how-to-access-data.md) 을 참조 하세요.

1. 캔버스 왼쪽에 있는 모듈 팔레트에서 **데이터 입력 및 출력** 섹션을 확장 하 고 **데이터 내보내기** 모듈을 찾습니다.

1. **데이터 분할** 모듈 아래에 두 개의 **내보내기 데이터** 모듈을 끌어서 놓습니다.

1. **데이터 분할** 모듈의 각 출력 포트를 다른 **데이터 내보내기** 모듈에 연결 합니다.

    파이프라인은 다음과 같이 표시 됩니다.

    ![데이터 내보내기 모듈을 연결 하는 방법을 보여 주는 스크린샷](media/how-to-designer-transform-data/export-data-pipeline.png).

1. 데이터 **분할** 모듈의 가장 *왼쪽*포트에 연결 된 **데이터 내보내기** 모듈을 선택 합니다.

    **데이터 분할** 모듈에 대 한 출력 포트의 순서입니다. 첫 번째 출력 포트는 정규식이 true 인 행을 포함 합니다. 이 경우 첫 번째 포트는 미국 기반 수입에 대 한 행을 포함 하 고, 두 번째 포트는 미국 외 수입에 대 한 행을 포함 합니다.

1. 캔버스 오른쪽에 있는 모듈 세부 정보 창에서 다음 옵션을 설정 합니다.
    
    **데이터 저장소 형식**: Azure Blob Storage

    **데이터 저장소**: 기존 데이터 저장소를 선택 하거나 "새 데이터 저장소"를 선택 하 여 지금 만듭니다.

    **경로**:`/data/us-income`

    **파일 형식**: csv

    > [!NOTE]
    > 이 문서에서는 현재 Azure Machine Learning 작업 영역에 등록 된 데이터 저장소에 대 한 액세스 권한이 있다고 가정 합니다. 데이터 저장소를 설정 하는 방법에 대 한 지침은 [Azure storage 서비스에 연결](how-to-access-data.md#azure-machine-learning-studio)을 참조 하세요.

    데이터 저장소가 없는 경우 지금 만들 수 있습니다. 예를 들어이 문서는 작업 영역과 연결 된 기본 blob 저장소 계정에 데이터 집합을 저장 합니다. 이라는 `azureml` `data`새 폴더의 컨테이너에 데이터 집합을 저장 합니다.

1.  **데이터 분할** 모듈의 맨 *오른쪽*포트에 연결 된 **데이터 내보내기** 모듈을 선택 합니다.

1. 캔버스 오른쪽에 있는 모듈 세부 정보 창에서 다음 옵션을 설정 합니다.
    
    **데이터 저장소 형식**: Azure Blob Storage

    **데이터 저장소**: 위와 동일한 데이터 저장소를 선택 합니다.

    **경로**:`/data/non-us-income`

    **파일 형식**: csv

1. **분할 데이터** 의 왼쪽 포트에 연결 된 **데이터 내보내기** 모듈이 **경로** `/data/us-income`를 포함 하는지 확인 합니다.

1. 오른쪽 포트에 연결 된 **데이터 내보내기** 모듈에 **경로가** `/data/non-us-income`있는지 확인 합니다.

    파이프라인 및 설정은 다음과 같습니다.
    
    ![데이터 내보내기 모듈을 구성 하는 방법을 보여 주는 스크린샷](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>실행 제출

이제 파이프라인을 설정 하 여 데이터를 분할 하 고 내보내고 파이프라인 실행을 제출 합니다.

1. 캔버스 위쪽에서 **제출**을 선택합니다.

1. **파이프라인 실행 설정** 대화 상자에서 **새로 만들기** 를 선택 하 여 실험을 만듭니다.

    실험은 관련 된 파이프라인 실행을 논리적으로 그룹화 합니다. 나중에이 파이프라인을 실행할 경우 로깅 및 추적을 위해 동일한 실험을 사용 해야 합니다.

1. "분할-인구 조사-데이터"와 같은 설명이 포함 된 실험 이름을 제공 합니다.

1. **제출**을 선택합니다.

## <a name="view-results"></a>결과 보기

파이프라인 실행이 완료 되 면 Azure Portal에서 blob 저장소로 이동 하 여 결과를 볼 수 있습니다. 데이터 **분할** 모듈의 중간 결과를 확인 하 여 데이터가 올바르게 분할 되었는지 확인할 수도 있습니다.

1. **데이터** 분할 모듈을 선택합니다.

1. 캔버스 오른쪽에 있는 모듈 세부 정보 창에서 **출력 + 로그**를 선택합니다. 

1. **결과 dataset1**옆의 ![시각화 아이콘](media/how-to-designer-transform-data/visualize-icon.png) 시각화 아이콘을 선택 합니다. 

1. "Native country" 열에 "미국" 값만 포함 되어 있는지 확인 합니다.

1. **결과 dataset2**옆의 ![시각화 아이콘](media/how-to-designer-transform-data/visualize-icon.png) 시각화 아이콘을 선택 합니다. 

1. "기본 국가" 열에 "미국" 값이 포함 되어 있지 않은지 확인 합니다.

## <a name="clean-up-resources"></a>리소스 정리

[다시 학습 designer를 Azure Machine Learning 사용](how-to-retrain-designer.md)하 여 모델을 하는이 방법의 2 부를 계속 진행 하려는 경우이 섹션을 건너뜁니다.

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 문서에서는 데이터 집합을 변환 하 고 등록 된 데이터 저장소에 저장 하는 방법을 배웠습니다.

변환 된 데이터 집합 및 파이프라인 매개 변수를 사용 하 여 기계 학습 모델을 학습 하려면 Azure Machine Learning designer를 사용 하 여 [다시 학습 모델](how-to-retrain-designer.md) 을 사용 하는이 방법 시리즈의 다음 부분으로 계속 진행 합니다.
