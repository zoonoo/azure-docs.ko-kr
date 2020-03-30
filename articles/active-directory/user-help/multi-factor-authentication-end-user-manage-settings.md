---
title: 2단계 확인 방법 및 설정 변경 - Azure Active Directory
description: 추가 보안 확인 페이지에서 직장 또는 학교 계정의 보안 확인 방법 및 설정을 변경하는 방법을 알아봅니다.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: curtand
ms.openlocfilehash: 5949f04ecc28a88e340a9c2de530031793f193a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253248"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>2단계 확인 방법 및 설정 변경

직장 또는 학교 계정에 대한 보안 확인 방법을 설정한 후 다음을 포함한 관련 세부 정보를 업데이트할 수 있습니다.

- 기본 보안 확인 방법

- 전화번호와 같은 보안 인증 방법 세부 정보

- 인증자 앱에서 장치를 설정 하거나 삭제 하는 인증자 응용 프로그램

## <a name="using-the-additional-security-verification-page"></a>추가 보안 확인 페이지 사용

조직에서 2단계 인증을 켜고 관리하는 방법에 대한 구체적인 단계를 제공한 경우 먼저 해당 지침을 따라야 합니다. 그렇지 않으면 [추가 보안 확인](https://docs.microsoft.com/azure/active-directory/user-help/multi-factor-authentication-end-user-first-time) 페이지에서 보안 확인 방법 설정으로 이동합니다.

>[!Note]
>화면에 표시되는 내용이 이 문서에서 다루는 내용과 일치하지 않는 경우 관리자가 **보안 정보(미리 보기)** 환경을 설정했거나 조직에서 사용자 지정 포털을 제공했거나 이 에 대해 알고 있는 내용이 일치하지 않는 것입니다. 새 보안 정보 경험에 대한 자세한 내용은 [보안 정보(미리 보기) 개요를](user-help-security-info-overview.md)참조하십시오. 조직의 사용자 지정 포털에 대한 자세한 내용은 조직의 헬프 데스크에 문의해야 합니다.

### <a name="to-get-to-the-additional-security-verification-page"></a>추가 보안 확인 페이지로 이동하려면

이 링크를 따라 [추가 보안 확인 페이지입니다.](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)

![추가 보안 확인 페이지, 사용 가능한 보안 확인 방법 세부 정보](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

다음 단계에 따라 **추가 보안 확인** 페이지로 이동하여 확인할 수도 있습니다.

1. 에 로그인합니다. [https://myapps.microsoft.com](https://myapps.microsoft.com)

1. 오른쪽 맨 위에서 계정 이름을 선택한 후 **프로필**을 선택합니다.

1. **추가 보안 확인을**선택합니다.  

    ![내 앱이 추가 보안 확인 페이지로 연결됩니다.](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>**추가 보안 확인** 페이지의 앱 **암호** 사용 섹션사용에 대한 자세한 내용은 [2단계 확인을 위한 앱 암호 관리를](multi-factor-authentication-end-user-app-passwords.md)참조하십시오. 앱 암호는 2단계 인증을 지원하지 않는 앱에만 사용해야 합니다.

## <a name="change-your-default-security-verification-method"></a>기본 보안 확인 방법 변경

사용자 이름과 비밀번호로 직장 또는 학교 계정에 로그인하면 선택한 보안 인증 방법이 자동으로 표시됩니다. 조직의 요구 사항에 따라 인증자 앱, 문자 메시지 또는 전화 통화를 통한 알림 또는 확인 코드일 수 있습니다.

사용 중이면 기본 보안 확인 방법을 변경하려는 경우 여기에서 변경할 수 있습니다.

### <a name="to-change-your-default-security-verification-method"></a>기본 보안 확인 방법을 변경하려면

1. 추가 **보안 확인** 페이지에서 기본 설정 옵션 목록에서 사용할 방법을 **선택합니다.** 모든 옵션이 표시되지만 조직에서 사용할 수 있는 옵션만 선택할 수 있습니다.

    - **앱을 통해 알림**: 인증자 앱을 통해 대기 확인 프롬프트가 있다는 알림을 받게 됩니다.

    - **내 인증 전화 전화**: 당신은 당신의 정보를 확인하도록 요청, 모바일 장치에서 전화를 받을 수 있습니다.

    - **내 인증 전화로 텍스트 코드**: 모바일 장치에서 문자 메시지의 일부로 확인 코드를 받게됩니다. 직장 또는 학교 계정의 확인 프롬프트에 이 코드를 입력해야 합니다.

    - **내 사무실 전화**전화 : 당신은 당신의 정보를 확인하도록 요청, 사무실 전화에 전화를 받을 수 있습니다.

    - **앱에서 인증 코드 사용**: 인증자 앱을 사용하여 직장 또는 학교 계정에서 프롬프트에 입력할 확인 코드를 가져옵니다.

2. **저장**을 선택합니다.

## <a name="add-or-change-your-phone-number"></a>전화번호 추가 또는 변경

**추가 보안 확인** 페이지에서 새 전화 번호를 추가하거나 기존 번호를 업데이트할 수 있습니다.

>[!Important]
>기본 휴대폰을 분실하거나 도난당한 경우 또는 새 휴대폰을 받고 원래 기본 전화 번호가 더 이상 없는 경우 계정에서 잠기지 않도록 보조 전화 번호를 추가하는 것이 좋습니다.

### <a name="to-change-your-phone-numbers"></a>전화번호를 변경하려면

1. 추가 보안 확인 페이지의 추가 보안 **확인** 페이지의 **어떻게 응답하시겠습니까?** 섹션에서 **인증 전화(기본** 모바일 장치)와 **Office 전화의**전화 번호 정보를 업데이트합니다.

1. **대체 인증 전화** 옵션 옆의 상자를 선택한 다음 기본 장치에 액세스할 수 없는 경우 문자 메시지 또는 전화 통화를 받을 수 있는 보조 전화 번호를 입력합니다.

1. **저장**을 선택합니다.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Microsoft 인증자 앱에 새 계정 추가

[당신은 안드로이드](https://play.google.com/store/apps/details?id=com.azure.authenticator) 또는 [iOS에](https://apps.apple.com/app/microsoft-authenticator/id983156458)대한 마이크로 소프트 인증 응용 프로그램에서 직장이나 학교 계정을 설정할 수 있습니다.

Microsoft 인증자 앱에서 직장 또는 학교 계정을 이미 설정한 경우 다시 설정할 필요가 없습니다.

1. 추가 보안 확인 페이지의 추가 보안 **확인** 페이지의 **어떻게 응답하시겠습니까?** 섹션에서 **인증자 앱 설정을**선택합니다.

    ![Microsoft 인증자 앱에서 직장 또는 학교 계정 설정](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. QR 코드를 스캔하기 위해 모바일 장치를 사용하는 것을 포함하여 화면의 지침을 따르고 **다음을**선택합니다.

    Microsoft 인증자 앱을 통해 알림을 승인하여 정보를 확인하라는 메시지가 표시됩니다.

1. **저장**을 선택합니다.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Microsoft 인증자 앱에서 계정 또는 장치 삭제

Microsoft 인증자 앱에서 계정을 삭제할 수 있으며 직장 또는 학교 계정에서 장치를 삭제할 수 있습니다. 일반적으로 계정에서 분실, 도난 또는 이전 기기를 영구적으로 제거하기 위해 기기를 삭제하고 계정을 삭제하여 일부 연결 문제를 해결하거나 새 사용자 이름과 같은 계정 변경 문제를 해결합니다.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>직장 또는 학교 계정에서 기기를 삭제하려면

1. **추가 보안 확인** 페이지의 응답 **방법** 섹션에서 **인증자 앱 설정** 단추를 선택합니다.

1. **저장**을 선택합니다.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Microsoft 인증자 앱에서 계정을 삭제하려면

Microsoft 인증자 앱에서 삭제하려는 장치 옆에 있는 **삭제** 단추를 선택합니다.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>신뢰할 수 있는 기기에서 2단계 인증 프롬프트 켜기

조직 설정에 따라 브라우저에서 2단계 인증을 수행할 때 **X 일 동안 다시 묻지 않는다는** 확인란이 표시될 수 있습니다. 2단계 인증 프롬프트를 중지하도록 이 옵션을 선택한 후 기기가 분실되거나 기기가 손상될 수 있는 경우 계정을 보호하기 위해 2단계 인증 메시지를 다시 켜야 합니다. 모든 기기에 대한 프롬프트를 동시에 켜야 합니다. 안타깝게도 특정 장치에 대해서만 프롬프트를 다시 켤 수 없습니다.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>장치에 대해 2단계 인증 프롬프트를 다시 켜려면

추가 [ **보안 확인** 페이지에서](#to-get-to-the-additional-security-verification-page) **이전에 신뢰할 수 있는 장치에서 다단계 인증 복원을 선택합니다.** 다음에 모든 기기에서 로그인할 때 2단계 인증을 수행하라는 메시지가 표시됩니다.

## <a name="next-steps"></a>다음 단계

2단계 인증 설정을 추가하거나 업데이트한 후 앱 암호를 관리하거나 로그인하거나 일반적인 2단계 확인 관련 문제에 대한 도움을 받을 수 있습니다.

- 2단계 인증을 지원하지 않는 앱에 [대해 2단계 인증을 위해 앱 암호를 관리합니다.](multi-factor-authentication-end-user-app-passwords.md)

- [2단계 인증을 사용하여 로그인하는 방법](multi-factor-authentication-end-user-signin.md)

- [2단계 검증을 통한 일반적인 문제 해결](multi-factor-authentication-end-user-troubleshoot.md)
