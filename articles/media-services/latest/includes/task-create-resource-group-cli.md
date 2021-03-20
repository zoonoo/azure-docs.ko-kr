---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88602353"
---
<!-- Create a resource group -->

다음 명령을 사용 하 여 리소스 그룹을 만듭니다. Media Services 계정에 대한 미디어 및 메타데이터 레코드를 저장하는 데 사용할 지리적 지역을 선택합니다. 이 지역은 미디어를 처리하고 스트림하는 데 사용됩니다.

```azurecli
az group create --name amsResourceGroup --location westus2
```
