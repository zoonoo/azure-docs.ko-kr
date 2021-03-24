---
author: baanders
description: Azure Digital Twins 인스턴스에 모델을 업로드 하는 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 3/23/2021
ms.author: baanders
ms.openlocfilehash: 987409f070f34f0fd9ee212fab8cc57e889e0146
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950600"
---
모델은 다음과 같습니다.
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

**이 모델을 쌍 인스턴스에 업로드** 하려면 위의 모델을 인라인 JSON으로 업로드 하는 다음 Azure CLI 명령을 실행 합니다. CLI를 [로컬로 설치](/cli/azure/install-azure-cli)하는 경우 브라우저 또는 컴퓨터에서 [Azure Cloud Shell](../articles/cloud-shell/overview.md) 에서 명령을 실행할 수 있습니다.

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

> [!Note]
> PowerShell 환경에서 Cloud Shell를 사용 하는 경우 해당 값을 올바르게 구문 분석 하려면 인라인 JSON 필드에서 인용 부호 문자를 이스케이프 해야 할 수 있습니다. 다음은이 수정 내용으로 모델을 업로드 하는 명령입니다.
>
> 모델 업로드:
> ```azurecli-interactive
> az dt model create --models '{  \"@id\": \"dtmi:contosocom:DigitalTwins:Thermostat;1\",  \"@type\": \"Interface\",  \"@context\": \"dtmi:dtdl:context;2\",  \"contents\": [    {      \"@type\": \"Property\",      \"name\": \"Temperature\",      \"schema\": \"double\"    }  ]}' -n {digital_twins_instance_name}
> ```