---
author: baanders
description: 파일을 포함하여 Azure Digital Twins 인스턴스에 모델 업로드
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: 9fcbfc0b0fccb5f130f0c554bac93748176387b7
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111438984"
---
모델은 다음과 같습니다.
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

**이 모델을 Twins 인스턴스에 업로드** 하려면 위 모델을 인라인 JSON으로 업로드하는 다음 Azure CLI 명령을 실행합니다. 브라우저의 [Azure Cloud Shell](../articles/cloud-shell/overview.md)(**Bash** 환경 사용)에서 또는 [CLI가 로컬에 설치](/cli/azure/install-azure-cli)된 경우 머신에서 명령을 실행할 수 있습니다.

```azurecli-interactive
az dt model create --dt-name <instance-name> --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' 
```