---
title: Azure Multi-Factor Authentication FAQ-Azure Active Directory
description: Azure Multi-Factor Authentication와 관련 된 질문과 대답입니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/18/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86cce0ee2a4966e2c2c8d74ad63a50976c199eb6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967305"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication에 대 한 질문과 대답

이 FAQ는 Azure Multi-Factor Authentication 및 Multi-Factor Authentication 서비스 사용에 대 한 일반적인 질문에 답변 합니다. 일반적으로 서비스에 대 한 질문, 청구 모델, 사용자 환경 및 문제 해결에 대해 자세히 구분 됩니다.

## <a name="general"></a>Általános

> [!IMPORTANT]
> 2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포에 대해 MFA 서버를 제공 하지 않습니다. 사용자에 게 multi-factor authentication을 요구 하려는 새 고객은 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다. 7 월 1 일 이전에 MFA 서버를 활성화 한 기존 고객은 최신 버전을 다운로드 하 고, 나중에 업데이트 하 고 활성화 자격 증명을 생성할 수 있습니다.
> [!NOTE]
> Azure Multi-Factor Authentication 서버와 관련 하 여 아래에서 공유 하는 정보는 이미 MFA 서버를 실행 하는 사용자 에게만 적용 됩니다.


**Q: Azure Multi-Factor Authentication 서버 사용자 데이터를 어떻게 처리 하나요?**

Multi-Factor Authentication 서버 사용자 데이터는 온-프레미스 서버에만 저장 됩니다. A felhőben nincsenek állandó felhasználói adatok. 사용자가 2 단계 인증을 수행 하는 경우 Multi-Factor Authentication 서버는 인증을 위해 Azure Multi-Factor Authentication 클라우드 서비스로 데이터를 보냅니다. Multi-Factor Authentication 서버와 Multi-Factor Authentication 클라우드 서비스 간의 통신에서는 443 아웃 바운드 포트를 통해 SSL (SSL(Secure Sockets Layer)) 또는 TLS (Transport Layer Security)를 사용 합니다.

인증 요청이 클라우드 서비스로 전송 되 면 인증 및 사용 보고서에 대 한 데이터가 수집 됩니다. 2 단계 인증 로그에 포함 된 데이터 필드는 다음과 같습니다.

* **고유 id** (사용자 이름 또는 온-프레미스 Multi-Factor Authentication 서버 id)
* **First 및 Last Name** (선택 사항)
* **전자 메일 주소** (선택 사항)
* **전화 번호** (음성 통화 또는 SMS 인증을 사용 하는 경우)
* **장치 토큰** (모바일 앱 인증을 사용 하는 경우)
* **인증 모드**
* **인증 결과**
* **Multi-Factor Authentication 서버 이름**
* **Multi-Factor Authentication 서버 IP**
* **클라이언트 IP** (사용할 수 있는 경우)

Multi-Factor Authentication 서버에서 선택적 필드를 구성할 수 있습니다.

확인 결과 (성공 또는 거부) 및 거부 된 이유는 인증 데이터와 함께 저장 됩니다. 이 데이터는 인증 및 사용 보고서에서 사용할 수 있습니다.

**Q: 사용자에 게 SMS 메시지를 전송 하는 데 사용 되는 SMS 짧은 코드는 무엇입니까?**

미국에서 Microsoft는 다음과 같은 SMS 짧은 코드를 사용 합니다.

   * 97671
   * 69829
   * 51789
   * 99399

캐나다에서 Microsoft는 다음과 같은 SMS 짧은 코드를 사용 합니다.

   * 759731 
   * 673801

Microsoft는 동일한 번호를 사용 하 여 일관 된 SMS 또는 음성 기반 Multi-Factor Authentication 프롬프트 배달을 보장 하지 않습니다. 사용자가 관심을 가질 때 Microsoft는 경로를 조정 하 여 SMS 배달 기능을 개선 하기 위해 언제 든 지 짧은 코드를 추가 하거나 제거할 수 있습니다. Microsoft는 미국 및 캐나다 외에도 국가/지역에 대 한 간단한 코드를 지원 하지 않습니다.

## <a name="billing"></a>Számlázás

