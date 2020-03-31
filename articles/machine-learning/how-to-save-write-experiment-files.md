---
title: '& 저장하려면 실험 파일 쓰기'
titleSuffix: Azure Machine Learning
description: 실험 입력 파일을 저장할 위치와 저장소 제한 오류 및 실험 대기 시간을 방지하기 위해 출력 파일을 작성하는 위치에 대해 알아봅니다.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 12a38b08fd429280f34b4eb02d4b72187b622261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79078442"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Azure 기계 학습 실험을 위해 파일을 저장하고 쓸 위치
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 입력 파일을 저장할 위치와 저장소 제한 오류 및 실험 대기 시간을 방지하기 위해 실험에서 출력 파일을 작성하는 위치를 알아봅니다.

시작 교육이 [계산 대상에서](how-to-set-up-training-targets.md)실행되면 외부 환경과 격리됩니다. 이 설계의 목적은 실험의 재현성과 이동성을 보장하는 것입니다. 동일한 스크립트를 동일한 또는 다른 계산 대상에서 두 번 실행하는 경우 동일한 결과를 받게 됩니다. 이 설계를 사용하면 계산 대상을 상태 비수기 계산 리소스로 처리할 수 있으며, 각 대상은 완료된 후 실행 중인 작업에 대한 선호도가 없습니다.

## <a name="where-to-save-input-files"></a>입력 파일을 저장할 위치

계산 대상 또는 로컬 컴퓨터에 대한 실험을 시작하기 전에 코드에서 실행해야 하는 종속성 파일 및 데이터 파일과 같이 해당 계산 대상에서 필요한 파일을 사용할 수 있는지 확인해야 합니다.

Azure Machine Learning은 전체 스크립트 폴더를 대상 계산 컨텍스트에 복사하여 학습 스크립트를 실행한 다음 스냅숏을 생성합니다. 실험 스냅샷의 스토리지 용량 한도는 300MB 및/또는 2000개 파일입니다.

이러한 이유로 다음을 권장합니다.

* **Azure 기계 학습 데이터 [스토어에](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)파일을 저장합니다.** 이렇게 하면 실험 대기 시간 문제를 방지할 수 있으며 원격 계산 대상에서 데이터에 액세스할 수 있는 이점이 있으며, 이는 Azure Machine Learning에서 인증 및 탑재를 관리한다는 것을 의미합니다. 데이터스토어를 소스 디렉터리로 지정하고 데이터스토어 문서의 Access 데이터에서 데이터스토어에 파일을 업로드하는 방법에 대해 자세히 [알아봅니다.](how-to-access-data.md)

* **데이터 파일과 종속성 스크립트만 필요하고 데이터스토어를 사용할 수 없는 경우** 교육 스크립트와 동일한 폴더 디렉터리에 파일을 배치합니다. 이 폴더를 `source_directory` 학습 스크립트또는 학습 스크립트를 호출하는 코드에서 직접 지정합니다.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>실험 스냅샷의 저장소 제한

실험의 경우 Azure Machine Learning은 실행을 구성할 때 제안하는 디렉터리에 따라 코드의 실험 스냅숏을 자동으로 만듭니다. 이렇게 하면 총 300MB 및/또는 2000개의 파일이 제한됩니다. 이 제한을 초과하면 다음과 같은 오류가 표시됩니다.

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

이 오류를 해결하려면 실험 파일을 데이터스토어에 저장합니다. 데이터스토어를 사용할 수 없는 경우 아래 표에서 가능한 대체 솔루션을 제공합니다.

실험&nbsp;설명|스토리지 제한 솔루션
---|---
데이터 스토어를 사용할 수 & 2000개 미만의 파일| 스냅샷 크기 제한을 재정의하여 <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> 파일의 수와 크기에 따라 몇 분 정도 걸릴 수 있습니다.
특정 스크립트 디렉터리를 사용해야 합니다.| 소스 `.amlignore` 코드의 일부가 아닌 실험 스냅샷에서 파일을 제외하도록 파일을 만듭니다. 파일 이름을 `.amlignore` 파일에 추가하고 교육 스크립트와 동일한 디렉터리에 배치합니다. 파일은 `.amlignore` `.gitignore` 파일과 동일한 [구문 및 패턴을](https://git-scm.com/docs/gitignore) 사용합니다.
파이프라인|각 단계에 대해 다른 하위 디렉터리 사용
Jupyter Notebook| `.amlignore` 파일을 만들거나 전자 필기장을 빈 새 하위 디렉터리로 이동하고 코드를 다시 실행합니다.

## <a name="where-to-write-files"></a>파일을 작성하는 위치

학습 실험이 격리되어 실행 중에 발생하는 파일의 변경 내용이 사용자 환경 외부에서 반드시 지속되는 것은 아닙니다. 스크립트가 로컬에서 계산할 파일을 수정하는 경우 다음 실험 실행에 대한 변경 내용이 유지되지 않으며 클라이언트 컴퓨터로 자동으로 다시 전파되지 않습니다. 따라서 첫 번째 실험 실행 중에 변경한 내용은 두 번째 실험에 영향을 미치지 않으며 영향을 주지 않아야 합니다.

변경 내용을 작성할 때 Azure 기계 학습 데이터 스토어에 파일을 작성하는 것이 좋습니다. [데이터 스토어의 데이터 액세스](how-to-access-data.md)를 참조하십시오.

데이터스토어가 필요하지 않은 경우 `./outputs` 및/또는 `./logs` 폴더에 파일을 씁니다.

>[!Important]
> 두 개의 폴더, *출력* 및 *로그,* Azure 기계 학습에 의해 특별 한 처리를 받을. 교육 중에 파일과`./outputs` `./logs` 폴더에 파일을 쓸 때 파일이 실행 기록에 자동으로 업로드되므로 실행이 완료되면 파일에 액세스할 수 있습니다.

* **상태 메시지 또는 점수 매기기 결과와 같은 출력의** `./outputs` 경우 폴더에 파일을 작성하여 실행 기록의 아티팩트로 유지합니다. 내용을 실행 기록을 업로드할 때 대기 시간이 발생할 수 있기 때문에 이 폴더에 기록된 파일의 수와 크기를 염두에 두어야 합니다. 대기 시간이 우려되는 경우 데이터스토어에 파일을 작성하는 것이 좋습니다.

* **기록된 파일을 실행 기록에 로그로 저장하려면 폴더에** `./logs` 파일을 씁니다. 로그는 실시간으로 업로드되므로 이 방법은 원격 실행에서 라이브 업데이트를 스트리밍하는 데 적합합니다.

## <a name="next-steps"></a>다음 단계

* 데이터 스토어에서 데이터에 액세스하는 방법에 대해 자세히 [알아봅니다.](how-to-access-data.md)

* [교육 목표를 설정하는 방법에](how-to-set-up-training-targets.md)대해 자세히 알아봅니다.
