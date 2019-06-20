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
ms.openlocfilehash: b0f93f950b55052ea8d8b31538c47226413dc82a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182294"
---
## <a name="access-the-media-services-api"></a>Media Services API 액세스

Azure Media Services API에 연결하려면 Azure AD 서비스 주체 인증을 사용합니다. 다음 명령은 Azure AD 애플리케이션을 만들고 계정에 서비스 주체를 연결합니다. 반환된 값을 사용하여 애플리케이션을 구성해야 합니다.

스크립트를 실행하기 전에 `amsaccount` 및 `amsResourceGroup`을 이러한 리소스로 만들 때 선택한 이름으로 바꿔야 합니다. `amsaccount`는 서비스 주체를 연결하는 Azure Media Services 계정의 이름입니다.

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
