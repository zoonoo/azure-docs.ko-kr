---
title: Azure 오스트레일리아에서 게이트웨이의 원격 관리 보안
description: 오스트레일리아 정부 정책, 규정 및 법규의 특정 요구 사항을 충족 하기 위해 오스트레일리아 지역 내에서 보안 원격 관리를 구성 하는 방법에 대 한 지침입니다.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 1e4c4712312faf2274a4a0737c4fc1f7ce39f98e
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68824194"
---
# <a name="secure-remote-administration-of-your-gateway-in-azure-australia"></a>Azure 오스트레일리아에서 게이트웨이의 원격 관리 보안

관리 활동이 안전 하 게 수행 되 고 제어 되는 시스템의 가용성과 무결성은 중요 합니다. 관리 활동은 안전한 연결을 통해 보안 장치에서 수행 되 고 강력한 인증 및 권한 부여 프로세스를 통해 지원 되어야 합니다. 보안 원격 관리를 사용 하면 공인 작업만 수행 되며 공인 관리자만이 작업을 수행할 수 있습니다.

이 문서에서는 Azure에서 호스트 되는 보안 시스템에 대 한 보안 원격 관리 기능을 구현 하는 방법에 대 한 정보를 제공 합니다 .이는 ACSC (오스트레일리아 사이버 Security Center) 소비자 지침과 ACSC 정보의 의도 보안 설명서 (ISM).

## <a name="australian-cyber-security-centre-acsc-requirements"></a>ACSC (오스트레일리아 사이버 Security Center) 요구 사항

