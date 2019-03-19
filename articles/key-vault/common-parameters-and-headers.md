---
title: 공통 매개 변수 및 헤더
description: 매개 변수 및 헤더는 Key Vault 리소스와 관련하여 사용자가 수행할 수 있는 모든 작업에 공통적입니다.
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.assetid: a715d13ca9-d6e8-4e54-ac5e-0ed9400fb15b15d13ca9-d6e8-4e54-ac5e-0ed9400fb15b
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: cc6c937e37ebb7919ca271e765ad6460c104e43b
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405048"
---
# <a name="common-parameters-and-headers"></a>공통 매개 변수 및 헤더

다음 정보는 Key Vault 리소스와 관련하여 사용자가 수행할 수 있는 모든 작업에 공통적입니다.

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
| 코드 | 문자열 | 발생한 오류의 형식입니다.|
| Message | 문자열 | 오류 원인에 대한 설명입니다. |



## <a name="see-also"></a>관련 항목
 [Azure Key Vault REST API 참조](/rest/api/keyvault/)
