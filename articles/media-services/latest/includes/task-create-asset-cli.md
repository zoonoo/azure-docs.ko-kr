---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: e16e3dc6788fb30a87a78fd6d6077087f461cfa7
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512559"
---
<!--Create a media services asset CLI-->

다음 Azure CLI 명령은 새 Media Services 자산을 만듭니다. `assetName` `amsAccountName` 및 `resourceGroup` 값을 현재 작업 중인 값으로 바꿉니다.

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```
