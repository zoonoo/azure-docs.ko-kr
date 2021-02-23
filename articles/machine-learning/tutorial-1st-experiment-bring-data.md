---
title: '자습서: 사용자 고유의 데이터 사용'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 시작 시리즈의 4부에서는 원격 학습 실행에서 사용자 고유의 데이터를 사용하는 방법을 보여 줍니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: tracking-python
ms.openlocfilehash: 5e1af60cccb48195db38e420dfe3df01f404669c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378005"
---
# <a name="tutorial-use-your-own-data-part-4-of-4"></a>자습서: 사용자 고유의 데이터 사용(4/4부)

이 자습서에서는 사용자 고유의 데이터를 업로드하고 사용하여 Azure Machine Learning에서 기계 학습 모델을 학습시키는 방법을 보여 줍니다.

이 자습서는 Azure Machine Learning의 기본 사항을 알아보고 Azure에서 작업 기반 기계 학습 작업을 완료하는 *4부로 구성된 자습서 시리즈 중 4부* 입니다. 이 자습서는 [1부: 설정](tutorial-1st-experiment-sdk-setup-local.md), [2부: “Hello World” 실행](tutorial-1st-experiment-hello-world.md) 및 [3부: 모델 학습](tutorial-1st-experiment-sdk-train.md)에서 완료한 작업을 기반으로 합니다.

[3부: 모델 학습](tutorial-1st-experiment-sdk-train.md)에서는 데이터가 PyTorch API에서 기본 제공 `torchvision.datasets.CIFAR10` 메서드를 사용하여 다운로드되었습니다. 그러나 대부분의 경우 원격 학습 실행에서 사용자 고유의 데이터를 사용하려고 합니다. 이 문서에서는 Azure Machine Learning에서 사용자 고유의 데이터로 작업하는 데 사용할 수 있는 워크플로를 보여 줍니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 로컬 디렉터리의 데이터를 사용하도록 학습 스크립트 구성
> * 로컬로 학습 스크립트 테스트
> * Azure에 데이터 업로드
> * 제어 스크립트 만들기
> * 새 Azure Machine Learning 개념(매개 변수, 데이터 세트, 데이터 저장소 전달) 이해
> * 학습 스크립트 제출 및 실행
> * 클라우드에서 코드 출력 보기

## <a name="prerequisites"></a>사전 요구 사항

* 시리즈의 [3부](tutorial-1st-experiment-sdk-train.md) 완료

## <a name="adjust-the-training-script"></a>학습 스크립트 조정

이제 Azure Machine Learning에서 실행되는 학습 스크립트(tutorial/src/train.py)가 있으며 모델 성능을 모니터링할 수 있습니다. 인수를 도입하여 학습 스크립트를 매개 변수화하겠습니다. 인수를 사용하면 다른 하이퍼 매개 변수를 쉽게 비교할 수 있습니다.

현재 학습 스크립트는 각 실행에서 CIFAR10 데이터 세트를 다운로드하도록 설정되어 있습니다. 아래의 Python 코드는 디렉터리에서 데이터를 읽도록 조정되었습니다.

>[!NOTE] 
> `argparse`를 사용하여 스크립트를 매개 변수화합니다.

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-your-data/train.py":::

### <a name="understanding-the-code-changes"></a>코드 변경 내용 이해

`train.py`의 코드는 `argparse` 라이브러리를 사용하여 `data_path`, `learning_rate` 및 `momentum`을 설정했습니다.

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

