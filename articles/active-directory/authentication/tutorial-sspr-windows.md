---
title: Windows 10 로그인 화면의 Azure AD SSPR
description: 이 자습서에서는 지원 센터 호출을 줄이기 위해 Windows 10 로그인 화면에서 암호 재설정을 사용하도록 설정하겠습니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea65120a2a735477d048b9012e160e0cdafe8835
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66253079"
---
# <a name="tutorial-azure-ad-password-reset-from-the-login-screen"></a>자습서: 로그인 화면에서 Azure AD 암호 재설정

이 자습서에서는 사용자가 Windows 10 로그인 화면에서 암호를 재설정할 수 있도록 설정합니다. 새로운 Windows 10 2018년 4월 업데이트에서는 **Azure AD 조인 디바이스** 또는 **하이브리드 Azure AD 조인** 디바이스를 사용하는 사용자는 로그인 화면에서 "암호 재설정" 링크를 사용할 수 있습니다. 사용자가 이 링크를 클릭하면 익숙한 SSPR(셀프 서비스 암호 재설정) 환경으로 이동됩니다. 사용자가 잠겨 있는 경우 이 프로세스는 온-프레미스 Active Directory에서 계정의 잠금을 해제하지 않습니다.

> [!div class="checklist"]
> * Intune을 사용하여 암호 재설정 링크 구성
> * 필요에 따라 Windows 레지스트리를 사용하여 구성
> * 사용자에게 무엇이 보이는지 이해

## <a name="prerequisites"></a>필수 조건

* Windows 10, 2018년 4월 업데이트(v1803) 이상의 버전을 사용 중이고 디바이스 상태는 다음 중 하나여야 합니다.
   * [Azure AD 조인](../device-management-azure-portal.md) 또는
   * [하이브리드 Azure AD 조인](../device-management-hybrid-azuread-joined-devices-setup.md)(도메인 컨트롤러에 네트워크가 연결됨)
* Azure AD 셀프 서비스 암호 재설정을 사용하도록 설정해야 합니다.
* Windows 10 디바이스에서 프록시 서버 또는 방화벽을 지지하는 경우 HTTPS 트래픽(443 포트) 허용 URL 목록에 `passwordreset.microsoftonline.com` 및 `ajax.aspnetcdn.com` URL을 추가해야 합니다.
* Windows 10용 SSPR은 머신 수준 프록시에서만 지원됩니다.
* 사용자 환경에서 이 기능을 시도하기 전에 아래 제한 사항을 검토하세요.
* 이미지를 사용하는 경우 sysprep을 수행하기 전에 기본 제공 관리자에 대한 웹 캐시를 지운 후에 CopyProfile 단계를 수행해야 합니다. 이에 대한 자세한 내용은 [사용자 지정 기본 사용자 프로필을 사용할 때 성능 저하](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile) 지원 문서에서 찾을 수 있습니다.

## <a name="configure-reset-password-link-using-intune"></a>Intune을 사용하여 암호 재설정 링크 구성

Intune을 사용하여 로그인 화면에서 암호 재설정을 사용하도록 설정하는 가장 유연한 방법은 구성 변경을 배포하는 것입니다. Intune을 사용하면 여러분이 정의하는 특정 머신 그룹에 구성 변경 내용을 배포할 수 있습니다. 이 메서드는 장치의 Intune 등록이 필요합니다.

### <a name="create-a-device-configuration-policy-in-intune"></a>Intune에서 장치 구성 정책 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **Intune** 클릭
2. **장치 구성** > **프로필** > **프로필 만들기**로 이동하여 새 장치 구성 프로필 만들기
   * 프로필의 의미 있는 이름 입력
   * 필요에 따라 프로필에 대한 의미 있는 설명 입력
   * 플랫폼 **Windows 10 이상**
   * 프로필 형식 **사용자 지정**

3. **설정** 구성
   * 다음 OMA URI 설정을 **추가**하여 암호 재설정 링크를 사용하도록 설정
      * 설정이 하는 일을 설명하는 의미 있는 이름 입력
      * 필요에 따라 설정에 대한 의미 있는 설명 입력
      * **OMA URI**을 `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`으로 설정
      * **데이터 형식**을 **정수**로 설정
      * **값**을 **1**로 설정
      * **확인**
   * **확인**
4. **만들기**

### <a name="assign-a-device-configuration-policy-in-intune"></a>Intune에서 장치 구성 정책 할당

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>장치 구성 정책을 적용할 그룹 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인하여 **Azure Active Directory** 클릭
2. **사용자 및 그룹** > **모든 그룹** > **새 그룹**으로 이동
3. 그룹 이름을 입력하고 **구성원 자격 유형** 아래에서 **할당됨** 선택
   * **구성원** 아래에서 정책을 적용할 Azure AD 가입 Windows 10 디바이스 선택
   * **선택** 클릭
4. **만들기**

그룹을 만드는 방법에 대한 자세한 내용은 [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](../fundamentals/active-directory-manage-groups.md) 문서에서 찾을 수 있습니다.

#### <a name="assign-device-configuration-policy-to-device-group"></a>장치 그룹에 장치 구성 정책 할당

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **Intune** 클릭
2. **장치 구성** > **프로필**로 이동한 후 앞에서 만든 프로필을 클릭하여 앞에서 만든 장치 구성 프로필 찾기
3. 해당 프로필을 디바이스 그룹에 할당 
   * **포함** > **포함할 그룹 선택** 아래에서 **할당** 클릭
   * 앞에서 만든 그룹을 선택하고 **선택** 클릭
   * 설정 메뉴에서 **저장**

   ![할당][Assignment]

