---
title: REST를 사용하여 ML 리소스 관리
titleSuffix: Azure Machine Learning
description: REST API를 사용하여 Azure ML 리소스를 생성, 실행 및 삭제하는 방법
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/31/2020
ms.openlocfilehash: 419dbd998abc5cbd2da64a990e13d46f3fb2efbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77580631"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>REST를 사용하여 Azure ML 리소스 생성, 실행 및 삭제

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure ML 리소스를 관리하는 방법에는 여러 가지가 있습니다. [포털,](https://portal.azure.com/) [명령줄 인터페이스](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)또는 Python [SDK를](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)사용할 수 있습니다. 또는 REST API를 선택할 수 있습니다. REST API는 HTTP 동사를 표준 방식으로 사용하여 리소스를 생성, 검색, 업데이트 및 삭제합니다. REST API는 HTTP 요청을 할 수 있는 모든 언어 또는 도구와 함께 작동합니다. REST의 간단한 구조는 종종 스크립팅 환경과 MLOps 자동화에서 좋은 선택입니다. 

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 권한 부여 토큰 검색
> * 서비스 주체 인증을 사용하여 적절한 형식의 REST 요청을 만듭니다.
> * GET 요청을 사용하여 Azure ML의 계층적 리소스에 대한 정보를 검색합니다.
> * PUT 및 POST 요청을 사용하여 리소스 를 만들고 수정합니다.
> * DELETE 요청을 사용하여 리소스 정리 
> * 키 기반 권한 부여를 사용하여 배포된 모델 점수 매기기

## <a name="prerequisites"></a>사전 요구 사항

- 관리 권한이 있는 **Azure 구독입니다.** 이러한 구독이 없는 경우 [무료 또는 유료 개인 구독을](https://aka.ms/AMLFree) 사용해 보십시오.
- [Azure 기계 학습 작업 영역](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
- 관리 REST 요청은 서비스 주체 인증을 사용합니다. Azure 기계 [학습 리소스 및 워크플로에 대한 인증 설정의](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) 단계를 수행하여 작업 영역에서 서비스 주체를 만듭니다.
- **컬** 유틸리티. **컬** 프로그램은 리눅스 또는 UNIX 배포판용 [Windows 서브시스템에서](https://aka.ms/wslinstall/) 사용할 수 있습니다. PowerShell에서 **컬은** **Invoke-WebRequest의** 별칭이며. `curl -d "key=val" -X POST uri` `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri` 

## <a name="retrieve-a-service-principal-authentication-token"></a>서비스 주체 인증 토큰 검색

관리 REST 요청은 OAuth2 암시적 흐름으로 인증됩니다. 이 인증 흐름은 구독의 서비스 주체가 제공한 토큰을 사용합니다. 이 토큰을 검색하려면 다음이 필요합니다.

- 테넌트 ID(구독이 속한 조직을 식별)
- 클라이언트 ID(생성된 토큰과 연결됨)
- 고객 비밀(보호해야 하는 것)

[Azure Machine Learning 리소스 및 워크플로에 대한 인증 설정에서](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication)설명한 대로 서비스 주체 만들기에 대한 응답에서 이러한 값을 가져야 합니다. 회사 구독을 사용하는 경우 서비스 주체를 만들 수 있는 권한이 없을 수 있습니다. 이 경우 [무료 또는 유료 개인 구독을](https://aka.ms/AMLFree)사용해야 합니다.

토큰을 검색하려면 다음을 수행하십시오.

1. 터미널 창 열기
1. 명령줄에 다음 코드를 입력합니다.
1. 에 대한 `{your-tenant-id}`사용자 `{your-client-id}`고유의 `{your-client-secret}`값을 대체합니다. 이 문서 전체에서 곱슬 대괄호로 둘러싸인 문자열은 고유한 적절한 값으로 대체해야 하는 변수입니다.
1. 명령 실행

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

응답은 한 시간 동안 좋은 액세스 토큰을 제공해야합니다.

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

토큰을 사용하여 이후의 모든 관리 요청을 인증하므로 토큰을 기록해 둡을 확인합니다. 모든 요청에서 권한 부여 헤더를 설정하여 이렇게 합니다.

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

이 값은 토큰을 추가하기 전에 단일 공백을 포함하여 "Bearer" 문자열로 시작합니다.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>구독과 연결된 리소스 그룹 목록 가져옵니다.

구독과 연결된 리소스 그룹 목록을 검색하려면 다음을 실행합니다.

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

Azure에서 많은 REST API가 게시됩니다. 각 서비스 공급자는 자체 케이던스에서 API를 업데이트하지만 기존 프로그램을 중단하지 않고 업데이트합니다. 서비스 공급자는 `api-version` 인수를 사용하여 호환성을 보장합니다. 인수는 `api-version` 서비스마다 다릅니다. 예를 들어 기계 학습 서비스의 경우 현재 `2019-11-01`API 버전은 . 저장소 계정의 경우 `2019-06-01`. 키 자격 증명 모음의 `2019-09-01`경우 . 모든 REST 호출은 `api-version` 인수를 예상 값으로 설정해야 합니다. API가 계속 발전하는 경우에도 지정된 버전의 구문과 의미 체계에 의존할 수 있습니다. `api-version` 인수 없이 공급자에게 요청을 보내는 경우 응답에는 사람이 읽을 수 있는 지원되는 값 목록이 포함됩니다. 

위의 호출은 양식의 압축된 JSON 응답을 생성합니다. 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>작업 영역 및 리소스로 드릴다운

리소스 그룹의 작업 영역 집합을 검색하려면 다음을 실행하고 `{your-subscription-id}`대체합니다. `{your-resource-group}` `{your-access-token}` 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

다시 한 번 JSON 목록이 포함 되어 있는 목록이 포함 된 각 항목의 작업 영역을 자세히 설명 합니다.

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

작업 영역 내의 리소스로 작업하려면 일반 **management.azure.com** 서버에서 작업 영역의 위치와 관련된 REST API 서버로 전환합니다. 위의 JSON `discoveryUrl` 응답에서 키의 값을 기록합니다. 해당 URL을 받으면 다음과 같은 응답을 받게 됩니다.

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

응답 값은 `api` 추가 요청에 사용할 서버의 URL입니다. 예를 들어 실험을 나열하려면 다음 명령을 보냅니다. 응답 값(예: `centralus.api.azureml.ms`)으로 바꿉니다. `regional-api-server` `api` 또한 `your-subscription-id`대체 `your-resource-group` `your-workspace-name`: `your-access-token` " 및 평소와 같이 :

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

마찬가지로 작업 영역에서 등록된 모델을 검색하려면 다음을 보내십시오.

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

실험을 나열하려면 경로를 나열하는 `history/v1.0` 동안 경로가 시작됩니다. `modelmanagement/v1.0` REST API는 각각 고유한 경로를 가진 여러 운영 그룹으로 나뉩니다. 아래 링크의 API 참조 문서는 다양한 작업에 대한 작업, 매개 변수 및 응답 코드를 나열합니다.

|영역|경로|참고|
|-|-|-|
|아티팩트|아티팩트/v2.0/|[REST API 참조](https://docs.microsoft.com/rest/api/azureml/artifacts)|
|데이터 저장소|데이터 스토어/v1.0/|[REST API 참조](https://docs.microsoft.com/rest/api/azureml/datastores)|
|하이퍼 매개 변수 조정|하이퍼드라이브/v1.0/|[REST API 참조](https://docs.microsoft.com/rest/api/azureml/hyperparametertuning)|
|모델|모델 관리/v1.0/|[REST API 참조](https://docs.microsoft.com/rest/api/azureml/modelsanddeployments/mlmodels)|
|실행 기록|실행/v1.0/ 및 기록/v1.0/|[REST API 참조](https://docs.microsoft.com/rest/api/azureml/runs)|

다음과 같은 일반적인 패턴을 사용하여 REST API를 탐색할 수 있습니다.

|URL 구성 요소|예제|
|-|-|
| https://| |
| 지역 API 서버/ | centralus.api.azureml.ms/ |
| 작업 경로/ | 히스토리/v1.0/ |
| 구독/{구독 ID}/ | 구독/abcde123-abab-1234-0123456789abc/ |
| 리소스 그룹/{리소스 그룹}/ | 리소스 그룹/마이리소스 그룹/ |
| 공급자/운영 공급자/ | 공급자/마이크로소프트.머신러닝서비스/ |
| 공급자-리소스 경로/ | 작업 영역/ML워크 스페이스/마이워크스페이스/첫 번째 실험/실행/1/ |
| 작업 끝점/ | 아티팩트/메타데이터/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>PUT 및 POST 요청을 사용하여 리소스 생성 및 수정

REST API는 GET 동사를 사용하여 리소스 를 검색하는 것 외에도 ML 솔루션을 학습, 배포 및 모니터링하는 데 필요한 모든 리소스를 생성할 수 있도록 지원합니다. 

ML 모델을 교육하고 실행하려면 계산 리소스가 필요합니다. 다음과 같은 작업 영역의 계산 리소스를 나열할 수 있습니다. 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

명명된 계산 리소스를 만들거나 덮어쓰려면 PUT 요청을 사용합니다. 다음에서는 `your-subscription-id`이제 익숙한 대체에 추가하여 에 대한 대체 `your-resource-group`및 `your-workspace-name` `your-access-token` `your-compute-name`에 대한 `location`값 , `vmSize`" `vmPriority` `scaleSettings`, `adminUserName`및 `adminUserPassword`. 기계 학습 컴퓨팅 [- SDK 참조 만들기 또는 업데이트의](https://docs.microsoft.com/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate)참조에 지정된 대로 다음 명령은 30분 후에 축소되는 전용 단일 노드 Standard_D1(기본 CPU 계산 리소스)를 만듭니다.

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> Windows 터미널에서 JSON 데이터를 보낼 때 이중 견적 기호를 이스케이프해야 할 수 있습니다. 즉, 텍스트와 `"location"` 같은 `\"location\"`텍스트가 됩니다. 

성공적인 요청은 `201 Created` 응답을 얻지만 이 응답은 단순히 프로비저닝 프로세스가 시작되었다는 것을 의미합니다. 성공적으로 완료를 확인하려면 설문 조사(또는 포털 사용)가 필요합니다.

### <a name="create-an-experimental-run"></a>실험 실행 만들기

실험 내에서 실행을 시작하려면 학습 스크립트 및 관련 파일과 실행 정의 JSON 파일이 포함된 zip 폴더가 필요합니다. zip 폴더에는 루트 디렉토리에 Python 항목 파일이 있어야 합니다. 예를 들어 다음과 같은 사소한 파이썬 프로그램을 **train.zip이라는**폴더에 압축합니다.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

이 다음 스니펫을 **definition.json로 저장합니다.** 방금 압축한 Python 파일의 이름과 일치하는 "스크립트" 값을 확인합니다. 사용 가능한 계산 리소스의 이름과 일치하는 "대상" 값을 확인합니다. 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

콘텐츠를 사용하여 `multipart/form-data` 서버에 이러한 파일을 게시합니다.

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

성공적인 POST 요청은 `200 OK` 생성된 실행의 식별자를 포함하는 응답 본문과 함께 상태를 생성합니다.

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

이제 익숙해져야 하는 REST-ful 패턴을 사용하여 실행을 모니터링할 수 있습니다.

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>더 이상 필요하지 않은 리소스 삭제

전부는 아니지만 일부 리소스는 DELETE 동사를 지원합니다. 삭제 사용 사례에 대 한 REST API에 커밋 하기 전에 [API 참조를](https://docs.microsoft.com/rest/api/azureml/) 확인 합니다. 예를 들어 모델을 삭제하려면 다음을 사용할 수 있습니다.

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>REST를 사용하여 배포된 모델 점수 매기기

서비스 주체로 인증되도록 모델을 배포할 수 있지만 대부분의 클라이언트 대면 배포는 키 기반 인증을 사용합니다. Studio의 **끝점** 탭에서 배포 페이지에서 적절한 키를 찾을 수 있습니다. 동일한 위치에 끝점의 점수 URI가 표시됩니다. 모델의 입력은 다음과 같은 `data`JSON 배열로 모델링되어야 합니다.

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>REST를 사용하여 작업 영역 만들기 

모든 Azure ML 작업 영역에는 관리가 활성화된 컨테이너 레지스트리, 키 자격 증명 모음, 응용 프로그램 인사이트 리소스 및 저장소 계정의 네 가지 다른 Azure 리소스에 대한 종속성이 있습니다. 이러한 리소스가 존재할 때까지 작업 영역을 만들 수 없습니다. 이러한 각 리소스를 만드는 세부 정보는 REST API 참조를 참조하십시오.

작업 영역을 만들려면 다음과 유사한 호출을 `management.azure.com`넣을 수 있습니다. 이 호출에서는 많은 수의 변수를 설정해야 하지만 이 문서에서 설명한 다른 호출과 구조적으로 동일합니다. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

`202 Accepted` 응답을 받아야 하며 반환된 헤더에서 URI를 `Location` 받아야 합니다. 종속 리소스 중 하나에 문제가 있는 경우(예: 컨테이너 레지스트리에서 관리자 액세스를 사용하도록 설정하지 않은 경우) 유용한 디버깅 정보를 포함하여 배포에 대한 정보를 얻으려면 이 URI를 얻을 수 있습니다. 

## <a name="troubleshooting"></a>문제 해결

### <a name="resource-provider-errors"></a>리소스 공급자 오류

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>작업 영역 이동

> [!WARNING]
> Azure Machine Learning 작업 영역을 다른 구독으로 이동하거나 소유 구독을 새 테넌트로 이동하는 것은 지원되지 않습니다. 이렇게 하면 오류가 발생할 수 있습니다.

### <a name="deleting-the-azure-container-registry"></a>Azure 컨테이너 레지스트리 삭제

Azure 기계 학습 작업 영역은 일부 작업에 Azure 컨테이너 레지스트리(ACR)를 사용합니다. ACR 인스턴스가 처음 필요할 때 자동으로 ACR 인스턴스가 생성됩니다.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>다음 단계

- 전체 [AzureML REST API 참조를](https://docs.microsoft.com/rest/api/azureml/)살펴봅시다.
- Studio & 디자이너를 사용하여 [디자이너(미리 보기)를 사용하여 자동차 가격을 예측하는](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score)방법에 대해 알아봅니다.
- [Jupyter 노트북을 통해 Azure 기계 학습을 탐색합니다.](https://docs.microsoft.com/azure//machine-learning/samples-notebooks)
