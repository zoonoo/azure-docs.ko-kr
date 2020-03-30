---
title: 작업 영역 데이터 내보내기 또는 삭제
titleSuffix: Azure Machine Learning
description: Azure 기계 학습 스튜디오, CLI, SDK 및 인증된 REST API를 사용하여 작업 영역을 내보내거나 삭제하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 4abef0146b4bf0cfaa254d196b0ca68f0d8ac883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218281"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Machine Learning 서비스 작업 영역 데이터 내보내기 또는 삭제

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning에서는 인증된 REST API를 사용하여 작업 영역 데이터를 내보내거나 삭제할 수 있습니다. 이 문서에서는 이러한 작업을 수행하는 방법에 대해 설명합니다.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>작업 영역 데이터 제어

Azure 기계 학습에 의해 저장된 제품 내 데이터는 Azure 기계 학습 스튜디오, CLI, SDK 및 인증된 REST API를 통해 내보내고 삭제할 수 있습니다. 원격 분석 데이터는 Azure 개인 정보 보호 포털을 통해 액세스할 수 있습니다. 

Azure Machine Learning에서 개인 데이터는 서비스와 일부 사용자 간 상호 작용의 실행 기록 문서 및 원격 분석 레코드의 사용자 정보로 구성됩니다.

## <a name="delete-workspace-data-with-the-rest-api"></a>REST API를 통해 작업 영역 데이터 삭제

데이터를 삭제하려면 HTTP DELETE 동사로 다음 API 호출을 만들 수 있습니다. 이러한 호출은 요청에 `Authorization: Bearer <arm-token>` 헤더를 포함하여 권한이 부여됩니다. 여기서 `<arm-token>`은 `https://management.core.windows.net/` 엔드포인트에 대한 AAD 액세스 토큰입니다.  

이 토큰을 받고 Azure 끝점을 호출하는 방법을 알아보려면 REST 사용을 참조하여 ML 리소스 및 [Azure REST API 설명서를](https://docs.microsoft.com/rest/api/azure/) [관리합니다.](how-to-manage-rest.md)  

다음 예제에서 {}의 텍스트를 관련된 리소스를 결정하는 인스턴스 이름으로 바꿉니다.

### <a name="delete-an-entire-workspace"></a>전체 작업 영역 삭제

다음 호출을 사용하여 전체 작업 영역을 삭제합니다.  
> [!WARNING]
> 모든 정보가 삭제되고 작업 영역은 더 이상 사용할 수 없습니다.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="delete-models"></a>모델 삭제

이 호출을 사용하여 모델 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

개별 모델은 다음을 통해 삭제할 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="delete-assets"></a>자산 삭제

다음 호출을 사용하여 자산 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

개별 자산은 다음을 통해 삭제할 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="delete-images"></a>이미지 삭제

다음 호출을 사용하여 이미지 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

개별 이미지는 다음을 통해 삭제할 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="delete-services"></a>서비스 삭제

다음 호출을 사용하여 서비스 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

개별 서비스는 다음을 통해 삭제할 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

## <a name="export-service-data-with-the-rest-api"></a>REST API를 통해 서비스 데이터 내보내기

데이터를 내보내려면 HTTP GET 동사로 다음 API 호출을 만들 수 있습니다. 이는 요청에 `Authorization: Bearer <arm-token>` 헤더를 포함하여 권한이 부여됩니다. 여기서 `<arm-token>`은 엔드포인트 `https://management.core.windows.net/`에 대한 AAD 액세스 토큰입니다.  

이 토큰을 받고 Azure 끝점을 호출하는 방법을 알아보려면 [REST 사용을 참조하여 ML 리소스](how-to-manage-rest.md) 및 Azure REST API [설명서를](https://docs.microsoft.com/rest/api/azure/)관리합니다.   

다음 예제에서 {}의 텍스트를 관련된 리소스를 결정하는 인스턴스 이름으로 바꿉니다.

### <a name="export-workspace-information"></a>작업 영역 정보 내보내기

다음 호출을 사용하여 모든 작업 영역의 목록을 가져옵니다.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01

개별 작업 영역에 대한 정보는 다음을 통해 가져올 수 있습니다.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="export-compute-information"></a>컴퓨팅 정보 내보내기

작업 영역에 연결된 모든 컴퓨팅 대상은 다음을 통해 가져올 수 있습니다.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01

단일 컴퓨팅 대상에 대한 정보는 다음을 통해 가져올 수 있습니다.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01

### <a name="export-run-history-data"></a>실행 기록 데이터 내보내기

다음 호출을 사용하여 모든 실험 및 해당 정보의 목록을 가져옵니다.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

특정 실험에 대한 모든 실행은 다음을 통해 가져올 수 있습니다.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

실행 기록 항목은 다음을 통해 얻을 수 있습니다.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

실험에 대한 모든 실행 메트릭은 다음을 통해 가져올 수 있습니다.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

단일 실행 메트릭은 다음을 통해 가져올 수 있습니다.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}

### <a name="export-artifacts"></a>아티팩트 내보내기

이 호출을 사용하여 아티팩트 및 해당 경로의 목록을 가져옵니다.

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}

