---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: e8c0b35f2ac6d3468a5e38a90cc7f4de09d3682b
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348491"
---
`deploymentconfig.json` 문서의 항목은 [localwebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py)에 대 한 매개 변수에 매핑됩니다. 다음 표에서는 JSON 문서의 엔터티 및 메서드에 대 한 매개 변수의 매핑에 대해 설명 합니다.

| JSON 엔터티 | 메서드 매개 변수 | Description |
| ----- | ----- | ----- |
| `computeType` | NA | 컴퓨팅 대상. Local의 경우 값은 이어야 `local`합니다. |
| `port` | `port` | 서비스의 HTTP 끝점을 노출할 로컬 포트입니다. |

다음 JSON은 CLI에서 사용할 수 있는 배포 구성의 예입니다.

```json
{
    "computeType": "local",
    "port": 32267
}
```
