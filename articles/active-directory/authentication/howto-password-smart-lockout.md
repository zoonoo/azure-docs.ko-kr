---
title: 스마트 잠금을 사용 하 여 공격 방지-Azure Active Directory
description: 스마트 잠금 Azure Active Directory 사용자 암호 추측을 시도 하는 무차별 암호 대입 공격 으로부터 조직을 보호 하는 데 도움이 되는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6ffbd23dccd7bac03e849241866416ac07af4a0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87035420"
---
# <a name="protect-user-accounts-from-attacks-with-azure-active-directory-smart-lockout"></a>Azure Active Directory 스마트 잠금과 함께 공격 으로부터 사용자 계정 보호

스마트 잠금 기능을 사용 하면 사용자의 암호를 추측 하거나 무차별 암호 대입 메서드를 사용 하 여를 가져오는 잘못 된 행위자를 잠글 수 있습니다. 스마트 잠금은 유효한 사용자의 로그인을 인식 하 여 공격자 및 다른 알 수 없는 소스와 다르게 처리할 수 있습니다. 사용자가 계속 해 서 계정에 액세스 하 여 생산성을 유지 하는 동안 공격자가 잠깁니다.

## <a name="how-smart-lockout-works"></a>스마트 잠금 작동 방법

스마트 잠금 기능은 기본적으로 로그인 실패가 10회 발생하면 1분 동안 계정에 로그인 시도를 차단합니다. 후속 로그인 시도가 실패할 때마다 계정이 다시 잠기는데, 처음에는 1분간 잠기고 그 이후에는 더 길게 잠깁니다.

스마트 잠금 기능은 동일한 암호에 대해 잠금 카운터가 증가하는 것을 방지하기 위해 마지막 세 개의 잘못된 암호 해시를 추적합니다. 누군가가 동일한 잘못 된 암호를 여러 번 입력 하는 경우이 동작으로 인해 계정이 잠기지 않습니다.

> [!NOTE]
> 통과 인증을 사용 하는 고객은 클라우드에서 온-프레미스에서 인증을 수행 하지 않으므로 해시 추적 기능을 사용할 수 없습니다.

AD FS 2016 및 AF FS 2019을 사용 하는 페더레이션된 배포는 [AD FS 엑스트라넷 잠금 및 엑스트라넷 스마트 잠금을](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)사용 하 여 유사한 이점을 제공할 수 있습니다.

스마트 잠금은 항상 모든 Azure AD 고객에 대해 보안 및 유용성을 적절 하 게 조합 하 여 제공 하는 기본 설정으로 설정 됩니다. 사용자의 사용자에 대해 Azure AD Premium P1 이상의 라이선스를 요구 하는 사용자의 조직에 특정 한 값을 사용 하 여 스마트 잠금 설정을 사용자 지정 합니다.

스마트 잠금을 사용 하면 정품 사용자가 잠기지 않도록 보장 되지 않습니다. 스마트 잠금이 사용자 계정을 잠그는 경우 정품 사용자를 잠그지 않는 것이 가장 좋습니다. 잠금 서비스는 잘못 된 행위자가 정품 사용자 계정에 대 한 액세스 권한을 얻을 수 없도록 하려고 시도 합니다. 고려 사항은 다음과 같습니다.

* 각 Azure AD 데이터 센터는 독립적으로 잠금을 추적 합니다. 사용자가 각 데이터 센터에 도달 하는 경우 사용자에 게 (*threshold_limit * datacenter_count*)의 시도 횟수가 있습니다.
* 스마트 잠금은 익숙한 위치 및 낯선 위치를 사용하여 악의적 행위자와 진정한 사용자를 구별합니다. 잘 모르는 위치에는 별도의 잠금 카운터가 있습니다.

암호 해시 동기화 또는 통과 인증을 사용 하 여 공격자가 온-프레미스 Active Directory Domain Services (AD DS) 계정을 잠그지 않도록 보호 하는 하이브리드 배포와 스마트 잠금을 통합할 수 있습니다. Azure AD에서 적절 하 게 스마트 잠금 정책을 설정 하 여 온-프레미스 AD DS에 도달 하기 전에 공격을 필터링 할 수 있습니다.

[통과 인증](../hybrid/how-to-connect-pta.md)을 사용 하는 경우 다음 사항을 고려해 야 합니다.

