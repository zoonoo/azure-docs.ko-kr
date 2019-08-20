---
title: 2 단계 인증 설정 관리-Azure Active Directory | Microsoft Docs
description: 2 단계 인증에 관련 된 보안 확인 방법 세부 정보를 변경 하는 방법에 대해 알아봅니다.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ed7d812a9c1cba356277a4454234531ce8d41ef
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616157"
---
# <a name="manage-your-two-factor-verification-method-settings"></a>2 단계 인증 방법 설정 관리

회사 또는 학교 계정에 대 한 보안 인증 방법을 설정한 후 다음을 비롯 한 관련 세부 정보를 업데이트할 수 있습니다.

- 기본 보안 확인 방법 선택

- 전화 번호와 같은 보안 확인 방법 세부 정보를 추가 하거나 업데이트 하는 중입니다.

- 새 authenticator 앱을 설정 하거나 인증자 앱에서 장치를 삭제 합니다.

## <a name="using-the-additional-security-verification-page"></a>추가 보안 인증 페이지 사용

조직에서 2 단계 인증을 설정 하 고 관리 하는 방법에 대 한 특정 단계를 제공한 경우 해당 지침을 따라야 합니다. 그렇지 않으면 [추가 보안 인증](https://aka.ms/mfasetup) 페이지에서 보안 확인 방법 설정을 가져올 수 있습니다.

>[!Note]
>화면에 표시 되는 내용이이 문서에서 설명 하는 내용과 일치 하지 않으면 관리자가 보안 정보 (미리 보기) 환경을 사용 하도록 설정 했거나 조직에 고유한 사용자 지정 포털이 있음을 의미 합니다. 보안 정보 환경에 대 한 자세한 내용은 [보안 정보 (미리 보기) 개요](user-help-security-info-overview.md)를 참조 하세요. 조직의 사용자 지정 포털에 대 한 자세한 내용은 기술 지원팀에 문의 해야 합니다.

### <a name="to-get-to-the-additional-security-verification-page"></a>추가 보안 인증 페이지를 가져오려면

- [https://partnercenter.microsoft.com/partner/support](https://aka.ms/mfasetup )로 이동하세요.

    ![사용 가능한 보안 확인 방법 세부 정보를 포함 하는 추가 보안 확인 화면](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

    이 링크를 클릭할 때 링크가 작동하지 않으면 다음 단계에 따라 **추가 보안 인증** 페이지로 이동할 수도 있습니다.

    1. [https://myapps.microsoft.com](https://myapps.microsoft.com)에 로그인합니다.

    2. 오른쪽 맨 위에서 계정 이름을 선택한 후 **프로필**을 선택합니다.

    3. **추가 보안 인증**을 선택합니다.  

        ![추가 보안 인증 페이지에 대 한 내 앱 링크](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>**추가 보안 인증** 페이지의 **앱 암호** 섹션을 사용 하는 방법에 대 한 자세한 내용은 [2 단계 인증을 위해 앱 암호 관리](multi-factor-authentication-end-user-app-passwords.md)를 참조 하세요. 앱 암호는 아직 2 단계 인증을 지원 하지 않는 앱에만 사용 해야 합니다.

## <a name="change-your-default-security-verification-method"></a>기본 보안 확인 방법 변경

사용자 이름 및 암호를 사용 하 여 회사 또는 학교 계정에 로그인 한 후에는 선택한 보안 확인 방법이 자동으로 표시 됩니다. 조직의 요구 사항에 따라 인증자 앱, 문자 메시지 또는 전화 통화를 통한 알림 또는 확인 코드가 될 수 있습니다.

사용 중인 기본 보안 확인 방법을 변경 하려는 경우 여기에서 수행할 수 있습니다.

### <a name="to-change-your-default-security-verification-method"></a>기본 보안 인증 방법을 변경 하려면

1. **추가 보안 인증** 페이지의 **기본 설정 옵션** 드롭다운 목록에서 사용할 방법을 선택 합니다. 모든 옵션을 볼 수 있지만 조직에서 사용할 수 있는 옵션을 선택할 수 있습니다.

    - **앱을 통해 알립니다.** 대기 중인 인증 프롬프트가 있음을 인증자 앱을 통해 알려 드리겠습니다.

    - **내 인증 전화를 호출 합니다.** 모바일 장치에서 사용자의 정보를 확인 하도록 요청 하는 전화를 받게 됩니다.

    - **인증 전화로 문자 코드를 확인 합니다.** 모바일 장치에서 문자 메시지의 일부로 확인 코드를 받게 됩니다. 회사 또는 학교 계정에 대 한 확인 프롬프트에이 코드를 입력 해야 합니다.

    - **사무실 전화를 전화 합니다.** 사용자의 정보를 확인 하도록 요청 하는 사무실 전화에 전화를 받습니다.

    - **앱에서 확인 코드를 사용 합니다.** Authenticator 앱을 사용 하 여 회사 또는 학교 계정의 프롬프트에 입력 하는 확인 코드를 가져옵니다.

2. **저장**을 선택합니다.

## <a name="add-or-change-your-phone-number"></a>전화 번호 추가 또는 변경

**추가 보안 인증** 페이지에서 새 전화 번호를 추가 하거나 기존 번호를 업데이트할 수 있습니다.

>[!Important]
>기본 전화를 분실 하거나 도난당 한 경우 또는 새 전화를 받고 더 이상 원래 주 전화 번호가 없는 경우 계정에서 잠기는 것을 방지 하기 위해 보조 전화 번호를 추가 하는 것이 좋습니다.

### <a name="to-change-your-phone-numbers"></a>전화 번호를 변경 하려면

1. **추가 보안 확인** 페이지의 **응답 방법** 섹션에서 **인증 전화** (기본 모바일 장치) 및 **사무실 전화**에 대 한 전화 번호 정보를 업데이트 합니다.

2. **대체 인증 전화** 옵션 옆의 상자를 선택 하 고 기본 장치에 액세스할 수 없는 경우 문자 메시지 또는 전화 통화를 받을 수 있는 보조 전화 번호를 입력 합니다.

3. **저장**을 선택합니다.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Microsoft authenticator 앱에 새 계정 추가

[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) 또는 [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458)용 Microsoft Authenticator 앱에서 회사 또는 학교 계정을 설정할 수 있습니다.

이전에 Microsoft Authenticator 앱에서 회사 또는 학교 계정을 설정한 경우 다시 수행할 필요가 없습니다.

1. **추가 보안 확인** 페이지의 **응답 방법** 섹션에서 **인증자 앱 설정** 단추를 선택 합니다.

    ![Microsoft Authenticator 앱에서 회사 또는 학교 계정 설정](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

2. 모바일 장치를 사용 하 여 QR 코드를 스캔 하는 등 화면의 지시에 따라 다음을 선택 합니다.

    사용자 정보를 확인 하기 위해 Microsoft Authenticator 앱을 통해 알림을 승인 하 라는 메시지가 표시 됩니다.

3. **저장**을 선택합니다.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱에서 계정 또는 장치 삭제

Microsoft Authenticator 앱에서 계정을 삭제할 수 있으며 회사 또는 학교 계정에서 장치를 삭제할 수 있습니다. 일반적으로 장치를 삭제 하 여 계정에서 분실 하거나 도난당 하거나 오래 된 장치를 영구적으로 제거 하 고, 계정을 삭제 하 여 일부 연결 문제를 해결 하거나 계정 변경 (예: 새 사용자 이름)을 해결 합니다.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>회사 또는 학교 계정에서 장치를 삭제 하려면

1. **추가 보안 확인** 페이지의 **응답 방법** 섹션에서 **인증자 앱 설정** 단추를 선택 합니다.

2. **저장**을 선택합니다.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱에서 계정을 삭제 하려면

- Microsoft Authenticator 앱에서 삭제 하려는 장치 옆에 있는 **삭제** 단추를 선택 합니다.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>신뢰할 수 있는 장치에서 2 단계 확인 메시지 켜기

조직 설정에 따라 브라우저에서 2 단계 인증을 수행할 때 **X 일 동안 다시 묻지 않음** 이라는 확인란이 표시 될 수 있습니다. 이 확인란을 선택 하 여 2 단계 확인 메시지를 중지 한 후 장치를 분실 하거나 장치가 손상 될 가능성이 있는 경우에는 계정을 보호 하기 위해 2 단계 확인 메시지를 다시 설정 해야 합니다. 그러나 단일 장치에 대 한 프롬프트를 다시 켤 수 없습니다. 모든 장치에 대 한 프롬프트를 동시에 켜야 합니다.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>장치에 대해 2 단계 확인 메시지를 다시 설정 하려면

- **추가 보안 인증** 페이지에서 **이전에 신뢰할 수 있는 장치에서 multi-factor authentication 복원**을 선택 합니다.

    다음 번에 장치에서 로그인 하면 2 단계 인증을 수행 하 라는 메시지가 표시 됩니다.

## <a name="next-steps"></a>다음 단계

2 단계 인증 설정을 추가 하거나 업데이트 한 후에 앱 암호를 관리 하거나, 로그인 하거나, 몇 가지 일반적인 2 단계 확인 관련 문제에 대 한 도움을 받을 수 있습니다.

- 2 단계 인증을 지원 하지 않는 앱에 대 한 [2 단계 인증에 대 한 앱 암호를 관리](multi-factor-authentication-end-user-app-passwords.md) 합니다.

- [2 단계 인증을 사용 하 여 로그인](multi-factor-authentication-end-user-signin.md)

- [2 단계 인증에 대 한 도움말 보기](multi-factor-authentication-end-user-troubleshoot.md)