또한 사용자 정의 매개 변수를 사용하도록 최적화 프로그램을 업데이트하기 위해 `train.py` 스크립트가 조정되었습니다.

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```
> [!div class="nextstepaction"]
> [학습 스크립트를 조정했습니다.](?success=adjust-training-script#test-locally) [문제가 발생했습니다.](https://www.research.net/r/7C6W7BQ?issue=adjust-training-script)

## <a name="test-the-script-locally"></a><a name="test-locally"></a> 스크립트를 로컬로 테스트

이제 스크립트에서 _데이터 경로_ 를 인수로 허용합니다. 먼저 로컬로 테스트합니다. `data`라는 폴더를 자습서 디렉터리 구조에 추가합니다. 디렉터리 구조는 다음과 같습니다.

:::image type="content" source="media/tutorial-1st-experiment-bring-data/directory-structure.png" alt-text="디렉터리 구조에는 .azureml, 데이터 및 src 하위 디렉터리가 표시됨":::


이전 자습서에서 `train.py`를 로컬로 실행하지 않은 경우 `data/` 디렉터리가 없습니다. 이 경우 `train.py` 스크립트에서 `download=True`를 사용하여 `torchvision.datasets.CIFAR10` 메서드를 로컬로 실행합니다.

또한 로컬에서 실행하려면 자습서 환경을 종료하고 새 conda 환경을 활성화해야 합니다.

```bash
conda deactivate                # If you are still using the tutorial environment, exit it
```

```bash
conda env create -f .azureml/pytorch-env.yml    # create the new conda environment with updated dependencies
```

```bash
conda activate pytorch-aml-env          # activate new conda environment
```

수정된 학습 스크립트를 로컬로 실행하려면 다음을 호출합니다.

```bash
python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
```

데이터에 대한 로컬 경로를 전달하여 CIFAR10 데이터 세트를 다운로드할 필요가 없습니다. 또한 학습 스크립트에서 하드 코딩하지 않고도 다른 값을 _학습 속도_ 및 _모멘텀_ 하이퍼 매개 변수에 사용하여 실험할 수도 있습니다.

> [!div class="nextstepaction"]
> [스크립트를 로컬로 테스트했습니다.](?success=test-locally#upload) [문제가 발생했습니다.](https://www.research.net/r/7C6W7BQ?issue=test-locally)

## <a name="upload-the-data-to-azure"></a><a name="upload"></a> Azure에 데이터 업로드

Azure Machine Learning에서 이 스크립트를 실행하려면 Azure에서 학습 데이터를 사용할 수 있도록 해야 합니다. Azure Machine Learning 작업 영역에는 _기본_ 데이터 저장소가 장착되어 제공됩니다. 이는 학습 데이터를 저장할 수 있는 Azure Blob Storage 계정입니다.

>[!NOTE] 
> Azure Machine Learning을 사용하면 데이터를 저장하는 다른 클라우드 기반 데이터 저장소를 연결할 수 있습니다. 자세한 내용은 [데이터 저장소 설명서](./concept-data.md)를 참조하세요.  

`05-upload-data.py`라는 새 Python 제어 스크립트를 `tutorial` 디렉터리에 만듭니다.

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/05-upload-data.py":::

`target_path` 값은 CIFAR10 데이터가 업로드되는 데이터 저장소의 경로를 지정합니다.

>[!TIP] 
> Azure Machine Learning을 사용하여 데이터를 업로드하는 동안 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 사용하여 임시 파일을 업로드할 수 있습니다. ETL 도구가 필요한 경우 [Azure Data Factory](../data-factory/introduction.md)를 사용하여 데이터를 Azure에 수집할 수 있습니다.

활성화된 *tutorial1* conda 환경이 있는 창에서 Python 파일을 실행하여 데이터를 업로드합니다. (업로드 속도는 60초 미만이어야 합니다.)

```bash
python 05-upload-data.py
```

다음과 같은 표준 출력이 표시됩니다.

```txt
Uploading ./data\cifar-10-batches-py\data_batch_2
Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
.
.
Uploading ./data\cifar-10-batches-py\data_batch_5
Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
Uploaded 9 files
```

> [!div class="nextstepaction"]
> [데이터를 업로드했습니다.](?success=upload-data#control-script) [문제가 발생했습니다.](https://www.research.net/r/7C6W7BQ?issue=upload-data)

## <a name="create-a-control-script"></a><a name="control-script"></a> 제어 스크립트 만들기

이전과 같이 `06-run-pytorch-data.py`라는 새 Python 제어 스크립트를 만듭니다.

```python
# 06-run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
    )
    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to compute cluster. Click link below")
    print("")
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>코드 변경 내용 이해

제어 스크립트는 [이 시리즈의 3부](tutorial-1st-experiment-sdk-train.md)에 있는 것과 비슷하며 다음과 같은 새 줄이 있습니다.

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      [데이터 세트](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py)는 Azure Blob Storage에 업로드한 데이터를 참조하는 데 사용됩니다. 데이터 세트는 데이터를 기반으로 하는 있는 추상화 계층이며, 안정성과 신뢰성을 향상시키도록 설계되었습니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py)는 `train.py`에 전달되는 인수 목록을 포함하도록 수정되었습니다. `dataset.as_named_input('input').as_mount()` 인수는 지정된 디렉터리가 컴퓨팅 대상에 _탑재됨_ 을 의미합니다.
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [제어 스크립트를 만들었습니다.](?success=control-script#submit-to-cloud) [문제가 발생했습니다.](https://www.research.net/r/7C6W7BQ?issue=control-script)

## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-to-cloud"></a> Azure Machine Learning에 실행 제출

이제 새 구성을 사용하기 위해 실행을 다시 제출합니다.

```bash
python 06-run-pytorch-data.py
```

그러면 Azure Machine Learning 스튜디오에서 실험에 대한 URL이 출력됩니다. 해당 링크로 이동하면 코드가 실행되는 것을 확인할 수 있습니다.

> [!div class="nextstepaction"]
> [실행을 다시 제출했습니다.](?success=submit-to-cloud#inspect-log) [문제가 발생했습니다.](https://www.research.net/r/7C6W7BQ?issue=submit-to-cloud)

### <a name="inspect-the-log-file"></a><a name="inspect-log"></a> 로그 파일 검사

스튜디오에서 이전 URL 출력을 선택하여 실험 실행으로 이동한 다음, **출력 + 로그** 로 이동합니다. `70_driver_log.txt` 파일을 선택합니다. 다음 출력이 표시됩니다.

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

참고:

- Azure Machine Learning에서 자동으로 Blob Storage를 컴퓨팅 클러스터에 탑재했습니다.
- 제어 스크립트에 사용된 ``dataset.as_named_input('input').as_mount()``가 탑재 지점으로 확인됩니다.

> [!div class="nextstepaction"]
> [로그 파일을 검사했습니다.](?success=inspect-log#clean-up-resources) [문제가 발생했습니다.](https://www.research.net/r/7C6W7BQ?issue=inspect-log)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

또한 리소스 그룹을 유지하면서 단일 작업 영역을 삭제할 수도 있습니다. 작업 영역 속성을 표시하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 `Datastore`를 사용하여 데이터를 Azure에 업로드하는 방법을 살펴보았습니다. 데이터 저장소는 작업 영역에 대한 클라우드 스토리지로 제공되므로 데이터를 유지할 수 있는 지속적이고 유연한 장소를 제공했습니다.

명령줄을 통해 데이터 경로를 허용하도록 학습 스크립트를 수정하는 방법을 살펴보았습니다. `Dataset`를 사용하여 디렉터리를 원격 실행에 탑재할 수 있었습니다. 

이제 모델이 있으므로 다음을 알아봅니다.

* [Azure Machine Learning을 사용하여 모델을 배포](how-to-deploy-and-where.md)하는 방법
