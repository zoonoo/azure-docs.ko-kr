---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 26ae372b6e431247d2038445daafcb3c997a232d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512626"
---
<!--Create a media services account -->

다음 Azure CLI 명령은 새 Media Services 계정을 만듭니다. `amsaccount` `storageaccountforams`(스토리지 계정에 지정한 값과 일치해야 함) 및 `amsResourceGroup`(리소스 그룹에 지정한 값과 일치해야 함) 값을 바꿀 수 있습니다.

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```
