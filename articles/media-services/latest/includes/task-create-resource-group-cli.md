---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: f1b77e4cee809d7ddb7bbbf7de6c5ac483b150e3
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110060119"
---
<!-- Create a resource group -->

다음 명령을 사용하여 리소스 그룹을 만듭니다. `your-resource-group-name`을 원하는 리소스 그룹의 이름으로 변경합니다.

`your-region`을 Media Services 계정에 대한 미디어 및 메타데이터 레코드를 저장하는 데 사용할 지리적 지역으로 변경합니다. 이 지역은 미디어를 처리하고 스트림하는 데 사용됩니다.

```azurecli-interactive
az group create --name <your-resource-group-name> --location <your-region>
```

예제 JSON 응답:

```json
{
  "id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/your-resource-group-name",
  "location": "your-region",
  "managedBy": null,
  "name": "your-resource-group-name",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": "Microsoft.Resources/resourceGroups"
}
```
