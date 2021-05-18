---
author: baanders
description: 파일을 포함하여 Azure Digital Twins 인스턴스에 모델 업로드
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: add49cabaece1187cb9bcda3a94c92feb08b2439
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304303"
---
모델은 다음과 같습니다.
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

**이 모델을 Twins 인스턴스에 업로드** 하려면 위 모델을 인라인 JSON으로 업로드하는 다음 Azure CLI 명령을 실행합니다. 브라우저의 [Azure Cloud Shell](../articles/cloud-shell/overview.md)(**Bash** 환경 사용)에서 또는 CLI가 [로컬에 설치](/cli/azure/install-azure-cli)된 경우 머신에서 명령을 실행할 수 있습니다.

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```