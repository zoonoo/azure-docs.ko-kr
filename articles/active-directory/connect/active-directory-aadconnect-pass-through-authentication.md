---
title: "Azure AD Connect: 통과 인증 | Microsoft Docs"
description: "이 항목은 Azure Active Directory(Azure AD) 및 연결된 서비스에 대한 액세스를 제공하기 위해 Azure AD 통과 인증이 온-프레미스 Active Directory(AD)와 작동하는 방식에 대해 알아야 하는 정보를 제공합니다."
services: active-directory
keywords: "Azure AD Connect의 정의, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on"
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 28b5da6098316f8fbe84966e0dac88f5b7d2cb1d
ms.openlocfilehash: 88d315c68c545b84833582ca6aeeed617bcbb75e


---

# <a name="what-is-azure-ad-pass-through-authentication"></a>Azure AD 통과 인증이란?
동일한 자격 증명(사용자 이름 및 암호)을 사용하여 회사 리소스 및 클라우드 기반 서비스에 액세스하면 사용자가 다른 자격 증명을 기억할 필요가 없으며 로그인 방법을 잊어버릴 가능성이 줄어듭니다.  또한 지원 센터가 암호 재설정에 관여하는 경우가 줄어드는 이점이 있습니다.  

다수의 조직이 Azure AD 암호 동기화를 편리하게 사용하여 온-프레미스 및 클라우드 서비스에 액세스할 수 있는 단일 자격 증명을 제공하는 반면에 일부 조직에서는 암호가(해시된 형태인 경우에도) 내부 조직 경계를 벗어나지 않도록 요구하는 경우도 있습니다.  

Azure AD 통과 인증은 이러한 고객이 클라우드에 복잡한 네트워크 인프라 또는 온-프레미스 암호를 어떠한 형태로도 준비하지 않고 온-프레미스 Active Directory에 대해 Azure AD 서비스에 대한 암호 유효성 검사를 수행할 수 있는 간단한 솔루션을 제공합니다.  

Single Sign-On과 함께 사용하면 사용자가 Azure AD 또는 기타 클라우드 서비스에 로그인하기 위해 암호를 입력할 필요가 없기 때문에 고객에게 실질적으로 통합된 회사 컴퓨터 사용 환경을 제공할 수 있습니다.

