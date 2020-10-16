---
title: 애플리케이션 프록시 에이전트 커넥터를 설치할 때 문제 발생
description: Azure Active Directory 용 응용 프로그램 프록시 에이전트 커넥터를 설치할 때 발생할 수 있는 문제를 해결 하는 방법입니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 7babe23426cafe01cadc7a5557f91896aa9bbae4
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108204"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>애플리케이션 프록시 에이전트 커넥터를 설치할 때 문제 발생

Microsoft Azure Active Directory 응용 프로그램 프록시 커넥터는 아웃 바운드 연결을 사용 하 여 클라우드 사용 가능 끝점에서 내부 도메인으로의 연결을 설정 하는 내부 도메인 구성 요소입니다.

## <a name="general-problem-areas-with-connector-installation"></a>커넥터 설치에 대한 일반적인 문제 영역

커넥터 설치가 실패한 경우 근본 원인은 대개 다음 영역 중 하나입니다.

1.  **연결** – 성공적인 설치를 완료하려면 새 커넥터를 등록하고 향후 트러스트 속성을 설정해야 합니다. Azure Active Directory 응용 프로그램 프록시 클라우드 서비스에 연결 하 여이 작업을 수행 합니다.

2.  **트러스트 설정** – 새 커넥터는 자체 서명된 인증서를 만들고 클라우드 서비스에 등록합니다.

3.  **관리자 인증** – 설치 중 사용자가 커넥터 설치를 완료하려면 관리자 자격 증명을 제공해야 합니다.

> [!NOTE]
> 커넥터 설치 로그 는% TEMP% 폴더에서 찾을 수 있으며 설치 오류의 원인이 되는 항목에 대 한 추가 정보를 제공 하는 데 도움이 됩니다.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>클라우드 애플리케이션 프록시 서비스 및 Microsoft 로그인 페이지에 대한 연결 확인

**목표:** 커넥터 컴퓨터가 Microsoft 로그인 페이지 및 응용 프로그램 프록시 등록 끝점에 연결할 수 있는지 확인 합니다.

1.  커넥터 서버에서 [텔넷](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) 또는 기타 포트 테스트 도구를 사용 하 여 포트 443 및 80이 열려 있는지 확인 하는 방식으로 포트 테스트를 실행 합니다.

2.  이러한 포트 중 하나라도 실패 하면 방화벽 또는 백 엔드 프록시가 필요한 도메인 및 포트에 액세스할 수 있는지 확인 하 고 [온-프레미스 환경 준비](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)를 참조 하세요.

3.  브라우저(별도 탭)를 열고 `https://login.microsoftonline.com`을 방문하여 해당 페이지에 로그인할 수 있는지 확인합니다.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>응용 프로그램 프록시 신뢰 인증서에 대 한 컴퓨터 및 백 엔드 구성 요소 지원 확인

**목표:** 커넥터 컴퓨터, 백 엔드 프록시와 방화벽이 커넥터에서 만든 인증서를 나중에 신뢰 하 고 인증서가 유효한 지 확인 합니다.

>[!NOTE]
>커넥터는 TLS1.2에서 지원하는 SHA512 인증서를 만들려고 합니다. 컴퓨터 또는 백 엔드 방화벽과 프록시가 TLS 1.2를 지원 하지 않는 경우 설치가 실패 합니다.
>
>

**필요한 필수 구성 요소를 검토 합니다.**

1.  컴퓨터가 TLS1.2를 지원하는지 확인합니다. 2012 R2 이후의 모든 Windows 버전은 TLS1.2를 지원합니다. 커넥터 컴퓨터가 2012 R2 또는 이전 버전인 경우 컴퓨터에 <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2> 기술 자료 문서가 설치되어 있는지 확인합니다.

2.  네트워크 관리자에게 문의하여 백 엔드 프록시와 방화벽이 나가는 트래픽에 대해 SHA512를 차단하지 않는지 확인합니다.

**클라이언트 인증서를 확인 하려면:**

