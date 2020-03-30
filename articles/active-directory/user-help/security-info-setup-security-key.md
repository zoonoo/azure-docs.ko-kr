---
title: 보안 키를 확인 방법으로 설정 - Azure AD
description: 보안 정보(미리 보기) 페이지를 설정하여 FIDO2(Fast IDENTITY Online) 보안 키를 확인 방법으로 사용하도록 신원을 확인하는 방법
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: ee868ded0dc3a46f2ec20d0141aa2576cbf71f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062339"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>보안 키를 확인 방법으로 설정

보안 키를 조직 내에서 암호 없는 로그인 방법으로 사용할 수 있습니다. 보안 키는 고유한 PIN과 함께 직장 또는 학교 계정에 로그인하는 데 사용되는 물리적 장치입니다. 보안 키에는 실제 장치와 사용자만 알고 있는 것이 있어야 하기 때문에 사용자 이름 및 암호보다 더 강력한 확인 방법으로 간주됩니다.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> 보안 키 옵션이 표시되지 않으면 조직에서 확인에 이 옵션을 사용하도록 허용하지 않을 수 있습니다. 이 경우 다른 방법을 선택하거나 조직의 헬프 데스크에 문의하여 추가 지원을 받아야 합니다.

## <a name="security-verification-versus-password-reset-authentication"></a>보안 확인 및 암호 재설정 인증

보안 정보 방법은 2단계 보안 확인 및 암호 재설정에 모두 사용됩니다. 그러나 모든 보안 정보 방법을 둘 모두에 사용할 수 있는 것은 아닙니다.

| 방법 | 사용 대상 |
| ------ | -------- |
| 인증자 앱 | 2단계 인증 및 암호 재설정 인증입니다. |
| 문자 메시지 | 2단계 인증 및 암호 재설정 인증입니다. |
| 전화 통화 | 2단계 인증 및 암호 재설정 인증입니다. |
| 보안 키 | 2단계 인증 및 암호 재설정 인증입니다. |
| 이메일 계정 | 암호 재설정 인증 전용입니다. 2단계 인증에는 다른 보안 정보 방법을 선택해야 합니다. |
| 보안 질문 | 암호 재설정 인증 전용입니다. 2단계 인증에는 다른 보안 정보 방법을 선택해야 합니다. |

## <a name="what-is-a-security-key"></a>보안 키란 무엇입니까?

