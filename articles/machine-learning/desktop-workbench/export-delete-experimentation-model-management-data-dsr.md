---
title: 실험 또는 모델 관리 데이터 내보내기 또는 삭제 - Azure Machine Learning | Microsoft Docs
description: Azure Machine Learning에서 Azure Portal, CLI, SDK 및 인증된 REST API를 통해 실험 또는 모델 관리와 관련된 계정 데이터를 내보내거나 삭제할 수 있습니다. 이 문서는 방법을 안내합니다.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
manager: haining
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 7db37865c99908e0fd44be3ec04a8493d190e941
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833515"
---
# <a name="export-or-delete-your-experimentation-or-model-management-data-in-machine-learning"></a>Machine Learning에서 실험 또는 모델 관리 데이터 내보내기 또는 삭제

Azure Machine Learning에서 인증된 REST API를 통해 실험 또는 모델 관리와 관련된 계정 데이터를 내보내거나 삭제할 수 있습니다. 이 문서는 그 방법을 안내합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-account-data"></a>계정 데이터 제어
Azure Machine Learning 실험 및 모델 관리에 의해 저장된 제품 내 데이터는 Azure Portal, CLI, SDK 및 인증된 REST API를 통해 내보내고 삭제할 수 있습니다. 원격 분석 데이터는 Azure Privacy 포털을 통해 액세스할 수 있습니다. 

Azure Machine Learning에서 개인 데이터는 서비스와 일부 사용자 간 상호 작용의 실행 기록 문서 및 원격 분석 레코드의 사용자 정보로 구성됩니다.

## <a name="delete-account-data-with-the-rest-api"></a>REST API를 통해 계정 데이터 삭제 

데이터를 삭제하려면 HTTP DELETE 동사로 다음 API 호출을 만들 수 있습니다. 이는 요청에 `Authorization: Bearer <arm-token>` 헤더를 포함하여 권한이 부여됩니다. 여기서 `<arm-token>`은 엔드포인트 `https://management.core.windows.net/`에 대한 AAD 액세스 토큰입니다.  

이 토큰을 가져오고 Azure 엔드포인트를 호출하는 방법을 알아보려면 [Azure REST API 설명서](https://docs.microsoft.com/rest/api/azure/)를 참조하세요.  

다음 예제에서 {}의 텍스트를 관련된 리소스를 결정하는 인스턴스 이름으로 바꿉니다.

## <a name="delete-from-a-hosting-account"></a>호스팅 계정에서 삭제

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningModelManagement/accounts/{account-name}?api-version=2017-09-01-preview      

## <a name="delete-from-the-model-management-service"></a>모델 관리 서비스에서 삭제

### <a name="model-document"></a>모델 문서
이 호출을 사용하여 모델 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

개별 모델은 다음을 통해 삭제할 수 있습니다.  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview

### <a name="manifest-document"></a>매니페스트 문서
이 호출을 사용하여 모든 매니페스트 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

개별 매니페스트는 다음을 통해 삭제할 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="service-documents"></a>서비스 문서
이 호출을 사용하여 모든 서비스 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

개별 서비스는 다음을 통해 삭제할 수 있습니다.    

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="image-document"></a>이미지 문서
이 호출을 사용하여 모든 이미지 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

개별 이미지는 다음을 통해 삭제할 수 있습니다.  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview

## <a name="delete-run-history-artifact-and-notification-data"></a>실행 기록, 아티팩트 및 알림 데이터 삭제
프로젝트에 대한 실행 기록, 아티팩트 및 알림 저장소는 해당 프로젝트 문서를 삭제한 후 모두 삭제됩니다.

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview
    
## <a name="delete-from-experimentation-account-resource-provider"></a>실험 계정 리소스 공급자에서 삭제
프로젝트 문서는 다음을 통해 삭제됩니다.

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview

작업 영역 문서는 다음을 통해 삭제됩니다.

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}?api-version=2017-05-01-preview

전체 실험 계정은 다음을 통해 삭제됩니다.
    
    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}?api-version=2017-05-01-preview


