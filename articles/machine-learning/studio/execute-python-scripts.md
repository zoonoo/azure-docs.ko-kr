---
title: Python 스크립트 실행
titleSuffix: ML Studio (classic) - Azure
description: 파이썬 스크립트 실행 모듈을 사용하여 기계 학습 스튜디오 (클래식) 실험 및 웹 서비스에서 파이썬 코드를 사용하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: c79f6bd63fa5d8d8c6b22ff271d8ca513a94fd64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218077"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Azure 기계 학습 스튜디오에서 파이썬 기계 학습 스크립트 실행(클래식)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

파이썬은 많은 데이터 과학자의 도구 상자에 귀중한 도구입니다. 데이터 탐색, 기능 추출, 모델 학습 및 유효성 검사, 배포를 비롯한 일반적인 기계 학습 워크플로의 모든 단계에서 사용됩니다.

이 문서에서는 Python 스크립트 실행 모듈을 사용하여 Azure 기계 학습 스튜디오(클래식) 실험 및 웹 서비스에서 파이썬 코드를 사용하는 방법을 설명합니다.

## <a name="using-the-execute-python-script-module"></a>파이썬 스크립트 실행 모듈 사용

스튜디오 (클래식)에서 파이썬에 대한 기본 인터페이스는 [파이썬 스크립트 모듈을 실행하는][execute-python-script] 것입니다. 최대 3개의 입력을 허용하고 [R 스크립트 실행][execute-r-script] 모듈과 유사한 최대 2개의 출력을 생성합니다. Python 코드는 라는 `azureml_main`특별히 명명 된 진입점 함수를 통해 매개 변수 상자에 입력 됩니다.

