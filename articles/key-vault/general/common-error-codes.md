---
title: Azure Key Vault에 대 한 일반적인 오류 코드 Microsoft Docs
description: Azure Key Vault에 대 한 일반적인 오류 코드
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: 25c79229a09db912903fba825f0d48f571880745
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876999"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Azure Key Vault에 대 한 일반적인 오류 코드

다음 표에 나열 된 오류 코드는 Azure key vault에 대 한 작업에 의해 반환 될 수 있습니다.

| 오류 코드 | 사용자 메시지 |
|--|--|
| VaultAlreadyExists |  지정 된 key vault가 이미 있는 경우 (일시 삭제 된 상태 또는 다른 구독에 있음) |
| VaultNameNotValid |  자격 증명 모음 이름은 24 자, 영숫자 및 영문자로 시작 해야 합니다. |
| AccessDenied |  액세스 정책에서이 작업을 수행 하는 데 필요한 권한이 없을 수 있습니다. |
| ForbiddenByFirewall |  클라이언트 주소는 권한이 없고 호출자는 신뢰할 수 있는 서비스가 아닙니다. |
| ConflictError |  동일한 항목에 대해 여러 작업을 요청 하 고 있습니다.  |
| 지역 Notsupported |  지정 된 azure 지역은이 리소스에 대해 지원 되지 않습니다. |
| 지원 되지 않음 |  지정 된 SKU 형식이이 리소스에 대해 지원 되지 않습니다. |
| ResourceNotFound |  지정 된 azure 리소스를 찾을 수 없습니다. |
| 인증서가 만료 됨 |  인증서의 만료 날짜 및 유효 기간을 확인 하십시오. |


## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 개발자 가이드](developers-guide.md) 참조
- [Key Vault에 인증](authentication.md)에 대해 자세히 알아보세요.
