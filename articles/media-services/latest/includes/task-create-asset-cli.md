---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: a1ed4a9e123459a697f0894ff05430c63988a70b
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585419"
---
<!--Create a media services asset cLI-->

다음 Azure CLI 명령은 새 Media Services 자산을 만듭니다. `assetName` `amsAccountName` 및 `resourceGroup` 값을 현재 작업 중인 값으로 바꿉니다.

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```