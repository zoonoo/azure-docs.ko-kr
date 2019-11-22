---
title: 보안 키를 확인 방법으로 설정-Azure AD
description: FIDO2 (Fast Identity Online) 보안 키를 확인 방법으로 사용 하기 위해 id를 확인 하도록 보안 정보 (미리 보기) 페이지를 설정 하는 방법입니다.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d02ca3a8b5b617edb8ee00ae7d15ea7e94cae80
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278956"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>보안 키를 확인 방법으로 설정 합니다.

보안 키를 조직 내에서 암호 없는 로그인 방법으로 사용할 수 있습니다. 보안 키는 회사 또는 학교 계정에 로그인 하기 위해 고유한 PIN과 함께 사용 되는 물리적 장치입니다. 보안 키에는 물리적 장치와 사용자가 알고 있어야 하는 사항이 있으므로 사용자 이름 및 암호 보다 더 강력한 확인 방법으로 간주 됩니다.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Important]
>이 콘텐츠는 사용자를 위한 것입니다. 관리자의 경우 [Azure Active Directory 문서](https://docs.microsoft.com/azure/active-directory)에서 Azure AD(Azure Active Directory) 환경을 설정하고 관리하는 방법에 대한 자세한 정보를 찾을 수 있습니다.

## <a name="what-is-a-security-key"></a>보안 키 란?

현재 [FIDO (Fast Identity Online)](https://fidoalliance.org/fido2/) (FIDO2) 암호 없는 인증 프로토콜을 사용 하 여 보안 키의 여러 디자인 및 공급자를 지원 합니다. 이러한 키를 통해 회사 또는 학교 계정에 로그인 하 여 지원 되는 장치 및 웹 브라우저에서 조직의 클라우드 기반 리소스에 액세스할 수 있습니다.

관리자 또는 조직에서는 회사 또는 학교 계정에 필요한 보안 키를 제공 합니다. 장치에 연결 하는 USB 키 또는 NFC 판독기를 누르는 NFC 키와 같이 사용할 수 있는 다양 한 유형의 보안 키가 있습니다. 보안 키에 대 한 자세한 내용은 제조업체의 설명서에서 확인할 수 있습니다.

> [!Note]
> FIDO2 보안 키를 사용할 수 없는 경우 Microsoft Authenticator 앱 또는 Windows Hello와 같이 사용할 수 있는 다른 암호 없는 인증 방법이 있습니다. Microsoft Authenticator 앱에 대 한 자세한 내용은 [Microsoft Authenticator 앱 이란?](user-help-auth-app-overview.md)을 참조 하세요. Windows Hello에 대 한 자세한 내용은 [Windows hello 개요](https://www.microsoft.com/windows/windows-hello)를 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

보안 키를 등록 하려면 다음 조건이 충족 되어야 합니다.

- 관리자가 조직 내에서이 기능을 사용 하도록 설정 했습니다.

- Windows 10 2019 5 월 업데이트를 실행 하는 장치에서 지원 되는 브라우저를 사용 하 고 있습니다.

- 관리자 또는 조직에서 승인한 물리적 보안 키가 있습니다. 보안 키는 FIDO2 및 Microsoft 규격 이어야 합니다. 보안 키와 호환 여부에 대 한 질문이 있는 경우 조직의 지원 센터에 문의 하세요.

## <a name="register-your-security-key"></a>보안 키 등록

키를 사용 하 여 회사 또는 학교 계정에 로그인 하려면 보안 키를 만들고 고유한 PIN을 지정 해야 합니다. 계정에 최대 10 개의 키가 등록 되어 있을 수 있습니다. 

1. https://myprofile.microsoft.com에서 **내 프로필** 페이지로 이동 하 여 아직 로그인 하지 않았으면 로그인 합니다.

2. **보안 정보**를 선택 하 **고 메서드 추가**를 선택한 다음 **방법 추가** 드롭다운 목록에서 **보안 키** 를 선택 합니다.

    ![보안 키가 선택 된 메서드 추가 상자](media/security-info/security-info-security-key-add-method.png)

3. **추가**를 선택한 다음, **USB 장치** 또는 **NFC 장치**중에서 보유 한 보안 키의 유형을 선택 합니다.

    ![USB 또는 NFC 유형의 보안 키를 선택 합니다.](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > 보안 키 유형을 잘 모르는 경우 제조업체의 설명서를 참조 하세요. 제조업체에 대해 잘 모르겠으면 조직의 지원 센터에 문의 하십시오.

4. 보안 키를 물리적으로 사용할 수 있도록 한 다음 **보안 키** 상자에서 **다음**을 선택 합니다.

    ![보안 키 시작 등록 상자](media/security-info/security-info-security-key-start-setup.png)

    새 로그인 방법을 설정 하는 과정을 안내 하는 새 상자가 표시 됩니다.

5. **새 로그인 방법 설정** 상자에서 **다음**을 선택 하 고 다음을 선택 합니다.

    - 보안 키가 USB 장치인 경우 장치의 USB 포트에 보안 키를 삽입 합니다.

    - 보안 키가 NFC 장치인 경우 사용자의 독자에 게 보안 키를 탭 합니다.

6. **Windows 보안** 상자에 고유한 보안 키 PIN을 입력 하 고 **확인**을 선택 합니다.

    **새 로그인 방법 설정** 상자로 돌아갑니다.

7. **다음**을 선택합니다.

8. **보안 정보** 페이지로 돌아가 나중에 새 보안 키에 대해 인식할 이름을 입력 한 후 **다음**을 선택 합니다.

    ![보안 정보 페이지, 보안 키 이름 지정](media/security-info/security-info-security-key-name.png)

    보안 키가 등록 되 고 회사 또는 학교 계정에 로그인 하는 데 사용할 수 있도록 준비 됩니다.

9. **완료** 를 선택 하 여 **보안 키** 상자를 닫습니다.

    보안 **정보** 페이지는 보안 키 정보로 업데이트 됩니다.

    ![모든 등록 된 메서드가 표시 된 보안 정보 페이지](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>보안 정보에서 보안 키 삭제

보안 키를 잃어버리지 하거나 더 이상 사용 하지 않으려는 경우 보안 정보에서 키를 삭제할 수 있습니다. 이렇게 하면 보안 키가 회사 또는 학교 계정에서 사용 되지 않고 보안 키가 계속 해 서 데이터 및 자격 증명 정보를 저장 합니다. 보안 키 자체에서 데이터 및 자격 증명 정보를 삭제 하려면이 문서의 [Microsoft 호환 보안 키 다시 설정](#reset-your-security-key) 섹션에 있는 지침을 따라야 합니다.

1. 제거할 보안 키에서 **삭제** 링크를 선택 합니다.

2. **보안 키 삭제** 상자에서 **확인을** 선택 합니다.

    보안 키가 삭제 되 고 더 이상 회사 또는 학교 계정에 로그인 하는 데 사용할 수 없습니다.

>[!Important]
>보안 키를 실수로 삭제 하는 경우이 문서의 [보안 키를 등록 하는 방법](#register-your-security-key) 섹션에 설명 된 지침에 따라 다시 등록할 수 있습니다.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Windows 설정에서 보안 키 설정 관리

보안 키 재설정 및 새 보안 키 PIN 만들기를 포함 하 여 **Windows 설정** 앱에서 보안 키 설정을 관리할 수 있습니다.

### <a name="reset-your-security-key"></a>보안 키 다시 설정

물리적 보안 키에 저장 된 모든 계정 정보를 삭제 하려면 키를 공장 기본값으로 다시 반환 해야 합니다. 보안 키를 다시 설정 하면 키에서 모든 항목을 삭제 하 여 다시 시작할 수 있습니다.

>[!IMPORTANT]
>보안 키를 다시 설정 하면 키에서 모든 항목을 삭제 하 고 공장 기본값으로 다시 설정 합니다.
>
> **모든 데이터 및 자격 증명은 지워집니다.**

#### <a name="to-reset-your-security-key"></a>보안 키를 다시 설정 하려면

1. Windows 설정 앱을 열고 **계정**을 선택 하 고 **로그인 옵션**을 선택한 다음 **보안 키**를 선택 하 고 **관리**를 선택 합니다.

2. 보안 키를 USB 포트에 삽입 하거나 NFC 판독기를 탭 하 여 id를 확인 합니다.

3. 특정 보안 키 제조업체에 따라 화면의 지시를 따릅니다. 키 제조업체가 화면에 표시 되지 않는 경우 자세한 내용은 제조업체 사이트를 참조 하십시오.

4. **닫기** 를 선택 하 여 **관리** 화면을 닫습니다.

### <a name="create-a-new-security-key-pin"></a>새 보안 키 PIN 만들기

보안 키에 대 한 새 보안 키 PIN을 만들 수 있습니다.

#### <a name="to-create-a-new-security-key-pin"></a>새 보안 키 PIN을 만들려면

1. Windows 설정 앱을 열고 **계정**을 선택 하 고 **로그인 옵션**을 선택한 다음 **보안 키**를 선택 하 고 **관리**를 선택 합니다.

2. 보안 키를 USB 포트에 삽입 하거나 NFC 판독기를 탭 하 여 id를 확인 합니다.
3. **보안 키 pin** 영역에서 **추가** 를 선택 하 고 새 보안 키 pin을 입력 한 다음 **확인**을 선택 합니다.

     보안 키는 회사 또는 학교 계정에 사용 하기 위해 새 보안 키 PIN으로 업데이트 됩니다. PIN을 다시 변경 하기로 결정 한 경우에는 **변경** 단추를 선택할 수 있습니다.
4. **닫기** 를 선택 하 여 **관리** 화면을 닫습니다.

## <a name="additional-security-info-methods"></a>추가 보안 정보 방법

보안 키를 등록 하려면 하나 이상의 추가 보안 확인 방법이 등록 되어 있어야 합니다. 자세한 내용은 [개요 섹션](security-info-add-update-methods-overview.md) 을 참조 하세요. 

## <a name="next-steps"></a>다음 단계

- 암호 없는 인증 방법에 대 한 자세한 내용은 [Microsoft의 Azure AD에서 FIDO2 보안 키에 대 한 공개 미리 보기를 시작 하 고, 암호 없는 로그인 블로그를 사용 하도록 설정](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) 하거나, [Microsoft Authenticator 앱 이란?](user-help-auth-app-overview.md) 및 [Windows Hello 개요](https://www.microsoft.com/windows/windows-hello) 문서를 참조 하세요.

- [Microsoft 규격 보안 키](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key)에 대 한 자세한 정보.

- 분실했거나 잊어버린 경우 [암호 재설정 포털](https://passwordreset.microsoftonline.com/)에서 암호를 다시 설정하거나 [회사 또는 학교 암호 재설정](active-directory-passwords-update-your-own-password.md) 문서의 단계를 수행합니다.

- [Microsoft 계정에 로그인할 수 없는 경우](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) 문서에서 로그인 문제에 대한 문제 해결 팁 및 도움말을 확인합니다.