### <a name="export-notifications"></a>알림 내보내기

다음 호출을 사용하여 저장된 작업의 목록을 가져옵니다.

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

단일 작업에 대한 알림은 다음을 통해 가져올 수 있습니다.

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>데이터 저장소 내보내기

다음 호출을 사용하여 데이터 저장소의 목록을 가져옵니다.

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

개별 데이터 저장소는 다음을 통해 가져올 수 있습니다.

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>모델 내보내기

이 호출을 사용하여 모델 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

개별 모델은 다음을 통해 얻을 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="export-assets"></a>자산 내보내기

다음 호출을 사용하여 자산 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

개별 자산은 다음을 통해 가져올 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="export-images"></a>이미지 내보내기

다음 호출을 사용하여 이미지 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

개별 이미지는 다음을 통해 가져올 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="export-services"></a>서비스 내보내기

다음 호출을 사용하여 서비스 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

개별 서비스는 다음을 통해 얻을 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

### <a name="export-pipeline-experiments"></a>파이프라인 실험 내보내기

개별 실험은 다음을 통해 가져올 수 있습니다.

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>파이프라인 그래프 내보내기

개별 그래프는 다음을 통해 가져올 수 있습니다.

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>파이프라인 모듈 내보내기

모듈은 다음을 통해 가져올 수 있습니다.

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>파이프라인 템플릿 내보내기

템플릿은 다음을 통해 가져올 수 있습니다.

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>파이프라인 데이터 원본 내보내기

데이터 원본은 다음을 통해 가져올 수 있습니다.

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-assets-in-the-designer"></a>디자이너의 자산 삭제

실험을 만든 디자이너에서 개별 자산을 삭제합니다.

1. 디자이너로 이동

    ![자산 삭제](./media/how-to-export-delete-data/delete-experiment.png)

1. 목록에서 삭제할 개별 파이프라인 초안을 선택합니다.

1. **삭제를 선택합니다.**

### <a name="delete-datasets-in-the-designer"></a>디자이너의 데이터 집합 삭제

디자이너에서 데이터 집합을 삭제하려면 Azure 포털 또는 저장소 탐색기를 사용하여 연결된 저장소 계정으로 이동하여 데이터 집합을 삭제합니다. 디자이너에서 데이터 집합을 등록 취소하는 것은 저장소의 참조점만 제거합니다.

## <a name="export-data-in-the-designer"></a>디자이너의 데이터 내보내기

실험을 만든 디자이너에서 추가한 데이터를 내보냅니다.

1. 왼쪽에서 데이터 **집합을**선택합니다.

1. 목록에서 내보낼 데이터 집합을 선택합니다.

    ![데이터 다운로드](./media/how-to-export-delete-data/unregister-dataset.png)