[Multi-Factor Authentication 가격 책정 페이지](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) 또는 [Azure Multi-Factor Authentication를 가져오는 방법](concept-mfa-licensing.md)에 대 한 설명서를 참조 하 여 대부분의 청구 관련 질문에 대답할 수 있습니다.

**Q: 인증에 사용 되는 전화 통화 및 문자 메시지를 전송 하기 위해 조직에서 요금을 청구 하나요?**

아니요, Azure Multi-Factor Authentication를 통해 사용자에 게 전송 된 개별 전화 통화 또는 문자 메시지에 대 한 요금이 청구 되지 않습니다. 인증 당 MFA 공급자를 사용 하는 경우 각 인증에 대해 요금이 청구 되지만 사용 되는 방법에 대해서는 요금이 청구 되지 않습니다.

사용자는 자신의 개인 전화 서비스에 따라 수신 하는 전화 통화 또는 문자 메시지에 대해 요금이 청구 될 수 있습니다.

**Q: 사용자 당 청구 모델은 사용 하도록 설정 된 모든 사용자에 대해 또는 2 단계 인증을 수행 하는 사용자에 대해 요금을 청구 하나요?**

청구는 해당 월에 2 단계 인증을 수행 했는지 여부에 관계 없이 Multi-Factor Authentication를 사용 하도록 구성 된 사용자 수를 기준으로 합니다.

**Q: Multi-Factor Authentication 대금 청구는 어떻게 작동 하나요?**

사용자 단위 또는 인증 단위 MFA 공급자를 만들 때 조직의 Azure 구독은 사용량에 따라 매월 청구 됩니다. 이 청구 모델은 Azure에서 가상 컴퓨터 및 웹 사이트의 사용량에 대 한 요금 청구 방법과 비슷합니다.

Azure Multi-Factor Authentication에 대 한 구독을 구매 하는 경우 조직에서는 각 사용자에 대 한 연간 라이선스 요금은 지불 합니다. MFA 라이선스 및 Office 365, Azure AD Premium 또는 Enterprise Mobility + Security 번들은 이러한 방식으로 청구 됩니다. 

[Azure Multi-Factor Authentication을 가져오는 방법](concept-mfa-licensing.md)에서 옵션에 대해 자세히 알아보세요.

**Q: Azure Multi-Factor Authentication의 무료 버전이 있나요?**

일부 경우에는 예입니다.

