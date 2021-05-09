---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 07b0897de3fce6a108836f1e72ce956a3a8092f1
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107893147"
---
<!--Create a media services account -->

다음 Azure CLI 명령은 새 Media Services 계정을 만듭니다. `amsaccount` `storageaccountforams`(스토리지 계정에 지정한 값과 일치해야 함) 및 `amsResourceGroup`(리소스 그룹에 지정한 값과 일치해야 함) 값을 바꿀 수 있습니다.

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```
