---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 9e5b344f15a92e7ac40182f8fc7ae3ca667f63a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88608909"
---
<!--Create a media services asset REST-->

다음 Azure REST 명령은 새 Media Services 자산을 만듭니다. `subscriptionID`, 및 값을 `resourceGroup` `amsAccountName` 현재 작업 중인 값으로 바꿉니다. 여기에서 설정 하 여 자산 이름을 지정 `assetName` 합니다.

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```