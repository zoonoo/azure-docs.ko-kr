---
title: 자동화 된 ML 실험 문제 해결
titleSuffix: Azure Machine Learning
description: 자동화 된 기계 학습 실험에서 문제를 해결 하 고 문제를 해결 하는 방법을 알아봅니다.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, automl, references_regions
ms.openlocfilehash: b66c768011c05e1105f1351ebe4ed7c3c9700b70
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519166"
---
# <a name="troubleshoot-automated-ml-experiments-in-python"></a>Python에서 자동화 된 ML 실험 문제 해결

이 가이드에서는 [AZURE MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro)를 사용 하 여 자동화 된 기계 학습 실험에서 알려진 문제를 식별 하 고 해결 하는 방법을 알아봅니다.

## <a name="version-dependencies"></a>버전 종속성

**`AutoML` 최신 패키지 버전에 대 한 종속성은 호환성을 중단** 합니다. SDK 버전 1.13.0 이후 이전 패키지에 고정 된 이전 버전과 최신 버전의 최신 버전이 호환 되지 않아 이전 Sdk에서 모델이 로드 되지 않습니다 `AutoML` .

다음과 같은 오류가 발생 합니다.

* 모듈을 찾을 수 없습니다. 오류:

  `No module named 'sklearn.decomposition._truncated_svd`

* 가져오기 오류 (예:)

  `ImportError: cannot import name 'RollingOriginValidator'`,
* 특성 오류 (예:)

  `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`

해결 방법은 SDK 교육 버전에 따라 달라 집니다 `AutoML` .

* `AutoML`SDK 교육 버전이 1.13.0 보다 큰 경우 및가 필요 `pandas == 0.25.1` `scikit-learn==0.22.1` 합니다.

    * 버전이 일치 하지 않는 경우 다음을 사용 하 여 scikit 및/또는 pandas를 올바른 버전으로 업그레이드 합니다.

      ```bash
          pip install --upgrade pandas==0.25.1
          pip install --upgrade scikit-learn==0.22.1
      ```

* `AutoML`SDK 교육 버전이 1.12.0 보다 작거나 같은 경우 및가 필요 `pandas == 0.23.4` `sckit-learn==0.20.3` 합니다.
  * 버전이 일치 하지 않는 경우 다음을 사용 하 여 scikit 및/또는 pandas를 올바른 버전으로 다운 그레이드 합니다.
  
    ```bash
      pip install --upgrade pandas==0.23.4
      pip install --upgrade scikit-learn==0.20.3
    ```

## <a name="setup"></a>설치 프로그램

`AutoML` 버전 1.0.76 이후의 패키지 변경 내용은 새 버전으로 업데이트 하기 전에 이전 버전을 제거 해야 합니다.

* **`ImportError: cannot import name AutoMLConfig`**

    V 1.0.76에서 v 1.0.76 이상으로 업그레이드 한 후에이 오류가 발생 하는 경우를 실행 하 여 오류를 해결 한 `pip uninstall azureml-train automl` 다음를 실행 `pip install azureml-train-automl` 합니다. Automl_setup 스크립트는이를 자동으로 수행 합니다.

