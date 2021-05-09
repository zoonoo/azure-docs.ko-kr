---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: dcd2cda3bad2a13a83c5f3f6700e5a57471e2065
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88653891"
---
<!--Create a media services asset REST-->

다음 Azure .NET 명령은 새 Media Services 자산을 만듭니다. `subscriptionID`, `resourceGroup` 및 `amsAccountName` 값을 현재 작업 중인 값으로 바꿉니다. 여기에서 `assetName`을 설정하여 자산 이름을 지정합니다.

[!code-csharp[Main](../../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]
