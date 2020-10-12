---
title: 공통 매개 변수 및 헤더
description: 매개 변수 및 헤더는 Key Vault 리소스와 관련하여 사용자가 수행할 수 있는 모든 작업에 공통적입니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d1d93bcd84fd9460e658b221089a4b24d46b0429
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83005808"
---
# <a name="common-parameters-and-headers"></a>공통 매개 변수 및 헤더

다음 정보는 Key Vault 리소스와 관련하여 사용자가 수행할 수 있는 모든 작업에 공통적입니다.

- HTTP `Host` 헤더는 항상 존재 해야 하며 자격 증명 모음 호스트 이름을 지정 해야 합니다. 예: `Host: contoso.vault.azure.net`. 대부분의 클라이언트 기술은 `Host` URI에서 헤더를 채웁니다. 예를 들어,는를 `GET https://contoso.vault.azure.net/secrets/mysecret{...}` 로 설정 합니다 `Host` `contoso.vault.azure.net` . 즉,와 같은 원시 IP 주소를 사용 하 Key Vault에 액세스 하는 경우 `GET https://10.0.0.23/secrets/mysecret{...}` 헤더의 자동 값 `Host` 이 잘못 되 고 `Host` 헤더에 자격 증명 모음 호스트 이름이 포함 되어 있는지 수동으로 확인할 수 있습니다.
- `{api-version}`을 URI의 API 버전으로 바꿉니다.
- `{subscription-id}`를 URI의 구독 ID로 바꿉니다.
- `{resource-group-name}`을 리소스 그룹으로 바꿉니다. 자세한 내용은 리소스 그룹을 사용하여 Azure 리소스 관리를 참조하세요.
- `{vault-name}`을 URI에서 사용자의 Key Vault로 바꿉니다.
- 콘텐츠 형식 헤더를 application/json으로 설정합니다.
- 권한 부여 헤더를 AAD(Azure Active Directory)에서 가져온 JSON 웹 토큰으로 설정합니다. 자세한 내용은 [Azure Resource Manager 인증](authentication-requests-and-responses.md) 요청을 참조하세요.

## <a name="common-error-response"></a>공통 오류 응답
서비스는 성공 또는 실패를 나타내는 HTTP 상태 코드를 사용합니다. 또한 실패에는 다음과 같은 형식의 응답이 포함됩니다.

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|요소 이름 | Type | 설명 |
|---|---|---|
| code | 문자열 | 발생한 오류의 형식입니다.|
| message | 문자열 | 오류 원인에 대한 설명입니다. |



## <a name="see-also"></a>참고 항목
 [Azure Key Vault REST API 참조](/rest/api/keyvault/)
