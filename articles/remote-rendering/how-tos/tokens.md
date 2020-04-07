---
title: 서비스 액세스 토큰 받기
description: ARR REST API에 액세스하기 위한 토큰을 만드는 방법에 대해 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 001a77734a8cedf08c5523380c1cbd00dce89f40
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681195"
---
# <a name="get-service-access-tokens"></a>서비스 액세스 토큰 받기

ARR REST API에 대한 액세스는 권한이 부여된 사용자에게만 허용됩니다. 승인을 증명하려면 REST 요청과 함께 *액세스 토큰을* 보내야 합니다. 이러한 토큰은 계정 키에 대한 대가로 *STS(보안 토큰 서비스)에서* 발급됩니다. 토큰의 **수명은 24시간이므로** 서비스에 대한 모든 권한을 부여하지 않고 사용자에게 발급할 수 있습니다.

이 문서에서는 이러한 액세스 토큰을 만드는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

[ARR 계정을 아직 만들지](create-an-account.md)않은 경우.

## <a name="token-service-rest-api"></a>토큰 서비스 REST API

액세스 토큰을 만들기 위해 *보안 토큰 서비스는* 단일 REST API를 제공합니다. ARR STS 서비스의 URL은 [https://sts.mixedreality.azure.com](https://sts.mixedreality.azure.com).

### <a name="get-token-request"></a>'토큰 받기' 요청

| URI | 방법 |
|-----------|:-----------|
| **/account/accountID/토큰** | GET |

| 헤더 | 값 |
|--------|:------|
| 권한 부여 | "베어러 **계정ID**:**계정키**" |

*accountId* 및 *accountKey를* 해당 데이터로 바꿉습니다.

### <a name="get-token-response"></a>'토큰 받기' 응답

| 상태 코드 | JSON 페이로드 | 주석 |
|-----------|:-----------|:-----------|
| 200 | 액세스 토큰: 문자열 | Success |

| 헤더 | 목적 |
|--------|:------|
| MS-이력서 | 이 값은 서비스 내에서 호출을 추적하는 데 사용할 수 있습니다. |

## <a name="getting-a-token-using-powershell"></a>PowerShell을 사용하여 토큰 받기

아래 PowerShell 코드는 필요한 REST 요청을 STS로 보내는 방법을 보여 줍니다. 그런 다음 토큰을 PowerShell 프롬프트에 인쇄합니다.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

스크립트는 토큰을 출력에 인쇄하여 복사할 수 & 붙여 넣기만 하면 됩니다. 실제 프로젝트의 경우 이 프로세스를 자동화해야 합니다.

## <a name="next-steps"></a>다음 단계

* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)
* [Azure 프런트 엔드 API](../how-tos/frontend-apis.md)
* [세션 관리 REST API](../how-tos/session-rest-api.md)
