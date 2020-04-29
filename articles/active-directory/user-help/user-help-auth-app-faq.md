---
title: Microsoft Authenticator 앱에 대 한 질문 & 답변-Azure AD
description: Microsoft 인증 앱 및 2 단계 인증에 대 한 Faq (질문과 대답)입니다.
services: active-directory
author: curtand
manager: daveba
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: c56db99b056d4b1e9b7dd75bc2f21bf582ee2ab7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756845"
---
# <a name="frequently-asked-questions-faqs-about-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱에 대 한 Faq (질문과 대답)

이 문서에서는 Microsoft Authenticator 앱에 대한 일반적인 질문에 대답합니다. 질문에 대한 답변을 찾을 수 없는 경우 [Microsoft Authenticator 앱 포럼](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)으로 이동하세요.

Microsoft Authenticator 앱은 Azure Authenticator 앱을 대신하며, Azure Multi-factor Authentication을 사용하는 경우 권장됩니다. [Android](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator) 및 [iOS](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458) 디바이스의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다.

## <a name="frequently-asked-questions"></a>자주 묻는 질문

| 질문 | 솔루션 |
| -------- | -------- |
| Android Microsoft Authenticator에서 OTP 코드의 스크린샷을 사용할 수 있나요? | Microsoft Authenticator Android 릴리스 6.2003.1704의 경우 기본적으로 인증자의 스크린샷를 사용 하는 경우 모든 OTP 코드는 사용자의 보호를 강화 하기 위해 숨겨집니다. 사용자가 스크린샷에서 OTP 코드를 보거나 다른 앱이 인증자의 화면을 캡처하도록 허용할 경우 인증자 앱에서 "화면 캡처" 설정을 사용 하도록 설정한 다음 앱을 다시 시작 하 여 수행할 수 있습니다. |
| Authenticator는 내 대신 어떤 데이터를 저장하며, 이러한 데이터는 어떻게 삭제할 수 있나요? | Microsoft Authenticator 앱은 세 가지 유형의 정보를 수집합니다.<ul><li>사용자 계정을 추가할 때 제공하는 계정 정보 계정을 제거하여 이 데이터를 제거할 수 있습니다.</li><li>Microsoft에 로그를 보낼 수 있는 앱의 **도움말** 메뉴를 **로그 보내기** 를 선택 하기 전 까지는 앱에만 남아 있는 진단 로그 데이터입니다. 이러한 로그 파일에는 전자 메일 주소 (예:, alain@contoso.com), 서버 또는 IP 주소, 장치 데이터 (예: 장치 이름 및 운영 체제 버전)와 같은 개인 데이터가 포함 되며, 개인 데이터는 앱 문제를 해결 하는 데 필요한 정보로 제한 됩니다. 수집된 정보를 확인하려면 언제든지 앱에서 이러한 로그 파일을 볼 수 있습니다. 로그 파일을 보내는 경우 인증 앱 엔지니어는 이를 사용하여 고객이 보고한 문제를 해결할 수 있습니다.</li><li>개인적으로 식별이 가능한 사용 현황 데이터 (예: "계정 흐름 추가/계정 추가 성공" 또는 "승인 승인 됨") 이 데이터는 당사의 엔지니어링 의사 결정의 핵심이며 사용자에게 중요한 기능 및 앱에 대한 업데이트의 양식에서 만들어야 하는 개선 사항의 위치를 확인할 수 있도록 도와줍니다. 앱 사용자는 앱을 처음 시작할 때이 데이터 수집에 대 한 알림을 확인 하 고 앱의 **설정** 페이지에서 해제할 수 있다는 알림을 받게 됩니다. 언제든지 이 설정을 활성화 또는 비활성화할 수 있습니다.</li></ul> |
| 앱에서 코드란 무엇인가요? | Microsoft Authenticator 앱을 열면 추가 된 계정이 타일로 표시 됩니다.<li>IOS 장치에서 회사 또는 학교 계정 및 개인 Microsoft 계정에는 계정의 전체 화면 보기에 6 자리 또는 8 자리 숫자가 표시 됩니다 (계정 타일을 눌러 액세스).<br><br>![앱의 계정 화면](./media/user-help-auth-app-faq/auth-app-accounts.png)<li>IOS 장치 및 Android 장치에 있는 모든 계정의 다른 계정에 대해 앱의 계정 페이지에 6 자리 또는 8 자리 숫자가 표시 됩니다. 이러한 코드는 사용자 신원을 확인하는 용도로 사용됩니다. 사용자 이름 및 암호를 사용 하 여 로그인 한 후에는 해당 계정과 연결 된 확인 코드를 입력 합니다. 예를 들어 Katy Contoso 계정에 로그인 하 고 iOS 장치를 사용 하는 경우 계정 타일을 탭 한 다음 확인 코드를 사용 하 여 id를 확인 합니다. Outlook 계정에 로그인 하는 것을 Katy 하는 경우 동일한 단계를 수행 합니다.<br><br>![앱에서 계정 타일을 탭핑 한 후](./media/user-help-auth-app-faq/katy-signin.png)<br><br>Contoso 계정 타일을 탭 하면 Katy는 전체 화면 보기에서 확인 코드를 확인 하 고 895823를 입력 하 여 로그인을 완료 합니다.<br><br>![앱의 확인 코드 화면](./media/user-help-auth-app-faq/verification-code.png) |
| 코드 옆에 있는 숫자가 계속 카운트다운되는 이유는 무엇인가요? | 활성 확인 코드 옆에 30초 타이머가 카운트다운되는 것을 볼 수 있습니다. 이 타이머는 같은 코드를 두 번 사용하여 로그인하는 일이 없도록 방지하기 위한 것입니다. 암호와는 달리, 이 숫자를 기억할 필요가 없습니다. 휴대폰에 액세스할 수 있는 사람만이 확인 코드를 알 수 있다는 점에 착안하였습니다. |
| 계정 타일이 회색으로 표시되는 이유는 무엇인가요? | 일부 조직에서는 Microsoft Authenticator 앱을 Single Sign-On과 함께 사용하여 조직의 리소스를 보호할 것을 요구합니다. 이 경우 계정이 2단계 인증에 사용되지 않고 회색 또는 비활성 상태로 표시됩니다. 이러한 종류의 계정을 일반적으로 "브로커" 계정이라고 부릅니다. |
| 디바이스 등록이란? | 조직에서 장치를 등록 하 여 장치에서 파일 및 앱과 같은 보안 리소스에 액세스할 수 있는지 파악할 수 있습니다. 또한 조건부 액세스를 설정 하 여 원치 않는 리소스 액세스의 위험을 줄일 수 있습니다. **설정**에서 장치의 등록을 취소할 수 있지만 Outlook의 메일, OneDrive의 파일에 대 한 액세스 권한이 손실 될 수 있으며 휴대폰 로그인을 사용할 수 없습니다. |
| 확인 코드를 가져와서 사용하려면 인터넷 또는 네트워크에 연결되어야 합니까? | 인터넷 또는 데이터에 연결되지 않아도 코드를 사용할 수 있으므로 전화 서비스에 로그인할 필요가 없습니다. 또한 앱을 닫는 즉시 앱 실행이 중지되므로 배터리가 소모되지 않습니다. |
| 앱이 열려 있을 때만 알림을 받습니다. 앱이 닫히면 알림이 오지 않습니다. | 벨 소리가 켜져 있어도 알림이 도착할 때 경고가 울리지 않으면 앱 설정을 확인하세요. 알림이 도착할 때 소리를 내거나 진동이 울리도록 앱을 설정해야 합니다. 알림이 전혀 수신되지 않으면 다음 사항을 확인합니다.<ul><li>휴대폰이 방해 금지 또는 자동 모드인가요? 이 모드에서는 앱이 알림을 보내지 않을 수 있습니다.</li><li>다른 앱에서 알림을 받을 수 있나요? 받을 수 없으면 휴대폰의 네트워크 연결 문제이거나 Android 또는 Apple의 알림 채널에 문제가 있는 것일 수 있습니다. 휴대폰 설정을 통해 네트워크 연결을 확인할 수 있지만, Android 또는 Apple 알림 채널 문제를 해결하려면 서비스 공급자에게 문의해야 할 수도 있습니다.</li><li>앱에서 일부 계정의 알림을 받을 수 있고 나머지 계정의 알림을 받을 수 없나요? 그렇다면 앱에서 문제가 있는 계정을 제거했다가 다시 추가하고 알림을 허용한 다음, 문제가 해결되었는지 확인하세요.</li></ul>이러한 단계를 모두 시도한 후에도 문제가 지속되면 로그 파일을 보내주세요. 앱을 열고, **도움말**로 이동한 다음, **로그 보내기**를 선택합니다. 그런 다음, [Microsoft Authenticator 앱 포럼](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)으로 이동하여 어떤 문제가 표시되고 있는지, 지금까지 시도한 단계에 대해 알려주세요. |
| 앱에서 확인 코드를 사용하고 있는데, 푸시 알림으로 전환하려면 어떻게 해야 하나요? | 회사/학교 계정(관리자가 설정한 경우) 또는 개인 Microsoft 계정에서 푸시 알림을 설정할 수 있지만, Google 또는 Facebook 같은 타사 계정에는 알림이 작동하지 않습니다.<br><br>알림을 통해 개인 계정을 전환하려면 계정에 디바이스를 다시 등록해야 합니다. 이렇게 하려면 **계정 추가**로 이동하여 **개인 Microsoft 계정**을 선택한 다음, 사용자 이름과 암호를 사용하여 로그인합니다.<br><br>회사 또는 학교 계정에 원 클릭 알림을 허용할 것인지 여부는 조직에서 결정하며, 조직에서 이 기능을 끌 수 있습니다.|
|Microsoft 이외의 계정에서도 알림이 작동하나요? | 아니요. 알림은 Microsoft 계정 및 Azure Active Directory 계정에서만 작동합니다. 회사 또는 학교에서 Azure AD 계정을 사용하는 경우 회사 또는 학교에서 이 기능을 끌 수 있습니다. |
| 새 디바이스가 생겼습니다/백업으로 디바이스를 복원했습니다. Microsoft Authenticator 앱에서 내 계정을 다시 설정하려면 어떻게 해야 하나요? | IOS 또는 Android 장치를 실행 하는 경우 이전 장치에서 **클라우드 백업을** 켠 경우 이전 백업을 사용 하 여 새 장치에서 계정 자격 증명을 복구할 수 있습니다. 자세한 내용은 [Microsoft Authenticator 앱을 사용하여 계정 자격 증명 백업 및 복구](user-help-auth-app-backup-recovery.md) 문서를 참조하세요. |
| 디바이스를 잃어버렸습니다/새 디바이스로 이동했습니다. 알림이 계속해서 기존 디바이스로 이동하지 않게 하려면 어떻게 해야 하나요? | 새 디바이스에 Microsoft Authenticator 앱을 추가해도 기존 디바이스에서 앱이 자동으로 제거되지는 않습니다. 기존 디바이스에서 앱을 삭제하는 것만으로는 충분하지 않습니다. 기존 디바이스에서 앱을 삭제하고, Microsoft 또는 소속 조직에 기존 디바이스를 제거하고 계정에서 등록을 취소해 달라고 요청해야 합니다.<ul><li>**개인 Microsoft 계정을 사용하여 디바이스에서 앱을 제거하려면:** [계정 보안](https://account.microsoft.com/security) 페이지의 2 단계 인증 영역으로 이동 하 여 이전 장치에 대 한 확인을 해제 하도록 선택 합니다.</li><li>**회사 또는 학교 Microsoft 계정을 사용하여 디바이스에서 앱을 제거하려면:** [Myapps](https://myapps.microsoft.com/) 페이지의 2 단계 인증 영역 또는 조직의 사용자 지정 포털로 이동 하 여 이전 장치에 대 한 확인을 해제 하도록 선택 합니다.</li></ul> |
| 앱에서 계정을 제거하려면 어떻게 해야 하나요? | <ul><li>**io.** 계정에 대 한 전체 화면 보기를 로드 하려면 앱에서 제거 하려는 계정에 대 한 계정 타일을 탭 합니다. **계정 제거** 옵션을 탭 하 여 앱에서 계정을 제거 합니다.</li><li>**용.** 메인 화면에서 메뉴 단추를 선택하고 **계정 편집**을 선택합니다. 계정 이름 옆에 있는 **X**를 누릅니다.</li></ul>조직에 등록된 디바이스가 있는 경우 계정을 제거하도록 추가 단계를 완료해야 합니다. 이러한 디바이스에서 Microsoft Authenticator 앱은 디바이스 관리자는 자동으로 등록됩니다. 앱을 완전히 제거하려면 먼저 앱 설정에서 앱 등록을 취소해야 합니다. |
| 앱이 너무 많은 권한을 요청하는 이유는 무엇인가요? | 요청 될 수 있는 사용 권한의 전체 목록 및 앱에서 사용 하는 방법은 다음과 같습니다. 표시 되는 특정 사용 권한은 보유 한 휴대폰 유형에 따라 달라 집니다.<ul><li>**생체 인식 하드웨어를 사용 합니다.** 일부 회사 및 학교 계정에는 ID를 인증할 때마다 추가 PIN이 필요합니다. 앱을 사용 하려면 PIN을 입력 하는 대신 생체 인식 또는 얼굴 인식을 사용 하는 데 동의 해야 합니다.</li><li>**카메라.** 회사 계정, 학교 계정 또는 Microsoft 이외의 계정을 추가할 때 QR 코드를 검사하는 데 사용됩니다.</li><li>**연락처 및 휴대폰.** 앱은 휴대폰에서 기존 회사 또는 학교 Microsoft 계정을 검색 하 고 앱에 추가 하 여 계정이 제대로 작동 하는지 확인 하는 데 도움이 되도록이 권한이 필요 합니다.</li><li>**SMS.** 전화 번호와 레코드의 숫자와 일치하는지 확인하는 데 사용됩니다. 사용자가 개인 Microsoft 계정을 사용하여 처음으로 로그인하면 앱을 다운로드한 휴대폰으로 6-8자리 확인 코드가 포함된 문자 메시지가 전송됩니다. 사용자에게 이 코드를 찾아 앱에 입력하라고 요청하는 대신, 문자 메시지에서 자동으로 코드가 검색됩니다.</li><li>**다른 앱을 통해 가져오기.** 사용자의 ID를 확인하는 알림은 실행 중일 수 있는 다른 앱에도 표시됩니다.</li><li>**인터넷의 데이터 수신.** 알림을 보내기 위해 이 사용 권한이 필요합니다.</li><li>**휴대폰 절전 모드 전환 방지.** 디바이스를 조직에 등록하면 조직에서는 휴대폰에서 이 정책을 변경할 수 있습니다.</li><li>**진동 제어.** ID를 인증하는 알림을 수신할 때마다 진동을 울릴지 여부를 선택할 수 있습니다.</li><li>**지문 하드웨어 사용.** 일부 회사 및 학교 계정에는 ID를 인증할 때마다 추가 PIN이 필요합니다. 프로세스를 보다 쉽게 수행하려면 PIN을 입력하는 대신 지문을 사용할 수 있습니다.</li><li> **네트워크 연결 보기.** Microsoft 계정을 추가할 때 앱에는 네트워크/인터넷 연결이 필요합니다.</li><li>**스토리지의 내용 읽기.** 앱 설정을 통해 기술 문제를 보고하는 경우에만 이 사용 권한을 사용하게 됩니다. 스토리지의 일부 정보를 수집하여 문제를 진단합니다.</li><li>**전체 네트워크 액세스.** ID를 인증하는 알림을 보내는 데 이 사용 권한이 필요합니다.</li><li>**시작 시 실행.** 전화를 다시 시작하는 경우 이 사용 권한을 사용하면 ID를 인증하는 알림을 계속 수신할 수 있습니다.</li></ul> |
| Microsoft Authenticator 앱에서 사용자가 디바이스를 잠금 해제하지 않아도 요청을 승인할 수 있도록 허용하는 이유는 무엇인가요? | 사용자가 휴대폰을 갖고 있는지만 증명하면 되므로 확인 요청을 승인하도록 디바이스의 잠금을 해제할 필요가 없습니다. 2단계 인증은 두 가지를 요구합니다. 하나는 사용자가 알고 있는 것이고, 다른 하나는 사용자가 갖고 있는 것입니다. 사용자가 알고 있는 것은 암호입니다. 사용자가 갖고 있는 것은 휴대폰 (Microsoft Authenticator 앱으로 설정 하 고 MFA 증명으로 등록)입니다. 따라서 전화를 하 고 요청을 승인 하면 두 번째 인증 단계에 대 한 조건을 충족 합니다. |
| 내 Apple Watch에서 Microsoft Authenticator 앱을 열 때 모든 계정이 표시 되는 이유는 무엇 인가요? | Microsoft Authenticator 앱은 Apple Watch 도우미 앱에서 Microsoft 개인 계정 또는 회사/학교 계정을 사용하는 것만 지원합니다. Google 또는 Facebook과 같은 다른 계정에 대해 휴대폰에서 인증 앱을 열어 확인 코드를 확인 해야 합니다. |
| 내 Apple Watch에 대 한 알림을 승인 하거나 거부할 수 없는 이유는 무엇입니까? | 먼저 iPhone에서 Microsoft Authenticator 앱, 버전 6.0.0 이상으로 업그레이드 했는지 확인 합니다. 그 후 Apple Watch에서 Microsoft Authenticator 도우미 앱을 열고 아래에 **설정** 단추가 있는 계정을 찾습니다. 이러한 계정에 대한 알림을 승인하려면 이 설정 프로세스를 완료해야 합니다. |
| Apple Watch와 내 폰 사이에 통신 오류가 발생 했습니다. 어떻게 하면 문제를 해결할 수 있나요? | 이 오류는 Watch가 휴대폰과 통신을 마치기 전에 해당 화면이 절전 모드로 전환되는 경우 발생합니다.<br><br><b>설치 중에 이런 문제가 발생하면:</b><br>설치를 다시 실행하고 프로세스가 완료될 때가지 Watch가 활성화 상태를 유지하도록 합니다. 동시에, 휴대폰에서 앱을 열고 표시되는 모든 메시지에 응답합니다.<br><br>전화와 감시가 여전히 통신 하지 않는 경우 다음을 시도할 수 있습니다.<ol><li>Microsoft Authenticator 휴대폰 앱을 강제로 종료하여 iPhone에서 다시 엽니다.</li><li>Apple Watch 도우미 앱을 강제로 종료합니다.<ol><li> Watch에서 Microsoft Authenticator 도우미 앱을 엽니다.</li><li>**종료** 화면이 나타날 때까지 옆면의 단추를 길게 누릅니다.</li><li>옆면의 단추를 놓고 Digital Crown을 길게 눌러 활성 앱을 강제로 종료합니다.</li></ol></li><li>휴대폰과 Watch에서 Bluetooth와 Wi-Fi를 모두 껐다가 다시 켭니다.</li><li>iPhone 및 Watch를 다시 시작합니다.</li></ol><b>알림을 승인 하려고 할 때이 오류가 발생 하는 경우:</b><br>다음 번에 Apple Watch에서 알림을 승인하려고 할 때, 요청이 완료되고 성공적으로 완료되었음을 알리는 소리가 들릴 때까지 화면을 활성화 상태로 유지합니다. |
| Microsoft Authenticator 자매 앱이 Apple Watch 동기화 하거나 내 Watch에 표시 되지 않는 이유는 무엇 인가요? | 앱이 시청에 표시 되지 않는 경우 다음을 시도 합니다. <ol><li>Watch가 watchOS 4.0 이상을 실행 중인지 확인합니다.</li><li>Watch를 다시 동기화합니다.</li></ol> |
| Apple Watch 도우미 앱이 충돌했습니다. 크래시 로그를 보내서 조사할 수 있나요? | 먼저 분석을 microsoft와 공유 하도록 선택 했는지 확인 해야 합니다. TestFlight 사용자는 이미 등록 되어 있습니다. 아닌 경우 **설정 > 개인 정보 > 분석**으로 이동하여 **iPhone 및 Watch 분석 공유** 및 **앱 개발자와 공유** 옵션을 모두 선택합니다.<br><br>가입한 후 크래시를 재현하면 자동으로 크래시 로그가 Microsoft로 전송됩니다. 그러나 충돌을 재현할 수 없는 경우 로그 파일을 수동으로 복사 하 여 microsoft에 보낼 수 있습니다.<ol><li>휴대폰에서 Watch 앱을 열고 **설정 > 일반**으로 이동한 다음, **Watch 분석 복사**를 클릭합니다.</li><li>**설정 > 개인 정보 > 분석 > 분석 데이터** 아래에서 해당 크래시를 찾은 다음, 전체 텍스트를 수동으로 복사합니다.</li><li>휴대폰에서 Microsoft Authenticator 앱을 열고, 앞에서 복사한 텍스트를 **로그 보내기** 페이지의 **앱 개발자와 공유** 텍스트 상자에 붙여넣습니다.</li></ol> |
| 앱 잠금 기능이란 무엇이며 보안을 강화하는 데 어떤 도움이 되나요? | 일회용 암호, 앱 정보 및 앱 설정을 더욱 안전하게 유지하려는 경우 Microsoft Authenticator 앱에서 앱 잠금 기능을 설정하면 됩니다. Microsoft Authenticator 앱의 설정 화면에서 앱 잠금 **설정** 이란 Microsoft Authenticator 앱을 열 때마다 PIN 또는 생체 인식 기능을 사용 하 여 인증 하 라는 메시지가 표시 됨을 의미 합니다. 이 기능은 추가 보호 기능을 제공 하며 Microsoft Authenticator 앱에서 알림을 승인 하는 방법은 변경 되지 않습니다.<br><br>**참고**<br>장치 등록은 회사 포털 앱 또는 Android 계정 설정에서와 같이 Microsoft Authenticator 앱 외부의 다른 위치에서 발생할 수 있기 때문에 앱 잠금으로 인해 사용자가 Microsoft Authenticator 앱에 액세스 하지 못하도록 할 수 있는 것은 아닙니다. |
| 내 계정 작업에 대한 알림이 수신되는 이유는 무엇인가요? | 개인 Microsoft 계정에서 발생하는 일을 정확하게 파악할 수 있도록 Microsoft Authenticator 앱으로 작업 알림을 보내드리고 있습니다. 무언가가 변경되는 즉시 이러한 알림이 표시되므로 보안 강화에 도움이 됩니다. 이전에는 이메일 및 SMS를 통해 이러한 알림을 전송했으며, 이제는 앱을 포함하도록 확장되었습니다. 이러한 작업 알림에 대한 자세한 내용은 [비정상적인 계정 로그인이 있는 경우 어떻게 되나요?](https://support.microsoft.com/help/13967/microsoft-account-unusual-sign-in)를 참조하세요. 알림을 받는 위치를 변경 하려면 계정에서 [중요 하지 않은 계정 경고로 연락할 수 있는 위치](https://account.live.com/SecurityNotifications/Update) 에 로그인 합니다. |
| IOS와 함께 제공 되는 기본 메일 앱을 사용 하 여 회사 또는 학교 계정에 로그인 하는 동안 Microsoft Authenticator 앱에서 보안 확인 정보를 요청 하는 메시지가 표시 됩니다. 해당 정보를 입력 하 고 메일 앱으로 돌아가면 오류가 발생 합니다. 어떻게 해야 하나요? | 이는 로그인과 메일 앱이 서로 다른 두 앱에서 발생 하 여 초기 백그라운드 로그인 프로세스의 작동이 중지 되 고 실패 하는 경우에 발생할 수 있습니다. 이 문제를 해결 하려면 메일 앱에 로그인 하는 동안 화면 오른쪽 아래에 있는 **Safari** 아이콘을 선택 하는 것이 좋습니다. Safari로 이동 하면 전체 로그인 프로세스가 단일 앱에서 발생 하므로 앱에 성공적으로 로그인 할 수 있습니다. |
| OTP (일회용 암호) 코드가 작동 하지 않습니다.   어떻게 해야 합니까? | 장치의 날짜와 시간이 올바르고 자동으로 동기화 되 고 있는지 확인 합니다. 날짜 및 시간이 잘못 되었거나 동기화 되지 않으면 코드가 작동 하지 않습니다. |
| Windows 10 Mobile 운영 체제는 12 월 2019 일에 사용 되지 않습니다. Windows Mobile 운영 체제의 Microsoft Authenticator는 더 이상 사용 되지 않습니다. | 2020 년 2 월 28 일 이후 모든 Windows Mobile 운영 체제의 Microsoft Authenticator 앱은 지원 되지 않습니다. 사용자는 앞서 언급 한 앱에 대 한 새 업데이트를 받을 자격이 없습니다. 2020 년 2 월 28 일 이후 모든 Windows Mobile 운영 체제에서 현재 Microsoft Authenticator를 사용 하 여 인증을 지 원하는 Microsoft 서비스는 지원 사용을 중지 하기 시작 합니다. Microsoft 서비스에 인증 하려면 모든 사용자가이 날짜 이전에 대체 인증 메커니즘으로 전환 하도록 권장 합니다. |

## <a name="next-steps"></a>다음 단계

- 개인 Microsoft 계정에 대 한 확인 코드를 가져오는 데 문제가 있는 경우 [Microsoft 계정 보안 정보 & 확인 코드](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) 문서에서 **확인 코드 문제 해결** 섹션을 참조 하세요.

- 2단계 인증에 대해 더 자세히 알아보려면 [2단계 인증에 내 계정 설정](multi-factor-authentication-end-user-first-time.md)을 참조하세요.

- 보안 정보에 대 한 자세한 내용은 [보안 정보 (미리 보기) 개요](user-help-security-info-overview.md) 를 참조 하세요.

- 궁금한 내용에 대한 답변이 여기에 없으면 직접 질문을 남겨주세요. [Microsoft Authenticator 앱 포럼](https://social.technet.microsoft.com/Forums/en-us/home?forum=MicrosoftAuthenticatorApp)으로 이동하여 질문을 게시하고, 커뮤니티의 도움을 받고, 이 페이지에 의견을 남기세요.
