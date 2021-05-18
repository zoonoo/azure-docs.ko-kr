---
title: 서비스 액세스 토큰 가져오기
description: ARR REST API에 액세스하기 위한 토큰을 만드는 방법을 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9721685fc3ccd2c1c80b55e9118d6d347cc97a9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97830703"
---
# <a name="get-service-access-tokens"></a>서비스 액세스 토큰 가져오기

ARR REST API에 대한 액세스는 권한 있는 사용자에게만 부여됩니다. 권한 부여를 증명하려면 REST 요청과 함께 *액세스 토큰* 을 보내야 합니다. 이러한 토큰은 계정 키 대신 *보안 토큰 서비스*(STS)에서 발급됩니다. 토큰의 수명은 **24시간** 이므로 서비스에 대한 모든 권한을 부여하지 않고도 사용자에게 발급될 수 있습니다.

이 문서에서는 이러한 액세스 토큰을 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

아직 없는 경우 [ARR 계정을 만듭니다](create-an-account.md).

## <a name="token-service-rest-api"></a>토큰 서비스 REST API

액세스 토큰을 만들기 위해 *보안 토큰 서비스* 는 단일 REST API를 제공합니다. STS 서비스의 URL은 원격 렌더링 계정의 계정 도메인에 따라 달라집니다. URL 형식은 https://sts.[계정 도메인]입니다. 예: `https://sts.southcentralus.mixedreality.azure.com`

### <a name="get-token-request"></a>'토큰 가져오기' 요청

| URI | 방법 |
|-----------|:-----------|
| /accounts/**accountId**/token | GET |

| 헤더 | 값 |
|--------|:------|
| 권한 부여 | "Bearer **accountId**:**accountKey**" |

*accountId* 및 *accountKey* 를 해당 데이터로 바꿉니다.

### <a name="get-token-response"></a>'토큰 가져오기' 응답

| 상태 코드 | JSON 페이로드 | 주석 |
|-----------|:-----------|:-----------|
| 200 | AccessToken: 문자열 | Success |

| 헤더 | 목적 |
|--------|:------|
| MS-CV | 이 값은 서비스 내에서 호출을 추적하는 데 사용할 수 있습니다. |

## <a name="getting-a-token-using-powershell"></a>PowerShell을 사용하여 토큰 가져오기

아래의 PowerShell 코드는 필요한 REST 요청을 STS로 보내는 방법을 보여 줍니다. 그런 다음 토큰을 PowerShell 프롬프트에 출력합니다.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"
$accountDomain = "<account_domain_from_portal>

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.$accountDomain/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

스크립트는 토큰을 복사 및 붙여넣을 수 있는 위치에서 출력합니다. 실제 프로젝트의 경우 이 프로세스를 자동화해야 합니다.

## <a name="next-steps"></a>다음 단계

* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)
* [Azure Frontend API](../how-tos/frontend-apis.md)
* [세션 관리 REST API](../how-tos/session-rest-api.md)
