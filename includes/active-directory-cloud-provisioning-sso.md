---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 3aa1571b46938b03f556fa124d3f0a2a70f2c5c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "79504366"
---
## <a name="steps-to-enable-single-sign-on"></a>단일 사인온을 활성화하는 단계
클라우드 프로비저닝은 단일 사인온과 함께 작동합니다.  현재 에이전트를 설치할 때 SSO를 사용하도록 설정하는 옵션은 없지만 아래 단계를 사용하여 SSO를 사용하도록 설정하고 사용할 수 있습니다. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>1단계: Azure AD 연결 파일 다운로드 및 추출
1.  먼저 Azure [AD Connect의](https://www.microsoft.com/download/details.aspx?id=47594) 최신 버전을 다운로드합니다.
2.  관리 권한을 사용하여 명령 프롬프트를 열고 방금 다운로드한 msi로 이동합니다.
3.  다음을 실행합니다.`msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. 파일 경로 및 extractfolder를 파일 경로 및 추출 폴더 의 이름과 일치하도록 변경합니다.  이제 내용이 추출 폴더에 있어야 합니다.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>2단계: 원활한 SSO PowerShell 모듈 가져오기

1. 다운로드 및 [Azure AD PowerShell을](https://docs.microsoft.com/powershell/azure/active-directory/overview)설치합니다.
2. `%programfiles%\Microsoft Azure Active Directory Connect` 폴더로 이동합니다.
3. `Import-Module .\AzureADSSO.psd1` 명령을 사용하여 Seamless SSO PowerShell 모듈을 가져옵니다.

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>3단계: 원활한 SSO가 활성화된 Active Directory 포리스트 를 가져옵니다.

1. PowerShell을 관리자 권한으로 실행합니다. PowerShell에서 `New-AzureADSSOAuthenticationContext`를 호출합니다. 메시지가 표시되면 테넌트의 전역 관리자 자격 증명을 입력합니다.
2. `Get-AzureADSSOStatus`을 호출합니다. 이 명령은 이 기능을 사용하도록 설정된 Active Directory 포리스트 목록("도메인" 목록에 표시됨)을 제공합니다.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>4단계: 각 Active Directory 포리스트에 대한 Seamless SSO 사용 설정

1. `Enable-AzureADSSOForest`을 호출합니다. 메시지가 표시되면 원하는 Active Directory 포리스트에 대한 도메인 관리자 자격 증명을 입력합니다.

   > [!NOTE]
   >도메인 관리자 자격 증명 사용자 이름은 SAM 계정 이름 형식(contoso\johndoe 또는 contoso.com\johndoe)으로 입력해야 합니다. 사용자 이름의 도메인 부분을 사용하여 DNS를 사용하여 도메인 관리자의 도메인 컨트롤러를 찾습니다.

   >[!NOTE]
   >사용된 도메인 관리자 계정은 보호된 사용자 그룹의 구성원이 아니어야 합니다. 이 경우 작업이 실패합니다.

2. 기능을 설정하려는 각 Active Directory 포리스트에 대해 앞의 단계를 반복합니다.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>5단계. 테넌트에서 기능 활성화

테넌트에서 기능을 켜려면 `Enable-AzureADSSO -Enable $true`를 호출하세요.
