---
title: "Azure Active Directory 통과 인증 보안 심층 분석 | Microsoft Docs"
description: "이 문서에서는 Azure AD(Active Directory) 통과 인증이 온-프레미스 계정을 보호하는 방법에 대해 설명합니다."
services: active-directory
keywords: "Azure AD Connect 통과 인증, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: femila
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: billmath
ms.openlocfilehash: 7a886cdb0c36008bdb66592a8d3428889739627e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory 통과 인증 보안 심층 분석

이 문서에서는 통과 인증의 작동 방식에 대한 자세한 설명을 제공합니다. 기능의 보안 측면에 대해 보다 중점적으로 설명합니다. 이 항목은 보안 및 IT 관리자, 최고 준수 및 보안 담당자, 중소기업 또는 대기업의 IT 보안 및 규정 준수를 담당하는 기타 IT 전문가가 관심을 가질 수 있는 내용입니다.

항목에서는 다음 내용에 대해 다룹니다.
- 인증 에이전트를 설치 및 구성하는 방법에 대한 자세한 기술 정보
- 사용자 로그인 시 암호의 암호화에 대한 자세한 기술 정보
- 온-프레미스 인증 에이전트와 Azure AD(Active Directory) 간의 채널 보안
- 인증 에이전트가 운영상 안전하게 유지되는 방법에 대한 자세한 기술 정보
- 기타 보안 관련 항목

## <a name="key-security-capabilities"></a>주요 보안 기능

다음은 이 기능의 주요 보안 측면입니다.
- 테넌트 간의 로그인 요청을 격리하는 안전한 멀티 테넌트 아키텍처를 기반으로 합니다.
- 온-프레미스 암호가 어떤 형태로든 클라우드에 저장되지 않습니다.
- 암호 유효성 검사 요청을 수신 대기하고 응답하는 온-프레미스 인증 에이전트는 네트워크 내에서만 아웃바운드 연결을 설정합니다. DMZ(경계 네트워크)에 이러한 인증 에이전트를 설치해야 할 필요는 없습니다.
- 인증 에이전트에서 Azure AD로의 아웃바운드 통신에는 표준 포트(80 및 443)만 사용됩니다. 인바운드 포트는 방화벽에서 열릴 필요가 없습니다. 
  - 인증된 모든 아웃바운드 통신에는 포트 443이 사용됩니다.
  - 포트 80은 CRL(인증서 해지 목록)을 다운로드하는 데만 사용되어 기능에서 사용하는 인증서가 해지되지 않도록 합니다.
  - 네트워킹 요구 사항에 대한 전체 목록은 [여기](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-prerequisites)를 참조하세요.
- 로그인 중에 사용자가 입력한 암호는 클라우드에서 암호화된 후 Active Directory에 대한 유효성 검사를 위해 온-프레미스 인증 에이전트에서 허용됩니다.
- Azure AD 및 온-프레미스 인증 에이전트 간의 HTTPS 채널은 상호 인증으로 보호됩니다.
- 조건부 액세스 정책(Multi-Factor Authentication 포함), ID 보호, 스마트 잠금과 같은 Azure AD의 클라우드 보호 기능과 자연스럽게 통합됩니다.

## <a name="components-involved"></a>관련 구성 요소