![통과 인증](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

통과 인증은 AAD Connect를 통해 구성할 수 있으며 암호 유효성 검사 요청을 수신하는 간단한 온-프레미스 에이전트를 활용합니다.  에이전트는 고가용성 및 부하 분산을 제공하도록 여러 컴퓨터에 손쉽게 배포될 수 있습니다.  모든 통신이 아웃바운드이기 때문에 DMZ가 필요하지 않고 DMZ에 커넥터를 설치할 필요가 없습니다.  커넥터에 대한 시스템 요구 사항은 다음과 같습니다.


- Windows Server 2012 이상 
- 사용자 유효성이 검사되는 포리스트의 도메인에 가입

## <a name="supported-clients-in-the-preview"></a>미리 보기에서 지원되는 클라이언트
통과 인증은 최신 인증을 지원하는 Office 클라이언트 및 웹 브라우저 기반 클라이언트를 통해 지원됩니다.  레거시 Office 클라이언트, Exchange Active Sync(예: 모바일 장치의 원시 전자 메일 클라이언트)와 같이 지원되는 않는 클라이언트의 경우 최신 인증과 동등한 인증을 사용하는 것이 좋습니다.  이렇게 하면 통과 인증이 가능할 뿐만 아니라 Multi-Factor Authentication과 같은 조건부 액세스를 적용할 수도 있습니다.

Azure AD에 가입되어 있는 Windows 10을 사용하는 경우에는 현재 통과 인증이 지원되지 않습니다.  하지만 위에서 언급한 레거시 클라이언트 및 Windows 10에 대한 자동 대체로 암호 해시 동기화를 활용할 수 있습니다.
>[!NOTE] 
>미리 보기 기간 중에 통과 인증이 Azure AD Connect의 로그인 옵션으로 선택되면 암호 해시 동기화가 기본적으로 설정됩니다. 이 설정은 Azure AD Connect의 옵션 화면에서 해제할 수 있습니다.

## <a name="how-azure-ad-pass-through-authentication-works"></a>Azure AD 통과 인증 작동 방식
사용자가 Azure AD 로그인 페이지에 사용자 이름과 암호를 입력하면 Azure AD는 유효성 검사를 위해 적절한 온-프레미스 커넥터 큐에 사용자 이름과 암호를 배치합니다.  사용 가능한 온-프레미스 커넥터 중 하나가 사용자 이름과 암호를 검색하고 Active Directory에 대해 유효성을 검사합니다.  유효성 검사는 Active Directory Federation Service가 암호의 유효성을 검사하는 것과 유사한 방식으로 표준 Windows API를 통해 이루어집니다.

온-프레미스 도메인 컨트롤러가 요청을 평가하고 커넥터에 응답을 반환하면 커넥터는 이것을 Azure AD에 반환합니다. Azure AD는 응답을 평가하고 사용자에게 토큰을 발급하거나 다단계 인증을 요청하는 등의 적절한 응답을 보냅니다.  아래 다이어그램은 다양한 단계를 보여줍니다.


![통과 인증](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="azure-ad-pass-through-prerequisites"></a>Azure AD 통과 필수 조건
Azure AD 통과 인증을 설정하고 사용하려면 다음과 같은 항목이 필요합니다. 


- Azure AD Connect 소프트웨어
- 전역 관리자 권한이 있는 Azure AD 디렉터리  

>[!NOTE] 
>온-프레미스 서비스에 장애가 있거나 사용할 없는 경우 테넌트의 구성을 관리할 수 있도록 계정이 클라우드 전용 관리자 계정인 것이 좋습니다.


- AAD Connect 도구를 실행할 Windows Server 2012 R2 이상을 실행하는 서버.  이 컴퓨터는 유효성을 검사할 사용자와 동일한 포리스트의 멤버여야 합니다.
- Azure AD와 동기화할 사용자를 포함하는 포리스트가 둘 이상인 경우에는 포리스트 간에 트러스트가 있어야 합니다. 
- 고가용성 및 부하 분산을 위해 두 번째 커넥터를 실행할 Windows Server 2012 R2 이상을 실행하는 두 번째 서버.  커넥터를 배포하는 방식은 아래 지침에 포함되어 있습니다.
- 커넥터와 Azure AD 사이에 방화벽이 있는 경우에는 다음 사항을 확인합니다.
    - URL 필터링을 사용하는 경우 커넥터가 다음 URL과 통신할 수 있는지 확인합니다. 
        -  *.msappproxy.net
        -  *.servicebus.windows.net.  
        -  커넥터가 Azure 데이터 센터 IP 범위에 대한 직접적인 IP 연결을 통해 연결을 생성합니다. 
    - 커넥터가 클라이언트 인증서를 사용하여 Azure AD와 통신하기 때문에 방화벽이 SSL 검사를 수행하지 않는지 확인합니다.
    - 커넥터가 아래 포트에서 Azure AD에 대해 HTTPS(TCP) 요청을 수행할 수 있는지 확인합니다. 

|포트 번호|설명
| --- | ---
|80|SSL과 같은 보안 유효성 검사에 아웃바운드 HTTP 트래픽을 사용하도록 설정합니다.
|443|   Azure AD에 대해 사용자 인증 사용하도록 설정합니다.
|8080/443|  커넥터 부트스트랩 시퀀스 및 커넥터 자동 업데이트를 사용하도록 설정합니다.
|9090|  커넥터 등록(커넥터 등록 프로세스에만 필요)을 사용하도록 설정합니다.
|9091|  커넥터 신뢰 인증서 자동 갱신을 사용하도록 설정합니다.
|9352, 5671|    수신 요청에 대한 Azure 서비스와 커넥터 간에 통신을 사용하도록 설정합니다.
|9350|  [선택 사항] 수신 요청에 더 높은 성능을 발휘하도록 설정합니다.
|10100–10120|   커넥터에서 다시 Azure AD로 응답이 가능하도록 설정합니다.

방화벽이 원래 사용자에 따라 트래픽에 적용되는 경우 네트워크 서비스로 실행되는 Windows 서비스에서 오는 트래픽에 대해 이러한 포트를 엽니다. 또한 NT Authority\System에 대해 포트 8080을 사용하도록 설정해야 합니다.

## <a name="enabling-pass-through-authentication"></a>통과 인증 사용 설정
Azure AD 통과 인증은 Azure AD Connect를 통해 사용하도록 설정됩니다.  통과 인증을 사용하도록 설정하면 첫 번째 커넥터가 Azure AD Connect와 동일한 서버에 배포됩니다.  Azure AD Connect 설치 시 사용자 지정 설치를 선택하고 로그인 옵션 페이지에서 통과 인증을 선택합니다. 자세한 내용은 [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)를 참조하세요.

![SSO(Single sign-on)](./media/active-directory-aadconnect-sso/sso3.png)

기본적으로 첫 번째 통과 인증 커넥터는 Azure AD Connect 서버에 설치됩니다.  인증 요청에 대한 부하 분산 및 고가용성을 보장하기 위해서 두 번째 커넥터는 다른 컴퓨터에 배포해야 합니다.

두 번째 커넥터를 배포하려면 아래 지침을 따르세요.

### <a name="step-1-install-the-connector-without-registration"></a>1단계: 등록 없이 커넥터 설치

1.  최신 [커넥터](https://go.microsoft.com/fwlink/?linkid=837580)를 다운로드합니다.
2.  관리자 권한으로 명령 프롬프트를 엽니다.
3.  다음 명령을 실행합니다. 여기서 /q는 자동 설치를 의미하며 설치 시 최종 사용자 사용권 계약에 동의할지 묻는 메시지가 표시되지 않습니다.

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>2단계: 통과 인증을 위해 커넥터를 Azure AD에 등록

1.  관리자 권한으로 PowerShell 창을 엽니다.
2.  “C:\Program Files\Microsoft AAD App Proxy Connector”로 이동하여 스크립트를 실행합니다.
.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication
3.  대화 상자가 표시되면 Azure AD 테넌트 관리자 계정의 자격 증명을 입력합니다.

## <a name="troubleshooting-pass-through-authentication"></a>통과 인증 문제 해결
통과 인증 문제를 해결하는 경우 발생할 수 있는 문제에는 몇 가지 범주가 있습니다.  문제의 유형에 따라 다른 위치를 살펴봐야 합니다.

커넥터와 관련된 오류는 커넥터 컴퓨터의 “Application and Service Logs\Windows\AadApplicationProxy\Connector\Admin”에서 이벤트 로그를 확인합니다.  필요한 경우 분석 및 디버깅 로그를 켜고 커넥터 세션 로그를 켜면 더 자세한 로그를 볼 수 있습니다.  기본적으로 이 로그를 사용하도록 설정한 상태에서 실행하는 것은 권장되지 않습니다.

C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace에 있는 커넥터에 대한 추적 로그에서 추가 정보를 찾을 수 있습니다. 이 로그는 개인 사용자에 대한 통과 인증이 실패한 이유(예: 오류 코드 1328을 포함하는 아래 항목)를 포함합니다.

    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ

명령 프롬프트를 시작하고 다음 명령을 실행하면 오류에 대한 세부 정보를 볼 수 있습니다. ('1328'을 요청의 오류 번호와 바꿉니다.)

Net helpmsg 1328

결과는 다음과 유사합니다.

![통과 인증](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

감사 로깅이 활성화되어 있는 경우에는 도메인 컨트롤러의 보안 로그에서도 추가 정보를 찾을 수 있습니다.  커넥터의 인증 요청에 대한 간단한 쿼리는 다음과 같습니다.

    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>

Azure AD 로그인 화면에 보고되는 다른 오류는 적절한 해결 단계와 함께 아래에 자세히 설명되어 있습니다.

|오류|설명|해결 방법
| --- | --- | ---
|AADSTS80001|Active Directory에 연결할 수 없음|커넥터 컴퓨터가 도메인에 가입되어 있고 Active Directory에 연결할 수 있는지 확인합니다.  
|AADSTS8002|Active Directory에 연결하는 동안 시간 초과 발생|Active Directory를 사용할 수 있고 커넥터의 요청에 응답하는지 확인합니다.
|AADSTS80004|커넥터에 전달된 사용자 이름이 유효하지 않음|사용자가 올바른 이름을 사용하여 로그인을 시도하는지 확인합니다.
|AADSTS80007|Active Directory와 통신 중 오류 발생|커넥터 로그에서 자세한 내용을 확인하고 Active Directory가 예상대로 작동하는지 확인합니다.








<!--HONumber=Dec16_HO3-->


