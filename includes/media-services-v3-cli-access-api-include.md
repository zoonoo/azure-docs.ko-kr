---
title: 포함 파일
description: 포함 파일
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 9d8de8826fc3dfcc2360eb3b6c82b3ff8d65d845
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "79461234"
---
### <a name="access-the-media-services-api"></a>Media Services API 액세스

Azure Media Services API에 연결하려면 Azure AD 서비스 주체 인증을 사용합니다. 다음 명령은 Azure AD 애플리케이션을 만들고 계정에 서비스 주체를 연결합니다. 반환된 값을 사용하여 애플리케이션을 구성해야 합니다.

스크립트를 실행하기 전에 `amsaccount` 및 `amsResourceGroup`을 이러한 리소스로 만들 때 선택한 이름으로 바꿔야 합니다. `amsaccount`는 서비스 주체를 연결하는 Azure Media Services 계정의 이름입니다.

여러 구독에 대한 액세스 권한이 있는 경우 먼저 Media Services 계정이 만들어진 구독에 대한 활성 구독을 설정합니다.

```azurecli
az account set --subscription subscriptionId
```

다음 명령은 `json` 출력을 반환합니다.

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

이 명령은 다음과 비슷한 응답을 생성합니다.

```json
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "00000000-0000-0000-0000-000000000000",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": "amsaccount",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "West US 2",
  "ResourceGroup": "amsResourceGroup",
  "SubscriptionId": "00000000-0000-0000-0000-000000000000"
}
```

응답에서 `xml`을 가져오려면 다음 명령을 사용합니다.

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
