---
title: 작업 영역 데이터 내보내기 또는 삭제
titleSuffix: Azure Machine Learning
description: Azure Machine Learning studio, CLI, SDK 및 인증 된 REST Api를 사용 하 여 작업 영역을 내보내거나 삭제 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b27675f152132a015a6dc6f5aea35aff036e1a63
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897547"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Machine Learning 서비스 작업 영역 데이터 내보내기 또는 삭제



Azure Machine Learning에서 포털의 그래픽 인터페이스 또는 Python SDK를 사용 하 여 작업 영역 데이터를 내보내거나 삭제할 수 있습니다. 이 문서에서는 두 가지 옵션에 대해 설명 합니다.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>작업 영역 데이터 제어

Azure Machine Learning에 의해 저장 된 제품 내 데이터는 내보내기 및 삭제에 사용할 수 있습니다. Azure Machine Learning studio, CLI 및 SDK를 사용 하 여 내보내기 및 삭제할 수 있습니다. 원격 분석 데이터는 Azure 개인 정보 보호 포털을 통해 액세스할 수 있습니다. 

Azure Machine Learning 개인 데이터는 실행 기록 문서의 사용자 정보로 구성 됩니다. 

## <a name="delete-high-level-resources-using-the-portal"></a>포털을 사용 하 여 상위 수준 리소스 삭제

작업 영역을 만들 때 Azure는 리소스 그룹 내에 많은 리소스를 만듭니다.

- 작업 영역 자체
- 스토리지 계정
- 컨테이너 레지스트리
- Application Insights 인스턴스
- 주요 자격 증명 모음

이러한 리소스는 목록에서 선택 하 고 **삭제** 를 선택 하 여 삭제할 수 있습니다. 

:::image type="content" source="media/how-to-export-delete-data/delete-resource-group-resources.png" alt-text="삭제 아이콘이 강조 표시 된 포털의 스크린샷":::

개인 사용자 정보를 포함할 수 있는 실행 기록 문서는의 하위 폴더에 있는 blob storage의 저장소 계정에 저장 됩니다 `/azureml` . 포털에서 데이터를 다운로드 하 고 삭제할 수 있습니다.

:::image type="content" source="media/how-to-export-delete-data/storage-account-folders.png" alt-text="포털 내에서 저장소 계정의 azureml 디렉터리 스크린샷":::

## <a name="export-and-delete-machine-learning-resources-using-azure-machine-learning-studio"></a>Azure Machine Learning studio를 사용 하 여 기계 학습 리소스 내보내기 및 삭제

Azure Machine Learning studio에서는 노트북, 데이터 집합, 모델 및 실험 등의 기계 학습 리소스에 대 한 통합 보기를 제공 합니다. Azure Machine Learning studio는 데이터 및 실험의 레코드 유지를 강조 합니다. 파이프라인 및 계산 리소스와 같은 계산 리소스는 브라우저를 사용 하 여 삭제할 수 있습니다. 이러한 리소스에 대해 문제의 리소스로 이동 하 고 **삭제**를 선택 합니다. 

데이터 집합의 등록을 취소 하 고 실험을 보관할 수 있지만 이러한 작업은 데이터를 삭제 하지 않습니다. 데이터를 완전히 제거 하려면 데이터 집합 및 실행 데이터를 저장소 수준에서 삭제 해야 합니다. 이전에 설명한 대로 저장소 수준에서 삭제는 포털을 사용 하 여 수행 됩니다.

스튜디오를 사용 하 여 실험 실행에서 학습 아티팩트를 다운로드할 수 있습니다. 관심이 있는 **실험** 및 **실행** 을 선택 합니다. **출력 + 로그** 를 선택 하 고 다운로드 하려는 특정 아티팩트로 이동 합니다. ... **및** **다운로드**를 선택 합니다.

원하는 **모델로** 이동 하 고 **다운로드**를 선택 하 여 등록 된 모델을 다운로드할 수 있습니다. 

:::image type="contents" source="media/how-to-export-delete-data/model-download.png" alt-text="다운로드 옵션이 강조 표시 된 스튜디오 모델 페이지의 스크린샷":::

## <a name="export-and-delete-resources-using-the-python-sdk"></a>Python SDK를 사용 하 여 리소스 내보내기 및 삭제

다음을 사용 하 여 특정 실행의 출력을 다운로드할 수 있습니다. 

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['my-experiment']
run = next(run for run in ex.get_runs() if run.id == run_id)
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

Python SDK를 사용 하 여 다음 machine learning 리소스를 삭제할 수 있습니다. 

| 형식 | 함수 호출 | 참고 | 
| --- | --- | --- |
| `Workspace` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#&preserve-view=truedelete-delete-dependent-resources-false--no-wait-false-) | `delete-dependent-resources`삭제를 계단식으로 배열 하는 데 사용 |
| `Model` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#&preserve-view=truedelete--) | | 
| `ComputeTarget` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#&preserve-view=truedelete--) | |
| `WebService` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true) | | 

