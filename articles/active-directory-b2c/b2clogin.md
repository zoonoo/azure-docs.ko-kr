---
title: b2clogin.com 사용 | Microsoft Docs
description: login.microsoftonline.com 대신 b2clogin.com을 사용하는 방법을 알아봅니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1d42d9a97244eeff501b9d02b0f143d6ef0c91b2
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440633"
---
# <a name="using-b2clogincom"></a>b2clogin.com 사용

>[!IMPORTANT]
>이 기능은 공개 미리 보기 
>

이제 `login.microsoftonline.com`을 사용하는 대신 `<YourTenantName>.b2clogin.com`을 통해 Azure AD B2C 서비스를 사용하는 옵션이 제공됩니다.  여기에는 많은 이점이 있습니다.
* 다른 Microsoft 제품과 동일한 쿠키 헤더 크기 제한을 더 이상 공유하지 않습니다.
* URL에서 Microsoft에 대한 모든 참조를 제거할 수 있습니다(`<YourTenantName>.onmicrosoft.com`을 테넌트 ID로 바꿀 수 있음). 예: `https://<tenantname>.b2clogin.com/tfp/<tenantname>/<policyname>/v2.0/.well-known/openid-configuration`

 b2clogin.com 활용하기 위해서는 다음 중 일부를 설정해야 합니다.

1. **지금 엔드포인트를 실행**하려면 `login.microsoftonline.com`를 사용하는 대신 `<YourTenantName>.b2clogin.com`을 사용하는지 확인합니다.
2. MSAL를 사용하는 경우 `validateauthority=false`을 설정해야 합니다.  토큰 발급자가 `<YourTenantName>.b2clogin.com`이기 때문입니다.