Azure 관리자를 위한 Multi-Factor Authentication은 [Azure Portal](https://portal.azure.com) 및 [Microsoft 365 관리 센터](https://admin.microsoft.com)를 비롯 하 여 Microsoft 온라인 서비스에 대 한 액세스를 위해 무료로 azure MFA 기능의 하위 집합을 제공 합니다. 이 제품은 MFA 라이선스, 번들 또는 독립 실행형 사용량 기반 공급자를 통해 Azure MFA의 정식 버전을가지고 있지 않은 Azure Active Directory 인스턴스의 전역 관리자 에게만 적용 됩니다. 관리자가 무료 버전을 사용 하는 경우 Azure MFA의 정식 버전을 구입 하면 모든 전역 관리자가 유료 버전으로 자동으로 승격 됩니다.

Office 365 Multi-Factor Authentication 사용자는 Exchange Online 및 SharePoint Online을 비롯 하 여 Office 365 서비스에 액세스 하기 위한 무료로 Azure MFA 기능의 하위 집합을 제공 합니다. 이 제품은 해당 Azure Active Directory 인스턴스에 MFA 라이선스, 번들 또는 독립 실행형 사용량 기반 공급자를 통해 Azure MFA의 정식 버전이 없는 경우 Office 365 라이선스가 할당 된 사용자에 게 적용 됩니다.

**Q: 조직에서 사용자 당 및 인증 당 사용량 청구 모델을 언제 든 지 전환할 수 있나요?**

조직에서 사용량 기반 청구를 사용 하는 독립 실행형 서비스로 MFA를 구매 하는 경우 MFA 공급자를 만들 때 청구 모델을 선택 합니다. MFA 공급자를 만든 후에는 청구 모델을 변경할 수 없습니다. 

MFA 공급자가 Azure AD 테 넌 트에 연결 되어 *있지* 않거나 새 mfa 공급자를 다른 azure ad 테 넌 트에 연결 하는 경우 사용자 설정 및 구성 옵션이 전송 되지 않습니다. Emellett a meglévő Azure MFA-kiszolgálókat újra aktiválni kell az új MFA szolgáltatón keresztül létrehozott aktiváló hitelesítők adatokkal. Az MFA-kiszolgálók az új MFA szolgáltatóhoz történő kapcsolás céljából történő újbóli aktiválása nincs hatással a telefonhívásban vagy szöveges üzenetben történő hitelesítésekre, a mobilalkalmazás-értesítések viszont minden felhasználó számára megszűnnek működni a mobilalkalmazás újbóli aktiválásáig.

[Azure Multi-factor Auth 공급자 시작](concept-mfa-authprovider.md)에서 MFA 공급자에 대해 자세히 알아보세요.

**Q: 조직에서 언제 든 지 사용량 기반 요금 청구 및 구독 (라이선스 기반 모델) 간을 전환할 수 있나요?**

일부 경우에는 예입니다.

사용자의 디렉터리에 *사용자 단위* Azure Multi-Factor Authentication 공급자가 있는 경우 MFA 라이선스를 추가할 수 있습니다. 라이선스가 있는 사용자는 사용자 단위 사용량 기반 요금 청구에서 계산 되지 않습니다. 라이선스가 없는 사용자도 MFA 공급자를 통해 MFA에 대해 사용 하도록 설정할 수 있습니다. Multi-Factor Authentication 사용 하도록 구성 된 모든 사용자에 게 라이선스를 구매 하 고 할당 하는 경우 Azure Multi-Factor Authentication 공급자를 삭제할 수 있습니다. 미래의 라이선스 보다 더 많은 사용자가 있는 경우 항상 다른 사용자별 MFA 공급자를 만들 수 있습니다.

디렉터리에 *인증 단위* Azure Multi-Factor Authentication 공급자가 있는 경우 MFA 공급자가 구독에 연결 되어 있기만 하면 항상 각 인증에 대해 요금이 청구 됩니다. 사용자에 게 MFA 라이선스를 할당할 수 있지만, MFA 라이선스를 할당 받은 사용자 로부터 제공 되는지 여부에 관계 없이 2 단계 인증 요청 마다 요금이 청구 됩니다.

**Q: 내 조직에서 Azure Multi-Factor Authentication를 사용 하려면 id를 사용 하 고 동기화 해야 하나요?**

조직에서 사용량 기반 청구 모델을 사용 하는 경우 Azure Active Directory는 선택 사항 이지만 필수는 아닙니다. MFA 공급자가 Azure AD 테 넌 트에 연결 되지 않은 경우 Azure Multi-Factor Authentication 서버 온-프레미스만 배포할 수 있습니다.

라이선스를 구입 하 고 디렉터리의 사용자에 게 할당할 때 라이선스는 Azure AD 테 넌 트에 추가 되기 때문에 라이선스 모델에 Azure Active Directory 필요 합니다.

## <a name="manage-and-support-user-accounts"></a>사용자 계정 관리 및 지원

**Q: 휴대폰에서 응답이 수신 되지 않는 경우 사용자에 게 어떻게 해야 하나요?**

사용자가 5 분 내에 최대 5 번 시도 하 여 인증을 위해 전화 통화 또는 SMS를 받을 수 있도록 합니다. Microsoft는 호출 및 SMS 메시지를 제공 하기 위해 여러 공급자를 사용 합니다. 그래도 문제가 해결 되지 않으면 Microsoft에서 지원 사례를 열어 추가로 문제를 해결 하세요.

위의 단계가 작동 하지 않는 경우 모든 사용자가 두 가지 이상의 확인 방법을 구성 하는 것이 더 나을 수 있습니다. Tanácsolja nekik, hogy próbáljanak újra bejelentkeznie, ezúttal azonban válasszanak egy másik ellenőrzési módszert a bejelentkezési oldalon.

사용자가 [최종 사용자 문제 해결 가이드](../user-help/multi-factor-authentication-end-user-troubleshoot.md)를 가리키도록 할 수 있습니다.

**Q: 내 사용자 중 하나가 자신의 계정으로 이동할 수 없는 경우 어떻게 해야 하나요?**

사용자가 등록 프로세스를 다시 진행 하도록 하 여 사용자 계정을 다시 설정할 수 있습니다. [클라우드에서 Azure Multi-Factor Authentication를 사용 하 여 사용자 및 장치 설정 관리](howto-mfa-userdevicesettings.md)에 대해 자세히 알아보세요.

**Q: 내 사용자 중 하나가 앱 암호를 사용 하는 휴대폰을 분실 한 경우 어떻게 해야 하나요?**

무단 액세스를 방지 하려면 모든 사용자의 앱 암호를 삭제 합니다. 사용자가 대체 장치를 사용 하는 경우 암호를 다시 만들 수 있습니다. [클라우드에서 Azure Multi-Factor Authentication를 사용 하 여 사용자 및 장치 설정 관리](howto-mfa-userdevicesettings.md)에 대해 자세히 알아보세요.

**Q: 사용자가 비 브라우저 앱에 로그인 할 수 없는 경우 어떻게 되나요?**

조직에서 여전히 레거시 클라이언트를 사용 하 고 [앱 암호를 사용할 수](howto-mfa-mfasettings.md#app-passwords)있는 경우 사용자는 사용자 이름 및 암호를 사용 하 여 이러한 레거시 클라이언트에 로그인 할 수 없습니다. 대신 [앱 암호를 설정](../user-help/multi-factor-authentication-end-user-app-passwords.md)해야 합니다. 사용자는 로그인 정보를 지우고 (삭제), 앱을 다시 시작한 후 일반 암호 대신 사용자 이름 및 *앱 암호* 를 사용 하 여 로그인 해야 합니다.

조직에 레거시 클라이언트가 없는 경우 사용자에 게 앱 암호를 만들 수 있도록 허용 하지 않아야 합니다.

> [!NOTE]
> Office 2013 클라이언트에 대 한 최신 인증
>
> 앱 암호는 최신 인증을 지원 하지 않는 앱에만 필요 합니다. Office 2013 클라이언트는 최신 인증 프로토콜을 지원 하지만 구성 해야 합니다. 이제 최신 인증은 Office 2013의 3 월 2015 이상 업데이트를 실행 하는 모든 고객에 게 제공 됩니다. 자세한 내용은 블로그 게시물 [업데이트 된 Office 365 최신 인증](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/)을 참조 하세요.

**Q: 사용자가 문자 메시지를 수신 하지 않거나 확인 시간이 초과 되는 경우도 있습니다.**

서비스의 안정성에 영향을 줄 수 있는 제어할 수 없는 요인이 있으므로 SMS 메시지 배달이 보장 되지 않습니다. 이러한 요인에는 대상 국가/지역, 휴대 전화 통신 회사 및 신호 강도가 포함 됩니다.

사용자에 게 문자 메시지를 안정적으로 수신 하는 데 문제가 있는 경우 대신 모바일 앱 또는 전화 통화 방법을 사용 하도록 지시 합니다. 모바일 앱은 셀룰러 및 Wi-fi 연결 모두에 대 한 알림을 받을 수 있습니다. 또한 장치에 신호가 전혀 없는 경우에도 모바일 앱에서 확인 코드를 생성할 수 있습니다. Microsoft Authenticator 앱은 [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [IOS](https://go.microsoft.com/fwlink/?Linkid=825073)및 [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)에서 사용할 수 있습니다.

**Q: 시스템 시간이 초과 되기 전에 사용자가 문자 메시지에서 확인 코드를 입력 해야 하는 시간을 변경할 수 있나요?**

일부 경우에는 예입니다. 

Azure MFA 서버 v 7.0 이상이 설치 된 단방향 SMS의 경우 레지스트리 키를 설정 하 여 시간 제한 설정을 구성할 수 있습니다. MFA 클라우드 서비스에서 문자 메시지를 보내면 확인 코드 (또는 일회용 암호)가 MFA 서버에 반환 됩니다. MFA 서버는 기본적으로 300 초 동안 메모리에 코드를 저장 합니다. 300 초가 경과 하기 전에 사용자가 코드를 입력 하지 않으면 해당 인증이 거부 됩니다. 다음 단계를 사용 하 여 기본 시간 제한 설정을 변경 합니다.

1. HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.로 이동 합니다.
2. **Pfsvc_pendingSmsTimeoutSeconds** 라는 DWORD 레지스트리 키를 만들고 Azure MFA 서버에서 일회성 암호을 저장 하는 시간 (초)을 설정 합니다.

>[!TIP] 
>여러 MFA 서버를 사용 하는 경우 원래 인증 요청을 처리 한 서버 에서만 사용자에 게 전송 된 확인 코드를 알고 있습니다. 사용자가 코드를 입력 하면 유효성을 검사 하기 위한 인증 요청을 같은 서버로 보내야 합니다. 코드 유효성 검사를 다른 서버로 보내는 경우에는 인증이 거부 됩니다. 

사용자가 정의 된 시간 제한 기간 내에 SMS에 응답 하지 않으면 해당 인증이 거부 됩니다. 

클라우드에서 Azure MFA를 사용 하는 단방향 SMS의 경우 (AD FS 어댑터 또는 네트워크 정책 서버 확장 포함) 시간 제한 설정을 구성할 수 없습니다. Azure AD는 180 초 동안 확인 코드를 저장 합니다. 

**Q: Azure Multi-Factor Authentication 서버에서 하드웨어 토큰을 사용할 수 있나요?**

Azure Multi-Factor Authentication 서버을 사용 하는 경우 타사 OATH (Open Authentication) 시간 기반, TOTP (일회용 암호) 토큰을 가져온 다음 2 단계 인증에 사용할 수 있습니다.

비밀 키를 CSV 파일에 넣고 Azure Multi-Factor Authentication 서버로 가져오면 OATH TOTP 토큰 인 ActiveIdentity 토큰을 사용할 수 있습니다. 클라이언트 시스템에서 사용자 입력을 허용할 수 있는 한, ADFS (Active Directory Federation Services), IIS (인터넷 정보 서버) 폼 기반 인증 및 RADIUS(Remote Authentication Dial-In User Service) (RADIUS)에서 OATH 토큰을 사용할 수 있습니다.

다음 형식으로 타사 OATH TOTP 토큰을 가져올 수 있습니다.  

- PC (이식 가능한 대칭 키 컨테이너)  
- CSV는 파일에 일련 번호, 기본 32 형식의 비밀 키, 시간 간격을 포함 하는 경우  

**Q: Azure Multi-Factor Authentication 서버를 사용 하 여 터미널 서비스를 보호할 수 있나요?**

예. 그러나 Windows Server 2012 R2 이상을 사용 하는 경우 원격 데스크톱 게이트웨이 (RD 게이트웨이)를 사용 하 여 터미널 서비스만 보호할 수 있습니다.

Windows Server 2012 r 2의 보안 변경 내용은 Azure Multi-Factor Authentication 서버 Windows Server 2012 이전 버전의 LSA (로컬 보안 기관) 보안 패키지에 연결 하는 방법을 변경 했습니다. Windows Server 2012 이전 버전의 터미널 서비스 버전의 경우 [Windows 인증을 사용 하 여 응용 프로그램을 보호할](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)수 있습니다. Windows Server 2012 r 2를 사용 하는 경우 RD 게이트웨이 필요 합니다.

**Q: MFA 서버에서 호출자 ID를 구성 했지만 사용자가 익명 호출자의 Multi-Factor Authentication 호출을 계속 받습니다.**

Multi-Factor Authentication 호출이 공용 전화 네트워크를 통해 전달 되는 경우에는 호출자 ID를 지원 하지 않는 통신 회사를 통해 라우팅되는 경우도 있습니다. 이로 인해 Multi-Factor Authentication 시스템에서 항상 전송 하더라도 호출자 ID는 보장 되지 않습니다.

**Q: 사용자에 게 보안 정보를 등록 하 라는 메시지가 표시 되는 이유는 무엇 인가요?**
사용자에 게 보안 정보를 등록 하 라는 메시지가 표시 되는 이유는 여러 가지가 있습니다.

- 사용자가 Azure AD의 관리자가 MFA를 사용 하도록 설정 했지만 계정에 대 한 보안 정보가 아직 등록 되지 않았습니다.
- 사용자가 Azure AD에서 셀프 서비스 암호 재설정을 사용 하도록 설정 되었습니다. 보안 정보를 잊어버린 경우 나중에 암호를 재설정 하는 데 도움이 됩니다.
- 사용자가 MFA를 요구 하는 조건부 액세스 정책이 있고 이전에 MFA에 등록 되지 않은 응용 프로그램에 액세스 했습니다.
- 사용자가 Azure AD에 장치를 등록 하 고 있습니다 (Azure AD 조인 포함). 조직에서 장치 등록을 위해 MFA를 요구 하지만, 사용자가 이전에 MFA에 등록 하지 않았습니다.
- 사용자가 Windows 10에서 비즈니스용 Windows Hello (MFA 필요)를 생성 하 고 MFA에 대해 이전에 등록 하지 않았습니다.
- 조직에서 사용자에 게 적용 된 MFA 등록 정책을 만들고 사용 하도록 설정 했습니다.
- 사용자가 이전에 MFA에 등록 했지만 관리자가 사용 하지 않도록 설정한 확인 방법을 선택 했습니다. 따라서 사용자는 MFA 등록을 다시 진행 하 여 새 기본 확인 방법을 선택 해야 합니다.

## <a name="errors"></a>Hibák

**Q: 모바일 앱 알림을 사용 하는 경우 "활성화 된 계정에 대 한 인증 요청이 아닙니다." 오류 메시지가 표시 되 면 사용자는 어떻게 해야 하나요?**

이 절차에 따라 모바일 앱에서 해당 계정을 제거한 다음 다시 추가 합니다.

1. [Azure Portal 프로필로](https://account.activedirectory.windowsazure.com/profile/) 이동 하 여 조직 계정으로 로그인 합니다.
2. **추가 보안 확인**을 선택 합니다.
3. 모바일 앱에서 기존 계정을 제거 합니다.
4. **구성**을 클릭 하 고 지침에 따라 모바일 앱을 다시 구성 합니다.

**Q: 브라우저가 아닌 응용 프로그램에 로그인 할 때 0x800434D4L 오류 메시지가 표시 되 면 사용자는 어떻게 해야 하나요?**

0x800434D4L 오류는 2 단계 인증이 필요한 계정에서 작동 하지 않는 로컬 컴퓨터에 설치 된 비 브라우저 응용 프로그램에 로그인 하려고 할 때 발생 합니다.

이 오류에 대 한 해결 방법은 관리자와 관련 없는 작업에 대해 별도의 사용자 계정을 사용 하는 것입니다. 나중에 관리자 계정이 아닌 계정 간에 사서함을 연결 하 여 관리자가 아닌 계정을 사용 하 여 Outlook에 로그인 할 수 있습니다. 이 솔루션에 대 한 자세한 내용은 관리자에 게 [사용자 사서함의 내용을 열고 보는 기능을 제공](https://help.outlook.com/141/gg709759.aspx?sl=1)하는 방법을 알아봅니다.

## <a name="next-steps"></a>Következő lépések

여기에서 질문이 답변 되지 않은 경우 페이지의 맨 아래에 있는 주석에 남겨 주세요. 또는 도움말을 얻기 위한 몇 가지 추가 옵션은 다음과 같습니다.

* 일반적인 기술 문제에 대 한 해결 방법에 대 한 [Microsoft 지원 기술 자료](https://support.microsoft.com) 를 검색 하십시오.
* 커뮤니티에서 기술 질문 및 답변을 검색 하 여 찾아보거나 [Azure Active Directory 포럼](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)에서 질문 하세요.
* 레거시 PhoneFactor 고객이 질문 하거나 암호를 다시 설정 하는 데 도움이 필요한 경우 [암호 재설정](mailto:phonefactorsupport@microsoft.com) 링크를 사용 하 여 지원 사례를 여세요.
* [Azure Multi-Factor Authentication 서버 (PhoneFactor) 지원을](https://support.microsoft.com/oas/default.aspx?prid=14947)통해 지원 전문가에 게 문의 하세요. 연락할 때 문제에 대 한 정보를 가능한 한 많이 포함할 수 있는 경우 유용 합니다. 사용자가 제공할 수 있는 정보에는 오류를 본 페이지, 특정 오류 코드, 특정 세션 ID 및 오류를 발견 한 사용자의 ID가 포함 됩니다.
