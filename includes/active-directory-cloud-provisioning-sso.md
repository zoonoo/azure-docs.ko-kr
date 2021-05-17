---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 6d95e40623f17a39145778a2fc067dccc68fd872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95559749"
---
## <a name="steps-to-enable-single-sign-on"></a>Single Sign-On을 활성화하는 단계
클라우드 프로비저닝은 Single Sign-On과 함께 작동합니다.  현재는 에이전트가 설치될 때 SSO를 사용하도록 설정하는 옵션이 없지만 아래 단계를 사용하여 SSO를 사용하도록 설정하고 사용할 수 있습니다. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>1단계: Azure AD Connect 파일 다운로드 및 추출
1.  먼저 최신 버전의 [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)를 다운로드합니다.
2.  관리자 권한을 사용하여 명령 프롬프트를 열고 방금 다운로드한 msi로 이동합니다.
3.  다음(`msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`)을 실행합니다.
4. 파일 경로 및 추출 폴더의 이름과 일치하도록 filepath 및 extractfolder를 변경합니다.  이제 콘텐츠가 추출 폴더에 있어야 합니다.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>2단계: Seamless SSO PowerShell 모듈 가져오기

1. [Azure AD PowerShell](/powershell/azure/active-directory/overview)을 다운로드하여 설치합니다.
2. `%programfiles%\Microsoft Azure Active Directory Connect` 폴더로 이동합니다.
3. `Import-Module .\AzureADSSO.psd1` 명령을 사용하여 Seamless SSO PowerShell 모듈을 가져옵니다.

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>3단계: Seamless SSO를 사용하도록 설정된 Active Directory 포리스트 목록 가져오기

1. PowerShell을 관리자 권한으로 실행합니다. PowerShell에서 `New-AzureADSSOAuthenticationContext`를 호출합니다. 메시지가 표시되면 테넌트의 전역 관리자 자격 증명을 입력합니다.
2. `Get-AzureADSSOStatus`을 호출합니다. 이 명령은 이 기능을 사용하도록 설정된 Active Directory 포리스트 목록("도메인" 목록에 표시됨)을 제공합니다.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>4단계: 각 Active Directory 포리스트에 대한 Seamless SSO 사용 설정

1. `Enable-AzureADSSOForest`을 호출합니다. 메시지가 표시되면 원하는 Active Directory 포리스트에 대한 도메인 관리자 자격 증명을 입력합니다.

   > [!NOTE]
   >도메인 관리자 자격 증명 사용자 이름은 SAM 계정 이름 형식(contoso\johndoe 또는 contoso.com\johndoe)으로 입력해야 합니다. 사용자 이름의 도메인 부분을 사용하여 DNS를 사용하는 도메인 관리자의 도메인 컨트롤러를 찾습니다.

   >[!NOTE]
   >사용되는 도메인 관리자 계정은 보호된 사용자 그룹의 구성원이 아니어야 합니다. 이 경우 작업이 실패합니다.

2. 기능을 설정하려는 각 Active Directory 포리스트에 대해 앞의 단계를 반복합니다.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>5단계. 테넌트에서 기능 활성화

테넌트에서 기능을 켜려면 `Enable-AzureADSSO -Enable $true`를 호출하세요.