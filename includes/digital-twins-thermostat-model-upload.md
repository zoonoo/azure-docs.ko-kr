---
author: baanders
description: 파일을 포함하여 Azure Digital Twins 인스턴스에 모델 업로드
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: 3a9ec169f91ebe048914c3ef2163e4fde7485389
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109783677"
---
모델은 다음과 같습니다.
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

**이 모델을 Twins 인스턴스에 업로드** 하려면 위 모델을 인라인 JSON으로 업로드하는 다음 Azure CLI 명령을 실행합니다. 브라우저의 [Azure Cloud Shell](../articles/cloud-shell/overview.md)(**Bash** 환경 사용)에서 또는 CLI가 [로컬에 설치](/cli/azure/install-azure-cli)된 경우 머신에서 명령을 실행할 수 있습니다.

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' --dt-name {digital_twins_instance_name}
```