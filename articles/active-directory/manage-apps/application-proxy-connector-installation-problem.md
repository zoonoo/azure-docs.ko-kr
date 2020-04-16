---
title: 애플리케이션 프록시 에이전트 커넥터를 설치할 때 문제 발생 | Microsoft Docs
description: 애플리케이션 프록시 에이전트 커넥터를 설치할 때 발생하는 문제를 해결하는 방법
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d773e6302edf0b799e6dfccc702750a9cc74f60
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406695"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>애플리케이션 프록시 에이전트 커넥터를 설치할 때 문제 발생

Microsoft AAD 애플리케이션 프록시 커넥터는 아웃바운드 연결을 사용하여 클라우드 사용 가능 엔드포인트에서 내부 도메인으로의 연결을 설정하는 내부 도메인 구성 요소입니다.

## <a name="general-problem-areas-with-connector-installation"></a>커넥터 설치에 대한 일반적인 문제 영역

커넥터 설치가 실패한 경우 근본 원인은 대개 다음 영역 중 하나입니다.

1.  **연결** – 성공적인 설치를 완료하려면 새 커넥터를 등록하고 향후 트러스트 속성을 설정해야 합니다. 이 작업은 AAD 애플리케이션 프록시 클라우드 서비스에 연결하여 수행됩니다.

2.  **트러스트 설정** – 새 커넥터는 자체 서명된 인증서를 만들고 클라우드 서비스에 등록합니다.

3.  **관리자 인증** – 설치 중 사용자가 커넥터 설치를 완료하려면 관리자 자격 증명을 제공해야 합니다.

> [!NOTE]
> 커넥터 설치 로그는 %TEMP% 폴더에서 찾을 수 있으며 설치 실패의 원인에 대한 추가 정보를 제공하는 데 도움이 될 수 있습니다.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>클라우드 애플리케이션 프록시 서비스 및 Microsoft 로그인 페이지에 대한 연결 확인

**목표:** 커넥터 머신이 Microsoft 로그인 페이지뿐만 아니라 AAD 애플리케이션 프록시 등록 엔드포인트에도 연결할 수 있는지 확인합니다.

1.  커넥터 서버에서 [텔넷](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) 또는 기타 포트 테스트 도구를 사용하여 포트 443과 80이 열려 있는지 확인하여 포트 테스트를 실행합니다.

2.  이러한 포트중 어느 라도 성공하지 못한 경우 방화벽 또는 백 엔드 프록시가 필요한 도메인 및 포트에 액세스할 수 있는지 확인하고 [온-프레미스 환경 준비합니다.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)

3.  브라우저(별도 탭)를 열고 `https://login.microsoftonline.com`을 방문하여 해당 페이지에 로그인할 수 있는지 확인합니다.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>응용 프로그램 프록시 트러스트 인증서에 대한 컴퓨터 및 백 엔드 구성 요소 지원 확인

**목표:** 커넥터 컴퓨터, 백 엔드 프록시 및 방화벽이 향후 신뢰를 위해 커넥터에서 만든 인증서를 지원할 수 있고 인증서가 유효한지 확인합니다.

>[!NOTE]
>커넥터는 TLS1.2에서 지원하는 SHA512 인증서를 만들려고 합니다. 컴퓨터 또는 백 엔드 방화벽 및 프록시가 TLS1.2를 지원하지 않으면 설치가 실패합니다.
>
>

**필요한 필수 구성 조건을 검토합니다.**

1.  컴퓨터가 TLS1.2를 지원하는지 확인합니다. 2012 R2 이후의 모든 Windows 버전은 TLS1.2를 지원합니다. 커넥터 컴퓨터가 2012 R2 또는 이전 버전인 경우 컴퓨터에 <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2> 기술 자료 문서가 설치되어 있는지 확인합니다.

2.  네트워크 관리자에게 문의하여 백 엔드 프록시와 방화벽이 나가는 트래픽에 대해 SHA512를 차단하지 않는지 확인합니다.

**클라이언트 인증서를 확인하려면 다음을 수행하십시오.**