* Azure AD 잠금 임계값이 AD DS 계정 잠금 임계값 보다 **작습니다** . AD DS 계정 잠금 임계값이 Azure AD 잠금 임계값 보다 두세 배 이상이 되도록 값을 설정 합니다.
* Azure AD 잠금 기간은 AD DS 계정 잠금 해제 카운터 다시 설정 기간 보다 길게 설정 해야 합니다. Azure AD 기간은 초 단위로 설정 되 고, AD 기간은 분 단위로 설정 됩니다.

예를 들어 Azure AD 카운터가 AD DS 보다 높은 경우 온-프레미스 AD가 1 분 (60 초)으로 설정 되는 동안 Azure AD는 120 초 (2 분)가 됩니다.

> [!IMPORTANT]
> 현재 관리자는 스마트 잠금 기능에 의해 잠긴 경우 사용자의 클라우드 계정을 잠금 해제할 수 없습니다. 잠금 기간이 만료될 때까지 기다려야 합니다. 그러나 사용자는 신뢰할 수 있는 장치 또는 위치에서 SSPR (셀프 서비스 암호 재설정)를 사용 하 여 잠금을 해제할 수 있습니다.

## <a name="verify-on-premises-account-lockout-policy"></a>온-프레미스 계정 잠금 정책 확인

온-프레미스 AD DS 계정 잠금 정책을 확인 하려면 관리자 권한으로 도메인에 가입 된 시스템에서 다음 단계를 완료 합니다.

1. 그룹 정책 관리 도구를 엽니다.
2. 조직의 계정 잠금 정책 (예: **기본 도메인 정책**)을 포함 하는 그룹 정책을 편집 합니다.
3. **컴퓨터 구성**  >  **정책**  >  **Windows 설정**  >  **보안 설정**  >  **계정 정책**  >  **계정 잠금 정책**으로 이동 합니다.
4. **계정 잠금 임계값** 을 확인 하 고 값 **뒤에 계정 잠금 카운터를 다시 설정** 합니다.

![온-프레미스 Active Directory 계정 잠금 정책 수정](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Azure AD 스마트 잠금 값 관리

조직의 요구 사항에 따라 Azure AD 스마트 잠금 값을 사용자 지정할 수 있습니다. 사용자의 사용자에 대해 Azure AD Premium P1 이상의 라이선스를 요구 하는 사용자의 조직에 특정 한 값을 사용 하 여 스마트 잠금 설정을 사용자 지정 합니다.

조직의 스마트 잠금 값을 확인 하거나 수정 하려면 다음 단계를 완료 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. *Azure Active Directory*를 검색 하 고 선택한 다음, **보안**  >  **인증 방법**  >  **암호 보호**를 선택 합니다.
1. 첫 번째 잠금 전에 계정에서 허용되는 실패한 로그인 횟수에 따라 **잠금 임계값**을 설정합니다.

    기본값은 10입니다.

1. **잠금 지속 기간(초)** 을 각 잠금의 길이(초)로 설정합니다.

    기본값은 60초(1분)입니다.

> [!NOTE]
> 잠금 후 첫 번째 로그인도 실패하는 경우 계정은 다시 잠깁니다. 계정이 반복적으로 잠기는 경우 잠금 지속 시간이 증가합니다.

![Azure Portal에서 Azure AD 스마트 잠금 정책 사용자 지정](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>스마트 잠금 기능이 작동 하는지 여부를 확인 하는 방법

스마트 잠금 임계값이 트리거되면 계정이 잠겨 있는 동안 다음 메시지가 표시 됩니다.

*무단 사용을 방지 하기 위해 계정이 임시로 잠겨 있습니다. 나중에 다시 시도 하세요. 문제가 계속 되 면 관리자에 게 문의 하세요.*

## <a name="next-steps"></a>다음 단계

환경을 더 사용자 지정 하려면 [AZURE AD 암호 보호에 대 한 사용자 지정 금지 된 암호를 구성](tutorial-configure-custom-password-protection.md)하면 됩니다.

사용자가 웹 브라우저에서 암호를 재설정 하거나 변경할 수 있도록 [AZURE AD 셀프 서비스 암호 재설정을 구성할](tutorial-enable-sspr.md)수 있습니다.
