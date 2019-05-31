---
title: 저장소 제한 방지 및 대기 시간 입력 및 출력 디렉터리를 사용 하 여 실험
description: 이 문서에서는 실험 입력된 파일을 저장할 위치 및 저장소 제한 오류를 방지 하 고 대기 시간을 실험에 대 한 출력 파일을 쓸 위치를 알아봅니다.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 05/28/2019
ms.openlocfilehash: 28d8c47db8ea9c8a51bc8e9deb0a689eb0b20177
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392913"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Azure Machine Learning 실험에 대 한 파일을 저장 하 고 작성 하는 경우

이 문서에서는 입력된 파일을 저장할 위치 및 저장소를 방지 하려면 실험에서 출력 파일 오류 제한 및 대기 시간을 실험에 기록할 수 있는 위치를 알아봅니다.

교육 시작 실행 하는 경우는 [계산 대상](how-to-set-up-training-targets.md), 외부 환경에서 격리 됩니다. 이 디자인의 목적은 재현성 및 실험의 이식성을 보장 하는 것입니다. 동일한 스크립트를 두 번 실행 하는 경우를 동일한 또는 다른 계산 대상으로 동일한 결과 수신 합니다. 이 디자인에서는 계산 대상으로 작업 완료 된 후 실행에 대 한 선호도 없이 상태 비저장 계산 리소스로 처리할 수 있습니다.

## <a name="where-to-save-input-files"></a>입력된 파일을 저장할 위치

계산 대상 또는 로컬 컴퓨터에서 실험을 시작 하기 전에 필요한 파일 종속성 파일과 데이터 파일에 코드를 실행 해야 합니다. 예: 해당 계산 대상으로 사용 가능한 지 확인 해야 합니다.

Azure Machine Learning 스크립트 전체 폴더를 대상 계산 컨텍스트로 복사 하 여 학습 스크립트를 실행 하 고 스냅숏을 만듭니다. 실험 스냅숏에 대해서는 저장소 제한은 300MB 및/또는 2000 파일입니다.

따라서 것이 좋습니다.

* **Azure Machine Learning에 사용자 파일을 저장할 [데이터 저장소](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)합니다.** 이 실험 대기 시간 문제를 방지 하 고 인증 및 Azure Machine Learning 서비스에서 관리 되는 원격 계산 대상에서 데이터에 액세스 하는 이점이 있습니다. 원본 디렉터리를로 데이터 저장소를 지정 하 고 파일의 데이터 저장소에 업로드 하는 방법에 대 한 자세한 정보는 [에 데이터 저장소에서 데이터에 액세스](how-to-access-data.md) 문서.

* **하면 몇 가지 데이터 파일 및 종속성 스크립트 및 데이터 저장소를 사용할 수 없는 경우** 학습 스크립트와 같은 폴더 디렉터리에서 파일을 배치 합니다. 이 폴더를 지정 하 `source_directory` 학습 스크립트에서 직접 또는 학습 스크립트를 호출 하는 코드입니다.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>실험 스냅숏 저장소 제한

실험에 대 한 Azure Machine Learning 실험 스냅숏을 실행을 구성할 때 제안 디렉터리 내용을 바탕으로 코드를 자동으로 만듭니다. 이 총 제한인 300MB 및/또는 2000 파일. 이 한도 초과 하면 다음 오류가 표시 됩니다.

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

이 오류를 해결 하려면 데이터 저장소에 실험 파일을 저장 합니다. 데이터 저장소를 사용할 수 없는 경우는 아래 표에서 가능한 대체 솔루션을 제공 합니다.

실험&nbsp;설명|저장소 제한 솔루션
---|---
2000 보다 작은 파일 및 데이터 저장소를 사용할 수 없습니다| 스냅숏 크기 제한으로 재정의 <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> 파일의 크기와 수에 따라 몇 분 정도 걸릴 수 있습니다.
특정 스크립트 디렉터리를 사용 해야 합니다.| 확인을 `.amlignore` 파일을 소스 코드의 일부분이 아닌 실험 스냅숏에서 파일을 제외 합니다. 파일 이름을 추가 합니다 `.amlignore` 파일과 학습 스크립트와 같은 디렉터리에 넣습니다. 합니다 `.amlignore` 파일을 사용 하 여 동일한 [구문과 패턴](https://git-scm.com/docs/gitignore) 로 `.gitignore` 파일입니다.
파이프라인|각 단계에 대 한 다른 하위 디렉터리가 사용
Jupyter Notebook| 만들기는 `.amlignore` 파일 전자 필기장을 새로 만든 빈을 하위 디렉터리로 이동 하 고 코드를 다시 실행 합니다.

## <a name="where-to-write-files"></a>파일을 쓸 위치

학습 실험의 격리로 인해 실행 하는 동안 발생 하는 파일 변경 내용이 유지 되지 않습니다 반드시 환경 외부에서. 계산에 대해 로컬 파일을 수정 하는 스크립트 실행, 다음 실험에 대 한 변경 내용이 유지 되지 않습니다 되며 해당 하는 전파 되지 클라이언트 컴퓨터에 다시 자동으로. 따라서 변경 중 첫 번째 실험 실행된 하지 않으며 두 번째의 영향을 미치지 않습니다.

변경 내용을 작성 하는 경우는 Azure Machine Learning 데이터 저장소에 파일을 작성 하는 것이 좋습니다. 참조 [에 데이터 저장소에서 데이터에 액세스](how-to-access-data.md)합니다.

데이터 저장소, 필요한 경우 파일을 작성 합니다 `./outputs` 및/또는 `./logs` 폴더입니다.

>[!Important]
> 두 폴더 *출력* 하 고 *로그*, Azure Machine Learning에서 별도로 처리 합니다. 파일을 작성 하는 경우 학습 중`./outputs` 고`./logs` 폴더, 파일을 자동으로 업로드 하 여 실행된 기록을 실행이 완료 되 면 해당에 액세스할 수 있도록 합니다.

* **예: 상태 메시지 또는 점수 매기기 결과 출력용** 파일을 쓰기는 `./outputs` 폴더, 실행된 기록에서 아티팩트를 유지 하기. 실행 기록을 내용을 업로드 되 면 대기 시간이 발생할 수 있습니다이 폴더에 기록 된 파일의 크기와 수에 주의 수입니다. 중요 한 대기 시간을 사용 하는 경우 데이터 저장소에 파일을 쓰는 것이 좋습니다.

* **실행 기록에서 로그로 작성 된 파일을 저장할** 파일을 쓰기 `./logs` 폴더입니다. 이 메서드는 원격 실행에서 라이브 업데이트를 스트리밍에 적합 하므로 실시간으로 로그 업로드 됩니다.

## <a name="next-steps"></a>다음 단계

* 에 대해 자세히 알아보세요 [에 데이터 저장소에서 데이터에 액세스](how-to-access-data.md)합니다.

* 에 대해 자세히 알아보세요 [교육 대상을 설정 하는 방법을](how-to-set-up-training-targets.md)합니다.
