---
title: '빠른 시작: Python에서 시작'
titleSuffix: Azure Machine Learning service
description: Python에서 Azure Machine Learning Service 시작 Python SDK를 사용하여 기계 학습 모델을 실험하고, 학습시키며, 배포하는 데 사용되는 클라우드의 기본 블록인 작업 영역을 만들 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 2c8edd73a287d5bca2f3deb68448ba951b7a3367
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54106565"
---
# <a name="quickstart-use-the-python-sdk-to-get-started-with-azure-machine-learning"></a>빠른 시작: Python SDK를 사용하여 Azure Machine Learning 시작

이 문서에서는 Python용 Azure Machine Learning SDK를 사용하여 Azure Machine Learning 서비스 [작업 영역](concept-azure-machine-learning-architecture.md)을 만들고 사용합니다. 작업 영역은 Machine Learning을 사용하여 기계 학습 모델을 실험하고, 교육하고, 배포하는 데 사용하는 클라우드의 기본 블록입니다. 

먼저 자체 Python 환경과 Jupyter Notebook 서버를 구성합니다. 설치 없이 실행하려면 [빠른 시작: Azure Portal을 사용하여 Azure Machine Learning 시작](quickstart-get-started.md)을 참조하세요.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

이 문서에서는 다음 작업을 수행합니다.
* Python SDK를 설치합니다.
* Azure 구독에 작업 영역을 만듭니다.
* 나중에 다른 노트북 및 스크립트에서 사용할 작업 영역에 대한 구성 파일을 만듭니다.
* 작업 영역 내부에 값을 기록하는 코드를 작성합니다.
* 기록한 값을 작업 영역에서 봅니다.

작업 영역 및 구성 파일을 만들어 다른 Azure Machine Learning 자습서 및 사용법 문서의 필수 구성 요소로 사용합니다. 다른 Azure 서비스와 마찬가지로, 특정 한도 및 할당량이 Machine Learning과 관련돼 있습니다. [할당량 및 더 많은 요청 방법에 대해 알아보세요.](how-to-manage-quotas.md)

다음 Azure 리소스는 지역에서 사용할 수 있게 되면 작업 영역에 자동으로 추가됩니다.
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

