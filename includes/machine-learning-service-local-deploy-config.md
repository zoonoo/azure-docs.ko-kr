---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 0eeb82245a53c93af75fc3ce3f37cb588295e5b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102508098"
---
`deploymentconfig.json` 문서의 항목은 [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration)에 대한 매개 변수에 매핑됩니다. 다음 표에서는 JSON 문서의 엔터티 및 메서드에 대한 매개 변수 간의 매핑에 대해 설명합니다.

| JSON 엔터티 | 메서드 매개 변수 | 설명 |
| ----- | ----- | ----- |
| `computeType` | 해당 없음 | 컴퓨팅 대상. 로컬 대상의 경우 값은 `local`이어야 합니다. |
| `port` | `port` | 서비스의 HTTP 엔드포인트를 노출할 로컬 포트입니다. |

이 JSON은 CLI에서 사용할 수 있는 배포 구성의 예제입니다.

```json
{
    "computeType": "local",
    "port": 32267
}
```