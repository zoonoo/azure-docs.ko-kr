---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4f13c171c5fafb13875f5f87d4eb3d6013f0ff30
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79486107"
---
문서의 항목은 `deploymentconfig.json` [Aciwebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py)에 대 한 매개 변수에 매핑됩니다. 다음 표에서는 JSON 문서의 엔터티 및 메서드에 대 한 매개 변수의 매핑에 대해 설명 합니다.

| JSON 엔터티 | 메서드 매개 변수 | 설명 |
| ----- | ----- | ----- |
| `computeType` | 해당 없음 | 컴퓨팅 대상. ACI의 경우 값은 이어야 합니다 `ACI` . |
| `containerResourceRequirements` | 해당 없음 | CPU 및 메모리 엔터티의 컨테이너입니다. |
| &emsp;&emsp;`cpu` | `cpu_cores` | 할당할 CPU 코어 수입니다. 기본값과`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | 이 웹 서비스에 할당할 메모리 양 (GB)입니다. 기본`0.5` |
| `location` | `location` | 이 웹 서비스를 배포할 Azure 지역입니다. 지정 하지 않으면 작업 영역 위치가 사용 됩니다. 사용 가능한 지역에 대 한 자세한 내용은 [ACI 지역](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) 에서 찾을 수 있습니다. |
| `authEnabled` | `auth_enabled` | 이 웹 서비스에 대 한 인증을 사용할지 여부입니다. 기본값은 False입니다. |
| `sslEnabled` | `ssl_enabled` | 이 웹 서비스에 대해 SSL을 사용 하도록 설정할지 여부입니다. 기본값은 False입니다. |
| `appInsightsEnabled` | `enable_app_insights` | 이 웹 서비스에 대해 AppInsights를 사용 하도록 설정할지 여부입니다. 기본값은 False입니다. |
| `sslCertificate` | `ssl_cert_pem_file` | SSL을 사용 하는 경우 필요한 인증서 파일 |
| `sslKey` | `ssl_key_pem_file` | SSL을 사용 하는 경우 필요한 키 파일 |
| `cname` | `ssl_cname` | SSL을 사용 하는 경우에 대 한 cname |
| `dnsNameLabel` | `dns_name_label` | 점수 매기기 끝점의 dns 이름 레이블입니다. 지정 하지 않으면 점수 매기기 끝점에 대해 고유한 dns 이름 레이블이 생성 됩니다. |

다음 JSON은 CLI에서 사용할 수 있는 배포 구성의 예입니다.

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```