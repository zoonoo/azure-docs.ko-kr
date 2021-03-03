---
title: OATH 토큰 인증 방법-Azure Active Directory
description: Azure Active Directory에서 OATH 토큰을 사용 하 여 로그인 이벤트를 개선 하 고 보안을 유지 하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2270ff360c7bb923555c9b4ffb0c35ccd4382d0e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101647492"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Azure Active Directory OATH 토큰의 인증 방법

OATH TOTP(시간 제약이 있는 일회성 암호)는 OTP(일회용 암호) 코드가 생성되는 방법을 지정하는 개방형 표준입니다. OATH TOTP는 코드를 생성하는 소프트웨어 또는 하드웨어를 사용하여 구현할 수 있습니다. Azure AD는 다른 코드 생성 표준인 OATH HOTP를 지원하지 않습니다.

## <a name="oath-software-tokens"></a>OATH 소프트웨어 토큰

소프트웨어 OATH 토큰은 일반적으로 Microsoft Authenticator 앱 및 다른 인증자 앱과 같은 애플리케이션입니다. Azure AD는 앱에 입력되고 각 OTP를 생성하는 데 사용되는 비밀 키 또는 시드를 생성합니다.

Authenticator 앱은 푸시 알림을 수행하도록 설정된 경우 자동으로 코드를 생성하므로 디바이스가 연결되지 않은 경우에도 사용자에게 백업이 있습니다. OATH TOTP를 사용하여 코드를 생성하는 타사 애플리케이션을 사용할 수도 있습니다.

일부 OATH TOTP 하드웨어 토큰은 프로그래밍 가능합니다. 즉, 사전 프로그래밍된 비밀 키 또는 시드가 제공되지 않습니다. 이러한 프로그래밍 가능한 하드웨어 토큰은 소프트웨어 토큰 설정 흐름에서 얻은 비밀 키 또는 시드를 사용하여 설정할 수 있습니다. 고객은 선택한 공급업체에서 이러한 토큰을 구매하고 해당 공급업체의 설정 프로세스에서 비밀 키 또는 시드를 사용할 수 있습니다.

## <a name="oath-hardware-tokens"></a>OATH 하드웨어 토큰

Azure AD는 30초 또는 60초마다 코드를 새로 고치는 OATH-TOTP SHA-1 토큰의 사용을 지원합니다. 고객은 자신이 선택한 공급업체에서 이러한 토큰을 구매할 수 있습니다.

OATH TOTP 하드웨어 토큰은 일반적으로 토큰에서 사전 프로그래밍된 비밀 키 또는 시드를 제공합니다. 이러한 키는 다음 단계에 설명된 대로 Azure AD에 입력해야 합니다. 비밀 키는 128자로 제한되며 일부 토큰과는 호환되지 않을 수 있습니다. 비밀 키에 *는* 문자 a-z 또는 a-z 및 *2-7* 숫자만 사용할 *수 있으며* , *Base32* 로 인코딩해야 합니다.

다시 시드될 수 있는 프로그래밍 가능한 OATH TOTP 하드웨어 토큰은 소프트웨어 토큰 설정 흐름에서 Azure AD를 사용하여 설정할 수도 있습니다.

![OATH 토큰을 MFA OATH 토큰 블레이드에 업로드](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

토큰을 얻은 후에는 다음 예제와 같이 UPN, 일련 번호, 비밀 키, 시간 간격, 제조업체 및 모델을 포함 하 여 쉼표로 구분 된 값 (CSV) 파일 형식으로 업로드 해야 합니다.

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,2234567abcdef1234567abcdef,60,Contoso,HardwareKey
```  

> [!NOTE]
> CSV 파일에 머리글 행을 포함해야 합니다. UPN에 작은따옴표가 있으면 다른 작은따옴표를 사용 하 여 이스케이프 합니다. 예를 들어, UPN이 my ' 이면 user@domain.com 파일을 업로드할 때 ' '로 변경 user@domain.com 합니다.

CSV 파일로 올바르게 형식이 지정되면 관리자는 Azure Portal에 로그인하여 **Azure Active Directory > 보안 > MFA > OATH 토큰** 으로 이동하고 CSV 파일을 업로드할 수 있습니다.

CSV 파일의 크기에 따라 처리하는 데 몇 분 정도가 소요될 수 있습니다. 현재 상태를 가져오려면 **새로 고침** 단추를 선택합니다. 파일에 오류가 있는 경우 오류가 나열된 CSV 파일을 다운로드하여 해결할 수 있습니다. 다운로드한 CSV 파일의 필드 이름은 업로드된 버전과 다릅니다.  

오류가 모두 처리되면 관리자는 토큰에 대해 **활성화** 를 선택하고 토큰에 표시된 OTP를 입력하여 각 키를 활성화할 수 있습니다. 5 분 마다 최대 200 OATH 토큰을 활성화할 수 있습니다. 

사용자는 언제든지 사용할 수 있도록 구성된 Microsoft Authenticator 앱과 같은 인증자 애플리케이션 또는 최대 5개의 OATH 하드웨어 토큰을 조합할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Graph REST API 베타](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)를 사용 하 여 인증 방법 구성에 대해 자세히 알아보세요.
