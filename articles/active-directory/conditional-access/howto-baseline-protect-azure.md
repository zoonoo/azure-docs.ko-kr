---
title: 기본 정책에는 서비스 관리 (미리 보기)에 대 한 MFA가 필요 합니다. Azure Active Directory
description: Azure Resource Manager에 대 한 MFA를 요구 하는 조건부 액세스 정책
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: aab2aa4415345747a0e87b90ef0a7ee770ef3465
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608129"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>기준 정책: 서비스 관리를 위해 MFA 필요 (미리 보기)

조직에서 다양 한 Azure 서비스를 사용할 수 있습니다. 이러한 서비스는 Azure Resource Manager API를 통해 관리할 수 있습니다.

* Azure Portal
* Azure PowerShell
* Azure CLI

Azure Resource Manager를 사용 하 여 서비스를 관리 하는 작업은 매우 특권 수준의 작업입니다. Azure Resource Manager은 서비스 설정 및 구독 청구와 같은 테 넌 트 전체 구성을 변경할 수 있습니다. 단일 요소 인증은 피싱 및 암호 스프레이와 같은 다양 한 공격에 취약 합니다. 따라서 액세스를 허용 하기 전에 multi-factor authentication을 요구 하 여 Azure Resource Manager 및 업데이트 구성에 액세스 하려는 사용자의 id를 확인 하는 것이 중요 합니다.

**서비스 관리를 위한 Mfa 요구** 는 Azure Portal, Azure PowerShell 또는 Azure CLI에 액세스 하는 모든 사용자에 대해 mfa를 요구 하는 [기준 정책](concept-baseline-protection.md) 입니다. 이 정책은 관리자 인지 여부에 관계 없이 Azure Resource Manager에 액세스 하는 모든 사용자에 게 적용 됩니다.

테 넌 트에서이 정책을 사용 하도록 설정 하면 Azure 관리 리소스에 로그인 하는 모든 사용자에 게 multi-factor authentication이 사용 됩니다. 사용자가 MFA에 등록 되지 않은 경우 사용자는 계속 하려면 Microsoft Authenticator 앱을 사용 하 여 등록 해야 합니다.

[Azure Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)을 사용 하 여 대화형 로그인을 수행 하려면 [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용 합니다.

```PowerShell
Connect-AzAccount
```

실행되면 이 cmdlet은 토큰 문자열을 제공합니다. 로그인 하려면이 문자열을 복사 하 여 브라우저의에 [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)  붙여 넣습니다. 그러면 PowerShell 세션이 인증되어 Azure에 연결됩니다.

[Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)를 사용 하 여 대화형 로그인을 수행 하려면 [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) 명령을 실행 합니다.

```azurecli
az login
```

CLI가 기본 브라우저를 열 수 있는 경우, 그렇게 하고 로그인 페이지를 로드합니다. 그렇지 않으면 브라우저 페이지를 열고 명령줄의 지침에 따라 브라우저에서로 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 이동한 후 인증 코드를 입력 해야 합니다. 그런 다음 브라우저에서 계정 자격 증명으로 로그인 합니다.

## <a name="deployment-considerations"></a>배포 고려 사항

**서비스 관리에 대 한 MFA 필요** 정책은 모든 Azure Resource Manager 사용자에 게 적용 되므로 원활한 배포를 위해 몇 가지 사항을 고려해 야 합니다. 이러한 고려 사항에는 MFA를 수행할 수 없는 Azure AD의 사용자 및 서비스 원칙과 최신 인증을 지원 하지 않는 조직에서 사용 하는 응용 프로그램 및 클라이언트를 식별 하는 작업이 포함 됩니다.

## <a name="enable-the-baseline-policy"></a>기준 정책 사용

정책 **기준 정책: 서비스 관리 (미리 보기)** 에 대 한 MFA 요구는 미리 구성 되어 있으며 Azure Portal의 조건부 액세스 블레이드로 이동 하면 위쪽에 표시 됩니다.

이 정책을 사용 하도록 설정 하 고 관리자를 보호 하려면 다음을 수행 합니다.

1. 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자 권한으로 **Azure Portal** 에 로그인 합니다.
1. **Azure Active Directory** > **조건부 액세스**로 이동 합니다.
1. 정책 목록에서 기준 정책을 선택 **합니다. 서비스 관리 (미리 보기)** 를 위해 MFA를 요구 합니다.
1. 정책 **사용을 즉시 정책**사용으로 설정 합니다.
1.  **저장**을 클릭 합니다.

## <a name="next-steps"></a>다음 단계

참조 항목:

* [조건부 액세스 기준 보호 정책](concept-baseline-protection.md)
* [ID 인프라를 보호하기 위한 5단계](../../security/fundamentals/steps-secure-identity.md)
* [Azure Active Directory의 조건부 액세스란?](overview.md)