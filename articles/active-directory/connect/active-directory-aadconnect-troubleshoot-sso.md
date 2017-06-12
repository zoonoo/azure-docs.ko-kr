---
title: "Azure AD Connect: Seamless Single Sign-On 문제 해결 | Microsoft Docs"
description: "이 항목에서는 Azure AD Seamless SSO(Azure Active Directory Seamless Single Sign On) 문제 해결 방법에 대해 설명합니다."
services: active-directory
keywords: "Azure AD Connect의 정의, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: a543be452abbbe3057a5e275612968cd52c8b7aa
ms.contentlocale: ko-kr
ms.lasthandoff: 05/09/2017

---

# <a name="how-to-troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory Seamless Single Sign On 문제 해결 방법

## <a name="known-issues"></a>알려진 문제

- Azure AD Connect를 사용하여 30개가 넘는 AD 포리스트를 동기화하는 경우 Seamless SSO를 설정하는 데 사용된 마법사가 제대로 작동하지 않습니다. 해결 방법으로 테넌트에서 Seamless SSO를 [수동으로 활성화](#manual-reset-of-azure-ad-seamless-sso)할 수 있습니다.
- "로컬 인트라넷" 영역 대신 "신뢰할 수 있는 사이트" 영역에 Azure AD 서비스 URL(https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net )을 추가합니다.

## <a name="troubleshooting-checklist"></a>문제 해결 검사 목록

Azure AD Seamless SSO 문제 해결에는 다음 검사 목록을 사용합니다.

1. Azure AD Connect 도구의 테넌트에서 Seamless SSO 기능이 사용되는지 확인합니다. 차단된 포트 등의 이유로 기능을 사용할 수 없으면 모든 [필수 구성 요소](active-directory-aadconnect-sso.md#pre-requisites)가 포함되어 있는지 확인합니다. 그래도 기능을 사용하도록 설정하는 데 문제가 있으면 Microsoft 지원에 문의하세요.
2. 두 서비스 URL(https://autologon.microsoftazuread-sso.com 및 https://aadg.windows.net.nsatc.net )이 모두 인트라넷 영역 설정의 일부로 정의됩니다.
3. 회사 데스크톱이 AD 도메인에 가입되어 있는지 확인합니다.
4. 사용자가 AD 도메인 계정으로 데스크톱에 로그온되어 있는지 확인합니다.
5. 사용자의 계정이 Seamless SSO가 설정된 AD 포리스트에 있는지 확인합니다.
6. 데스크톱이 회사 네트워크에 연결되어 있는지 확인합니다.
7. 데스크톱의 시간이 Active Directory 및 도메인 컨트롤러 시간과 동기화되어 있고 서로 5분 이내에 있는지 확인합니다.
8. 데스크톱에서 기존 Kerberos 티켓을 제거합니다. 이 작업을 수행하려면 명령 프롬프트에서 **klist purge** 명령을 실행합니다. 일반적으로 사용자의 Kerberos 티켓은 12시간 동안 유효하며 Active Directory에서 다르게 설정했을 수 있습니다.
9. 브라우저의 콘솔 로그("개발자 도구" 아래)를 검토하면 잠재적인 문제를 쉽게 확인할 수 있습니다.
10. [도메인 컨트롤러 로그](#domain-controller-logs)도 검토하세요.

### <a name="domain-controller-logs"></a>도메인 컨트롤러 로그

도메인 컨트롤러에서 성공 감사가 사용되면 사용자가 Seamless SSO를 통해 로그인할 때마다 보안 항목(컴퓨터 계정 **AzureADSSOAcc$**와 연결된 이벤트 4769)이 이벤트 로그에 기록됩니다. 다음 쿼리를 사용하여 이러한 보안 이벤트를 찾을 수 있습니다.

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-azure-ad-seamless-sso"></a>Azure AD Seamless SSO의 수동 다시 설정

문제 해결이 도움이 되지 않는 경우 다음 단계에 따라 테넌트에서 해당 기능을 수동으로 다시 설정/활성화합니다.

### <a name="1-import-the-seamless-sso-powershell-module"></a>1. Seamless SSO PowerShell 모듈 가져오기

- 우선 [Microsoft Online Services 로그인 도우미](http://go.microsoft.com/fwlink/?LinkID=286152)를 다운로드 및 설치합니다.
- 그런 다음 [Windows PowerShell 용 64비트 Azure Active Directory 모듈](http://go.microsoft.com/fwlink/p/?linkid=236297)을 다운로드하고 설치합니다.
- `%programfiles%\Microsoft Azure Active Directory Connect` 폴더로 이동합니다.
- 다음 명령을 사용하여 Seamless SSO PowerShell 모듈을 가져옵니다. `Import-Module .\AzureADSSO.psd1`

### <a name="2-get-the-list-of-ad-forests-on-which-seamless-sso-has-been-enabled"></a>2. Seamless SSO가 사용하도록 설정된 AD 포리스트 목록 가져오기

- PowerShell에서 `New-AzureADSSOAuthenticationContext`를 호출합니다. 그러면 Azure AD 테넌트 관리자 자격 증명을 입력하라는 팝업 메시지가 표시되어야 합니다.
- `Get-AzureADSSOStatus`를 호출합니다. 그러면 이 기능이 사용하도록 설정된 AD 포리스트 목록("도메인" 목록처럼 보임)이 표시됩니다.

### <a name="3-disable-seamless-sso-for-each-ad-forest-that-it-was-set-it-up-on"></a>3. 각 AD 포리스트에 대해 설정했던 Seamless SSO 비활성화

- PowerShell에서 `New-AzureADSSOAuthenticationContext`를 호출합니다. 그러면 Azure AD 테넌트 관리자 자격 증명을 입력하라는 팝업 메시지가 표시되어야 합니다.
- `$creds = Get-Credential`을 호출합니다. 그러면 의도한 AD 포리스트에 대한 도메인 관리자 자격 증명을 입력하라는 팝업이 제공됩니다.
- `Disable-AzureADSSOForest -OnPremCredentials $creds`를 호출합니다. 그러면 온-프레미스 DC에서 AZUREADSSOACCT 컴퓨터 계정이 제거되고 이 특정 AD 포리스트에 대해 이 기능이 비활성화됩니다.
- 기능을 설정한 각 AD 포리스트에 대해 위의 단계를 반복합니다.

### <a name="4-enable-seamless-sso-for-each-ad-forest"></a>4. 각 AD 포리스트에 대해 Seamless SSO 활성화

- `New-AzureADSSOAuthenticationContext`를 호출합니다. 그러면 Azure AD 테넌트 관리자 자격 증명을 입력하라는 팝업 메시지가 표시되어야 합니다.
- `Enable-AzureADSSOForest`를 호출합니다. 그러면 의도한 AD 포리스트에 대한 도메인 관리자 자격 증명을 입력하라는 팝업이 제공됩니다.
- 기능을 설정한 각 AD 포리스트에 대해 위의 단계를 반복합니다.

### <a name="5-enable-seamless-sso-on-your-tenant"></a>5. 테넌트에서 Seamless SSO 활성화

- `New-AzureADSSOAuthenticationContext`를 호출합니다. 그러면 Azure AD 테넌트 관리자 자격 증명을 입력하라는 팝업 메시지가 표시되어야 합니다.
- `Enable-AzureADSSO`를 호출하고 `Enable: ` 프롬프트에서 "true"를 입력하여 테넌트에서 기능을 설정합니다.

