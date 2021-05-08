---
title: 실험 파일을 저장하고 쓰는 곳
titleSuffix: Azure Machine Learning
description: 스토리지 제한 오류와 실험 대기 시간을 방지하기 위해 입력 및 출력 파일을 저장하는 곳을 알아봅니다.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 59955b291ce706a77d0dd5ab052809fe725166d9
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387890"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Azure Machine Learning 실험을 위해 파일을 저장하고 쓰는 곳


이 문서에서는 스토리지 제한 오류 및 실험 대기 시간을 방지하기 위해 입력 파일을 저장하는 곳과 출력 파일을 쓰는 곳을 알아봅니다.

[컴퓨팅 대상](concept-compute-target.md)에서 학습을 실행하면 학습은 외부 환경과 격리됩니다. 이러한 설계의 목적은 실험의 재현성과 이식성을 보장하는 것입니다. 같은 스크립트를 동일한 컴퓨팅 대상 또는 다른 컴퓨팅 대상에서 두 번 실행하는 경우 같은 결과가 수신됩니다. 이 디자인에서는 컴퓨팅 대상이 완료된 후 실행된 작업에 대한 선호도 없이 컴퓨팅 대상을 상태 비저장 컴퓨팅 리소스로 처리할 수 있습니다.

## <a name="where-to-save-input-files"></a>입력 파일을 저장하는 위치

컴퓨팅 대상 또는 로컬 컴퓨터에서 실험을 시작하려면 먼저 코드 실행에 필요한 종속성 파일 및 데이터 파일 같은 해당 컴퓨팅 대상에서 필요한 파일을 사용할 수 있는지 확인해야 합니다.

Azure Machine Learning는 전체 원본 디렉터리를 복사하여 학습 스크립트를 실행합니다. 업로드를 원하지 않는 중요한 데이터가 있다면 [.ignore 파일](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)을 사용하거나 데이터를 원본 디렉터리에 포함하지 마세요. 대신 [데이터 저장소](/python/api/azureml-core/azureml.data)를 사용하여 데이터에 액세스하세요.

실험 스냅샷의 스토리지 용량 한도는 300MB 및/또는 2000개 파일입니다.

이러한 이유로 다음 작업을 권장합니다.

* **파일을 Azure Machine Learning [데이터 세트](/python/api/azureml-core/azureml.data)에 저장합니다.** 이렇게 하면 실험 대기 시간 문제가 발생하지 않으며, 원격 컴퓨팅 대상에서 데이터에 액세스할 수 있어 인증과 탑재를 Azure Machine Learning에서 관리할 수 있습니다. [데이터 세트를 이용한 학습](how-to-train-with-datasets.md)을 통해 데이터 세트를 학습 스크립트의 입력 데이터 원본으로 지정하는 방법을 자세히 알아보세요.

* **몇 가지 데이터 파일과 종속성 스크립트만 필요하며 데이터 저장소를 사용할 수 없다면,** 파일을 학습 스크립트와 같은 폴더 디렉터리에 저장하세요. 이 폴더를 훈련 스크립트 또는 훈련 스크립트를 호출하는 코드의 `source_directory`로 지정하세요.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>실험 스냅샷의 스토리지 제한

실험을 위해 Azure Machine Learning에서는 사용자가 실행을 구성할 때 제안하는 디렉터리를 바탕으로 코드의 실험 스냅샷을 자동으로 만듭니다. 이 스냅샷의 총 제한은 300MB 및/또는 2000개 파일입니다. 이 제한을 초과하면 다음 오류가 표시됩니다.

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

이 오류를 해결하려면 데이터 저장소에 실험 파일을 저장하세요. 데이터 저장소를 사용할 수 없는 경우 아래 표에서 가능한 대체 솔루션을 확인할 수 있습니다.

실험&nbsp;설명|스토리지 제한 솔루션
---|---
2000개 미만의 파일, 데이터 저장소를 사용 불가| 다음을 통해 스냅샷 제한 크기 재정의 <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> 파일 수와 크기에 따라 몇 분 정도 걸릴 수 있습니다.
구체적인 스크립트 디렉터리를 사용해야 합니다.| [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
파이프라인|각 단계에 다른 하위 디렉터리 사용
Jupyter Notebook| `.amlignore` 파일을 만들거나 노트북을 비어 있는 새 하위 디렉터리로 옮긴 다음 코드를 다시 실행합니다.

## <a name="where-to-write-files"></a>파일을 쓰는 곳

학습 실험의 격리 때문에, 실행 중에 발생하는 파일에 대한 변경 내용은 사용자 환경 외부에서 유지되지 않을 수도 있습니다. 스크립트에서 컴퓨팅에 대해 로컬로 파일을 수정하는 경우, 변경 내용이 다음 실험 실행에서는 유지되지 않으며 클라이언트 머신에 자동으로 다시 전파되지도 않습니다. 따라서 첫 번째 실험을 실행하는 동안 변경된 내용은 두 번째 실험에 영향을 주지 않으며 영향을 주면 안 됩니다.

변경 내용을 쓸 때는 [OutputFileDatasetConfig 개체](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)를 사용하여 Azure Machine Learning 데이터 세트를 통해 파일을 스토리지에 쓰는 것이 좋습니다. [OutputFileDatasetConfig를 만드는 방법](how-to-train-with-datasets.md#where-to-write-training-output)을 참조하세요.

파일을 `./outputs` 및/또는 `./logs` 폴더에 써도 됩니다.

>[!Important]
> *outputs* 와 *logs* 라는 두 폴더는 Azure Machine Learning에서 별도로 처리합니다. 학습하는 동안 파일을 `./outputs` 및`./logs` 폴더에 쓰면, 파일은 실행 기록에 자동으로 업로드되므로 실행이 완료되면 해당 파일에 액세스할 수 있습니다.

* **상태 메시지나 채점 결과 같은 출력의 경우에는 실행 기록에서 아티팩트로 유지될 수 있도록** 파일을 `./outputs` 폴더에 쓰세요. 내용이 실행 기록에 업로드되면 대기 시간이 발생할 수 있으니, 이 폴더에 쓰는 파일의 수와 크기에 주의해야 합니다. 대기 시간이 중요하다면 파일을 데이터 저장소에 쓰는 것이 좋습니다.

* **작성한 파일을 실행 기록에 로그로 저장하려면** 파일을 `./logs` 폴더에 쓰세요. 로그는 실시간으로 업로드되므로 이 방법은 원격 실행에서 라이브 업데이트를 스트리밍하는데 적합합니다.

## <a name="next-steps"></a>다음 단계

* [스토리지에 있는 데이터에 액세스](how-to-access-data.md)하는 방법에 대해 자세히 알아보세요.

* [모델 학습 및 배포를 위한 컴퓨팅 대상 만들기](how-to-create-attach-compute-studio.md)에 대해 자세히 알아보세요.