---
title: 'Azure AD Connect: Seamless Single Sign-On Single Sign-On - FAQ(질문과 대답) | Microsoft Docs'
description: Azure Active Directory Seamless Single Sign-On에 대한 질문과 대답입니다.
services: active-directory
keywords: Azure AD Connect의 정의, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96d0253e1e656f4bdb5180af0d57824aceb4f0dd
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176652"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory Seamless Single Sign-On: 질문과 대답

이 문서에서는 Azure Active Directory Seamless SSO(Seamless Single Sign-On)에 대한 질문과 대답을 다룹니다. 새로운 내용을 계속 확인해 주세요.

**Q: 원활한 SSO가 작동 하는 로그인 방법**

Seamless SSO는 [암호 해시 동기화](how-to-connect-password-hash-synchronization.md) 또는 [통과 인증](how-to-connect-pta.md) 로그인 방법과 결합할 수 있습니다. 그러나 AD FS(Active Directory Federation Services)에는 이 기능을 사용할 수 없습니다.

**Q: 원활한 SSO는 무료 기능이 있나요?**

평가판 체험 기능이며, Azure AD 유료 버전으로 이 기능을 사용할 필요가 없습니다.

**Q: [Microsoft Azure 독일 클라우드](https://www.microsoft.de/cloud-deutschland) 및 [MICROSOFT AZURE GOVERNMENT 클라우드에서](https://azure.microsoft.com/features/gov/)원활한 SSO를 사용할 수 있나요?**

아니요. Seamless SSO는 Azure AD의 전 세계 인스턴스에서만 사용할 수 있습니다.

**Q: 원활한 SSO의 또는 `domain_hint` `login_hint` 매개 변수 기능을 활용 하는 응용 프로그램은 무엇 인가요?**

다음은 이러한 매개 변수를 Azure AD에 전송할 수 있는 애플리케이션의 부분 목록으로, Seamless SSO를 사용하여 자동 로그온 환경을 제공합니다(예: 사용자가 사용자 이름 또는 암호를 입력할 필요가 없음).

| 응용 프로그램 이름 | 사용할 애플리케이션 URL |
| -- | -- |
| 액세스 패널 | https:\//myapps.microsoft.com/contoso.com |
| 웹용 Outlook | https:\//outlook.office365.com/contoso.com |
| Office 365 포털 | https:\//portal.office.com?domain_hint=contoso.com, https:\//www.office.com?domain_hint=contoso.com |

또한 응용 프로그램에서 테 넌 트 (즉, https:\//login.microsoftonline.com/contoso.com/<)로 설정 된 Azure AD의 끝점에 로그인 요청을 전송 하는 경우 자동 로그온 환경을 이용할 수 있습니다. > 또는 https:\//login.microsoftonline.com/<tenant_ID>/<.. >-Azure AD의 공통 끝점 (즉, https:\//login.microsoftonline.com/common/<) 대신 ... >. 이러한 형식의 로그인을 요청하는 애플리케이션의 부분 목록이 아래에 나열됩니다.

| 응용 프로그램 이름 | 사용할 애플리케이션 URL |
| -- | -- |
| SharePoint Online | https:\//contoso.sharepoint.com |
| Azure Portal | https:\//portal.azure.com/contoso.com |

위 표에 나오는 "contoso.com"을 도메인 이름으로 바꾸어 테넌트에 적절한 애플리케이션 URL로 이동합니다.

다른 애플리케이션에서 자동 로그온 환경을 사용하려는 경우 사용자 의견 섹션에서 알려주십시오.

**Q: `Alternate ID` 는`userPrincipalName`대신 사용자 이름으로 원활한 SSO를 지원 합니다.**

예. Seamless SSO는 [여기서](how-to-connect-install-custom.md) 보여주듯이 Azure AD Connect에서 구성할 때 `Alternate ID`를 사용자 이름으로 지원합니다. 모든 Office 365 애플리케이션에서 `Alternate ID`를 지원하지는 않습니다. 지원 내용은 특정 애플리케이션의 설명서를 참조하세요.

**Q: [AZURE AD 조인](../active-directory-azureadjoin-overview.md) 및 원활한 SSO에서 제공 하는 Single Sign-On 환경 간의 차이점은 무엇 인가요?**

[Azure AD 조인](../active-directory-azureadjoin-overview.md)에서는 Azure AD에서 해당 디바이스를 등록한 경우 사용자에게 SSO를 제공합니다. 이러한 디바이스를 반드시 도메인에 가입할 필요는 없습니다. *기본 새로 고침 토큰*이나 *PRT* 및 Kerberos를 사용하여 SSO를 제공하지 않습니다. 사용자 환경은 Windows 10 디바이스에서 가장 적합합니다. SSO는 Microsoft Edge 브라우저에서 자동으로 실행됩니다. 또한 브라우저 확장을 사용하여 Chrome에서 작동합니다.

테넌트에서 Azure AD 조인 및 Seamless SSO를 사용할 수 있습니다. 이러한 두 기능은 상호 보완적입니다. 두 기능이 모두 설정되어 있으면 Azure AD Join에서 SSO는 Seamless SSO보다 우선합니다.

**Q: AD FS를 사용하지 않고 비Windows 10 디바이스를 Azure AD에 등록하려고 합니다. 대신 원활한 SSO를 사용할 수 있나요?**

예, 이 시나리오에는 [작업 공간 연결 클라이언트](https://www.microsoft.com/download/details.aspx?id=53554) 버전 2.1 이상이 필요합니다.

**Q: `AZUREADSSOACC` 컴퓨터 계정의 Kerberos 암호 해독 키를 롤오버 하려면 어떻게 해야 하나요?**

온-프레미스 AD 포리스트에 만든 `AZUREADSSOACC` 컴퓨터 계정(Azure AD를 나타냄)의 Kerberos 암호 해독 키를 자주 롤오버하는 것이 중요합니다.

>[!IMPORTANT]
>적어도 30일마다 Kerberos 암호 해독 키를 롤오버하는 것이 좋습니다.

Azure AD Connect를 실행 중인 온-프레미스 서버에서 다음 단계를 따릅니다.

    **Step 1. Get list of AD forests where Seamless SSO has been enabled**

    1. 먼저 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview)을 다운로드한 후 설치합니다.
    2. `%programfiles%\Microsoft Azure Active Directory Connect` 폴더로 이동합니다.
    3. 다음 명령을 사용하여 Seamless SSO PowerShell 모듈을 가져옵니다. `Import-Module .\AzureADSSO.psd1`
    4. 관리자 권한으로 PowerShell을 실행합니다. PowerShell에서 `New-AzureADSSOAuthenticationContext`를 호출합니다. 이 명령으로 테넌트의 전역 관리자 자격 증명을 입력하라는 팝업 메시지가 표시됩니다.
    5. `Get-AzureADSSOStatus | ConvertFrom-Json`를 호출합니다. 사용하도록 설정된 AD 포리스트 목록("도메인" 목록에 있음)이 표시됩니다.

    **2 단계. 설정 된 각 AD 포리스트에서 Kerberos 암호 해독 키를 업데이트 합니다.**

    1. `$creds = Get-Credential`를 호출합니다. 메시지가 표시되면 의도한 AD 포리스트에 대한 도메인 관리자 자격 증명을 입력합니다.

    > [!NOTE]
    > 의도된 AD 포리스트를 찾기 위해 도메인 관리자의 사용자 이름을 사용합니다. 해당 항목은 UPN(사용자 계정 이름)(johndoe@contoso.com) 형식이나 도메인 정규화 SAM 계정 이름(contoso\johndoe 또는 contoso.com\johndoe) 형식으로 제공됩니다. 도메인 정규화 SAM 계정 이름을 사용하는 경우 사용자 이름의 도메인 부분을 사용하여 [DNS를 사용하는 도메인 관리자의 도메인 컨트롤러를 찾습니다](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). 대신 UPN을 사용하는 경우 적절한 도메인 컨트롤러를 찾기 전에 [도메인 정규화 SAM 계정 이름으로 변환](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa)합니다.

    2. `Update-AzureADSSOForest -OnPremCredentials $creds`를 호출합니다. 이 명령은 이 특정 AD 포리스트에서 `AZUREADSSOACC` 컴퓨터 계정에 대한 Kerberos 암호 해독 키를 업데이트하고 Azure AD에서 키를 업데이트 합니다.
    3. 기능을 설정한 각 AD 포리스트에 대해 위의 단계를 반복합니다.

    >[!IMPORTANT]
    >`Update-AzureADSSOForest` 명령을 두 번 이상 실행하지 _않아야 합니다_. 그렇지 않으면 해당 기능은 사용자의 Kerberos 티켓이 만료되고 온-프레미스 Active Directory에 의해 재발급될 때까지 작동하지 않습니다.

**Q: 원활한 SSO를 사용 하지 않도록 설정 하려면 어떻게 해야 하나요?**

    **Step 1. Disable the feature on your tenant**

        **Option A: Disable using Azure AD Connect**

        1. Run Azure AD Connect, choose **Change user sign-in page** and click **Next**.
        2. Uncheck the **Enable single sign on** option. Continue through the wizard.

        After completing the wizard, Seamless SSO will be disabled on your tenant. However, you will see a message on screen that reads as follows:

        "Single sign-on is now disabled, but there are additional manual steps to perform in order to complete clean-up. Learn more"

        To complete the clean-up process, follow steps 2 and 3 on the on-premises server where you are running Azure AD Connect.

        **Option B: Disable using PowerShell**

        Run the following steps on the on-premises server where you are running Azure AD Connect:

        1. First, download, and install [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
        2. Navigate to the `%programfiles%\Microsoft Azure Active Directory Connect` folder.
        3. Import the Seamless SSO PowerShell module using this command: `Import-Module .\AzureADSSO.psd1`.
        4. Run PowerShell as an Administrator. In PowerShell, call `New-AzureADSSOAuthenticationContext`. This command should give you a popup to enter your tenant's Global Administrator credentials.
        5. Call `Enable-AzureADSSO -Enable $false`.

        >[!IMPORTANT]
        >Disabling Seamless SSO using PowerShell will not change the state in Azure AD Connect. Seamless SSO will show as enabled in the **Change user sign-in** page.

    **Step 2. Get list of AD forests where Seamless SSO has been enabled**

    Follow tasks 1 through 4 below if you have disabled Seamless SSO using Azure AD Connect. If you have disabled Seamless SSO using PowerShell instead, jump ahead to task 5 below.

    1. 먼저 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview)을 다운로드한 후 설치합니다.
    2. `%programfiles%\Microsoft Azure Active Directory Connect` 폴더로 이동합니다.
    3. 다음 명령을 사용하여 Seamless SSO PowerShell 모듈을 가져옵니다. `Import-Module .\AzureADSSO.psd1`
    4. 관리자 권한으로 PowerShell을 실행합니다. PowerShell에서 `New-AzureADSSOAuthenticationContext`를 호출합니다. 이 명령으로 테넌트의 전역 관리자 자격 증명을 입력하라는 팝업 메시지가 표시됩니다.
    5. `Get-AzureADSSOStatus | ConvertFrom-Json`를 호출합니다. 사용하도록 설정된 AD 포리스트 목록("도메인" 목록에 있음)이 표시됩니다.

    **3 단계: 표시 된 각 `AZUREADSSOACCT` AD 포리스트에서 컴퓨터 계정을 수동으로 삭제 합니다.**

## <a name="next-steps"></a>다음 단계

- [**빠른 시작**](how-to-connect-sso-quick-start.md) - Azure AD Seamless SSO를 준비하고 실행합니다.
- [**기술 심층 분석**](how-to-connect-sso-how-it-works.md) - 이 기능의 작동 방식을 이해합니다.
- [**문제 해결**](tshoot-connect-sso.md) - 기능과 관련된 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 새로운 기능 요청을 제출합니다.