![파이썬 스크립트 모듈 실행](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![모듈 매개 변수 상자의 샘플 파이썬 코드](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>입력 매개 변수

파이썬 모듈에 대한 입력은 팬더 데이터 프레임으로 노출됩니다. 이 `azureml_main` 함수는 최대 2개의 옵션인 팬더 DataFrames를 매개 변수로 허용합니다.

입력 포트와 함수 매개 변수 간의 매핑은 위치와 관련됩니다.

- 첫 번째로 연결된 입력 포트는 함수의 첫 번째 매개 변수에 매핑됩니다.
- 두 번째 입력(연결된 경우)은 함수의 두 번째 매개 변수에 매핑됩니다.
- 세 번째 입력은 [추가 파이썬 모듈을 가져오는 데 사용됩니다.](#import-modules)

입력 포트가 함수의 매개 변수에 매핑되는 방법에 대한 `azureml_main` 자세한 의미 체계는 다음과 같습니다.

![입력 포트 구성 및 결과 파이썬 서명 표](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>출력 반환 값

함수는 `azureml_main` 튜플, 목록 또는 NumPy 배열과 같은 파이썬 [시퀀스로](https://docs.python.org/2/c-api/sequence.html) 패키지된 단일 팬더 DataFrame을 반환해야 합니다. 이 시퀀스의 첫 번째 요소는 모듈의 첫 번째 출력 포트로 반환됩니다. 모듈의 두 번째 출력 포트는 [시각화에](#visualizations) 사용되며 반환 값이 필요하지 않습니다. 이 구성표는 아래와 같습니다.

![입력 포트를 매개 변수로 매핑하고 값을 출력 포트로 반환합니다.](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>입력 및 출력 데이터 형식 의 변환

스튜디오 데이터 집합은 팬더 데이터 프레임과 동일하지 않습니다. 결과적으로 스튜디오(클래식)의 입력 데이터 집합이 팬더 DataFrame으로 변환되고 출력 DataFrame이 다시 스튜디오(클래식) 데이터 집합으로 변환됩니다. 이 변환 프로세스 중에 다음 번역도 수행됩니다.

 **파이썬 데이터 형식** | **스튜디오 번역 절차** |
| --- | --- |
| 문자열 및 숫자| 있는 것으로 번역 |
| 팬더 'NA' | '누락된 값'으로 번역 |
| 인덱스 벡터 | 지원되지 않음* |
| 문자열이 아닌 열 이름 | 열 `str` 이름 호출 |
| 열 이름 중복 | 숫자 접미사: (1), (2), (3) 등등.

**Python 함수의 모든 입력 데이터 프레임에는 항상 0에서 행 수에서 1을 뺀 64비트 숫자 인덱스가 있습니다.*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>기존 Python 스크립트 모듈 가져오기

파이썬을 실행하는 데 사용되는 백 엔드는 널리 사용되는 과학 파이썬 분포인 [Anaconda를](https://www.anaconda.com/distribution/)기반으로합니다. 데이터 중심 워크로드에 사용되는 가장 일반적인 Python 패키지 는 200개에 가깝습니다. Studio(클래식)는 현재 외부 라이브러리를 설치하고 관리하기 위해 Pip 또는 Conda와 같은 패키지 관리 시스템의 사용을 지원하지 않습니다.  추가 라이브러리를 통합해야 하는 경우 다음 시나리오를 지침으로 사용합니다.

일반적인 사용 사례는 기존 파이썬 스크립트를 Studio(클래식) 실험에 통합하는 것입니다. [파이썬 스크립트 실행][execute-python-script] 모듈은 세 번째 입력 포트에서 파이썬 모듈이 포함된 zip 파일을 수락합니다. 이 파일은 런타임에 입력 프레임워크에서 압축이 풀리며, 파일의 내용은 Python 인터프리터의 라이브러리 경로에 추가됩니다. 그러면 `azureml_main` 진입점 함수가 이러한 모듈을 직접 가져올 수 있습니다. 

예를 들어 파일 Hello.py 간단한 "Hello, World" 함수가 포함되어 있다고 생각하십시오.

![Hello.py 파일에서 사용자 정의 함수](./media/execute-python-scripts/figure4.png)

다음으로 Hello.py을 포함하는 Hello.zip이라는 파일을 만듭니다.

![사용자 정의 파이썬 코드가 포함된 Zip 파일](./media/execute-python-scripts/figure5.png)

zip 파일을 데이터 집합으로 스튜디오(클래식)에 업로드합니다. 그런 다음 다음 이미지와 같이 Execute Python 스크립트 모듈의 세 번째 입력 포트에 연결하여 Hello.zip 파일에서 **파이썬** 코드를 사용하는 실험을 만들고 실행합니다.

![실행 파이썬 스크립트 모듈에 대한 입력으로 Hello.zip샘플 실험](./media/execute-python-scripts/figure6a.png)

![zip 파일로 업로드된 사용자 정의 파이썬 코드](./media/execute-python-scripts/figure6b.png)

모듈 출력에는 zip 파일의 패키지가 해제되었으며 `print_hello` 함수가 실행되었음이 표시됩니다.

![사용자 정의 기능을 보여주는 모듈 출력](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Azure 저장소 Blob에 액세스

다음 단계를 사용하여 Azure Blob Storage 계정에 저장된 데이터에 액세스할 수 있습니다.

1. [파이썬에 대한 Azure Blob 저장소 패키지를](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) 로컬로 다운로드합니다.
1. zip 파일을 데이터 집합으로 Studio(클래식) 작업 영역에 업로드합니다.
1. BlobService 개체를 사용하여 만듭니다.`protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. 저장소 **구성** 설정 탭에서 **필요한 보안 전송** 사용 안 함

![Azure 포털에서 필요한 보안 전송 사용 안 함](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Python 스크립트 운영 가능화

점수 매기기 실험에서 모든 [Python 스크립트 실행][execute-python-script] 모듈은 웹 서비스로 게시될 때 호출됩니다. 예를 들어 아래 이미지는 단일 Python 식을 평가하는 코드를 포함하는 채점 실험을 보여 주며 있습니다.

![웹 서비스를 위한 스튜디오 작업 공간](./media/execute-python-scripts/figure3a.png)

![파이썬 팬더 표현](./media/execute-python-scripts/python-script-with-python-pandas.png)

이 실험에서 만든 웹 서비스는 다음과 같은 작업을 수행합니다.

1. 파이썬 식을 입력으로 사용합니다(문자열).
1. 파이썬 인터프리터에 파이썬 표현식 보내기
1. 식과 평가된 결과를 모두 포함하는 테이블을 반환합니다.

## <a name="working-with-visualizations"></a><a id="visualizations"></a>시각화 작업

MatplotLib을 사용하여 만든 플롯은 [파이썬 스크립트 실행에서][execute-python-script]반환할 수 있습니다. 그러나 플롯은 R을 사용할 때와 같이 이미지로 자동으로 리디렉션되지 않습니다. 따라서 사용자는 PNG 파일에 플롯을 명시적으로 저장해야 합니다.

MatplotLib에서 이미지를 생성하려면 다음 단계를 수행해야 합니다.

1. 백 엔드를 기본 Qt 기반 렌더러에서 "AGG"로 전환합니다.
1. 새 그림 개체를 만듭니다.
1. 축을 얻고 축에 모든 플롯을 생성합니다.
1. 그림을 PNG 파일에 저장합니다.

이 프로세스는 팬더의 scatter_matrix 함수를 사용하여 분산형 플롯 행렬을 만드는 다음 이미지에 설명되어 있습니다.

![MatplotLib 수치를 이미지에 저장하는 코드](./media/execute-python-scripts/figure-v1-8.png)

![그림을 보려면 파이썬 스크립트 실행 모듈에서 시각화를 클릭하십시오.](./media/execute-python-scripts/figure-v2-9a.png)

![파이썬 코드를 사용하여 샘플 실험에 대한 플롯 시각화](./media/execute-python-scripts/figure-v2-9b.png)

여러 그림을 다른 이미지로 저장하여 반환할 수 있습니다. Studio(클래식) 런타임은 모든 이미지를 선택하고 시각화를 위해 통합합니다.

## <a name="advanced-examples"></a>고급 예제

스튜디오 (클래식)에 설치된 아나콘다 환경에는 NumPy, SciPy 및 Scikits-Learn과 같은 일반적인 패키지가 포함되어 있습니다. 이러한 패키지는 기계 학습 파이프라인의 데이터 처리에 효과적으로 사용할 수 있습니다.

예를 들어 다음 실험 및 스크립트에서는 Scikits-Learn에서 앙상블 학습자의 사용을 보여 주어 데이터 집합에 대한 기능 중요도 점수를 계산합니다. 점수는 다른 모델로 공급되기 전에 감독된 피쳐 선택을 수행하는 데 사용할 수 있습니다.

중요도 점수를 계산하고 점수에 따라 기능의 순서를 지정하는 데 사용되는 Python 함수는 다음과 같습니다.

![점수별 기능 순위를 매기는 기능](./media/execute-python-scripts/figure8.png)

다음 실험에서는 Azure 기계 학습 스튜디오(클래식)의 "Pima Indian Diabetes" 데이터 집합에서 기능의 중요도 점수를 계산하고 반환합니다.

![파이썬을 사용하여 피마 인도 당뇨병 데이터 세트의 기능 순위를 매기는 실험](./media/execute-python-scripts/figure9a.png)

![실행 파이썬 스크립트 모듈의 출력 시각화](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>제한 사항

[파이썬 스크립트 실행][execute-python-script] 모듈에는 현재 다음과 같은 제한 사항이 있습니다.

### <a name="sandboxed-execution"></a>샌드박스 실행

Python 런타임은 현재 샌드박스화되어 있으며 네트워크 또는 로컬 파일 시스템에 대한 지속적인 액세스를 허용하지 않습니다. 모듈이 완료되면 로컬에 저장된 모든 파일이 격리되어 삭제됩니다. Python 코드는 현재 디렉터리와 하위 디렉터리를 제외하고, 코드가 실행되는 컴퓨터에 있는 대부분의 디렉터리에 액세스할 수 없습니다.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>정교한 개발 및 디버깅 지원 부족

현재 Python 모듈에서는 Intellisense와 디버깅 같은 IDE 기능을 지원하지 않습니다. 또한 모듈이 런타임에서 실패하는 경우 전체 Python 스택 추적을 사용할 수 있습니다. 그러나 스택 추적은 모듈의 출력 로그에서 확인해야 합니다. 현재로서는 IPython과 같은 환경에서 Python 스크립트를 개발하고 디버그한 다음 모듈에 코드를 가져오는 것이 좋습니다.

### <a name="single-data-frame-output"></a>단일 데이터 프레임 출력

Python 진입점은 단일 데이터 프레임을 출력으로 반환할 수만 있습니다. 현재 는 학습된 모델과 같은 임의의 파이썬 개체를 Studio(클래식) 런타임으로 직접 되돌릴 수 없습니다. 동일한 제한 사항이 있는 [R 스크립트 실행][execute-r-script]과 마찬가지로, 대부분의 경우 개체를 바이트 배열로 만든 다음 데이터 프레임 내부에서 반환할 수 있습니다.

### <a name="inability-to-customize-python-installation"></a>파이썬 설치를 사용자 정의 할 수 없음

현재 사용자 지정 Python 모듈을 추가하는 유일한 방법은 이전에 설명한 zip 파일 메커니즘을 통한 것입니다. 이것은 작은 모듈에 대 한 가능 하지만, 그것은 큰 모듈에 대 한 성가신 (특히 네이티브 DLL모듈) 또는 모듈의 많은 수의.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Python 개발자 센터](https://azure.microsoft.com/develop/python/)를 참조하세요.

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script
