---
title: Microsoft Authenticator 앱 도움말 - Azure Active Directory | Microsoft Docs
description: Microsoft Authentication 앱 및 Azure Multi-Factor Authentication과 관련된 질문과 대답 목록을 제공합니다.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99c8c074365707fed0a6d079bdfdbd138a91255d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730427"
---
# <a name="microsoft-authenticator-app-faq"></a>Microsoft Authenticator 앱 FAQ

이 문서에서는 Microsoft Authenticator 앱에 대한 일반적인 질문에 대답합니다. 질문에 대한 답변을 찾을 수 없는 경우 [Microsoft Authenticator 앱 포럼](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)으로 이동하세요.

Microsoft Authenticator 앱은 Azure Authenticator 앱을 대신하며, Azure Multi-factor Authentication을 사용하는 경우 권장됩니다. [Android](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator) 및 [iOS](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458) 디바이스의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

|질문|해결 방법|
|--------|--------|
|Authenticator는 내 대신 어떤 데이터를 저장하며, 이러한 데이터는 어떻게 삭제할 수 있나요?|Microsoft Authenticator 앱은 세 가지 유형의 정보를 수집합니다.<ul><li>사용자 계정을 추가할 때 제공하는 계정 정보 계정을 제거하여 이 데이터를 제거할 수 있습니다.</li><li>앱의 **도움말** 메뉴를 통해 Microsoft에 **로그를 보내도록** 선택할 때까지 앱에만 상주하는 진단 로그 데이터 이러한 로그 파일은 앱 문제를 해결하는 데 필요한 정보로 제한된 개인 데이터가 있는 이메일 주소(예: alain@contoso.com)와 같은 개인 데이터, 서버/IP 주소 및 디바이스 데이터(예: 디바이스 이름 및 운영 체제 버전)를 포함합니다. 수집된 정보를 확인하려면 언제든지 앱에서 이러한 로그 파일을 볼 수 있습니다. 로그 파일을 보내는 경우 인증 앱 엔지니어는 이를 사용하여 고객이 보고한 문제를 해결할 수 있습니다.</li><li>"계정 흐름 추가 시작됨/성공적으로 계정을 추가함" 또는 "알림 승인됨"과 같은 비-개인 식별이 가능한 사용량 데이터 이 데이터는 당사의 엔지니어링 의사 결정의 핵심이며 사용자에게 중요한 기능 및 앱에 대한 업데이트의 양식에서 만들어야 하는 개선 사항의 위치를 확인할 수 있도록 도와줍니다. 앱 사용자로서 앱의 첫 번째 실행 시 이 데이터 컬렉션의 알림을 확인하고, 앱의 **설정** 페이지에서 해제할 수 있음을 알 수 있습니다. 언제든지 이 설정을 활성화 또는 비활성화할 수 있습니다.</li></ul>|
|앱에서 코드란 무엇인가요?|Microsoft Authenticator 앱을 열면 추가한 계정이 타일로 표시되고 각각 6자리 또는 8자리 숫자가 붙어 있습니다.<br><br>이러한 코드는 사용자 신원을 확인하는 용도로 사용됩니다. 사용자 이름 및 암호를 사용하여 로그인한 후 계정과 같은 타일에 있는 확인 코드를 입력합니다. 예를 들어 Alain이 Contoso 계정에 로그인하는 하는 경우 확인 코드 427303을 사용합니다.<br><br>![앱의 계정 화면](./media/user-help-auth-app-faq/auth-app-accounts.png)|
|코드 옆에 있는 숫자가 계속 카운트다운되는 이유는 무엇인가요?|활성 확인 코드 옆에 30초 타이머가 카운트다운되는 것을 볼 수 있습니다. 이 타이머는 같은 코드를 두 번 사용하여 로그인하는 일이 없도록 방지하기 위한 것입니다. 암호와는 달리, 이 숫자를 기억할 필요가 없습니다. 휴대폰에 액세스할 수 있는 사람만이 확인 코드를 알 수 있다는 점에 착안하였습니다.|
|계정 타일이 회색으로 표시되는 이유는 무엇인가요?|일부 조직에서는 Microsoft Authenticator 앱을 Single Sign-On과 함께 사용하여 조직의 리소스를 보호할 것을 요구합니다. 이 경우 계정이 2단계 인증에 사용되지 않고 회색 또는 비활성 상태로 표시됩니다. 이러한 종류의 계정을 일반적으로 "브로커" 계정이라고 부릅니다.|
|디바이스 등록이란?|조직에서는 파일 및 앱 같은 보안 리소스에 어떤 디바이스가 액세스하는지 파악하고, 가능하다면 조건부 액세스를 설정하여 보안 리소스에 대한 부적절한 액세스 위험을 줄이기 위해 사용자에게 디바이스 등록을 요구할 수 있습니다. **설정**을 사용하여 디바이스 등록을 취소할 수 있지만, Outlook의 이메일과 OneDrive의 파일에 액세스할 수 없게 될 수 있고 휴대폰 로그인 기능을 잃게 됩니다.|
|확인 코드를 가져와서 사용하려면 인터넷 또는 네트워크에 연결되어야 합니까?|인터넷 또는 데이터에 연결되지 않아도 코드를 사용할 수 있으므로 전화 서비스에 로그인할 필요가 없습니다. 또한 앱을 닫는 즉시 앱 실행이 중지되므로 배터리가 소모되지 않습니다.|
|앱이 열려 있을 때만 알림을 받습니다. 앱이 닫히면 알림이 오지 않습니다.|벨 소리가 켜져 있어도 알림이 도착할 때 경고가 울리지 않으면 앱 설정을 확인하세요. 알림이 도착할 때 소리를 내거나 진동이 울리도록 앱을 설정해야 합니다. 알림이 전혀 수신되지 않으면 다음 사항을 확인합니다.<ul><li>휴대폰이 방해 금지 또는 자동 모드인가요? 이 모드에서는 앱이 알림을 보내지 않을 수 있습니다.</li><li>다른 앱에서 알림을 받을 수 있나요? 받을 수 없으면 휴대폰의 네트워크 연결 문제이거나 Android 또는 Apple의 알림 채널에 문제가 있는 것일 수 있습니다. 휴대폰 설정을 통해 네트워크 연결을 확인할 수 있지만, Android 또는 Apple 알림 채널 문제를 해결하려면 서비스 공급자에게 문의해야 할 수도 있습니다.</li><li>앱에서 일부 계정의 알림을 받을 수 있고 나머지 계정의 알림을 받을 수 없나요? 그렇다면 앱에서 문제가 있는 계정을 제거했다가 다시 추가하고 알림을 허용한 다음, 문제가 해결되었는지 확인하세요.</li></ul>이러한 단계를 모두 시도한 후에도 문제가 지속되면 로그 파일을 보내주세요. 앱을 열고, **도움말**로 이동한 다음, **로그 보내기**를 선택합니다. 그런 다음, [Microsoft Authenticator 앱 포럼](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)으로 이동하여 어떤 문제가 표시되고 있는지, 지금까지 시도한 단계에 대해 알려주세요.|
|앱에서 확인 코드를 사용하고 있는데, 푸시 알림으로 전환하려면 어떻게 해야 하나요?|회사/학교 계정(관리자가 설정한 경우) 또는 개인 Microsoft 계정에서 푸시 알림을 설정할 수 있지만, Google 또는 Facebook 같은 타사 계정에는 알림이 작동하지 않습니다.<br><br>알림을 통해 개인 계정을 전환하려면 계정에 디바이스를 다시 등록해야 합니다. 이렇게 하려면 **계정 추가**로 이동하여 **개인 Microsoft 계정**을 선택한 다음, 사용자 이름과 암호를 사용하여 로그인합니다.<br><br>회사 또는 학교 계정에 원 클릭 알림을 허용할 것인지 여부는 조직에서 결정하며, 조직에서 이 기능을 끌 수 있습니다.|
|Microsoft 이외의 계정에서도 알림이 작동하나요?|아니요. 알림은 Microsoft 계정 및 Azure Active Directory 계정에서만 작동합니다. 회사 또는 학교에서 Azure AD 계정을 사용하는 경우 회사 또는 학교에서 이 기능을 끌 수 있습니다.|
|새 디바이스가 생겼습니다/백업으로 디바이스를 복원했습니다. Microsoft Authenticator 앱에서 내 계정을 다시 설정하려면 어떻게 해야 하나요?|iOS 디바이스를 실행 중이고 **iCould 백업**을 설정했으며 기존 디바이스에 계정 백업을 만들었으면 해당 백업을 사용하여 새 디바이스에서 계정 자격 증명을 복구할 수 있습니다. 자세한 내용은 [Microsoft Authenticator 앱을 사용하여 계정 자격 증명 백업 및 복구](user-help-auth-app-backup-recovery.md) 문서를 참조하세요.|
|디바이스를 잃어버렸습니다/새 디바이스로 이동했습니다. 알림이 계속해서 기존 디바이스로 이동하지 않게 하려면 어떻게 해야 하나요?|새 디바이스에 Microsoft Authenticator 앱을 추가해도 기존 디바이스에서 앱이 자동으로 제거되지는 않습니다. 기존 디바이스에서 앱을 삭제하는 것만으로는 충분하지 않습니다. 기존 디바이스에서 앱을 삭제하고, Microsoft 또는 소속 조직에 기존 디바이스를 제거하고 계정에서 등록을 취소해 달라고 요청해야 합니다.<ul><li>**개인 Microsoft 계정을 사용하여 장치에서 앱을 제거하려면:** [계정 보안](https://account.microsoft.com/security) 페이지의 2단계 확인 영역으로 이동하여 기존 장치의 확인 기능을 끕니다.</li><li>**회사 또는 학교 Microsoft 계정을 사용하여 장치에서 앱을 제거하려면:** [MyApps](https://myapps.microsoft.com/) 페이지의 2단계 확인 영역 또는 소속 조직의 사용자 지정 포털로 이동하여 기존 장치의 확인 기능을 끕니다.</li></ul>|
|앱에서 계정을 제거하려면 어떻게 해야 하나요?|<ul><li>**iOS.** 메인 화면에서 메뉴 단추를 선택하고 **계정 편집**을 선택합니다. 계정 이름 옆의 빨간색 아이콘을 탭하고 **계정 제거**를 탭합니다.</li><li>**Android.** 메인 화면에서 메뉴 단추를 선택하고 **계정 편집**을 선택합니다. 계정 이름 옆에 있는 **X**를 누릅니다.</li></ul>조직에 등록된 디바이스가 있는 경우 계정을 제거하도록 추가 단계를 완료해야 합니다. 이러한 디바이스에서 Microsoft Authenticator 앱은 디바이스 관리자는 자동으로 등록됩니다. 앱을 완전히 제거하려면 먼저 앱 설정에서 앱 등록을 취소해야 합니다.|
|앱이 너무 많은 권한을 요청하는 이유는 무엇인가요?|에 대 한 라는 메시지가 표시 될 수 있는 사용 권한의 전체 목록은 다음과 같습니다 및 앱에서 사용 하는 방법입니다. 특정 사용 권한을 표시 해야 하는 휴대폰의 종류에 따라 다릅니다.<ul><li>**카메라.** 회사 계정, 학교 계정 또는 Microsoft 이외의 계정을 추가할 때 QR 코드를 검사하는 데 사용됩니다.</li><li>**연락처 및 휴대폰.** 앱을 기존 회사 또는 학교 Microsoft 계정을 휴대폰에 대 한 검색 하 고 계정의 제대로 작동 하는 데 앱을 추가할 수 있도록이 권한이 필요 합니다. 또한이 사용 권한을 개인 Microsoft 계정, 입력 하 여 자동으로 정보의 일부를 첫 번째 및 마지막 이름에 추가 하는 동안 시간을 절약할 수 있습니다.</li><li>**SMS.** 전화 번호와 레코드의 숫자와 일치하는지 확인하는 데 사용됩니다. 사용자가 개인 Microsoft 계정을 사용하여 처음으로 로그인하면 앱을 다운로드한 휴대폰으로 6-8자리 확인 코드가 포함된 문자 메시지가 전송됩니다. 사용자에게 이 코드를 찾아 앱에 입력하라고 요청하는 대신, 문자 메시지에서 자동으로 코드가 검색됩니다.</li><li>**다른 앱을 통해 가져오기.** 사용자의 ID를 확인하는 알림은 실행 중일 수 있는 다른 앱에도 표시됩니다.</li><li>**인터넷의 데이터 수신.** 알림을 보내기 위해 이 사용 권한이 필요합니다.</li><li>**휴대폰 절전 모드 전환 방지.** 디바이스를 조직에 등록하면 조직에서는 휴대폰에서 이 정책을 변경할 수 있습니다.</li><li>**진동 제어.** ID를 인증하는 알림을 수신할 때마다 진동을 울릴지 여부를 선택할 수 있습니다.</li><li>**지문 하드웨어 사용.** 일부 회사 및 학교 계정에는 ID를 인증할 때마다 추가 PIN이 필요합니다. 프로세스를 보다 쉽게 수행하려면 PIN을 입력하는 대신 지문을 사용할 수 있습니다.</li><li> **네트워크 연결 보기.** Microsoft 계정을 추가할 때 앱에는 네트워크/인터넷 연결이 필요합니다.</li><li>**저장소의 내용 읽기.** 앱 설정을 통해 기술 문제를 보고하는 경우에만 이 사용 권한을 사용하게 됩니다. 저장소의 일부 정보를 수집하여 문제를 진단합니다.</li><li>**전체 네트워크 액세스.** ID를 인증하는 알림을 보내는 데 이 사용 권한이 필요합니다.</li><li>**시작 시 실행.** 전화를 다시 시작하는 경우 이 사용 권한을 사용하면 ID를 인증하는 알림을 계속 수신할 수 있습니다.</li></ul>|
|Microsoft Authenticator 앱에서 사용자가 디바이스를 잠금 해제하지 않아도 요청을 승인할 수 있도록 허용하는 이유는 무엇인가요?|사용자가 휴대폰을 갖고 있는지만 증명하면 되므로 확인 요청을 승인하도록 디바이스의 잠금을 해제할 필요가 없습니다. 2단계 인증은 두 가지를 요구합니다. 하나는 사용자가 알고 있는 것이고, 다른 하나는 사용자가 갖고 있는 것입니다. 사용자가 알고 있는 것은 암호입니다. 사용자가 갖고 있는 것은 휴대폰(Microsoft Authenticator 앱을 통해 설정하고 MFA 증명으로 등록된)입니다. 따라서 휴대폰을 갖고 있고 요청을 승인하면 인증의 두 번째 요소에 대한 조건을 충족합니다.|
|Apple Watch에서 Microsoft Authenticator 앱을 열 때 모든 계정이 표시되지 않는 이유는 무엇인가요?|Microsoft Authenticator 앱은 Apple Watch 도우미 앱에서 Microsoft 개인 계정 또는 회사/학교 계정을 사용하는 것만 지원합니다. Google 또는 Facebook 같은 다른 계정의 경우 확인 코드를 보려면 휴대폰에서 Authenticator 앱을 열어야 합니다.|
|Apple Watch에서 알림을 승인 또는 거부할 수 없는 이유는 무엇인가요?|먼저 iPhone에서 Microsoft Authenticator 앱을 버전 6.0.0 이상으로 업그레이드 했는지 확인합니다. 그 후 Apple Watch에서 Microsoft Authenticator 도우미 앱을 열고 아래에 **설정** 단추가 있는 계정을 찾습니다. 이러한 계정에 대한 알림을 승인하려면 이 설정 프로세스를 완료해야 합니다.|
|Apple Watch와 휴대폰 사이에 통신 오류가 발생합니다. 어떻게 하면 문제를 해결할 수 있나요?|이 오류는 Watch가 휴대폰과 통신을 마치기 전에 해당 화면이 절전 모드로 전환되는 경우 발생합니다.<br><br><b>설치 중에 이런 문제가 발생하면:</b><br>설치를 다시 실행하고 프로세스가 완료될 때가지 Watch가 활성화 상태를 유지하도록 합니다. 동시에, 휴대폰에서 앱을 열고 표시되는 모든 메시지에 응답합니다.<br><br>이렇게 해도 휴대폰과 Watch가 서로 통신할 수 없으면 다음 조치를 시도할 수 있습니다.<ol><li>Microsoft Authenticator 휴대폰 앱을 강제로 종료하여 iPhone에서 다시 엽니다.</li><li>Apple Watch 도우미 앱을 강제로 종료합니다.<ol><li> Watch에서 Microsoft Authenticator 도우미 앱을 엽니다.</li><li>**종료** 화면이 나타날 때까지 옆면의 단추를 길게 누릅니다.</li><li>옆면의 단추를 놓고 Digital Crown을 길게 눌러 활성 앱을 강제로 종료합니다.</li></ol></li><li>휴대폰과 Watch에서 Bluetooth와 Wi-Fi를 모두 껐다가 다시 켭니다.</li><li>iPhone 및 Watch를 다시 시작합니다.</li></ol><b>알림을 승인하려고 할 때 이 문제가 발생하면:</b><br>다음 번에 Apple Watch에서 알림을 승인하려고 할 때, 요청이 완료되고 성공적으로 완료되었음을 알리는 소리가 들릴 때까지 화면을 활성화 상태로 유지합니다.|
|Apple Watch용 Microsoft Authenticator 도우미 앱이 동기화되지 않거나 Watch에 표시되지 않는 이유는 무엇인가요?|앱이 Watch에 표시되지 않는 경우 다음 방법을 시도합니다. <ol><li>Watch가 watchOS 4.0 이상을 실행 중인지 확인합니다.</li><li>Watch를 다시 동기화합니다.</li></ol>|
|Apple Watch 도우미 앱이 충돌했습니다. 크래시 로그를 보내서 조사할 수 있나요? |먼저 분석 정보를 Microsoft와 공유하도록 선택했는지 확인합니다. TestFlight 사용자인 경우 이미 등록되어 있습니다. 아닌 경우 **설정 > 개인 정보 > 분석**으로 이동하여 **iPhone 및 Watch 분석 공유** 및 **앱 개발자와 공유** 옵션을 모두 선택합니다.<br><br>가입한 후 크래시를 재현하면 자동으로 크래시 로그가 Microsoft로 전송됩니다. 하지만 크래시를 재현할 수 없는 경우 로그 파일을 수동으로 복사해서 보내주시면 됩니다.<ol><li>휴대폰에서 Watch 앱을 열고 **설정 > 일반**으로 이동한 다음, **Watch 분석 복사**를 클릭합니다.</li><li>**설정 > 개인 정보 > 분석 > 분석 데이터** 아래에서 해당 크래시를 찾은 다음, 전체 텍스트를 수동으로 복사합니다.</li><li>휴대폰에서 Microsoft Authenticator 앱을 열고, 앞에서 복사한 텍스트를 **로그 보내기** 페이지의 **앱 개발자와 공유** 텍스트 상자에 붙여넣습니다.</li></ol>|
|앱 잠금 기능이란 무엇이며 보안을 강화하는 데 어떤 도움이 되나요?|일회용 암호, 앱 정보 및 앱 설정을 더욱 안전하게 유지하려는 경우 Microsoft Authenticator 앱에서 앱 잠금 기능을 설정하면 됩니다. Microsoft Authenticator 앱의 **설정** 화면에서 앱 잠금을 설정하면 Microsoft Authenticator 앱을 열 때마다 PIN이나 생체 인식 정보를 사용하여 인증하라는 메시지가 표시됩니다. 이 기능은 추가적인 보호를 제공하기 위한 것이며 Microsoft Authenticator 앱에서 알림을 승인하는 방식은 변경되지 않습니다.<br><br>**참고**<br>30초 이내에 Android 디바이스에서 실행 중인 Microsoft Authenticator 앱으로 돌아오면 다시 인증하라는 메시지가 표시되지 않습니다.|
|내 계정 작업에 대한 알림이 수신되는 이유는 무엇인가요?|개인 Microsoft 계정에서 발생하는 일을 정확하게 파악할 수 있도록 Microsoft Authenticator 앱으로 작업 알림을 보내드리고 있습니다. 무언가가 변경되는 즉시 이러한 알림이 표시되므로 보안 강화에 도움이 됩니다. 이전에는 이메일 및 SMS를 통해 이러한 알림을 전송했으며, 이제는 앱을 포함하도록 확장되었습니다. 이러한 작업 알림에 대한 자세한 내용은 [비정상적인 계정 로그인이 있는 경우 어떻게 되나요?](https://support.microsoft.com/help/13967/microsoft-account-unusual-sign-in)를 참조하세요. 알림을 받는 위치를 변경 하려면 로그인을 https:\//account.live.com/SecurityNotifications/Update 합니다.


## <a name="next-steps"></a>다음 단계

- 개인 Microsoft 계정에 대 한 확인 코드를 시작 하는 데 문제가 있는 경우 참조를 **확인 코드 문제 해결** 섹션을 [Microsoft 계정 보안 정보 및 확인 코드](https://support.microsoft.com/en-us/help/12428/microsoft-account-security-info-verification-codes) 문서.

-   2단계 인증에 대해 더 자세히 알아보려면 [2단계 인증에 내 계정 설정](multi-factor-authentication-end-user-first-time.md)을 참조하세요.

-   보안 정보에 대 한 자세한 정보를 원한다 면 참조 [보안 정보 (미리 보기) 개요](user-help-security-info-overview.md)

- 궁금한 내용에 대한 답변이 여기에 없으면 직접 질문을 남겨주세요. [Microsoft Authenticator 앱 포럼](https://social.technet.microsoft.com/Forums/en-us/home?forum=MicrosoftAuthenticatorApp)으로 이동하여 질문을 게시하고, 커뮤니티의 도움을 받고, 이 페이지에 의견을 남기세요.
