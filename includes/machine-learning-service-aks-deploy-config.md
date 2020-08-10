---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: c71f35a06d904b45cb014d5199197220b57cf230
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79486124"
---
`deploymentconfig.json` 문서의 항목은 [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)에 대한 매개 변수에 매핑됩니다. 다음 표에서는 JSON 문서의 엔터티 및 메서드에 대한 매개 변수 간의 매핑에 대해 설명합니다.

| JSON 엔터티 | 메서드 매개 변수 | 설명 |
| ----- | ----- | ----- |
| `computeType` | 해당 없음 | 컴퓨팅 대상. AKS의 경우 값은 `aks`여야 합니다. |
| `autoScaler` | 해당 없음 | 자동 크기 조정에 대한 구성 요소를 포함합니다. 자동 크기 조정기 표를 참조하세요. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | 자동 크기 조정을 웹 서비스에 사용하도록 설정할지 여부입니다. `numReplicas` = `0`이면 `True`이고, 그렇지 않으면 `False`입니다. |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | 이 웹 서비스의 크기를 자동으로 조정할 때 사용할 최소 컨테이너 수입니다. 기본값은 `1`입니다. |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | 이 웹 서비스의 크기를 자동으로 조정할 때 사용할 최대 컨테이너 수입니다. 기본값은 `10`입니다. |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | 자동 크기 조정기에서 이 웹 서비스의 크기를 조정하려고 시도하는 빈도입니다. 기본값은 `1`입니다. |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | 자동 크기 조정기에서 이 웹 서비스에 대해 유지하려고 시도해야 하는 목표 사용률(백분율)입니다. 기본값은 `70`입니다. |
| `dataCollection` | 해당 없음 | 데이터 수집을 위한 구성 요소를 포함합니다. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | 모델 데이터 수집을 웹 서비스에 사용하도록 설정할지 여부입니다. 기본값은 `False`입니다. |
| `authEnabled` | `auth_enabled` | 키 인증을 웹 서비스에 사용하도록 설정할지 여부입니다. `tokenAuthEnabled` 및 `authEnabled`는 모두 `True`일 수 없습니다. 기본값은 `True`입니다. |
| `tokenAuthEnabled` | `token_auth_enabled` | 토큰 인증을 웹 서비스에 사용하도록 설정할지 여부입니다. `tokenAuthEnabled` 및 `authEnabled`는 모두 `True`일 수 없습니다. 기본값은 `False`입니다. |
| `containerResourceRequirements` | 해당 없음 | CPU 및 메모리 엔터티에 대한 컨테이너입니다. |
| &emsp;&emsp;`cpu` | `cpu_cores` | 이 웹 서비스에 할당할 CPU 코어 수입니다. 기본값은 `0.1`입니다 |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | 이 웹 서비스에 할당할 메모리 양(GB)입니다. 기본값은 `0.5`입니다 |
| `appInsightsEnabled` | `enable_app_insights` | Application Insights 로깅을 웹 서비스에 사용하도록 설정할지 여부입니다. 기본값은 `False`입니다. |
| `scoringTimeoutMs` | `scoring_timeout_ms` | 웹 서비스 호출에 대한 점수 매기기에 적용할 시간 제한입니다. 기본값은 `60000`입니다. |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | 이 웹 서비스에 대한 노드당 최대 동시 요청 수입니다. 기본값은 `1`입니다. |
| `maxQueueWaitMs` | `max_request_wait_time` | 503 오류가 반환되기 전까지 요청을 큐에서 유지하는 최대 시간(밀리초)입니다. 기본값은 `500`입니다. |
| `numReplicas` | `num_replicas` | 이 웹 서비스에 할당할 컨테이너 수입니다. 기본값은 없습니다. 이 매개 변수를 설정하지 않으면 기본적으로 자동 크기 조정기를 사용하도록 설정됩니다. |
| `keys` | 해당 없음 | 키에 대한 구성 요소를 포함합니다. |
| &emsp;&emsp;`primaryKey` | `primary_key` | 이 웹 서비스에 사용할 기본 인증 키입니다. |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | 이 웹 서비스에 사용할 보조 인증 키입니다. |
| `gpuCores` | `gpu_cores` | 이 웹 서비스에 할당할 GPU 코어 수(컨테이너 복제본당)입니다. 기본값은 1입니다. 정수 값만 지원합니다. |
| `livenessProbeRequirements` | 해당 없음 | 활동성 프로브 요구 사항에 대한 구성 요소를 포함합니다. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | 활동성 프로브를 수행하는 빈도(초)입니다. 기본값은 10초입니다. 최솟값은 1입니다. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | 컨테이너가 시작된 후 활동성 프로브가 시작되기 전까지의 시간(초)입니다. 기본값은 310입니다. |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | 활동성 프로브의 시간이 초과된 후 경과된 시간(초)입니다. 기본값은 2초입니다. 최솟값은 1입니다. |
| &emsp;&emsp;`successThreshold` | `success_threshold` | 활동성 프로브가 실패한 후 성공한 것으로 간주되는 최소 연속 성공 횟수입니다. 기본값은 1입니다. 최솟값은 1입니다. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Pod가 시작되고 활동성 프로브가 실패하면 Kubernetes에서 포기하기 전에 failureThreshold 시간을 시도합니다. 기본값은 3입니다. 최솟값은 1입니다. |
| `namespace` | `namespace` | 웹 서비스가 배포되는 Kubernetes 네임스페이스입니다. 최대 63자의 소문자 영숫자('a'-'z', '0'-'9') 및 하이픈('-') 문자입니다. 첫 번째 및 마지막 문자는 하이픈이 될 수 없습니다. |

다음 JSON은 CLI에서 사용할 수 있는 배포 구성의 예제입니다.

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```