Azure 구독이 아직 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 바로 [Azure Machine Learning Service의 무료 또는 유료 버전](http://aka.ms/AMLFree)을 사용해 보세요.

## <a name="install-the-sdk"></a>SDK 설치

> [!IMPORTANT]
> 2018년 9월 27일 이후에 만들어진 데이터 과학 가상 머신을 사용하는 경우 이 섹션을 건너뜁니다.
> 이 날짜 이후에 만들어진 데이터 과학 가상 머신은 Python SDK가 미리 설치된 상태로 제공됩니다.

이 문서의 코드를 실행하려면 Azure Machine Learning SDK 버전 1.0.2 이상이 필요합니다.

SDK를 설치하기 전에, 격리된 Python 환경을 만드는 것이 좋습니다. 이 문서에서 [Miniconda](https://conda.io/docs/user-guide/install/index.html)를 사용한다해도 설치된 전체 [Anaconda](https://www.anaconda.com/) 또는 [Python virtualenv](https://virtualenv.pypa.io/en/stable/)를 사용할 수도 있습니다.

### <a name="install-miniconda"></a>Miniconda 설치

[Miniconda를 다운로드하여 설치합니다](https://conda.io/miniconda.html). Python 3.7 이상을 선택합니다. Python 2.x를 선택하지 마세요.

### <a name="create-an-isolated-python-environment"></a>격리된 Python 환경 만들기 

1. 명령줄 창을 연 다음, Python 3.6으로 *myenv*라는 새로운 conda 환경을 만듭니다.

    ```shell
    conda create -n myenv -y Python=3.6
    ```

1. 환경을 활성화합니다.

    ```shell
    conda activate myenv
    ```

### <a name="install-the-sdk"></a>SDK 설치

활성화된 conda 환경에 SDK를 설치합니다. 이 코드는 Machine Learning SDK의 핵심 구성 요소를 설치합니다. 또한 conda 환경에 Jupyter Notebook 서버를 설치합니다. 머신 구성에 따라 설치를 완료하는 데 몇 분이 걸립니다.

```shell
# Install Jupyter
conda install nb_conda

# Install the base SDK and Jupyter Notebook
pip install azureml-sdk[notebooks]
```

추가 키워드를 사용하여 SDK의 다른 구성 요소를 설치할 수 있습니다.

```shell
# Install the base SDK and auto ml components
pip install azureml-sdk[automl]

# Install the base SDK and the model explainability component
pip install azureml-sdk[explain]

# Install the base SDK and experimental components
pip install azureml-sdk[contrib]
```

Azure Databricks 환경에서 [Databricks 설치 단계](how-to-configure-environment.md#azure-databricks
)를 대신 사용합니다.


## <a name="create-a-workspace"></a>작업 영역 만들기

1. Jupyter Notebook을 시작하려면 다음 명령을 입력합니다.

    ```shell
    jupyter notebook
    ```

1. 브라우저 창에서 기본 `Python 3` 커널을 사용하여 새 Notebook을 만듭니다. 

1. SDK 버전을 표시하려면 Notebook 셀에 다음 Python 코드를 입력한 다음, 실행합니다.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. [Azure Portal의 구독 목록](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 `<azure-subscription-id>` 매개 변수에 대한 값을 찾습니다. 역할이 소유자 또는 기여자인 구독을 사용합니다.

   ```python
   from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
                         subscription_id='<azure-subscription-id>', 
                         resource_group='myresourcegroup',
                         create_resource_group=True,
                         location='eastus2' # Or other supported Azure region   
                        )
   ```

   코드를 실행하는 경우 Azure 계정에 로그인하라는 메시지가 표시될 수 있습니다. 로그인하면 인증 토큰이 로컬로 캐시됩니다.

1. 연결된 스토리지, 컨테이너 레지스트리, 키 자격 증명 모음 등의 작업 영역 세부 정보를 확인하려면 다음 코드를 입력합니다.

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>구성 파일 작성

구성 파일에 있는 작업 영역의 세부 정보를 현재 디렉터리에 저장합니다. 이 파일의 이름은 *aml_config\config.json*입니다.  

`write_config()` API 호출은 현재 디렉터리에 구성 파일을 만듭니다. *config.json* 파일에는 다음 항목이 포함되어 있습니다.

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

나중에 이 작업 영역 구성 파일을 사용하여 동일한 작업 영역을 간편하게 로드할 수 있습니다. 다른 노트북 및 스크립트를 사용하여 동일한 디렉터리 또는 하위 디렉터리에 로드할 수 있습니다. 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]



## <a name="use-the-workspace"></a>작업 영역 사용

SDK의 기본 API를 사용하여 실험 실행을 추적하는 코드를 작성합니다.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>기록된 결과 보기
실행이 완료되면 Azure Portal에서 실험 실행을 볼 수 있습니다. 마지막 실행 결과로 이동하는 URL을 인쇄하려면 다음 코드를 사용합니다.

```python
print(run.get_portal_url())
```

이 링크를 사용하여 브라우저의 Azure Portal에서 기록된 값을 볼 수 있습니다.

![Azure Portal에서 기록된 값](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>리소스 정리 
>[!IMPORTANT]
>다른 Machine Learning 자습서 및 방법 문서에 대한 필수 조건으로 여기서 만든 리소스를 사용할 수 있습니다.

이 문서에서 만든 리소스를 사용하지 않으려는 경우 요금이 발생하지 않도록 해당 리소스를 삭제합니다.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>다음 단계

이 문서에서는 모델을 실험하고 배포하는 데 필요한 리소스를 만들었습니다. 또한 노트북에서 코드를 실행하고 클라우드의 작업 영역에서 코드의 실행 기록을 살펴보았습니다.

Machine Learning 자습서에서 코드를 사용하려면 사용자 환경에 패키지가 몇 개 더 필요합니다.

1. 브라우저에서 노트북을 닫습니다.
1. 명령줄 창에서 Ctrl+C를 선택하여 Jupyter Notebook 서버를 중지합니다.
1. 추가로 패키지를 설치합니다.  위의 `azureml-sdk[automl]`를 설치하지 않은 경우 지금 설치합니다.

    ```shell
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```

이러한 패키지를 설치한 후에는 계속 자습서에 따라 모델을 학습시키고 배포합니다. 

> [!div class="nextstepaction"]
> [자습서: 이미지 분류 모델 학습시키기](tutorial-train-models-with-aml.md)

[GitHub에서 고급 예제](https://aka.ms/aml-notebooks)를 탐색할 수도 있습니다.
