---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: c902ea001d18504dabffc8431acafe33822792e1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88539140"
---
<!--Create a media services account -->

다음 Azure CLI 명령은 새 Media Services 계정을 만듭니다. 다음 값을 바꿀 수 있습니다. `amsaccount` `storageaccountforams` (는 저장소 계정에 대해 지정한 값과 일치 해야 함) 및 `amsResourceGroup` (리소스 그룹에 대해 지정한 값과 일치 해야 함)  

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```