Azure AD 운영, 서비스 및 데이터 보안에 대한 일반 정보는 [보안 센터](https://azure.microsoft.com/support/trust-center/)를 참조하세요. 사용자 로그인에 통과 인증이 사용된 경우 다음 구성 요소가 관련됩니다.
- **Azure AD STS**: 로그인 요청을 처리하고 필요에 따라 사용자의 브라우저, 클라이언트 또는 서비스에 보안 토큰을 발행하는 상태 비저장 STS(보안 토큰 서비스)입니다.
- **Azure Service Bus**: 온-프레미스 솔루션을 클라우드에 연결할 수 있는 릴레이된 통신과 엔터프라이즈 메시지를 사용하여 클라우드 사용 통신을 제공합니다.
- **Azure AD Connect 인증 에이전트(인증 에이전트)**: 암호 유효성 검사 요청을 수신 대기하고 응답하는 온-프레미스 구성 요소입니다.
- **Azure SQL Database**: 테넌트의 인증 에이전트에 대한 정보(메타데이터 및 암호화 키 포함)를 보유합니다.
- **AD(Active Directory)**: 사용자 계정(및 암호)이 저장되는 온-프레미스 Active Directory입니다.

## <a name="installation-and-registration-of-authentication-agents"></a>인증 에이전트의 설치 및 등록

인증 에이전트는 [Azure AD Connect를 사용하여 통과 인증을 사용하도록 설정](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)하거나 [로그인 요청의 고가용성을 보장하기 위해 추가 인증 에이전트를 추가](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability)할 때 설치되어 Azure AD에 등록됩니다. 인증 에이전트 작업은 세 가지 주요 단계로 구성되어 있습니다.

- 인증 에이전트 설치
- 인증 에이전트 등록
- 인증 에이전트 초기화

이러한 각 내용은 다음 항목에서 자세히 설명합니다.

### <a name="authentication-agent-installation"></a>인증 에이전트 설치

전역 관리자만 온-프레미스 서버에 인증 에이전트(Azure AD Connect 사용 또는 독립 실행형)를 설치할 수 있습니다. 설치하면 다음 새로운 두 항목이 **[제어판] -> [프로그램] -> [프로그램 및 기능]** 목록에 추가됩니다.
- 인증 에이전트 응용 프로그램 자체. [네트워크 서비스](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) 권한으로 실행됩니다.
- 인증 에이전트 자동 업데이트에 사용된 업데이트 프로그램 응용 프로그램. [로컬 시스템](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) 권한으로 실행됩니다.


### <a name="authentication-agent-registration"></a>인증 에이전트 등록

인증 에이전트를 설치한 후 Azure AD에 자체 등록해야 합니다. Azure AD는 각 인증 에이전트에 Azure AD와의 보안 통신에 사용할 수 있는 고유한 디지털 ID 인증서를 할당하여 이를 수행합니다.

또한 등록 절차에서 인증 에이전트를 테넌트와 바인딩하므로 Azure AD는 이 특정 인증 에이전트가 테넌트에 대한 암호 유효성 검사 요청을 처리할 권한이 있는 유일한 에이전트임을 알게 됩니다. 이 절차를 등록할 각 새 인증 에이전트에 대해 반복합니다.

인증 에이전트를 Azure AD에 자체적으로 등록하는 방법은 다음과 같습니다.

![에이전트 등록](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Azure AD에는 먼저 전역 관리자에게 해당 자격 증명으로 Azure AD에 로그인하도록 요청합니다. 로그인하는 동안 인증 에이전트는 전역 관리자를 대신하여 사용할 수 있는 액세스 토큰을 획득합니다.
2. 그러면 인증 에이전트는 키 쌍(공개 키와 개인 키)을 생성합니다.
    - 이 키 쌍은 표준 **RSA 2048비트** 암호화를 사용하여 생성됩니다.
    - 개인 키는 인증 에이전트가 설치된 온-프레미스 서버를 벗어나지 않습니다.
3.  인증 에이전트는 HTTPS를 통해 Azure AD에 “등록” 요청을 생성하고 요청에는 다음 구성 요소가 포함됩니다.
    - 1단계에서 획득한 액세스 토큰
    - 2단계에서 생성된 공개 키
    - **인증서 서명 요청**(CSR 또는 인증서 요청). 이것은 Azure AD를 인증 기관으로 사용하여 디지털 ID 인증서에 적용됩니다.
4. Azure AD는 등록 요청에서 액세스 토큰의 유효성을 검사하고 요청이 전역 관리자로부터 온 것인지 확인합니다.
5. Azure AD는 다시 인증 에이전트로 디지털 ID 인증서를 서명하여 발급합니다.
    - 인증서는 **Azure AD의 루트 CA(인증 기관)**를 사용하여 서명됩니다. 이 CA는 Windows의 **신뢰할 수 있는 루트 인증 기관** 저장소에 _없습니다_.
    - 인증서의 제목(**고유 이름 또는 DN**)은 **테넌트 ID**로 설정됩니다. 테넌트를 고유하게 식별하는 GUID입니다. 이렇게 하면 인증서를 테넌트에만 사용하도록 범위가 지정됩니다.
6. Azure AD는 인증 에이전트의 공개 키를 Azure AD만 액세스할 수 있는 Azure SQL Database에 저장합니다.
7. 5단계에서 발행한 인증서는 **Windows 인증서 저장소**(특히 **[CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE)** 위치)의 온-프레미스 서버에 저장되며 인증 에이전트 및 업데이트 프로그램 응용 프로그램 모두에 사용됩니다.

### <a name="authentication-agent-initialization"></a>인증 에이전트 초기화

인증 에이전트가 등록 후 또는 서버가 다시 시작된 후 처음으로 시작되면 Azure AD 서비스와 안전하게 통신하고 암호 유효성 검사 요청을 받기 시작하는 방법이 필요합니다.

![에이전트 초기화](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

인증 에이전트를 초기화하는 방법은 다음과 같습니다.



1. 인증 에이전트는 Azure AD에 대해 아웃바운드 “부트스트랩” 요청을 수행하여 시작됩니다. 
    - 이 부트스트랩 요청은 포트 443을 통해 상호 인증된 HTTPS 채널을 통해 이루어집니다(인증 에이전트 등록 중에 발급된 것과 동일한 인증서 사용).
2. Azure AD는 테넌트(테넌트 ID로 식별) 고유의 Azure Service Bus 큐에 **액세스 키**를 제공하여 이 부트스트랩 요청에 응답합니다.
3. 인증 에이전트는 큐에 영구적 아웃바운드 HTTPS 연결(포트 443을 통해)을 설정합니다. 
    - 이제 암호 유효성 검사 요청을 검색 및 처리할 준비가 되었습니다.

테넌트에 여러 인증 에이전트가 등록된 경우 초기화 절차를 통해 각각 동일한 Azure Service Bus 큐에 연결되는지 확인합니다. 

## <a name="processing-sign-in-requests"></a>로그인 요청 처리 

아래 다이어그램은 통과 인증에서 사용자 로그인 요청을 처리하는 방식을 보여 줍니다.

![로그인 처리](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

통과 인증에서 사용자 로그인 요청을 다음과 같이 처리합니다. 

1. 사용자가 응용 프로그램에 액세스하려고 합니다(예: Outlook Web App - [https://outlook.office365.com/owa](https://outlook.office365.com/owa)).
2. 사용자가 아직 로그인하지 않은 경우 해당 응용 프로그램은 브라우저를 Azure AD 로그인 페이지로 리디렉션합니다.
3. Azure AD STS 서비스는 사용자 로그인 페이지로 다시 응답합니다.
4. 사용자가 사용자 이름과 암호를 Azure AD 로그인 페이지에 입력하고 "로그인" 단추를 클릭합니다.
5. 사용자 이름 및 암호는 HTTPS POST 요청으로 Azure AD STS로 전송됩니다.
6. Azure AD STS는 Azure SQL Database로부터 테넌트에 등록된 모든 인증 에이전트에 대한 공개 키를 검색하고 이를 사용하여 암호를 암호화합니다. 
    - 테넌트에 등록된 ‘N’ 인증 에이전트에 대해 ‘N’ 암호화된 암호 값을 생성합니다.
7. Azure AD STS는 테넌트 전용 Azure Service Bus 큐에 암호 유효성 검사 요청(사용자 이름 및 암호화된 암호 값)을 수행합니다.
8. 초기화된 인증 에이전트는 Azure Service Bus 큐에 영구적으로 연결되므로 제공되는 인증 에이전트 중 하나가 암호 유효성 검사 요청을 검색합니다.
9. 인증 에이전트는 ID를 사용하여 공개 키에 고유한 암호화된 암호 값을 찾고 개인 키를 사용하여 암호 해독합니다.
10. 인증 에이전트는 **[Win32 LogonUser API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx)**(**dwLogonType** 매개 변수를 **LOGON32_LOGON_NETWORK**로 설정)를 사용하여 온-프레미스 Active Directory에 대해 사용자 이름 및 암호의 유효성 검사를 시도합니다. 
    - 이는 페더레이션 로그인 시나리오에서 사용자 로그인을 위해 AD FS(Active Directory Federation Service)에서 사용한 API와 동일합니다.
11. 인증 에이전트는 Active Directory에서 결과를 받습니다(성공, 사용자 이름 또는 암호 불일치, 암호 만료, 사용자 잠김 등).
12. 인증 에이전트는 포트 443을 통한 아웃바운드 상호 인증 HTTPS 채널을 통해 결과를 다시 Azure AD STS로 전달합니다. 상호 인증에서는 등록하는 동안 이전에 인증 에이전트에 발급된 것과 동일한 인증서를 사용합니다.
13. Azure AD STS는 이 결과가 테넌트의 특정 로그인 요청과 관련이 있는지 확인합니다.
14. Azure AD STS는 구성된 대로 로그인 절차를 계속 진행합니다. 예를 들어, 암호 유효성 검사가 성공하면 사용자에게 Multi-Factor Authentication을 요청하거나 다시 응용 프로그램으로 리디렉션될 수 있습니다.

## <a name="operational-security-of-authentication-agents"></a>인증 에이전트의 운영 보안

통과 인증이 안전하게 계속 운영되도록 하기 위해 Azure AD는 주기적으로 인증서를 갱신합니다. 이러한 갱신은 Azure AD에서 트리거되며 인증 에이전트 자체에서 관리되지 않습니다.

![운영 보안](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

다음은 인증 에이전트가 Azure AD와의 트러스트를 갱신하는 방법입니다.

1. 인증 에이전트는 Azure AD를 주기적으로 ping(몇 시간마다)하여 인증서를 갱신할 때인지 확인합니다. 
    - 이 확인은 상호 인증된 HTTPS 채널을 통해 수행됩니다(등록 시 발급된 것과 동일한 인증서 사용).
2. 서비스에서 갱신할 시간임을 나타내면 인증 에이전트는 새로운 키 쌍(공개 키 및 개인 키)을 생성합니다.
    - 이러한 키는 표준 **RSA 2048비트** 암호화를 사용하여 생성됩니다.
    - 개인 키는 온-프레미스 서버를 벗어나지 않습니다.
3. 그러면 인증 에이전트는 요청에 포함된 다음 구성 요소와 함께 HTTPS를 통해 Azure AD에 “인증서 갱신” 요청을 합니다.
    - 기존 인증서(Windows 인증서 저장소의 **CERT_SYSTEM_STORE_LOCAL_MACHINE** 위치에서 검색됨). 이 절차에는 전역 관리자가 관련되지 않으므로 전역 관리자 대신 필요한 액세스 토큰이 없습니다.
    - 2단계에서 생성된 공개 키
    - **인증서 서명 요청**(CSR 또는 인증서 요청). 이것은 Azure AD를 인증 기관으로 사용하여 새 디지털 ID 인증서에 적용됩니다.
4. Azure AD는 인증서 갱신 요청에서 기존 인증서의 유효성을 검사합니다. 그런 다음 요청이 테넌트에 등록된 인증 에이전트에서 발생한 것인지 확인합니다.
5. 기존 인증서가 여전히 유효한 경우 Azure AD는 새 디지털 ID 인증서를 서명하고 인증 에이전트에 새 인증서를 다시 발급합니다. 
6. 기존 인증서가 만료된 경우 Azure AD는 테넌트의 등록된 인증 에이전트 목록에서 인증 에이전트를 삭제합니다. 그런 다음 전역 관리자는 새 인증 에이전트를 수동으로 설치하고 등록해야 합니다.
    - 인증서는 **Azure AD의 루트 CA(인증 기관)**를 사용하여 서명됩니다.
    - 인증서의 제목(**고유 이름 또는 DN**)은 테넌트를 고유하게 식별하는 GUID인 **테넌트 ID**로 설정됩니다. 즉, 이 인증서는 테넌트로만 범위가 국한됩니다.
6. Azure AD는 인증 에이전트의 “새로운” 공개 키를 Azure AD만 액세스할 수 있는 Azure SQL Database에 저장합니다. 그리고 인증 에이전트와 관련된 “이전” 공개 키를 무효화합니다.
7. 그러면 새 인증서(5단계에서 발급)가 **Windows 인증서 저장소**(특히 **[CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER)** 위치)의 서버에 저장됩니다.
    - 트러스트 갱신 절차는 비대화형으로 발생하기 때문에(전역 관리자 존재 없이) 인증 에이전트는 **CERT_SYSTEM_STORE_LOCAL_MACHINE** 위치에 있는 기존 인증서를 업데이트할 권한이 더 이상 없습니다. **CERT_SYSTEM_STORE_LOCAL_MACHINE** 위치에 있는 인증서 자체는 이 절차 중에 제거되지 않습니다.
8. 새 인증서는 이 시점부터 인증에 사용됩니다. 이후 모든 인증서 갱신에서는 **CERT_SYSTEM_STORE_LOCAL_MACHINE** 위치의 인증서를 대체합니다.

## <a name="auto-update-of-authentication-agents"></a>인증 에이전트의 자동 업데이트

새 버전이 출시되는 경우 업데이트 프로그램 응용 프로그램이 인증 에이전트를 자동으로 업데이트합니다. 테넌트에 대한 암호 유효성 검사 요청은 처리하지 않습니다. 

Azure AD는 서명된 **Windows Installer 패키지(MSI)**로 소프트웨어의 새 버전을 호스팅합니다. MSI는 다이제스트 알고리즘으로 **SHA256**과 [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx)를 사용하여 서명됩니다. 

![자동 업데이트](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

인증 에이전트가 자동 업데이트되는 방식은 다음과 같습니다.

1. 업데이트 프로그램에서 Azure AD를 주기적으로 ping(매시간)하여 새 버전의 인증 에이전트가 제공되는지 확인합니다. 
    - 이 확인은 상호 인증된 HTTPS 채널을 통해 수행됩니다(등록 시 발급된 것과 동일한 인증서 사용). 인증 에이전트와 업데이트 프로그램은 서버에 저장된 인증서를 공유합니다.
2. 새 버전이 제공되는 경우 Azure AD는 서명된 MSI를 업데이트 프로그램에 다시 반환합니다.
3. 업데이트 프로그램은 Microsoft에서 MSI를 서명했는지 확인합니다.
4. 업데이트 프로그램은 MSI를 실행합니다. 이 작업은 다음 단계를 수행합니다(업데이트 프로그램은 [로컬 시스템](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) 권한으로 실행됨).
    - 인증 에이전트 서비스를 중지합니다.
    - 서버에서 새 버전의 인증 에이전트를 설치합니다.
    - 인증 에이전트 서비스를 다시 시작합니다.

>[!NOTE]
>테넌트에 여러 인증 에이전트가 등록된 경우 Azure AD는 인증서를 갱신하거나 동시에 업데이트하지 않습니다. 대신 Azure AD는 로그인 요청의 고가용성을 보장하기 위해 점진적으로 수행합니다.


## <a name="next-steps"></a>다음 단계
- [**현재 제한 사항** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - 지원되는 시나리오와 지원되지 않는 시나리오를 알아봅니다.
- [**빠른 시작**](active-directory-aadconnect-pass-through-authentication-quick-start.md) - Azure AD 통과 인증을 작동하고 실행합니다.
- [**스마트 잠금**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) - 테넌트에서 사용자 계정을 보호하도록 스마트 잠금 기능을 구성합니다.
- [**작동 방법**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) - Azure AD 통과 인증의 작동 방식에 대한 기본 사항을 설명합니다.
- [**FAQ(질문과 대답)**](active-directory-aadconnect-pass-through-authentication-faq.md) - 질문과 대답을 다루고 있습니다.
- [**문제 해결**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 기능과 관련된 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
- [**Azure AD 원활한 SSO**](active-directory-aadconnect-sso.md) - 이 보완 기능에 대해 자세히 알아봅니다.

