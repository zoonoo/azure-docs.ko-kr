---
title: 암호화 키 정보 가져오기
titleSuffix: Azure Cognitive Search
description: Azure Key Vault에서 키를 관리할 수 있도록 인덱스 또는 동의어 맵에서 사용되는 암호화 키 이름 및 버전을 검색합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 22eefcde250057fae142142ed91c6e339849b0ba
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110689262"
---
# <a name="get-customer-managed-key-information-from-indexes-and-synonym-maps"></a>인덱스 및 동의어 맵에서 고객 관리형 키 정보 가져오기

Azure Cognitive Search에서는 고객 관리형 암호화 키가 Azure Key Vault에서 생성, 저장, 관리됩니다. 개체가 암호화되었는지 여부나 사용된 키 이름 또는 버전을 확인해야 하는 경우 REST API 또는 SDK를 사용하여 인덱스 또는 동의어 맵 정의에서 **encryptionKey** 속성을 검색합니다. 

키 사용을 모니터링할 수 있도록 Key Vault에서 [로깅을 사용하도록 설정](../key-vault/general/logging.md)하는 것이 좋습니다.

## <a name="get-the-admin-api-key"></a>관리 API 키 가져오기

Search Service에서 개체 정의를 가져오려면 관리자 권한으로 인증해야 합니다. 관리 API 키를 가져오는 가장 쉬운 방법은 포털을 통하는 것입니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 Search Service 개요 페이지를 엽니다.

1. 왼쪽에서 **키** 를 클릭하고 관리 API를 복사합니다. 인덱스 및 동의어 맵 검색에는 관리자 키가 필요합니다.

나머지 단계를 수행하려면 PowerShell 및 REST API로 전환합니다. 포털에는 동의어 맵과 인덱스의 암호화 키 속성이 표시되지 않습니다.

## <a name="use-powershell-and-rest"></a>PowerShell 및 REST 사용

다음 명령을 실행하여 변수를 설정하고 개체 정의를 가져옵니다.

```powershell
<# Connect to Azure #>
$Connect-AzAccount

<# Provide the admin API key used for search service authentication  #>
$headers = @{
'api-key' = '<YOUR-ADMIN-API-KEY>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' }

<# List all existing synonym maps #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# List all existing indexes #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific synonym map definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms/<YOUR-SYNONYM-MAP-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific index definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/<YOUR-INDEX-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json
```

## <a name="next-steps"></a>다음 단계

사용되는 암호화 키와 버전을 확인했으므로 이제 Azure Key Vault에서 키를 관리하거나 다른 구성 설정을 검사할 수 있습니다.

+ [빠른 시작: PowerShell을 사용하여 Azure Key Vault에서 비밀을 설정하고 검색](../key-vault/secrets/quick-create-powershell.md)

+ [Azure Cognitive Search에서 데이터 암호화에 사용할 고객 관리형 키 구성](search-security-manage-encryption-keys.md)
