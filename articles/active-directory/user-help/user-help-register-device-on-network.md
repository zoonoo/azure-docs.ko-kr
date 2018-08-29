---
title: 조직의 네트워크에 개인 장치 등록 - Azure Active Directory | Microsoft Docs
description: 조직의 보호된 리소스에 액세스할 수 있도록 조직의 네트워크에서 개인 장치를 등록하는 방법을 알아봅니다.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 7126a47bd90168c7d86fe9fcc05fab0a60955063
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180691"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>조직의 네트워크에 개인 장치 등록
일반적으로 휴대폰이나 태블릿에 해당하는 개인 장치를 조직의 네트워크에 등록합니다. 장치가 등록되면 조직의 제한된 리소스에 액세스할 수 있게 됩니다.

>[!Note]
>이 문서에서는 데모용으로 Windows 장치를 사용하지만 iOS, Android 또는 macOS를 실행하는 장치를 등록할 수도 있습니다.

## <a name="what-happens-when-you-register-your-device"></a>장치를 등록할 때 나타나는 결과
조직의 네트워크에 장치를 등록하는 동안 다음 작업이 발생합니다.

- Windows에서 조직의 네트워크에 장치를 등록합니다.

- 조직의 선택에 따라, [Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) 또는 [보안 정보](user-help-security-info-overview.md)를 통해 2단계 인증을 설정하도록 요구될 수 있습니다.

- 조직의 선택에 따라, Microsoft Intune과 같은 모바일 장치 관리 기능에 자동으로 등록될 수도 있습니다. Microsoft Intune에 등록하는 방법에 대한 자세한 내용은 [Intune에서 장치 등록](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all)을 참조하세요.

- 개인 Microsoft 계정의 사용자 이름 및 암호를 사용하여 로그인 프로세스가 진행됩니다.

## <a name="to-register-your-windows-device"></a>Windows 장치를 등록하려면

네트워크에서 개인 장치를 등록하려면 다음 단계를 수행합니다.

1. **설정**을 열고 **계정**을 선택합니다.

    ![설정 화면의 계정](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. **Email & accounts**(이메일 및 계정)을 선택하고 **Join a Microsoft account**(Microsoft 계정 가입)를 선택합니다.

    ![이메일 및 계정과 Microsoft 계정 추가 링크](./media/user-help-register-device-on-network/register-device-email-and-accounts.png)

3. **Microsoft 계정 추가** 화면에서 개인 Microsoft 계정의 이메일 주소를 입력합니다.

    ![Microsoft 계정 추가 화면(이메일 포함)](./media/user-help-register-device-on-network/register-device-add-accounts.png)

4. **암호 입력** 화면에서 개인 Microsoft 계정의 암호를 입력하고, **로그인**을 선택합니다.

    ![암호 입력 화면](./media/user-help-register-device-on-network/register-device-enter-password.png)

5. 본인 여부 확인 요청(2단계 인증을 사용하는 경우) 및 Windows Hello 설정(필요한 경우)을 포함하는 나머지 등록 프로세스를 완료합니다.

## <a name="to-make-sure-youre-registered"></a>등록되었는지 확인하려면
설정을 통해 등록되었는지 확인할 수 있습니다.

1. **설정**을 열고 **계정**을 선택합니다.

    ![설정 화면의 계정](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. **Email & accounts**(이메일 및 계정)을 선택하고 개인 Microsoft 계정이 표시되는지 확인합니다.

    ![연결된 contoso 계정을 사용하여 회사 또는 학교 액세스 화면](./media/user-help-register-device-on-network/register-device-verify-account.png)

## <a name="next-steps"></a>다음 단계
조직의 네트워크에 개인 장치를 등록한 후에는 대부분의 리소스에 액세스할 수 있어야 합니다.

- 조직이 회사 장치의 가입을 원할 경우 [조직의 네트워크에 회사 장치 가입](user-help-join-device-on-network.md)을 참조하세요.



