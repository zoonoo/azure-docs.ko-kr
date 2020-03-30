---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: c71f35a06d904b45cb014d5199197220b57cf230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486124"
---
문서의 `deploymentconfig.json` 항목은 [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)매개 변수에 매핑됩니다. 다음 표에서는 JSON 문서의 엔터티와 메서드의 매개 변수 간의 매핑에 대해 설명합니다.

| JSON 엔티티 | 메서드 매개 변수 | 설명 |
| ----- | ----- | ----- |
| `computeType` | 해당 없음 | 컴퓨팅 대상. AKS의 경우 값은 `aks`값이어야 합니다. |
| `autoScaler` | 해당 없음 | 자동 크기 조정을 위한 구성 요소가 포함되어 있습니다. 자동 크기 조정기 테이블을 참조하십시오. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | 웹 서비스에 대한 자동 크기 조정을 사용하도록 설정할지 여부입니다. `numReplicas`  = . `0` `True` 그렇지 `False`않으면 . |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | 이 웹 서비스를 자동 크기 조정할 때 사용할 최소 컨테이너 수입니다. 기본값은 `1`. |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | 이 웹 서비스의 자동 크기 조정 시 사용할 최대 컨테이너 수입니다. 기본값은 `10`. |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | 자동 크기 조정기에서 이 웹 서비스의 크기를 조정하려고 시도하는 빈도입니다. 기본값은 `1`. |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | 자동 크기 조정기에서 이 웹 서비스에 대해 유지 관리하려고 시도해야 하는 대상 사용률(100점 만점)입니다. 기본값은 `70`. |
| `dataCollection` | 해당 없음 | 데이터 수집을 위한 구성 요소를 포함합니다. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | 웹 서비스에 대한 모델 데이터 수집을 사용하도록 설정할지 여부입니다. 기본값은 `False`. |
| `authEnabled` | `auth_enabled` | 웹 서비스에 대한 키 인증을 사용하도록 설정할지 여부입니다. 둘 `tokenAuthEnabled` `authEnabled` 다 `True`될 수 없습니다. 기본값은 `True`. |
| `tokenAuthEnabled` | `token_auth_enabled` | 웹 서비스에 대한 토큰 인증을 사용하도록 설정할지 여부입니다. 둘 `tokenAuthEnabled` `authEnabled` 다 `True`될 수 없습니다. 기본값은 `False`. |
| `containerResourceRequirements` | 해당 없음 | CPU 및 메모리 엔터티에 대한 컨테이너입니다. |
| &emsp;&emsp;`cpu` | `cpu_cores` | 이 웹 서비스에 할당할 CPU 코어 수입니다. 기본값`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | 이 웹 서비스에 할당할 메모리 양(GB)입니다. 기본`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | 웹 서비스에 대한 응용 프로그램 인사이트 로깅을 사용하도록 설정할지 여부입니다. 기본값은 `False`. |
| `scoringTimeoutMs` | `scoring_timeout_ms` | 웹 서비스에 대한 호출 점수를 매기기 위해 적용하는 시간 시간 입니다. 기본값은 `60000`. |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | 이 웹 서비스의 노드당 최대 동시 요청입니다. 기본값은 `1`. |
| `maxQueueWaitMs` | `max_request_wait_time` | 503 오류가 반환되기 전에 요청이 대기열에 머무르는 최대 시간(밀리초)입니다. 기본값은 `500`. |
| `numReplicas` | `num_replicas` | 이 웹 서비스에 할당할 컨테이너 수입니다. 기본값은 없습니다. 이 매개 변수가 설정되지 않은 경우 기본적으로 자동 크기 조정기가 활성화됩니다. |
| `keys` | 해당 없음 | 키에 대한 구성 요소가 포함되어 있습니다. |
| &emsp;&emsp;`primaryKey` | `primary_key` | 이 웹 서비스에 사용할 기본 인증 키 |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | 이 웹 서비스에 사용할 보조 인증 키 |
| `gpuCores` | `gpu_cores` | 이 웹 서비스에 할당할 GPU 코어(컨테이너별 복제본당)의 수입니다. 기본값은 1입니다. 정수 값만 지원합니다. |
| `livenessProbeRequirements` | 해당 없음 | 라이브 프로브 요구 사항에 대한 구성 요소가 포함되어 있습니다. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | 얼마나 자주 (초) 라이브 프로브를 수행합니다. 기본값은 10초입니다. 최소값은 1입니다. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | 생동 프로브가 시작되기 전에 컨테이너가 시작된 후의 시간(초)입니다. 기본값 310으로 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | 라이브 프로브가 시간 아웃된 후의 시간(초) 수입니다. 기본값은 2초입니다. 최소값은 1입니다. |
| &emsp;&emsp;`successThreshold` | `success_threshold` | 라이브 프로브에 대한 최소 연속 성공은 실패한 후 성공한 것으로 간주됩니다. 기본값은 1입니다. 최소값은 1입니다. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | 포드가 시작되고 라이브 프로브가 실패하면 Kubernetes는 실패를 시도합니다.포기하기 전에 임계 시간을 시도합니다. 기본값은 3입니다. 최소값은 1입니다. |
| `namespace` | `namespace` | 웹 서비스가 배포되는 Kubernetes 네임스페이스입니다. 최대 63개의 소문자 영숫자('a'-z', '0'-'9') 및 하이픈('-'')의 문자. 첫 번째 와 마지막 문자는 하이픈이 될 수 없습니다. |

다음 JSON은 CLI와 함께 사용할 수 있는 예제 배포 구성입니다.

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
