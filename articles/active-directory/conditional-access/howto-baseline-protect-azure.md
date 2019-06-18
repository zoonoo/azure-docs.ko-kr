---
title: 기본 정책 관리 (미리 보기)-Azure Active Directory 서비스에 대 한 MFA 필요
description: Azure Resource Manager에 대 한 mfa 조건부 액세스 정책
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
ms.openlocfilehash: 24b54a3645fe97903219841dd148c0942dfcda76
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112377"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>기본 정책: 서비스 관리 (미리 보기)에 대해 MFA 요구

Azure 서비스의 다양 한 조직에서 사용할 수 있습니다. Azure Resource Manager API를 통해 이러한 서비스를 관리할 수 있습니다.

* Azure portal
* Azure PowerShell
* Azure CLI

Azure Resource Manager를 사용 하 여 서비스를 관리 하려면 높은 권한이 필요한 작업입니다. Azure Resource Manager는 서비스 설정 및 구독 청구와 같은 테 넌 트 전체 구성을 변경할 수 있습니다. 단일 단계 인증은 다양 한 스프레이 피싱 및 암호와 같은 공격에 취약 합니다. 따라서 Azure Resource Manager에 액세스 하 고 액세스를 허용 하기 전에 multi-factor authentication을 요구 하 여 구성을 업데이트 하려는 사용자의 id를 확인 하는 것이 반드시 합니다.

**서비스 관리에 대 한 MFA를 요구할** 은 [기준 정책](concept-baseline-protection.md) 경우 Azure portal, Azure PowerShell 또는 Azure CLI를 액세스 하는 모든 사용자에 대 한 MFA 필요 합니다. 이 정책은 관리자가 있는 경우에 관계 없이 Azure Resource Manager에 액세스 하는 모든 사용자에 게 적용 됩니다.

이 정책은 테 넌 트에서 활성화 되 면 Azure 관리 리소스에 로그인 하는 모든 사용자가 multi-factor authentication 증명을 해야 합니다. 사용자 MFA에 등록 되어 있지 않으면, 사용자 계속 하려면 Microsoft Authenticator 앱을 사용 하 여 등록 해야 합니다.

![Azure Resource Manager에 대 한 MFA를 요구 합니다.](./media/howto-baseline-protect-azure/baseline-policy-require-mfa-for-service-management.png)

대화형 사용 하 여 로그인 하는 데 [Azure Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)를 사용 합니다 [Connect AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet.

```PowerShell
Connect-AzAccount
```

실행되면 이 cmdlet은 토큰 문자열을 제공합니다. 로그인 하려면이 문자열을 복사 하 고 붙여 넣습니다 [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)  브라우저에서 합니다. 그러면 PowerShell 세션이 인증되어 Azure에 연결됩니다.

대화형 사용 하 여 로그인 하는 데 [Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)을 실행 합니다 [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) 명령입니다.

```azurecli
az login
```

CLI가 기본 브라우저를 열 수 있는 경우, 그렇게 하고 로그인 페이지를 로드합니다. 브라우저 페이지를 열고로 이동한 후 권한 부여 코드를 입력 하는 명령줄의 지침에 따라 해야이 고, 그렇지 [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) 브라우저에서 합니다. 그런 다음 브라우저에서 계정 자격 증명으로 로그인 합니다.

## <a name="deployment-considerations"></a>배포 고려 사항

때문에 합니다 **서비스 관리에 대 한 MFA를 요구** 모든 Azure 리소스 관리자에 게 정책 적용, 원활한 배포에 대 한 몇 가지 고려 사항이 필요 합니다. 이러한 고려 사항은 사용자 및 응용 프로그램 및 최신 인증을 지원 하지 않는 조직에서 사용 하는 클라이언트와 함께 MFA를 수행 하지 않아야 하거나 없는 Azure AD에서 서비스 주체를 식별을 포함 합니다.

### <a name="user-exclusions"></a>사용자 제외

이 기준 정책을 사용자를 제외 하는 옵션을 제공 합니다. 정책이 테 넌 트를 설정 하기 전에 다음 계정을 제외 하는 것이 좋습니다.

* **응급 액세스** 나 **비상** 테 넌 트 전체 계정 잠금을 방지 하기 위해 계정. 모든 관리자가 테 넌 트를 잠글 가능성이 적은 시나리오에서 응급 액세스 관리 계정의 테 넌 트에 대 한 액세스 복구 단계를 수행을 로그인에 사용할 수 있습니다.
   * 자세한 내용은 문서에서 찾을 수 있습니다 [Azure AD에서 응급 액세스 계정을 관리할](../users-groups-roles/directory-emergency-access.md)합니다.
* **서비스 계정** 하 고 **원칙 서비스**, Azure AD Connect 동기화 계정 등입니다. 서비스 계정에는 특정 사용자에 게 고정 되지 않은 비 대화형 계정입니다. 백 엔드 서비스에서 일반적으로 사용 하는 하며 응용 프로그램에 대 한 프로그래밍 방식의 액세스를 허용 합니다. MFA는 프로그래밍 방식으로 완료할 수 없습니다 때문 서비스 계정은 제외 해야 합니다.
   * 조직에 이러한 계정은 스크립트 또는 코드에서 사용 중인 경우 대체 하 여 고려해 야 [identities 관리](../managed-identities-azure-resources/overview.md)합니다. 임시 해결 방법으로 이러한 특정 계정 기본 정책에서 제외할 수 있습니다.
* 사용자 없는 또는 스마트 폰을 사용할 수 없습니다.
   * 이 정책에는 Microsoft Authenticator 앱을 사용 하 여 MFA에 등록 하려면 사용자가 필요 합니다.

## <a name="enable-the-baseline-policy"></a>기준 정책을 사용 하도록 설정

정책 **기준 정책: 서비스 관리 (미리 보기)에 대 한 MFA를 요구할** 미리 구성 되어 및 Azure portal에서 조건부 액세스 블레이드로 이동 하면 위쪽에 표시 됩니다.

이 정책을 사용 하도록 설정 및 관리자에 게 보호 합니다.

1. 에 로그인 합니다 **Azure portal** 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자입니다.
1. 이동할 **Azure Active Directory** > **조건부 액세스**합니다.
1. 정책 목록에서 선택 **기준 정책: 서비스 관리 (미리 보기)에 대 한 MFA를 요구할**합니다.
1. 설정할 **정책을 사용 하도록 설정** 하 **즉시 정책을 사용 하 여**입니다.
1. 클릭 하 여 모든 사용자 제외를 추가할 **사용자** > **제외 된 사용자 선택** 제외할 수 해야 하는 사용자를 선택 합니다. 클릭 **선택** 한 다음 **수행**합니다.
1. 클릭 **저장할**합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

* [조건부 액세스 기준 보호 정책](concept-baseline-protection.md)
* [ID 인프라를 보호하기 위한 5단계](../../security/azure-ad-secure-steps.md)
* [Azure Active Directory의 조건부 액세스는 무엇입니까?](overview.md)