현재 클라이언트 인증서의 지문을 확인합니다. 인증서 저장소는 %ProgramData%\마이크로소프트[마이크로소프트 AAD 응용 프로그램 프록시 커넥터\Config\TrustSettings.xml에서 찾을 수 있습니다.]

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

가능한 **IsInUserStore** 값과 의미는 다음과 같습니다.

- **false** - 등록-AppProxyConnector 명령에 의해 시작된 설치 또는 등록 중에 클라이언트 인증서가 만들어졌습니다. 로컬 컴퓨터의 인증서 저장소에 개인 컨테이너에 저장 됩니다. 

단계에 따라 인증서를 확인합니다.

1. **certlm.msc** 실행
2. 관리 콘솔에서 개인 컨테이너를 확장하고 인증서를 클릭합니다.
3. connectorregistrationca.msappproxy.net 발급된 인증서 를 **찾습니다.**

- **true** - 자동으로 갱신된 인증서는 네트워크 서비스의 사용자 인증서 저장소에 있는 개인 컨테이너에 저장됩니다. 

단계에 따라 인증서를 확인합니다.

1. [PsTools.zip](https://docs.microsoft.com/sysinternals/downloads/pstools) 다운로드
2. 패키지에서 [PsExec을](https://docs.microsoft.com/sysinternals/downloads/psexec) 추출하고 높은 명령 프롬프트에서 **psexec -i -u "nt 기관\네트워크 서비스" cmd.exe를** 실행합니다.
3. 새로 나타난 명령 프롬프트에서 **certmgr.msc** 실행
2. 관리 콘솔에서 개인 컨테이너를 확장하고 인증서를 클릭합니다.
3. **connectorregistrationca.msappproxy.ne 발급된 인증서 를 찾습니다.

**클라이언트 인증서를 갱신하려면 다음을 수행하십시오.**

몇 달 동안 커넥터가 서비스에 연결되지 않는 경우 인증서가 만료될 수 있습니다. 인증서 갱신이 실패하면 만료된 인증서가 발생합니다. 이렇게 하면 커넥터 서비스가 작동을 중지합니다. 이벤트 1000은 커넥터의 관리자 로그에 기록됩니다.

"커넥터 재등록실패: 커넥터 신뢰 인증서가 만료되었습니다. 커넥터가 실행 중인 컴퓨터에서 PowerShell cmdlet 레지스터-AppProxyConnector를 실행하여 커넥터를 다시 등록합니다."

이 경우 연결을 제거하고 다시 설치하여 등록을 트리거하거나 다음 PowerShell 명령을 실행할 수 있습니다.

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

Register-AppProxyConnector 명령에 대해 자세히 알아보려면 [Azure AD 응용 프로그램 프록시 커넥터에 대한 무인 설치 스크립트 만들기를](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-register-connector-powershell) 참조하십시오.

## <a name="verify-admin-is-used-to-install-the-connector"></a>관리자가 커넥터를 설치했는지 확인합니다.

**목표:** 커넥터를 설치하려고 하는 사용자가 올바른 자격 증명을 가진 관리자인지 확인합니다. 현재 사용자는 설치가 성공하려면 최소한 응용 프로그램 관리자여야 합니다.

**자격 증명이 올바른지 확인하려면:**

`https://login.microsoftonline.com`에 연결하고 동일한 자격 증명을 사용합니다. 로그인이 성공하는지 확인합니다. **Azure Active Directory**  - &gt; **사용자 및**  - &gt; **그룹 모든 사용자로**이동하여 사용자 역할을 확인할 수 있습니다. 

사용자 계정을 선택한 다음 결과 메뉴에서 "디렉터리 역할"을 선택합니다. 선택한 역할이 "응용 프로그램 관리자"인지 확인합니다. 이러한 단계에서 페이지에 액세스할 수 없는 경우 필수 역할이 없습니다.

## <a name="next-steps"></a>다음 단계
[Azure AD 응용 프로그램 프록시 커넥터 이해](application-proxy-connectors.md)