## <a name="export-service-data-with-the-rest-api"></a>REST API를 통해 서비스 데이터 내보내기
데이터를 내보내려면 HTTP GET 동사로 다음 API 호출을 만들 수 있습니다. 이는 요청에 `Authorization: Bearer <arm-token>` 헤더를 포함하여 권한이 부여됩니다. 여기서 `<arm-token>`은 엔드포인트 `https://management.core.windows.net/`에 대한 AAD 액세스 토큰입니다.  

이 토큰을 가져오고 Azure 엔드포인트를 호출하는 방법을 알아보려면 [Azure REST API 설명서](https://docs.microsoft.com/rest/api/azure/)를 참조하세요.   

다음 예제에서 {}의 텍스트를 관련된 리소스를 결정하는 인스턴스 이름으로 바꿉니다.

## <a name="export-hosting-account-data"></a>호스팅 계정을 데이터 내보내기

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningModelManagement/accounts/{accountName}?api-version=2017-09-01-preview     

## <a name="export-model-management-service-data"></a>모델 관리 서비스 데이터 내보내기
### <a name="model-document"></a>모델 문서

이 호출을 사용하여 모델 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

개별 모델은 다음을 통해 얻을 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview 

### <a name="manifests"></a>매니페스트
이 호출을 사용하여 모든 매니페스트 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

개별 매니페스트는 다음을 통해 얻을 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="services"></a>Services
이 호출을 사용하여 모든 서비스 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

개별 서비스는 다음을 통해 얻을 수 있습니다. 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="images"></a>이미지
이 호출을 사용하여 모든 이미지 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

개별 서비스는 다음을 통해 얻을 수 있습니다. 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview     

## <a name="export-compute-data"></a>계산 데이터 내보내기
### <a name="compute-clusters"></a>계산 클러스터
이 호출을 사용하여 모든 계산 클러스터 및 해당 이름의 목록을 가져옵니다.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

개별 클러스터는 다음을 통해 얻을 수 있습니다.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{compute-name}?api-version=2018-03-01-preview

### <a name="operationalization-clusters"></a>클러스터 운영화
이 호출을 사용하여 모든 클러스터 및 해당 이름의 목록을 가져옵니다.

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters?api-version=2017-06-01-preview

개별 클러스터는 다음을 통해 얻을 수 있습니다.

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters/{clusterName}?api-version=2017-06-01-preview

## <a name="export-run-history-data"></a>실행 기록 데이터 내보내기
이 호출을 사용하여 모든 실행 및 해당 ID의 목록을 가져옵니다.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs

이 호출을 사용하여 모든 실험 및 해당 ID의 목록을 가져옵니다.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments

실행 기록 항목은 다음을 통해 얻을 수 있습니다.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}

실행 메트릭 항목은 다음을 통해 얻을 수 있습니다.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runmetrics

실행 실험은 다음을 통해 얻을 수 있습니다.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments/{experimentId}    

실행 기록 아티팩트:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts

실행 기록 아티팩트 URI:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacturi?name={artifactName}

## <a name="export-artifacts"></a>아티팩트 내보내기
이 호출을 사용하여 자산 및 해당 이름의 목록을 가져옵니다.

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets

이 호출을 사용하여 아티팩트 및 해당 경로의 목록을 가져옵니다.

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/origins/{origin}/containers/{runId}
        
        
### <a name="artifact-contents"></a>아티팩트 콘텐츠

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/contentinfo/ExperimentRun/{runId}/{artifactPath}

### <a name="artifact-documents"></a>아티팩트 문서

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts
        
### <a name="assets"></a>자산

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets/name/{name}

## <a name="export-notifications"></a>알림 내보내기

1. [Azure Portal의 사용자 섹션](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/)으로 이동한 다음, **이름** 열에서 사용자를 선택합니다. 
2. **개체 ID**를 확인하고 다음 호출에서 사용합니다.     

        https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/users/{objectId}/jobs

## <a name="export-experimentation-account-information"></a>실험 계정 정보 내보내기
### <a name="experimentation-account-information"></a>실험 계정 정보

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}?api-version=2017-05-01-preview
        
### <a name="workspace-information"></a>작업 영역 정보

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}?api-version=2017-05-01-preview  

### <a name="project-information"></a>프로젝트 정보

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}?api-version=2017-05-01-preview
