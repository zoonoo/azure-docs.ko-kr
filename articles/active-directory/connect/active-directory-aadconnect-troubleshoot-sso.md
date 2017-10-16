---
title: "Azure AD Connect: Seamless Single Sign-On 문제 해결 | Microsoft Docs"
description: "이 항목에서는 Azure AD Seamless SSO(Azure Active Directory Seamless Single Sign-On) 문제를 해결하는 방법을 설명합니다."
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
ms.date: 08/26/2017
ms.author: billmath
ms.openlocfilehash: 7eea3621a52bf13dc44e89c342c503905ff24a0d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory Seamless Single Sign-On 문제 해결

이 문서에서는 Azure AD Seamless Single Sign-On과 관련된 일반적인 문제에 대한 문제 해결 정보를 찾을 수 있습니다.

## <a name="known-issues"></a>알려진 문제

- 경우에 따라 Seamless SSO를 활성화하는 데 최대 30분이 소요될 수 있습니다.
- Edge 브라우저는 지원되지 않습니다.
- 특히 공유 컴퓨터 시나리오에서 Office 클라이언트의 라이선스 활성화로 인해 사용자에 대한 추가 로그인 메시지가 표시됩니다.
- Firefox의 개인 검색 모드에서는 Seamless SSO가 작동하지 않습니다. and 
- Internet Explorer에서 향상된 보호 모드가 설정되어 있을 때는 Seamless SSO가 작동하지 않습니다.
- iOS 및 Android의 모바일 브라우저에서는 Seamless SSO가 작동하지 않습니다.
- 30개 이상의 AD 포리스트를 동기화하면 Azure AD Connect를 통해 Seamless SSO를 활성화할 수 없습니다. 이 경우 테넌트에서 이 기능을 [수동으로 활성화](#manual-reset-of-azure-ad-seamless-sso)하여 해결할 수 있습니다.
- "로컬 인트라넷" 영역 대신 "신뢰할 수 있는 사이트" 영역에 Azure AD 서비스 URL(https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net )을 추가하면 **사용자가 로그인 할 수 없습니다**.

## <a name="check-status-of-the-feature"></a>기능의 상태 확인

테넌트에서 Seamless SSO 기능이 여전히 **활성화**되어 있는지 확인합니다. [Azure Active Directory 관리 센터](https://aad.portal.azure.com/)의 **Azure AD Connect** 블레이드로 이동하여 상태를 확인할 수 있습니다.

![Azure Active Directory 관리 센터 - Azure AD Connect 블레이드](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Azure Active Directory 관리 센터에서 로그인이 실패한 이유(프리미엄 라이선스 필요)

테넌트에 연결된 Azure AD Premium 라이선스가 있는 경우 [Azure Active Directory 관리 센터](https://aad.portal.azure.com/)에서 [로그인 활동 보고서](../active-directory-reporting-activity-sign-ins.md)를 볼 수도 있습니다.

![Azure Active Directory 관리 센터 - 로그인 보고서](./media/active-directory-aadconnect-sso/sso9.png)

[Azure Active Directory 관리 센터](https://aad.portal.azure.com/)에서 **Azure Active Directory** -> **로그인**으로 차례로 이동하고 특정 사용자의 로그인 활동을 클릭합니다. **로그인 오류 코드** 필드를 찾습니다. 다음 표를 사용하여 해당 필드의 값을 실패 이유 및 해결에 매핑합니다.

|로그인 오류 코드|로그인 실패 이유|해결 방법
| --- | --- | ---
| 81001 | 사용자의 Kerberos 티켓이 너무 큽니다. | 사용자의 그룹 멤버 자격 수를 줄이고 다시 시도합니다.
| 81002 | 사용자의 Kerberos 티켓에 대한 유효성을 검사할 수 없습니다. | [문제 해결 검사 목록](#troubleshooting-checklist)을 참조하세요.
| 81003 | 사용자의 Kerberos 티켓에 대한 유효성을 검사할 수 없습니다. | [문제 해결 검사 목록](#troubleshooting-checklist)을 참조하세요.
| 81004 | Kerberos 인증 시도가 실패했습니다. | [문제 해결 검사 목록](#troubleshooting-checklist)을 참조하세요.
| 81008 | 사용자의 Kerberos 티켓에 대한 유효성을 검사할 수 없습니다. | [문제 해결 검사 목록](#troubleshooting-checklist)을 참조하세요.
| 81009 | 사용자의 Kerberos 티켓에 대한 유효성을 검사할 수 없습니다. | [문제 해결 검사 목록](#troubleshooting-checklist)을 참조하세요.
| 81010 | 사용자의 Kerberos 티켓이 만료되었거나 잘못되었으므로 Seamless SSO가 실패했습니다. | 사용자는 회사 네트워크 내부의 도메인 가입 장치에서 로그인해야 합니다.
| 81011 | 사용자의 Kerberos 티켓 정보에 기반하여 사용자 개체를 찾을 수 없습니다. | Azure AD Connect를 사용하여 사용자 정보를 Azure AD와 동기화합니다.
| 81012 | Azure AD에 로그인하려는 사용자가 장치에 로그인한 사용자와 다릅니다. | 다른 장치에서 로그인합니다.
| 81013 | 사용자의 Kerberos 티켓 정보에 기반하여 사용자 개체를 찾을 수 없습니다. |Azure AD Connect를 사용하여 사용자 정보를 Azure AD와 동기화합니다. 

## <a name="troubleshooting-checklist"></a>문제 해결 검사 목록

다음 검사 목록을 사용하여 Seamless SSO 문제를 해결합니다.

- Azure AD Connect에서 Seamless SSO 기능이 활성화되는지 확인합니다. 차단된 포트 등과 같은 이유로 기능을 활성화할 수 없으면 모든 [필수 조건](active-directory-aadconnect-sso-quick-start.md#step-1-check-prerequisites)가 제대로 충족되고 있는지 확인합니다.
- 이러한 Azure AD URL(https://autologon.microsoftazuread-sso.com 및 https://aadg.windows.net.nsatc.net)이 모두 사용자의 인트라넷 영역 설정의 일부인지 확인합니다.
- 회사 장치가 AD 도메인에 가입되어 있는지 확인합니다.
- 사용자가 AD 도메인 계정으로 장치에 로그인했는지 확인합니다.
- 사용자의 계정이 Seamless SSO가 설정된 AD 포리스트에 있는지 확인합니다.
- 장치가 회사 네트워크에 연결되어 있는지 확인합니다.
- 장치의 시간이 Active Directory 및 도메인 컨트롤러의 시간과 동기화되고 서로 5분 이내에 있는지 확인합니다.
- 명령 프롬프트에서 **klist** 명령을 사용하여 장치의 기존 Kerberos 티켓을 나열합니다. `AZUREADSSOACCT` 컴퓨터 계정으로 발급된 티켓이 있는지 확인합니다. 사용자의 Kerberos 티켓은 일반적으로 12시간 동안 유효합니다. Active Directory에 다른 설정이 있을 수 있습니다.
- **klist purge** 명령을 사용하여 장치에서 기존 Kerberos 티켓을 제거한 다음 다시 시도합니다.
- JavaScript 관련 문제가 있는지 확인하려면 브라우저의 콘솔 로그("개발자 도구" 아래)를 검토합니다.
- [도메인 컨트롤러 로그](#domain-controller-logs)도 검토하세요.

### <a name="domain-controller-logs"></a>도메인 컨트롤러 로그

도메인 컨트롤러에서 성공 감사가 사용되면 사용자가 Seamless SSO를 사용하여 로그인할 때마다 보안 항목이 이벤트 로그에 기록됩니다. 다음 쿼리를 사용하여 이러한 보안 이벤트를 찾을 수 있습니다(**AzureADSSOAcc$** 컴퓨터 계정과 연결된 **4769** 이벤트 찾기)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>기능의 수동 다시 설정

문제 해결이 도움이 되지 않으면 테넌트에서 해당 기능을 수동으로 다시 설정할 수 있습니다. Azure AD Connect를 실행 중인 온-프레미스 서버에서 다음 단계를 따릅니다.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>1단계: Seamless SSO PowerShell 모듈 가져오기

1. 먼저 [Microsoft Online Services 로그인 도우미](http://go.microsoft.com/fwlink/?LinkID=286152)를 다운로드하여 설치합니다.
2. 그런 다음 [Windows PowerShell 용 64비트 Azure Active Directory 모듈](http://go.microsoft.com/fwlink/p/?linkid=236297)을 다운로드하고 설치합니다.
3. `%programfiles%\Microsoft Azure Active Directory Connect` 폴더로 이동합니다.
4. 다음 명령을 사용하여 Seamless SSO PowerShell 모듈을 가져옵니다. `Import-Module .\AzureADSSO.psd1`

### <a name="step-2-get-the-list-of-ad-forests-on-which-seamless-sso-has-been-enabled"></a>2단계: Seamless SSO가 사용하도록 설정된 AD 포리스트 목록 가져오기

1. 관리자 권한으로 PowerShell을 실행합니다. PowerShell에서 `New-AzureADSSOAuthenticationContext`를 호출합니다. 메시지가 표시되면 테넌트의 전역 관리자 자격 증명을 입력합니다.
2. `Get-AzureADSSOStatus`를 호출합니다. 사용하도록 설정된 AD 포리스트 목록("도메인" 목록에 있음)이 표시됩니다.

### <a name="step-3-disable-seamless-sso-for-each-ad-forest-that-it-was-set-it-up-on"></a>3단계: 사용하도록 설정된 각 AD 포리스트에 대한 Seamless SSO 비활성화

1. `$creds = Get-Credential`를 호출합니다. 메시지가 표시되면 의도한 AD 포리스트에 대한 도메인 관리자 자격 증명을 입력합니다.
2. `Disable-AzureADSSOForest -OnPremCredentials $creds`를 호출합니다. 이 명령은 이 특정 AD 포리스트에 대한 온-프레미스 도메인 컨트롤러에서 `AZUREADSSOACCT` 컴퓨터 계정을 제거합니다.
3. 기능을 설정한 각 AD 포리스트에 대해 위의 단계를 반복합니다.

### <a name="step-4-enable-seamless-sso-for-each-ad-forest"></a>4단계: 각 AD 포리스트에 대한 Seamless SSO 활성화

1. `Enable-AzureADSSOForest`를 호출합니다. 메시지가 표시되면 의도한 AD 포리스트에 대한 도메인 관리자 자격 증명을 입력합니다.
2. 기능을 설정하려는 각 AD 포리스트에 대해 위의 단계를 반복합니다.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>5단계. 테넌트에서 기능 활성화

`Enable-AzureADSSO`를 호출하고 `Enable: ` 프롬프트에서 "true"를 입력하여 테넌트에서 해당 기능을 설정합니다.