현재 클라이언트 인증서의 지문을 확인 합니다. 인증서 저장소는에서 찾을 수 있습니다 `%ProgramData%\microsoft\Microsoft AAD Application Proxy Connector\Config\TrustSettings.xml` .

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

가능한 **IsInUserStore** 값은 **true** 및 **false**입니다. 값이 **true 이면** 자동으로 갱신 된 인증서가 네트워크 서비스의 사용자 인증서 저장소에 있는 개인 컨테이너에 저장 됩니다. **False** 값은 설치 또는 등록 중에 클라이언트 인증서가 생성 되 고, Register-AppProxyConnector 명령으로 시작 되며, 로컬 컴퓨터의 인증서 저장소에 있는 개인 컨테이너에 저장 됨을 의미 합니다.

값이 **true**이면 다음 단계를 수행 하 여 인증서를 확인 합니다.
1. 다운로드 [PsTools.zip](https://docs.microsoft.com/sysinternals/downloads/pstools)
2. 패키지에서 [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) 를 추출 하 고 관리자 권한 명령 프롬프트에서 **PsExec-i-u "nt authority\network service" cmd.exe** 를 실행 합니다.
3. 새로 나타난 명령 프롬프트에서 certmgr.exe를 실행 **합니다.**
4. 관리 콘솔에서 개인 컨테이너를 확장 하 고 인증서를 클릭 합니다.
5. **Connectorregistrationca.msappproxy.net** 에서 발급 한 인증서 찾기

값이 **false**인 경우 다음 단계에 따라 인증서를 확인 합니다.
1. " **실행"**
2. 관리 콘솔에서 개인 컨테이너를 확장 하 고 인증서를 클릭 합니다.
3. **Connectorregistrationca.msappproxy.net** 에서 발급 한 인증서 찾기

**클라이언트 인증서를 갱신 하려면:**

몇 달 동안 커넥터가 서비스에 연결되지 않는 경우 인증서가 만료될 수 있습니다. 인증서 갱신 오류는 만료 된 인증서로 이어집니다. 이렇게 하면 커넥터 서비스의 작동이 중지 됩니다. 이벤트 1000는 커넥터의 관리자 로그에 기록 됩니다.

"커넥터 다시 등록 실패: 커넥터 신뢰 인증서가 만료 되었습니다. 커넥터를 다시 등록 하려면 커넥터가 실행 중인 컴퓨터에서 PowerShell cmdlet Register-AppProxyConnector를 실행 합니다. "

이 경우 커넥터를 제거 했다가 다시 설치 하 여 등록을 트리거하거나 다음 PowerShell 명령을 실행할 수 있습니다.

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

Register-AppProxyConnector 명령에 대해 자세히 알아보려면 [Azure AD 응용 프로그램 프록시 커넥터에 대 한 무인 설치 스크립트 만들기](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-register-connector-powershell) 를 참조 하세요.

## <a name="verify-admin-is-used-to-install-the-connector"></a>관리자가 커넥터를 설치했는지 확인합니다.

**목표:** 커넥터를 설치하려고 하는 사용자가 올바른 자격 증명을 가진 관리자인지 확인합니다. 현재 설치를 완료 하려면 사용자가 적어도 응용 프로그램 관리자 여야 합니다.

**자격 증명이 올바른지 확인하려면:**

`https://login.microsoftonline.com`에 연결하고 동일한 자격 증명을 사용합니다. 로그인이 성공하는지 확인합니다. **Azure Active Directory**  - &gt; **사용자 및 그룹**  - &gt; **모든 사용자**로 이동 하 여 사용자 역할을 확인할 수 있습니다. 

사용자 계정을 선택 하 고 결과 메뉴에서 "디렉터리 역할"을 선택 합니다. 선택한 역할이 "응용 프로그램 관리자" 인지 확인 합니다. 이러한 단계에서 페이지에 액세스할 수 없는 경우 필수 역할이 없습니다.

## <a name="next-steps"></a>다음 단계
[Azure AD 애플리케이션 프록시 커넥터 이해](application-proxy-connectors.md)