Intune을 사용하여 로그인 화면에 암호 재설정 링크를 사용하도록 설정하기 위한 장치 구성 정책을 만들어서 할당했습니다.

## <a name="configure-reset-password-link-using-the-registry"></a>레지스트리를 사용하여 암호 재설정 링크 구성

1. 관리 자격 증명을 사용하여 Windows PC에 로그인
2. 관리자 권한으로 **regedit** 실행
3. 다음 레지스트리 키를 설정
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>사용자에게 표시되는 내용

정책을 구성하고 할당했으니, 사용자에 어떤 변화가 있을까요? 로그인 화면에서 암호를 재설정할 수 있다는 사실을 사용자가 어떻게 알 수 있을까요?

![로그인 화면][LoginScreen]

이제 사용자가 로그인을 시도하면 셀프 서비스 암호 재설정 환경을 제공하는 암호 재설정 링크가 로그인 화면에 표시됩니다. 사용자는 이 기능을 사용하면 다른 디바이스를 사용하여 웹 브라우저에 액세스할 필요 없이 암호를 재설정할 수 있습니다.

이 기능을 사용하는 방법에 대한 지침은 [회사 또는 학교 암호 재설정](../user-help/active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in)에서 찾을 수 있습니다.

Azure AD 감사 로그에는 암호 재설정이 발생하는 IP 주소 및 ClientType에 대한 정보가 포함됩니다.

![예제 로그온 화면 Azure AD 감사 로그의 암호 재설정](media/tutorial-sspr-windows/windows-sspr-azure-ad-audit-log.png)

사용자가 Windows 10 디바이스 로그인 화면에서 암호를 재설정하면 “defaultuser1”이라는 낮은 권한의 임시 계정이 생성됩니다. 이 계정은 암호 재설정 프로세스를 안전하게 유지하는 데 사용됩니다. 계정 자체는 무작위로 생성된 암호를 가지고 있으며, 디바이스 로그인 시 표시되지 않으며, 사용자가 암호를 설정하면 자동으로 제거됩니다. 여러 개의 “defaultuser” 프로필이 있을 수 있지만 무시해도 무방합니다.

## <a name="limitations"></a>제한 사항

Hyper-V를 사용하여 이 기능을 테스트할 때에는 "암호 재설정" 링크가 표시되지 않습니다.

* 테스트에 사용하는 VM으로 이동하여 **보기**를 클릭한 다음 **고급 세션**을 선택 취소합니다.

원격 데스크톱 또는 고급 VM 세션을 사용하여 이 기능을 테스트할 때에는 “암호 재설정” 링크가 표시되지 않습니다.

* 현재 원격 데스크톱에서는 암호 재설정이 지원되지 않습니다.

Windows 10 1809 이전 버전에서 정책이 Ctrl+Alt+Del을 요구하는 경우 **암호 재설정**이 작동하지 않습니다.

잠금 화면 알림이 꺼진 경우 **암호 재설정**이 작동하지 않습니다.

다음 정책 설정은 암호를 재설정하는 기능을 방해하는 것으로 알려져 있습니다.

   * HideFastUserSwitching이 사용하도록 설정 또는 1로 설정됨
   * DontDisplayLastUserName이 사용하도록 설정 또는 1로 설정됨
   * NoLockScreen이 사용하도록 설정 또는 1로 설정됨
   * EnableLostMode가 디바이스에서 설정됨
   * Explorer.exe는 사용자 지정 셸로 바뀜

이 기능은 802.1x 네트워크 인증이 배포된 네트워크에 및 “사용자가 로그온하기 직전에 수행” 옵션에는 작동하지 않습니다. 802.1x 네트워크 인증이 배포된 네트워크의 경우 머신 인증을 사용하여 이 기능을 사용하는 것이 좋습니다.

하이브리드 도메인 조인 시나리오의 경우, Active Directory 도메인 컨트롤러 없이도 SSPR 워크플로가 성공적으로 완료되는 시나리오가 있습니다. Active Directory 도메인 컨트롤러와 통신할 수 없을 때(예: 원격으로 작업할 때) 사용자가 암호 재설정 프로세스를 완료할 경우에는 디바이스가 도메인 컨트롤러와 통신하고 캐시된 자격 증명을 업데이트할 수 있을 때까지 사용자가 디바이스에 로그인할 수 없습니다. **새 암호를 처음 사용하려면 도메인 컨트롤러와 연결해야 합니다**.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 구성한 기능을 더 이상 사용하지 않으려면 앞에서 만든 Intune 장치 구성 프로필 또는 레지스트리 키를 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 사용자가 Windows 10 로그인 화면에서 암호를 재설정할 수 있도록 설정했습니다. Azure Identity Protection을 셀프 서비스 암호 재설정 및 Multi-Factor Authentication 환경에 통합하는 방법을 보려면 그 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [로그인 시 위험 평가](tutorial-risk-based-sspr-mfa.md)

[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Windows 10 장치 그룹에 Intune 장치 구성 정책 할당"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Windows 10 로그인 화면의 암호 재설정 링크"