* **automl_setup 실패**

  * Windows에서는 Anaconda 프롬프트에서 automl_setup를 실행 합니다. [Miniconda를 설치](https://docs.conda.io/en/latest/miniconda.html)합니다.

  * Conda 64 비트 버전 4.4.10 이상이 설치 되어 있는지 확인 합니다. 명령을 사용 하 여 비트를 확인할 수 있습니다 `conda info` . 는 `platform` `win-64` Windows 또는 Mac 용 이어야 합니다 `osx-64` . 버전을 확인 하려면 명령을 사용 `conda -V` 합니다. 이전 버전이 설치 되어 있는 경우 명령을 사용 하 여 업데이트할 수 있습니다 `conda update conda` . 을 실행 하 여 32 비트를 확인 하려면 

  * Conda이 설치 되어 있는지 확인 합니다. 

  * 용 `gcc: error trying to exec 'cc1plus'`

    1. `gcc: error trying to exec 'cc1plus': execvp: No such file or directory`오류가 발생 하면 Linux 배포용 GCC 빌드 도구를 설치 합니다. 예를 들어 Ubuntu에서 명령을 사용 `sudo apt-get install build-essential` 합니다.

    1. 새 이름을 automl_setup에 대 한 첫 번째 매개 변수로 전달 하 여 새 conda 환경을 만듭니다. 를 사용 하 여 기존 conda 환경을 보고 `conda env list` 제거 `conda env remove -n <environmentname>` 합니다.

* **automl_setup_linux sh 실패**: automl_setup_linus. sh가 오류가 발생 한 Ubuntu Linux에서 실패 합니다. `unable to execute 'gcc': No such file or directory`

  1. 아웃 바운드 포트 53 및 80을 사용 하도록 설정 했는지 확인 합니다. Azure 가상 컴퓨터에서 VM을 선택 하 고 **네트워킹** 을 클릭 하 여 Azure Portal에서이 작업을 수행할 수 있습니다.
  1. `sudo apt-get update` 명령을 실행합니다.
  1. `sudo apt-get install build-essential --fix-missing` 명령을 실행합니다.
  1. `automl_setup_linux.sh`다시 실행

* **구성. ipynb 실패**:

  * 로컬 conda의 경우 먼저 `automl_setup` 이 성공적으로 실행 되었는지 확인 합니다.
  * Subscription_id 올바른지 확인 하십시오. 모든 서비스를 선택한 다음 구독을 선택 하 여 Azure Portal에서 subscription_id를 찾습니다. 문자 "<" 및 ">"는 subscription_id 값에 포함 되지 않아야 합니다. 예를 들어에는 `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` 유효한 형식이 있습니다.
  * 구독에 대 한 참가자 또는 소유자의 액세스 권한이 있는지 확인 합니다.
  * 지역이 지원 되는 지역,,,,,,, 중 하나 인지 확인 `eastus2` `eastus` `westcentralus` `southeastasia` `westeurope` `australiaeast` `westus2` `southcentralus` 합니다.
  * Azure Portal를 사용 하 여 지역에 대 한 액세스를 확인 합니다.
  
* **workspace.from_config 실패**:

  호출에 `ws = Workspace.from_config()` 실패 하면 다음을 수행 합니다.

  1. 구성. ipynb 노트북이 성공적으로 실행 되었는지 확인 합니다.
  1. 가 실행 된 폴더에 없는 폴더에서 노트북을 실행 하는 경우 `configuration.ipynb` 폴더 aml_config 폴더를 복사 하 고 해당 폴더에 포함 된 config.js파일을 새 폴더에 복사 합니다. Workspace.from_config 노트북 폴더 또는 해당 부모 폴더에 대 한 config.js를 읽습니다.
  1. 새 구독, 리소스 그룹, 작업 영역 또는 지역이 사용 중인 경우에는 다시 노트북을 실행 해야 `configuration.ipynb` 합니다. 지정 된 구독에서 지정 된 리소스 그룹에 작업 영역이 이미 있는 경우에만 config.js의 변경 내용이 적용 됩니다.
  1. 지역을 변경 하려면 작업 영역, 리소스 그룹 또는 구독을 변경 합니다. `Workspace.create` 는 이미 있는 경우 작업 영역을 만들거나 업데이트 하지 않습니다. 지정 된 지역이 다른 경우에도 마찬가지입니다.

## <a name="tensorflow"></a>TensorFlow

SDK의 버전 1.5.0을 기준으로 자동화 된 machine learning은 기본적으로 TensorFlow 모델을 설치 하지 않습니다. TensorFlow를 설치 하 고 자동화 된 ML 실험에서 사용 하려면을 `tensorflow==1.12.0` 통해 설치 `CondaDependencies` 합니다.

```python
  from azureml.core.runconfig import RunConfiguration
  from azureml.core.conda_dependencies import CondaDependencies
  run_config = RunConfiguration()
  run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
```

## <a name="numpy-failures"></a>Numpy 실패

* **`import numpy` windows에서 실패**: 일부 windows 환경에서는 최신 Python 버전 3.6.8를 사용 하 여 numpy를 로드 하는 동안 오류가 발생 합니다. 이 문제가 표시 되는 경우 Python 버전 3.6.7으로 시도 하세요.

* **`import numpy` 실패**: 자동화 된 ml Conda 환경에서 TensorFlow 버전을 확인 합니다. 지원 되는 버전은 < 1.13입니다. 버전이 >= 1.13 인 경우 환경에서 TensorFlow를 제거 합니다.

TensorFlow의 버전을 확인 하 고 다음과 같이 제거할 수 있습니다.

  1. 명령 셸을 시작 하 고 자동 ml 패키지가 설치 된 conda 환경을 활성화 합니다.
  1. 을 입력 `pip freeze` 하 고 검색 `tensorflow` 하는 경우 나열 된 버전 < 1.13 이어야 합니다.
  1. 표시 된 버전이 지원 되는 버전이 아닌 경우 `pip uninstall tensorflow` 명령 셸에서 y를 입력 하 여 확인 합니다.

## `jwt.exceptions.DecodeError`

정확한 오류 메시지: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` .

SDK 버전 <= 1.17.0의 경우에는 설치 중에 지원 되지 않는 PyJWT 버전이 생성 될 수 있습니다. 자동화 된 ml conda 환경의 PyJWT 버전이 지원 되는 버전 인지 확인 합니다. PyJWT version < 2.0.0입니다.

PyJWT의 버전은 다음과 같이 확인할 수 있습니다.

1. 명령 셸을 시작 하 고 자동 ML 패키지가 설치 된 conda 환경을 활성화 합니다.

1. 을 입력 `pip freeze` 하 고 검색 `PyJWT` 하는 경우 나열 된 버전 < 2.0.0 여야 합니다.

표시 된 버전이 지원 되는 버전이 아닌 경우:

1. 최신 버전의 AutoML SDK로 업그레이드 하는 것이 좋습니다. `pip install -U azureml-sdk[automl]`

1. 이를 실행할 수 없는 경우 환경에서 PyJWT를 제거 하 고 다음과 같이 올바른 버전을 설치 합니다.

    1. `pip uninstall PyJWT` 명령 셸에서를 입력 하 고 `y` 확인을 입력 합니다.
    1. 을 사용 하 여 설치 `pip install 'PyJWT<2.0.0'` 합니다.
  
## <a name="databricks"></a>Databricks
[Databricks를 사용 하 여 자동화 된 ML 실험을 구성 하는 방법을](how-to-configure-databricks-automl-environment.md#troubleshooting)참조 하세요.

## <a name="forecasting-r2-score-is-always-zero"></a>예측 R2 점수는 항상 0입니다.

 제공 된 학습 데이터에 마지막 데이터 요소에 대해 동일한 값을 포함 하는 시계열이 있는 경우이 문제가 발생 `n_cv_splits`  +  `forecasting_horizon` 합니다.

시계열에서이 패턴이 예상 되는 경우 기본 메트릭을 **정규화 된 근본 제곱 오차** 로 전환할 수 있습니다.

## <a name="failed-deployment"></a>실패 한 배포

 SDK의 버전 <= 1.18.0 인 경우 배포를 위해 만든 기본 이미지는 다음 오류와 함께 실패할 수 있습니다. `ImportError: cannot import name cached_property from werkzeug` .

  다음 단계를 통해이 문제를 해결할 수 있습니다.

  1. 모델 패키지 다운로드
  1. 패키지 압축 풀기
  1. 압축을 푼 자산을 사용 하 여 배포

## <a name="sample-notebook-failures"></a>샘플 노트북 오류

 예제 노트북이 실패 하 고 속성, 메서드 또는 라이브러리가 없다는 오류가 발생 하면 다음을 수행 합니다.

* Jupyter Notebook에서 올바른 커널을 선택 했는지 확인 합니다. 커널은 노트북 페이지의 오른쪽 위에 표시 됩니다. 기본값은 *azure_automl* 입니다. 커널은 노트북의 일부로 저장 됩니다. 새 conda 환경으로 전환 하는 경우 노트북에서 새 커널을 선택 해야 합니다.

  * Azure Notebooks의 경우 Python 3.6 이어야 합니다.
  * 로컬 conda 환경의 경우에는 automl_setup에서 지정한 conda 환경 이름 이어야 합니다.

* 사용 중인 SDK 버전에 대 한 노트북이 있는지 확인 하려면
  * Jupyter Notebook 셀에서를 실행 하 여 SDK 버전을 확인 합니다 `azureml.core.VERSION` .
  * 다음 단계를 사용 하 여 GitHub에서 이전 버전의 샘플 노트북을 다운로드할 수 있습니다.
    1. 단추를 선택 합니다. `Branch`
    1. 탭으로 이동 `Tags`
    1. 버전 선택

## <a name="next-steps"></a>다음 단계

+ [자동화된 Machine Learning을 사용하여 회귀 모델을 학습시키는 방법](tutorial-auto-train-models.md) 또는 [원격 리소스에서 자동화된 Machine Learning을 사용하여 학습하는 방법](how-to-auto-train-remote.md)에 대해 자세히 알아봅니다.

+ [모델 배포 방법 및 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다.