시스템에 대 한 전반적인 보안 요구 사항은 ISM에 정의 되어 있습니다. 보안 관리를 제공 하는 행정 엔터티를 지원 하기 위해 acsc [는 acsc Protect를 게시 했습니다. 보안 관리](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

이 문서에서는 보안 관리의 중요성에 대해 설명 하 고 보안 관리 환경을 구현 하는 방법 중 하나를 제안 합니다. 이 문서에서는 다음과 같이 보안 관리 솔루션의 요소에 대해 설명 합니다.

|요소   |설명   |
|---|---|
|권한 있는 액세스 제어   |권한 있는 계정에 대 한 액세스를 제어 하는 것은 권한 있는 계정을 오용 하지 않도록 보호 하는 기본 보안 컨트롤입니다. 액세스 제어 방법론은 서비스 계정 및 직원 이동을 관리 하기 위한 프로세스 및 절차 뿐만 아니라 ' 최소 권한 ' 및 ' 필요성 ' 개념을 포함 합니다.   |
|다단계 인증   |실제 토큰 또는 스마트 카드와 같이 사용자 이름 및 암호를 벗어난 인증의 추가 요소를 구현 하면 중요 한 자산을 보호할 수 있습니다. 악의적 사용자가 권한 있는 계정에 대 한 자격 증명을 손상 시키는 경우 모든 관리 작업은 먼저 특정 형태의 다단계 인증을 수행 해야 하므로 결과를 크게 줄일 수 있습니다.|
|권한 있는 워크스테이션|관리 작업에 대해 알려진 보안 환경을 사용 하면 추가 보안 제어의 구현으로 인해 네트워크가 손상 될 위험이 줄어듭니다.|
|로깅 및 감사   |워크스테이션, 서버, 네트워크 장치 및 점프 상자에서 보안 및 관리 관련 이벤트의 자동 생성, 수집 및 분석을 통해 손상 및 손상 된 손상을 검색할 수 있습니다. Automation을 사용 하면 조직에서 더 신속 하 게 응답 하 여 손상의 영향을 줄일 수 있습니다.|
|네트워크 조각화 및 분리|네트워크를 서로 다른 보안 도메인과 같은 논리 영역으로 분할 하 고, 한 영역에서 다른 영역으로 흐르는 데이터 형식을 제한 하 여 이러한 논리 네트워크를 추가로 분리 하 여 측면 이동을 제한 합니다. 구분은 악의적 사용자가 추가 리소스에 액세스 하지 못하도록 방지 합니다.|
|점프 상자|점프 상자는 보안이 강화 된 원격 액세스 서버 이며 일반적으로 Microsoft의 SSH (원격 데스크톱 서비스 또는 Secure Shell) 소프트웨어를 utilising 합니다. 점프 상자는 전용 호스트에서 수행 되는 모든 관리 작업을 사용 하 여 중요 한 시스템에 액세스 하는 관리자의 단계별 실행 지점 역할을 합니다.|

이 문서에서는 위의 요소를 Azure에 배포 된 시스템의 보안 관리에 사용할 수 있는 방법에 대 한 참조 아키텍처를 제공 합니다.

## <a name="architecture"></a>아키텍처

보안 관리 기능을 제공 하려면 모든 구성 요소가 함께 작동 하 여 결합 된 솔루션을 구성 해야 합니다. 제공 된 참조 아키텍처에서 구성 요소는 acsc Protect에 [설명 된 요소에 매핑됩니다. 보안 관리](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

![Azure 보안 원격 관리 아키텍처](media/remote-admin.png)

## <a name="components"></a>구성 요소

아키텍처는 권한 있는 계정에 필요한 권한만 부여 되 고, 안전 하 게 식별 되며, 공인 장치 에서만 관리 인터페이스에 대 한 액세스를 제공 하 고, 보안 통신을 통해 제공 되도록 설계 되었습니다. 제어 및 감사 되는 메커니즘입니다.

|솔루션| 구성 요소|요소|
|---|---|---|
|보안 장치 |<ul><li>권한 있는 워크스테이션</li><li>모바일 장치</li><li>Microsoft Intune</li><li>그룹 정책</li><li>점프 서버/방호 호스트</li><li>JIT (just-in-time) 관리</li></ul> |<ul><li>권한 있는 워크스테이션</li><li>점프 상자</li></ul>|
|보안 통신 |<ul><li>Azure Portal</li><li>Azure VPN 게이트웨이</li><li>RD (원격 데스크톱) 게이트웨이</li><li>NSG(네트워크 보안 그룹)</li></ul> |<ul><li>네트워크 조각화 및 분리</li></ul>|
|강력한 인증 |<ul><li>DC (도메인 컨트롤러)</li><li>Azure AD(Azure Active Directory)</li><li>NPS (네트워크 정책 서버)</li><li>Azure MFA</li></ul> |<ul><li>다단계 인증</li></ul> |
|강력한 권한 부여 |<ul><li>IAM(ID 및 액세스 관리)</li><li>PIM(Privileged Identity Management)</li><li>조건부 액세스</li></ul>|<ul><li>권한 있는 액세스 제어</li></ul>|
|||

>[!NOTE]
>로깅 및 감사 요소에 대 한 자세한 내용은 [게이트웨이 로깅, 감사 및 표시 유형](gateway-log-audit-visibility.md) 문서를 참조 하세요.

## <a name="administration-workflow"></a>관리 워크플로

Azure에 배포 된 시스템을 관리 하는 것은 azure 구성을 관리 하 고 Azure에 배포 된 워크 로드를 관리 하는 두 개의 고유한 범주로 나뉩니다. Azure 구성은 Azure Portal를 통해 수행 되며, 작업 관리는 SQL Management Studio 같은 도구를 사용 하 여 RDP (원격 데스크톱 프로토콜), Secure Shell (SSH) 또는 PaaS 기능과 같은 관리 메커니즘을 통해 완료 됩니다.

관리에 대 한 액세스 권한은 아키텍처에 나열 된 구성 요소를 포함 하는 다중 단계 프로세스 이며 Azure 워크 로드에 대 한 액세스를 수행 하기 전에 Azure Portal 및 Azure 구성에 액세스 해야 합니다.

>[!NOTE]
> 여기에 설명 된 단계는 Azure의 GUI (그래픽 사용자 인터페이스) 구성 요소를 사용 하는 일반적인 프로세스입니다. PowerShell 같은 다른 인터페이스를 사용 하 여 이러한 단계를 완료할 수도 있습니다.

### <a name="azure-configuration-and-azure-portal-access"></a>Azure 구성 및 Azure Portal 액세스

|단계 |Description |
|---|---|
|권한 있는 워크스테이션 로그인 |관리자는 관리자 자격 증명을 사용 하 여 권한 있는 워크스테이션에서 로그인 합니다. 그룹 정책 관리 되지 않는 계정이 권한 있는 워크스테이션에 인증 되지 않도록 방지 하 고 관리자 계정이 권한이 없는 워크스테이션에 인증 하지 못하도록 합니다. Microsoft Intune은 소프트웨어 패치, 맬웨어 방지 및 기타 규정 준수 요구 사항을 사용 하 여 최신 상태를 유지 하기 위해 권한 있는 워크스테이션의 호환성을 관리 합니다. |
|Azure Portal 로그인 |관리자는 TLS (전송 계층 보안)를 사용 하 여 암호화 되 고 관리 자격 증명을 사용 하 여 로그인 하는 Azure Portal에 대 한 웹 브라우저를 엽니다. 인증 요청은 Azure Active Directory를 통해 직접 또는 Active Directory Federation Services (AD FS) 또는 통과 인증과 같은 인증 메커니즘을 통해 처리 됩니다. |
|Azure MFA |Azure MFA는 권한 있는 계정의 등록 된 모바일 장치에 인증 요청을 보냅니다. 모바일 장치는 보안 요구 사항 준수를 보장 하기 위해 Intune에서 관리 됩니다. 관리자는 인증 시도를 Azure MFA로 공인 전에 PIN 또는 생체 인식 시스템을 사용 하 여 모바일 장치에 먼저 인증 한 다음 Microsoft Authenticator 앱에 인증 해야 합니다. |
|조건부 액세스 |조건부 액세스 정책은 인증 시도를 확인 하 여 연결이 들어오는 IP 주소, 권한 있는 계정에 대 한 그룹 멤버 자격 및의 관리 및 준수 상태와 같은 필요한 요구 사항을 충족 하는지 확인 합니다. Intune에서 보고 한 권한 있는 워크스테이션입니다. |
|PIM(Privileged Identity Management) |Azure Portal를 통해 관리자는 이제 PIM을 통해 권한 부여 된 권한 있는 역할에 대 한 활성화를 활성화 하거나 요청할 수 있습니다. PIM은 권한 있는 계정에 관리 작업을 수행 하는 데 필요한 시간 동안만 권한 있는 계정에 대 한 관리 권한이 없는지 확인 합니다. 또한 PIM은 감사를 위해 모든 요청 및 활성화에 대 한 로깅을 제공 합니다. |
|ID 및 액세스 관리|권한 있는 계정이 안전 하 게 식별 되 고 역할이 활성화 되 면 관리자는 Id 및 액세스 관리를 통해 권한이 할당 된 Azure 구독 및 리소스에 대 한 액세스를 제공 합니다.|

권한 있는 계정에서 Azure Portal에 대 한 관리 액세스를 얻는 단계를 완료 한 후에는 워크 로드에 대 한 액세스를 구성 하 고 관리 연결을 설정할 수 있습니다.

### <a name="azure-workload-administration"></a>Azure 워크 로드 관리

|단계 |설명|
|---|---|
|JIT (just-in-time) 액세스|관리자는 가상 컴퓨터에 대 한 액세스 권한을 얻기 위해 JIT를 사용 하 여 RD 게이트웨이 IP 주소와 RDP 또는 SSH를 사용 하 여 점프 서버에서 관련 워크 로드 가상 컴퓨터로 RDP에 대 한 액세스를 요청 합니다.|
|Azure VPN 게이트웨이|이제 관리자는 권한 있는 워크스테이션에서 Azure VPN Gateway로 지점 및 사이트 간 IPSec VPN 연결을 설정 하 고, 연결을 설정 하기 위해 인증서 인증을 수행 합니다.|
|RD 게이트웨이|이제 관리자는 원격 데스크톱 연결 구성에 지정 된 RD 게이트웨이를 사용 하 여 점프 서버에 대 한 RDP 연결을 시도 합니다. RD 게이트웨이에는 Azure VPN Gateway 연결을 통해 연결할 수 있는 개인 IP 주소가 있습니다. RD 게이트웨이 정책은 권한 있는 계정이 요청 된 점프 서버에 액세스 하는 데 공인 여부를 제어 합니다. RD 게이트웨이 관리자에 게 자격 증명을 묻는 메시지를 표시 하 고 NPS (네트워크 정책 서버)에 인증 요청을 전달 합니다.|
|NPS (네트워크 정책 서버)|NPS는 RD 게이트웨이에서 인증 요청을 수신 하 고, Azure MFA 인증 요청을 트리거하기 위해 Azure Active Directory 요청을 보내기 전에 Active Directory에 대 한 사용자 이름과 암호의 유효성을 검사 합니다.|
|Azure MFA|Azure MFA는 권한 있는 계정의 등록 된 모바일 장치에 인증 요청을 보냅니다. 모바일 장치는 보안 요구 사항 준수를 보장 하기 위해 Intune에서 관리 됩니다. 관리자는 인증 시도를 Azure MFA로 공인 전에 PIN 또는 생체 인식 시스템을 사용 하 여 모바일 장치에 먼저 인증 한 다음 Microsoft Authenticator 앱에 인증 해야 합니다.|
|점프 서버|성공적으로 인증 된 후에는 TLS (전송 계층 보안)를 사용 하 여 RDP 연결을 암호화 한 다음 암호화 된 IPSec 터널을 통해 RD 게이트웨이 및 점프 서버를 통해 Azure VPN Gateway에 전송 합니다. 이제 점프 서버에서 관리자는 JIT 요청에 지정 된 대로 작업 가상 컴퓨터에 대 한 RDP 또는 SSH 작업을 수행할 수 있습니다.|

## <a name="general-guidance"></a>일반 지침

이 문서에 나열 된 구성 요소를 구현 하는 경우 다음과 같은 일반적인 지침이 적용 됩니다.

* 서비스의 지역 가용성의 유효성을 검사 하 여 모든 데이터가 공인 위치 내에 유지 되 고 보호 된 워크 로드에 대 한 첫 번째 기본 설정으로 AU Central 또는 AU Central 2에 배포 합니다.

* 개별 서비스의 인증 상태는 *Azure-Acsc 인증 보고서 – 보호 된 2018* 게시를 참조 하 고, *ACSC 소비자 가이드에 따라 보고서에 포함 되지 않은 관련 구성 요소에 대해 자체 평가를 수행 합니다. 보호 된 Microsoft Azure*

* 네트워크 연결 및 필요한 인증 구성 요소에 액세스 하는 데 필요한 프록시 구성 (예: Azure AD, ADFS 및 PTA)을 확인 합니다.

* Azure Policy를 사용 하 여 요구 사항을 모니터링 하 고 준수 적용

* 가상 컴퓨터, 특히 Active Directory 도메인 컨트롤러는 암호화 된 저장소 계정 및 제품에 저장 되어 있는지 확인 Azure Disk Encryption

* 이 문서에 나열 된 기술 제어를 기반이 되 강력한 id 및 관리 권한 관리 프로세스 및 관리를 만들고 유지 관리 합니다.

|리소스|URL|
|---|---|
|오스트레일리아 규정 및 정책 준수 문서|[오스트레일리아 규정 및 정책 준수 문서](https://aka.ms/au-irap)|
|Azure 제품-오스트레일리아 지역 및 비 지역|[Azure 제품-오스트레일리아 지역 및 비 지역](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|사이버 보안 인시던트를 완화 하기 위한 전략|[사이버 보안 인시던트를 완화 하기 위한 전략](https://acsc.gov.au/infosec/mitigationstrategies.htm)|
|ACSC 보호: 보안 관리|[ACSC 보호: 보안 관리](https://acsc.gov.au/publications/protect/secure-administration.htm)|
|방법: NPS(네트워크 정책 서버) 확장 및 Azure AD를 사용하여 원격 데스크톱 게이트웨이 인프라 통합|[NPS 및 Azure AD와 RD 게이트웨이 통합](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-rdg)|

## <a name="component-guidance"></a>구성 요소 지침

이 섹션에서는 전체 보안 원격 관리 아키텍처에서 각 구성 요소 및 해당 역할의 용도에 대 한 정보를 제공 합니다. 참조 설명서, 가이드 및 자습서와 같은 유용한 리소스에 액세스 하기 위한 추가 링크가 제공 됩니다.

## <a name="secure-devices"></a>보안 장치

권한 있는 사용자가 관리 기능을 수행 하는 데 사용 하는 물리적 장치는 악의적인 행위자에 게 중요 한 목표입니다. 물리적 장치의 보안과 무결성을 유지 하 고 악성 소프트웨어에서 무료 이며 손상 되지 않도록 보호 하는 것은 안전한 원격 관리 기능을 제공 하는 주요 부분입니다. 여기에는 응용 프로그램 허용 목록, 패치 응용 프로그램, 응용 프로그램 강화, 운영 체제 패치와 같은 사이버 보안 인시던트를 완화 하기 위해 ACSC의 필수적인 8 전략에 지정 된 우선 순위가 높은 보안 구성이 포함 됩니다. 이러한 기능을 설치, 구성, 감사, 유효성 검사 및 보고 하 여 장치의 상태가 조직의 요구 사항을 준수 하는지 확인 해야 합니다.

### <a name="privileged-workstation"></a>권한 있는 워크스테이션

권한 있는 워크스테이션은 관리 업무를 수행 하는 데 사용할 수 있으며 관리 계정에만 액세스할 수 있는 확정 된 컴퓨터입니다. 권한 있는 워크스테이션에는 실행할 수 있는 소프트웨어를 제한 하기 위해 정책 및 구성이 있어야 하 고, 네트워크 리소스에 대 한 액세스 및 인터넷 및 자격 증명은 장치를 도난당 하거나 손상 된 경우 보호 해야 합니다. |

|리소스|링크|
|---|---|
|권한 있는 액세스 워크스테이션 아키텍처 개요|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)|
|권한 있는 액세스 보안 참조 자료|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)|

### <a name="mobile-device"></a>모바일 장치

모바일 장치는 이식성 및 크기 때문에 실수로 인 한 손실이 나 도난 위험이 있으며 적절 하 게 보호 해야 합니다. 모바일 장치는 장치 액세스를 위한 인증을 적용 하는 기능, 위치 서비스를 통한 추적 가능성, 암호화 기능 및 원격 초기화 기능을 제공 하 여 인증을 위한 강력한 추가 요소를 제공 합니다. 모바일 장치를 Azure에 대 한 추가 인증 요소로 사용 하는 경우 장치는 전화 통화 또는 문자 메시지를 통하지 않고 PIN 또는 생체 인식 인증을 사용 하 여 Microsoft Authenticator 앱을 사용 하도록 구성 해야 합니다.

|리소스|링크|
|---|---|
|Azure AD 인증 메서드|[https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)|
|Microsoft Authenticator 앱을 사용 하는 방법|[https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app](https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app)|

### <a name="microsoft-intune"></a>Microsoft Intune

Intune은 모바일 장치 및 앱을 관리 하는 Enterprise Mobility + Security 구성 요소입니다. Id 및 액세스 제어를 위한 Azure Active Directory와 같은 다른 구성 요소와 긴밀 하 게 통합 되어 데이터 보호를 위한 Azure Information Protection 합니다. Intune은 리소스 액세스를 위한 규정 준수 요구 사항을 설정 하는 워크스테이션 및 모바일 장치에 대 한 정책을 제공 하 고 관리 장치의 상태에 대 한 통찰력을 얻기 위한 보고 및 감사 기능을 제공 합니다.

|리소스|링크|
|---|---|
|Microsoft Intune 설명서|[https://docs.microsoft.com/intune/](https://docs.microsoft.com/intune/)|
|Intune에서 장치 준수 시작|[https://docs.microsoft.com/intune/device-compliance-get-started](https://docs.microsoft.com/intune/device-compliance-get-started)|

### <a name="group-policy"></a>그룹 정책

그룹 정책는 운영 체제 및 응용 프로그램의 구성을 제어 하는 데 사용 됩니다. 보안 정책은 시스템의 인증, 권한 부여 및 감사 설정을 제어 합니다. 그룹 정책를 사용 하 여 권한 있는 워크스테이션을 강화 하 고, 관리 자격 증명을 보호 하 고, 권한 없는 계정을 권한 있는 장치에 액세스 하지 못하도록 제한 합니다.

|리소스|링크|
|---|---|
|로컬에서 로그인 그룹 정책 설정 허용|[https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally)|

### <a name="jump-server--bastion-host"></a>점프 서버/방호 호스트

점프 서버/요새 호스트는 관리를 위한 centralised 지점입니다. 관리 의무를 수행 하는 데 필요한 도구를 포함 하 고 있으며 관리 포트에서 리소스에 연결 하는 데 필요한 네트워크 액세스 권한도 가집니다. 점프 서버는이 문서에서 가상 머신 워크 로드를 관리 하는 중심점 이지만, SQL과 같은 PaaS (Platform as a Service) 기능을 관리 하기 위한 공인 점으로 구성할 수도 있습니다. Id 및 네트워크 제어를 사용 하 여 서비스 별로 PaaS 기능에 대 한 액세스를 제한할 수 있습니다.

|리소스|링크|
|---|---|
|보안 관리 호스트 구현|[https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts)|

### <a name="just-in-time-jit-access"></a>JIT (just-in-time) 액세스

JIT는 NSGs (네트워크 보안 그룹)를에서는 하 여 Virtual Machines에서 RDP 및 SSH와 같은 관리 프로토콜에 대 한 액세스를 차단 하는 Azure Security Center 기능입니다. Virtual Machines에서 호스트 되는 응용 프로그램은 계속 정상적으로 작동 하지만, 관리 액세스 권한을 부여 하려면 설정 된 기간 동안에만 권한을 부여 해야 합니다. 모든 요청은 감사를 위해 기록 됩니다.

|리소스 |링크 |
|---|---|
|JIT (Just-in-time) 액세스 관리|[https://docs.microsoft.com/azure/security-center/security-center-just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|
|Azure Just-in-time VM 액세스 자동화|[https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access](https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access)|

## <a name="secure-communication"></a>보안 통신

관리 활동에 대 한 통신 트래픽은 관리 자격 증명과 같은 매우 중요 한 정보를 포함할 수 있으며 그에 따라 관리 하 고 보호 해야 합니다. 보안 통신을 제공 하는 것은 신뢰할 수 있는 암호화 기능을 통해 신뢰할 수 있는 암호화 기능을 통해 끝점을 공인 하 고 시스템이 손상 되는 경우 측면의 움직임을 제어 하는 관리 트래픽을 제한 하는 제한 사항

### <a name="azure-portal"></a>Azure Portal

Azure Portal에 대 한 통신은 TLS (Transport Layer Security)를 사용 하 여 암호화 되 고 Azure Portal 사용은 ACSC에서 인증 되었습니다. 사용자 엔터티는 *Acsc 소비자 가이드* 의 권장 사항을 따르고, 최신 버전의 TLS를 사용 하 고 지원 되는 암호화 알고리즘을 사용 하도록 웹 브라우저를 구성 해야 합니다.

|리소스 |링크 |
|---|---|
|Azure 암호화 개요 – 전송 중 암호화|[https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit](https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit)|

### <a name="azure-vpn-gateway"></a>Azure VPN 게이트웨이

Azure VPN Gateway는 권한 있는 워크스테이션에서 Azure로 암호화 된 보안 연결을 제공 합니다. Azure VPN Gateway는 보안 IPSec 통신을 제공 하기 위해 ACSC에 의해 인증 되었습니다. Azure VPN Gateway은 ACSC 소비자 가이드, ACSC 인증 보고서 및 기타 특정 지침에 따라 Azure를 구성 해야 합니다.

|리소스 |링크 |
|---|---|
|지점 및 사이트 간 연결 정보|[https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about)|
|Azure VPN Gateway 암호화 세부 정보|[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto)|
|Azure VPN Gateway 구성|[Azure VPN Gateway 구성](vpn-gateway.md)|

### <a name="remote-desktop-rd-gateway"></a>RD (원격 데스크톱) 게이트웨이

RD 게이트웨이는 시스템에 대 한 RDP 연결을 제어 하 고 authorising 하는 보안 메커니즘입니다. TLS를 사용 하 여 암호화 된 HTTPS (하이퍼텍스트 전송 프로토콜)에서 RDP 트래픽을 캡슐화 하는 방식으로 작동 합니다. TLS는 관리 트래픽에 대 한 추가 보안 계층을 제공 합니다.

|리소스 |링크 |
|---|---|
|원격 데스크톱 서비스 아키텍처|[https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)|

### <a name="network-security-groups-nsgs"></a>NSG(네트워크 보안 그룹)

NSGs는 서브넷 또는 가상 컴퓨터를 시작 하거나 종료 하는 네트워크 트래픽에 대 한 Acl (Access Control 목록) 기능입니다. NSGs는 네트워크 조각화를 제공 하 고 시스템 간에 허용 되는 통신 흐름을 제어 하 고 제한 하는 메커니즘을 제공 합니다. NSGs는 관리 프로토콜에 대 한 액세스를 허용 하거나 거부 하는 JIT (Just-in-time) 관리의 핵심 구성 요소입니다.

|리소스 |링크 |
|---|---|
|Azure 보안 그룹 개요|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|방법: 가상 네트워크 계획|[https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|

## <a name="strong-authentication"></a>강력한 인증

시스템에 대 한 액세스 권한을 부여 하기 전에 권한 있는 사용자를 안전 하 게 식별 하는 것은 안전한 관리의 핵심 구성 요소 특권 수준의 계정에 연결 된 자격 증명을 보호 하 고 악의적인 행위자가 가장 또는 자격 증명 도난을 통해 시스템에 액세스 하지 못하도록 하는 메커니즘을 준비 해야 합니다.

### <a name="domain-controller-dc"></a>DC (도메인 컨트롤러)

상위 수준에서 DC는 도메인 내의 모든 사용자, 컴퓨터 및 그룹을 포함 하는 Active Directory 데이터베이스의 복사본을 호스팅합니다. Dc 사용자 및 컴퓨터에 대 한 인증을 수행 합니다. 이 아키텍처의 Dc는 Azure 내에서 가상 머신으로 호스팅되며, 점프 서버 및 워크 로드 가상 머신에 연결 하는 권한 있는 계정에 대 한 인증 서비스를 제공 합니다.

|리소스 |링크 |
|---|---|
|Active Directory Domain Services 개요|[https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)|

### <a name="azure-active-directory-azure-ad"></a>Azure AD(Azure Active Directory)

Azure AD는 Azure에 대 한 인증 서비스입니다. 클라우드를 포함 합니다.

id 및는 Azure 환경에 대 한 인증 및 권한 부여을 제공 합니다. Azure AD는 Azure AD Connect를 통해 Active Directory 전환은 수 있으며, Active Directory Federation Services (AD FS) 및 Azure AD Connect를 통해 페더레이션 인증을 제공할 수 있습니다. Azure AD는 보안 관리의 핵심 구성 요소입니다.

|리소스 |링크 |
|---|---|
|Azure Active Directory 설명서|[https://docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory)|
|하이브리드 Id 설명서|[https://docs.microsoft.com/azure/active-directory/hybrid](https://docs.microsoft.com/azure/active-directory/hybrid)|

### <a name="network-policy-server-nps"></a>NPS (네트워크 정책 서버)

NPS는 고급 인증과 권한 부여 프로세스를 제공 하는 인증 및 정책 서버입니다. 이 아키텍처의 NPS 서버는 Azure MFA 인증과 RD 게이트웨이 인증 요청을 통합 하기 위해 제공 됩니다. NPS에는 Azure AD에서 Azure MFA와의 통합을 지 원하는 특정 플러그 인이 있습니다.

|리소스 |링크 |
|---|---|
|네트워크 정책 서버 설명서|[https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)|

### <a name="azure-mfa"></a>Azure MFA

Azure MFA는 Azure Portal와 같은 클라우드 리소스에 액세스 하기 위해 사용자 이름 및 암호 외에 인증 요청을 사용 하도록 Azure Active Directory 내에서 제공 하는 인증 서비스입니다. Azure MFA는 다양 한 인증 방법을 지원 하며,이 아키텍처는 Microsoft Authenticator 앱을에서는 하 여 보안을 강화 하 고 NPS와 통합 합니다.

|리소스 |링크 |
|---|---|
|작동 방법: Azure Multi-Factor Authentication|[https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)|
|방법: 클라우드 기반 Azure 다단계 인증 배포|[https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)|

## <a name="strong-authorisation"></a>강력한 권한 부여

권한 있는 계정을 안전 하 게 식별 한 후에는 리소스에 대 한 액세스 권한을 부여할 수 있습니다. 권한 부여는 특정 계정에 할당 된 권한을 제어 하 고 관리 합니다. 강력한 권한 부여 프로세스는 관리 권한을 제한 하는 사이버 보안 인시던트를 완화 하기 위해 ACSC의 필수 8 전략에 부합 합니다.

### <a name="identity-and-access-management"></a>ID 및 액세스 관리

Azure 내에서 권한 있는 작업을 수행 하기 위한 액세스는 해당 계정에 할당 된 역할을 기반으로 합니다. Azure에는 특정 작업을 수행 하기 위한 특정 권한이 있는 광범위 하 고 세분화 된 범위의 역할이 포함 되어 있습니다. 이러한 역할은 구독 또는 리소스 그룹과 같은 여러 수준에서 부여할 수 있습니다. 역할 할당 및 권한 관리는 Azure Active Directory의 계정 및 그룹을 기반으로 하며 Azure 내에서 Access Control (IAM)을 통해 관리 됩니다.

|리소스 |링크 |
|---|---|
|Azure 역할 기반 Access Control|[https://docs.microsoft.com/azure/role-based-access-control](https://docs.microsoft.com/azure/role-based-access-control)|
|역할 정의 이해|[https://docs.microsoft.com/azure/role-based-access-control/role-definitions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions)|

### <a name="privileged-identity-management-pim"></a>PIM(Privileged Identity Management)

PIM은 권한 있는 역할에 대 한 액세스를 제어 하는 Azure Active Directory 구성 요소입니다. 권한 있는 계정에는 영구 또는 특권 수준의 액세스 권한이 필요 하지 않지만, 권한 있는 작업을 완료 하기 위해 일정 기간 동안 권한 있는 액세스를 요청 하는 기능이 대신 부여 될 수 있습니다. PIM은 권한 있는 액세스를 유지 관리 하 고 제한 하는 추가 제어와 권한 인스턴스 사용을 추적 하기 위한 로깅 및 감사를 제공 합니다.

|리소스 |링크 |
|---|---|
|Privileged Identity Management (PIM) 설명서|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)|
|PIM 사용|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started)|

### <a name="conditional-access"></a>조건부 액세스

조건부 액세스는 조건에 따라 리소스에 대 한 액세스를 허용 하거나 거부 하는 Azure Active Directory 구성 요소입니다. 이러한 조건은 네트워크 위치 기반, 장치 유형, 준수 상태, 그룹 멤버 자격 등이 될 수 있습니다. 조건부 액세스는 Intune 및 관리 계정의 그룹 멤버 자격을 통해 MFA, 장치 관리 및 규정 준수를 적용 하는 데 사용 됩니다.

|리소스 |링크 |
|---|---|
|조건부 액세스 설명서|[https://docs.microsoft.com/azure/active-directory/conditional-access](https://docs.microsoft.com/azure/active-directory/conditional-access)|
|방법: 조건부 액세스를 사용 하는 클라우드 앱 액세스를 위한 관리 되는 장치 필요|[https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|

## <a name="next-steps"></a>다음 단계

Azure에서 게이트웨이 구성 요소를 통해 트래픽 흐름을 제어 하는 방법에 대 한 자세한 내용은 [게이트웨이 수신 트래픽 관리 및 제어](gateway-ingress-traffic.md) 에 대 한 문서를 검토 하세요.