현재 [빠른 ID 온라인(FIDO) (FIDO2)](https://fidoalliance.org/fido2/) 암호없는 인증 프로토콜을 사용하여 보안 키의 여러 디자인과 공급자를 지원합니다. 이러한 키를 사용하면 지원되는 장치 및 웹 브라우저에서 조직의 클라우드 기반 리소스에 액세스하기 위해 직장 또는 학교 계정에 로그인할 수 있습니다.

관리자 또는 조직에서 직장 또는 학교 계정에 필요한 경우 보안 키를 제공합니다. 장치에 연결하는 USB 키 또는 NFC 리더에서 탭하는 NFC 키 와 같은 다양한 유형의 보안 키가 있습니다. 보안 키에 대한 자세한 정보(형식 포함)는 제조업체 설명서에서 확인할 수 있습니다.

> [!Note]
> FIDO2 보안 키를 사용할 수 없는 경우 Microsoft 인증자 앱 또는 Windows Hello와 같은 다른 암호 없는 확인 방법을 사용할 수 있습니다. Microsoft 인증자 앱에 대한 자세한 내용은 [Microsoft 인증자 앱이란 무엇입니까?](user-help-auth-app-overview.md) Windows 안녕하세요에 대한 자세한 내용은 [Windows Hello 개요를](https://www.microsoft.com/windows/windows-hello)참조하십시오.

## <a name="before-you-begin"></a>시작하기 전에

보안 키를 등록하려면 먼저 다음 이 사실이 어야 합니다.

- 관리자가 조직 내에서 사용할 수 위해 이 기능을 사용 설정했습니다.

- Windows 10 May 2019 업데이트를 실행 하 고 지원 되는 브라우저를 사용 하 여 장치에 있습니다.

- 관리자 또는 조직에서 승인한 물리적 보안 키가 있습니다. 보안 키는 FIDO2 와 Microsoft 를 모두 준수해야 합니다. 보안 키와 호환되는지 여부에 대한 질문이 있는 경우 조직의 헬프 데스크에 문의하십시오.

## <a name="register-your-security-key"></a>보안 키 등록

키를 사용하여 직장 또는 학교 계정에 로그인하려면 먼저 보안 키를 만들고 고유한 PIN을 제공해야 합니다. 계정에 최대 10개의 키가 등록되어 있을 수 있습니다. 

1. **내** https://myprofile.microsoft.com 프로필 페이지로 이동하여 아직 로그인하지 않은 경우 로그인합니다.

2. **보안 정보를**선택하고 **방법 추가를**선택한 다음 메서드 드롭다운 목록에서 **보안 키를** 선택합니다. **Add a method**

    ![보안 키를 선택한 방법 상자 추가](media/security-info/security-info-security-key-add-method.png)

3. **추가를**선택한 다음 **USB 장치** 또는 **NFC 장치**중 에서 사용할 보안 키 유형을 선택합니다.

    ![USB 또는 NFC 유형의 보안 키가 있는지 선택](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > 어떤 유형의 보안 키가 있는지 잘 모르는 경우 제조업체 설명서를 참조하십시오. 제조업체에 대해 잘 모르는 경우 조직의 헬프 데스크에 문의하여 도움을 받으십시오.

4. 보안 키를 물리적으로 사용할 수 있게 한 다음 **보안 키** 상자에서 **다음**을 선택합니다.

    ![보안 키 시작 등록 상자](media/security-info/security-info-security-key-start-setup.png)

    새 로그인 방법을 설정하는 데 도움이 되는 새 상자가 나타납니다.

5. 새 **로그인 메서드 설정** 상자에서 **다음을**선택한 다음 다음을 선택합니다.

    - 보안 키가 USB 장치인 경우 보안 키를 장치의 USB 포트에 삽입합니다.

    - 보안 키가 NFC 장치인 경우 판독기의 보안 키를 탭합니다.

6. 고유한 보안 키 PIN을 **Windows 보안** 상자에 입력한 다음 **확인을**선택합니다.

    새 로그인 방법 **상자 설정으로** 돌아갑니다.

7. **다음**을 선택합니다.

8. **보안 정보** 페이지로 돌아가서 새 보안 키에 대해 나중에 인식할 이름을 입력한 다음 **다음을**선택합니다.

    ![보안 정보 페이지, 보안 키 이름 지정](media/security-info/security-info-security-key-name.png)

    보안 키가 등록되어 직장 또는 학교 계정에 로그인할 때 사용할 준비가 되었습니다.

9. **보안 키** 상자를 닫을 **완료를** 선택합니다.

    **보안 정보** 페이지가 보안 키 정보로 업데이트됩니다.

    ![등록된 모든 메서드가 표시된 보안 정보 페이지](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>보안 정보에서 보안 키 삭제

보안 키를 잘못 사용하거나 더 이상 사용하지 않으려는 경우 보안 정보에서 키를 삭제할 수 있습니다. 이렇게 하면 보안 키가 직장 이나 학교 계정과 함께 사용되지 않지만 보안 키는 데이터 및 자격 증명 정보를 계속 저장합니다. 보안 키 자체에서 데이터 및 자격 증명 정보를 삭제하려면 이 문서의 [Microsoft 호환 보안 키](#reset-your-security-key) 재설정 섹션의 지침을 따라야 합니다.

1. 제거할 보안 키에서 **링크 삭제를** 선택합니다.

2. 보안 삭제 **키** 상자에서 **확인을** 선택합니다.

    보안 키가 삭제되면 더 이상 직장 또는 학교 계정에 로그인하는 데 사용할 수 없습니다.

>[!Important]
>실수로 보안 키를 삭제한 경우 이 문서의 보안 키 를 [등록하는 방법](#register-your-security-key) 섹션의 지침을 사용하여 다시 등록할 수 있습니다.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Windows 설정에서 보안 키 설정 관리

보안 키를 재설정하고 새 보안 키 PIN을 만드는 등 **Windows 설정** 앱에서 보안 키 설정을 관리할 수 있습니다.

### <a name="reset-your-security-key"></a>보안 키 재설정

실제 보안 키에 저장된 모든 계정 정보를 삭제하려면 키를 공장 기본값으로 되돌려야 합니다. 보안 키를 재설정하면 키에서 모든 것이 삭제되므로 다시 시작할 수 있습니다.

>[!IMPORTANT]
>보안 키를 재설정하면 키에서 모든 것이 삭제되어 공장 기본값으로 재설정됩니다.
>
> **모든 데이터와 자격 증명이 지워집니다.**

#### <a name="to-reset-your-security-key"></a>보안 키를 재설정하려면

1. Windows 설정 앱을 열고 **계정을**선택하고 **로그인 옵션을**선택하고 보안 **키를**선택한 다음 **에서 관리를**선택합니다.

2. 보안 키를 USB 포트에 삽입하거나 NFC 리더를 탭하여 신원을 확인합니다.

3. 특정 보안 키 제조업체에 따라 화면상의 지침을 따르십시오. 주요 제조업체가 화면 지침에 나열되지 않은 경우 자세한 내용은 제조업체 사이트를 참조하십시오.

4. **닫기를** 선택하여 관리 화면 **닫기를 선택합니다.**

### <a name="create-a-new-security-key-pin"></a>새 보안 키 PIN 만들기

보안 키에 대한 새 보안 키 PIN을 만들 수 있습니다.

#### <a name="to-create-a-new-security-key-pin"></a>새 보안 키 PIN을 만들려면

1. Windows 설정 앱을 열고 **계정을**선택하고 **로그인 옵션을**선택하고 보안 **키를**선택한 다음 **에서 관리를**선택합니다.

2. 보안 키를 USB 포트에 삽입하거나 NFC 리더를 탭하여 신원을 확인합니다.
3. 보안 키 **PIN** 영역에서 **추가를** 선택하고 새 보안 키 PIN을 입력하고 확인한 다음 **확인을**선택합니다.

     보안 키는 직장 또는 학교 계정과 함께 사용할 새 보안 키 PIN으로 업데이트됩니다. PIN을 다시 변경하려는 경우 **변경** 버튼을 선택할 수 있습니다.
4. **닫기를** 선택하여 관리 화면 **닫기를 선택합니다.**

## <a name="additional-security-info-methods"></a>추가 보안 정보 메서드

보안 키를 등록하려면 하나 이상의 추가 보안 확인 방법이 등록되어 있어야 합니다. 자세한 내용은 [개요 섹션을](security-info-add-update-methods-overview.md) 참조하십시오. 

## <a name="next-steps"></a>다음 단계

- 암호 없는 확인 방법에 대 한 자세한 내용은 읽기 [마이크로소프트의 Azure AD FIDO2 보안 키의 공개 미리 보기를 시작, 암호 없는 로그인 블로그를 사용](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) 하 여, 또는 [읽기는 마이크로소프트 인증자 응용 프로그램?](user-help-auth-app-overview.md) 및 [Windows 안녕하세요 개요](https://www.microsoft.com/windows/windows-hello) 문서.

- [Microsoft 를 준수하는 보안 키에](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key)대한 자세한 내용은 .

- 분실했거나 잊어버린 경우 [암호 재설정 포털](https://passwordreset.microsoftonline.com/)에서 암호를 다시 설정하거나 [회사 또는 학교 암호 재설정](active-directory-passwords-update-your-own-password.md) 문서의 단계를 수행합니다.

- [Microsoft 계정에 로그인할 수 없는 경우](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) 문서에서 로그인 문제에 대한 문제 해결 팁 및 도움말을 확인합니다.
