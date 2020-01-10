---
title: Machine learning 파이프라인 디버그 및 문제 해결
titleSuffix: Azure Machine Learning
description: Python 용 Azure Machine Learning SDK에서 기계 학습 파이프라인을 디버그 하 고 문제를 해결 합니다. 파이프라인 개발에 대 한 일반적인 문제 및 원격 실행 전후에 스크립트를 디버그 하는 데 도움이 되는 팁에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 12/12/2019
ms.openlocfilehash: e0e4ea4689baeb8e85a765a33c3f8124d6bc4362
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75764289"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Machine learning 파이프라인 디버그 및 문제 해결
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 및 [Azure Machine Learning designer (미리 보기)](https://docs.microsoft.com/azure/machine-learning/concept-designer)에서 [machine learning 파이프라인](concept-ml-pipelines.md) 을 디버그 하 고 문제를 해결 하는 방법에 대해 알아봅니다.


## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK의 디버그 및 문제 해결
다음 섹션에서는 파이프라인을 빌드할 때 발생 하는 일반적인 문제에 대 한 개요와 파이프라인에서 실행 되는 코드를 디버깅 하기 위한 다른 전략을 제공 합니다. 예상 대로 실행 되는 파이프라인을 가져오는 데 문제가 있는 경우 다음 팁을 사용 하세요. 
### <a name="testing-scripts-locally"></a>로컬에서 스크립트 테스트

파이프라인에서 가장 일반적으로 발생 하는 오류 중 하나는 연결 된 스크립트 (데이터 정리 스크립트, 점수 매기기 스크립트 등)가 의도 한 대로 실행 되 고 있지 않거나, Azure 컴퓨터의 작업 영역에서 디버깅 하기 어려운 원격 계산 컨텍스트의 런타임 오류를 포함 하는 것입니다. 학습 스튜디오. 

파이프라인 자체를 로컬로 실행할 수는 없지만 로컬 컴퓨터에서 격리 된 스크립트를 실행 하면 계산 및 환경 빌드 프로세스를 기다릴 필요가 없기 때문에 더 빠르게 디버그할 수 있습니다. 이 작업을 수행 하려면 몇 가지 개발 작업이 필요 합니다.

* 데이터가 클라우드 데이터 저장소에 있는 경우 데이터를 다운로드 하 여 스크립트에 사용할 수 있도록 해야 합니다. 데이터의 작은 샘플을 사용 하는 것은 런타임에 빠르게 중단 하 고 스크립트 동작에 대 한 피드백을 빠르게 얻는 좋은 방법입니다.
* 중간 파이프라인 단계를 시뮬레이션 하려는 경우 이전 단계에서 특정 스크립트가 예상 하는 개체 유형을 수동으로 빌드해야 할 수 있습니다.
* 또한 사용자 고유의 환경을 정의 하 고 원격 계산 환경에 정의 된 종속성을 복제 해야 합니다.

로컬 환경에서 스크립트를 설치 하 고 나면 다음과 같은 디버깅 작업을 수행 하는 것이 훨씬 쉽습니다.

* 사용자 지정 디버그 구성 연결
* 실행 일시 중지 및 개체 상태 검사
* 런타임이 될 때까지 노출 되지 않는 형식 또는 논리 오류를 catch 합니다.

> [!TIP] 
> 스크립트가 예상 대로 실행 되는지 확인 한 후에는 여러 단계로 이루어진 파이프라인에서 실행을 시도 하기 전에 단일 단계 파이프라인에서 스크립트를 실행 하는 것이 좋습니다.

### <a name="debugging-scripts-from-remote-context"></a>원격 컨텍스트에서 스크립트 디버깅

스크립트를 로컬로 테스트 하는 것은 파이프라인 빌드를 시작 하기 전에 주요 코드 조각과 복잡 한 논리를 디버깅 하는 좋은 방법 이지만, 특정 시점에는 특히 발생 하는 동작을 진단 하는 데 실제 파이프라인 실행 중에 스크립트를 디버그 해야 할 수 있습니다. 파이프라인 단계 간의 상호 작용 중입니다. JavaScript 코드를 디버그 하는 방법과 비슷하게 원격 실행 중에 개체 상태와 예상 값을 볼 수 있도록 단계 스크립트에서 `print()` 문을 자유롭게 사용 하는 것이 좋습니다.

로그 파일 `70_driver_log.txt` 다음을 포함 합니다. 

* 스크립트를 실행 하는 동안 모든 인쇄 된 문
* 스크립트에 대 한 스택 추적입니다. 

포털에서이 파일 및 기타 로그 파일을 찾으려면 먼저 작업 영역에서 파이프라인 실행을 클릭 합니다.

![파이프라인 실행 목록 페이지](./media/how-to-debug-pipelines/pipelinerun-01.png)

파이프라인 실행 세부 정보 페이지로 이동 합니다.

![파이프라인 실행 세부 정보 페이지](./media/how-to-debug-pipelines/pipelinerun-02.png)

특정 단계에 대 한 모듈을 클릭 합니다. **로그** 탭으로 이동 합니다. 기타 로그에는 환경 이미지 빌드 프로세스 및 단계 준비 스크립트에 대 한 정보가 포함 됩니다.

![파이프라인 실행 세부 정보 페이지 로그 탭](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> 게시 된 *파이프라인* 에 대 한 실행은 작업 영역의 **끝점** 탭에서 찾을 수 있습니다. *게시 되지 않은 파이프라인* 에 대 한 실행은 **실험** 또는 **파이프라인**에서 찾을 수 있습니다.

### <a name="troubleshooting-tips"></a>문제 해결 팁

다음 표에는 잠재적인 솔루션을 포함 하는 파이프라인 개발 중에 일반적인 문제가 포함 되어 있습니다.

| 문제 | 가능한 해결 방법 |
|--|--|
| `PipelineData` 디렉터리에 데이터를 전달할 수 없습니다. | 파이프라인이 단계 출력 데이터를 기대 하는 위치에 해당 하는 디렉터리를 스크립트에 만들었는지 확인 합니다. 대부분의 경우 입력 인수는 출력 디렉터리를 정의 하 고 디렉터리를 명시적으로 만듭니다. `os.makedirs(args.output_dir, exist_ok=True)`를 사용 하 여 출력 디렉터리를 만듭니다. 이 디자인 패턴을 보여 주는 점수 매기기 스크립트 예제에 대 한 [자습서](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) 를 참조 하세요. |
| 종속성 버그 | 스크립트를 로컬로 개발 하 고 테스트 했지만 파이프라인에서 원격 계산을 실행할 때 종속성 문제가 발견 되 면 계산 환경 종속성 및 버전이 테스트 환경과 일치 하는지 확인 합니다. |
| 계산 대상의 모호한 오류 | 계산 대상을 삭제 하 고 다시 만들면 계산 대상의 특정 문제를 해결할 수 있습니다. |
| 파이프라인이 단계를 다시 사용 하지 않음 | 단계 다시 사용은 기본적으로 사용 되지만 파이프라인 단계에서 사용 하지 않도록 설정 하지 않았는지 확인 합니다. 다시 사용 하지 않도록 설정 된 경우 단계의 `allow_reuse` 매개 변수는 `False`로 설정 됩니다. |
| 파이프라인이 불필요 하 게 다시 실행 되 고 있습니다. | 기본 데이터 또는 스크립트가 변경 될 때만 단계가 다시 실행 되도록 하려면 각 단계에 대 한 디렉터리를 분리 합니다. 여러 단계에 동일한 원본 디렉터리를 사용 하는 경우 불필요 한 다시 실행이 발생할 수 있습니다. 파이프라인 단계 개체의 `source_directory` 매개 변수를 사용 하 여 해당 단계에 대 한 격리 된 디렉터리를 가리키고 여러 단계에 대해 동일한 `source_directory` 경로를 사용 하지 않는지 확인 합니다. |

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Azure Machine Learning 디자이너에서 디버그 및 문제 해결 (미리 보기)

이 섹션에서는 디자이너의 파이프라인 문제를 해결 하는 방법에 대 한 개요를 제공 합니다.
디자이너에서 만든 파이프라인의 경우 제작 페이지나 파이프라인 실행 세부 정보 페이지에서 **로그 파일** 을 찾을 수 있습니다.

### <a name="access-logs-from-the-authoring-page"></a>제작 페이지에서 로그 액세스

파이프라인 실행을 제출 하 고 제작 페이지에 유지 하는 경우 각 모듈에 대해 생성 되는 로그 파일을 찾을 수 있습니다.

1. 제작 캔버스에서 모듈을 선택 합니다.
1. 속성 창에서 **로그** 탭으로 이동 합니다.
1. 로그 파일을 선택 `70_driver_log.txt`

    ![페이지 모듈 로그 제작](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>파이프라인 실행에서 로그 액세스

**파이프라인 또는** **실험** 섹션의 파이프라인 실행 세부 정보 페이지에서 특정 실행의 로그 파일을 찾을 수도 있습니다.

1. 디자이너에서 만든 파이프라인 실행을 선택 합니다.
    ![파이프라인 실행 페이지](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. 미리 보기 창에서 모듈을 선택 합니다.
1. 속성 창에서 **로그** 탭으로 이동 합니다.
1. 로그 파일을 선택 `70_driver_log.txt`

## <a name="next-steps"></a>다음 단계

* [Azureml-파이프라인-코어](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) 패키지 및 [azureml 파이프라인 단계](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) 패키지에 대 한 도움말은 SDK 참조를 참조 하세요.

* [디자이너 예외 및 오류 코드](algorithm-module-reference/designer-error-codes.md)의 목록을 참조 하세요.
