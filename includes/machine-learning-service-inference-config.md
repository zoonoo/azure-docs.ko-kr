---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: ab31d45808a8c77c53b895643eec63952201d9e4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799979"
---
`inferenceconfig.json` 문서의 항목은 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 클래스에 대 한 매개 변수에 매핑됩니다. 다음 표에서는 JSON 문서의 엔터티 및 메서드에 대 한 매개 변수의 매핑에 대해 설명 합니다.

| JSON 엔터티 | 메서드 매개 변수 | 설명 |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | 이미지에 대해 실행할 코드를 포함 하는 로컬 파일의 경로입니다. |
| `runtime` | `runtime` | 이미지에 사용할 런타임입니다. 현재 지원 되는 런타임은 `spark-py` 및 `python`입니다. |
| `condaFile` | `conda_file` | 선택 사항입니다. 이미지에 사용할 Conda 환경 정의가 포함 된 로컬 파일의 경로입니다. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | 선택 사항입니다. 이미지를 설정할 때 실행할 추가 Docker 단계가 포함 된 로컬 파일의 경로입니다. |
| `sourceDirectory` | `source_directory` | 선택 사항입니다. 이미지를 만드는 모든 파일이 포함 된 폴더의 경로입니다. |
| `enableGpu` | `enable_gpu` | 선택 사항입니다. 이미지에서 GPU 지원을 사용할지 여부를 지정 합니다. GPU 이미지는 Azure Container Instances, Azure Machine Learning Compute, Azure Virtual Machines 및 Azure Kubernetes 서비스와 같은 Azure 서비스에서 사용 해야 합니다. 기본값은 False입니다. |
| `baseImage` | `base_image` | 선택 사항입니다. 기본 이미지로 사용 되는 사용자 지정 이미지입니다. 기본 이미지를 제공 하지 않는 경우 이미지는 제공 된 런타임 매개 변수를 기반으로 합니다. |
| `baseImageRegistry` | `base_image_registry` | 선택 사항입니다. 기본 이미지를 포함 하는 이미지 레지스트리 |
| `cudaVersion` | `cuda_version` | 선택 사항입니다. GPU 지원이 필요한 이미지에 대해 설치 하는 VERDA의 버전입니다. GPU 이미지는 Azure Container Instances, Azure Machine Learning Compute, Azure Virtual Machines 및 Azure Kubernetes 서비스와 같은 Azure 서비스에서 사용 해야 합니다. 지원 되는 버전은 9.0, 9.1 및 10.0입니다. `enable_gpu` 설정 된 경우 기본값은 9.1입니다. |
| `description` | `description` | 이미지에 대 한 설명입니다. |

다음 JSON은 CLI와 함께 사용 하기 위한 유추 구성의 예입니다.

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```