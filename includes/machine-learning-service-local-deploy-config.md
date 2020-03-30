---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 60c754aeec3157abee02eb136dcf148c440d8da0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477275"
---
문서의 `deploymentconfig.json` 항목은 [LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py)매개 변수에 매핑됩니다. 다음 표에서는 JSON 문서의 엔터티와 메서드의 매개 변수 간의 매핑에 대해 설명합니다.

| JSON 엔티티 | 메서드 매개 변수 | 설명 |
| ----- | ----- | ----- |
| `computeType` | 해당 없음 | 컴퓨팅 대상. 로컬 대상의 경우 값은 값이어야 `local`합니다. |
| `port` | `port` | 서비스의 HTTP 끝점을 노출할 로컬 포트입니다. |

이 JSON은 CLI와 함께 사용할 수 있는 예제 배포 구성입니다.

```json
{
    "computeType": "local",
    "port": 32267
}
```
