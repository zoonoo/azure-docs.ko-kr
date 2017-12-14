---
title: "Azure MFA 구성 | Microsoft Docs"
description: "MFA로 다음에 수행할 작업을 설명하는 Azure Multi-Factor Authentication 페이지입니다.  보고서, 사기 행위 경고, 일회성 바이패스, 사용자 지정 음성 메시지, 캐시, 신뢰할 수 있는 ip 및 앱 암호를 포함합니다."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: joflore
ms.openlocfilehash: a6064e33dca09a304074efa4029c0f96fc19b2a2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Azure Multi-Factor Authentication 구성 설정
이 문서는 준비하고 실행 중인 Multi-Factor Authentication을 관리하는데 도움이 됩니다.  Azure Multi-Factor Authentication을 최대한 활용할 수 있게 하는 다양한 항목을 설명합니다.  모든 버전의 Azure Multi-Factor Authentication에서 이러한 모든 기능을 사용할 수는 없습니다.

| 기능 | 설명 | 
|:--- |:--- |
| [사기 행위 경고](#fraud-alert) |사기 행위 경고를 구성하고 설정하여 사용자가 해당 리소스에 액세스하려는 사기성 시도를 사기를 보고할 수 있습니다. |
| [일회성 바이패스](#one-time-bypass) |일회성 바이패스로 Multi-Factor Authentication "바이패스"하여 사용자가 단일 시간을 인증할 수 있습니다. |
| [사용자 지정 음성 메시지](#custom-voice-messages) |사용자 지정 음성 메시지를 사용하면 Multi-Factor Authentication으로 사용자 고유의 녹음/녹화 또는 인사말을 사용할 수 있습니다. |
| [캐싱](#caching-in-azure-multi-factor-authentication) |캐싱을 사용하면 특정 시간 기간을 설정하여 후속 인증 시도가 자동으로 성공하도록 할 수 있습니다. |
| [신뢰할 수 있는 IP](#trusted-ips) |관리되거나 페더레이션된 테넌트의 관리자는 회사의 로컬 인트라넷에서 로그인하는 사용자를 위해 2단계 인증을 바이패스하는 신뢰할 수 있는 IP를 사용할 수 있습니다. |
| [앱 암호](#app-passwords) |앱 암호를 사용하면 MFA를 인식하지 않는 응용 프로그램은 Multi-Factor Authentication를 바이패스하고 계속 작업할 수 있습니다. |
| [기억된 장치 및 브라우저용 Multi-Factor Authentication 기억](#remember-multi-factor-authentication-for-devices-that-users-trust) |사용자가 MFA를 사용하여 성공적으로 로그인한 후 정해진 일수 동안 장치를 기억할 수 있습니다. |
| [선택 가능한 확인 방법](#selectable-verification-methods) |사용자가 사용할 수 있는 인증 방법을 선택할 수 있습니다. |

## <a name="access-the-azure-mfa-management-portal"></a>Azure MFA 관리 포털에 액세스

이 문서에서 다룬 기능은 Azure Multi-Factor Authentication 관리 포털에서 구성할 수 있습니다. Azure 클래식 포털을 통해 MFA 관리 포털에 액세스하는 방법은 두 가지가 있습니다. 첫 번째는 Multi-Factor Auth 공급자를 직접 관리하여 수행합니다. 두 번째는 MFA 서비스 설정을 통해 수행합니다. 

### <a name="use-an-authentication-provider"></a>인증 공급자 사용

사용량 기반 MFA에 대한 Multi-Factor Auth 공급자를 사용하는 경우 이 방법을 사용하여 관리 포털에 액세스합니다.

Azure Multi-Factor Auth 공급자를 통해 MFA 관리 포털에 액세스하려면 관리자 권한으로 Azure 클래식 포털에 로그인하고 Active Directory 옵션을 선택합니다. **Multi-Factor Auth 공급자** 탭을 클릭한 다음 디렉터리를 선택하고 아래쪽에서 **관리** 단추를 클릭합니다.

### <a name="use-the-mfa-service-settings-page"></a>MFA 서비스 설정 페이지 사용 

다음 라이선스 중 하나가 있으면 MFA 서비스 설정 페이지를 사용할 수 있습니다.
- Azure MFA
- Azure AD Premium 
- Enterprise Mobility + Security

MFA 서비스 설정 페이지를 통해 MFA 관리 포털에 액세스하려면 관리자 권한으로 Azure 클래식 포털에 로그인하고 Active Directory 옵션을 선택합니다. 디렉터리를 클릭한 다음 **구성** 탭을 클릭합니다. Multi-Factor Authentication 섹션에서 **서비스 설정 관리**를 선택합니다. MFA 서비스 설정 페이지의 맨 아래에서 **포털로 이동** 링크를 클릭합니다.


## <a name="fraud-alert"></a>사기 행위 경고
사기 행위 경고를 구성하고 설정하여 사용자가 해당 리소스에 액세스하려는 사기성 시도를 사기를 보고할 수 있습니다.  사용자가 모바일 앱 또는 자신의 전화를 통해 사기 행위를 보고할 수 있습니다.

### <a name="set-up-fraud-alert"></a>사기 행위 경고 설정
1. 이 페이지 맨 위에 있는 지침에 따라 MFA 관리 포털로 이동합니다.
2. Azure Multi-Factor Authentication 관리 포털의 구성 섹션에서 **설정**을 클릭합니다.
3. 설정 페이지의 사기 경고 섹션에서 허용 사용자를 선택하여 **사기 행위 경고 제출 허용** 확인란을 제출합니다.
4. **저장**을 선택하여 변경 내용을 적용합니다. 

### <a name="configuration-options"></a>구성 옵션

- **사기 행위가 보고되면 사용자 차단** - 사용자가 사기 행위를 보고하면 해당 계정은 차단됩니다.
- **초기 인사말 중 사기 행위를 보고할 코드** - 사용자는 2단계 인증을 확인하기 위해 일반적으로 #를 누릅니다. 사기 행위를 보고하려는 경우 #를 누르기 전에 코드를 입력합니다. 이 코드의 기본값은 **0**이지만, 사용자가 지정할 수 있습니다.

> [!NOTE]
> Microsoft의 기본 음성 인사말은 사용자가 사기 행위 경고 제출하기 위해 0# 키를 누르도록 지시합니다. 0이 아닌 코드를 사용하려는 경우 적절한 지침과 함께 고유의 사용자 지정 음성 인사말을 기록하고 업로드해야 합니다.

![사기 행위 경고 옵션 - 스크린샷](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="how-users-report-fraud"></a>사용자가 사기 행위를 보고하는 방법 
두 가지 방법으로 사기 행위 경고를 보고할 수 있습니다.  모바일 앱 또는 전화를 통해 보고합니다.  

#### <a name="report-fraud-with-the-mobile-app"></a>모바일 앱으로 사기 행위 보고
1. 휴대폰으로 확인 메시지를 전송되면 이를 선택하여 Microsoft Authenticator 앱이 시작됩니다.
2. 알림에서 **거부**를 선택합니다. 
3. **사기 행위 보고**를 선택합니다.
4. 앱을 닫습니다.

#### <a name="report-fraud-with-a-phone"></a>휴대폰으로 사기 행위 보고
1. 전화로 확인 호출이 들어오면 응답합니다.  
2. 사기 행위를 보고하려면 사기 행위 코드(기본값은 0)와 # 기호를 입력합니다. 그러면 사기 행위 경고가 전송되었다는 알림이 표시됩니다.
3. 통화를 종료합니다.

### <a name="view-fraud-reports"></a>사기 행위 보고 보기
1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 왼쪽에서 Active Directory를 선택합니다.
3. 맨 위에서 Multi-Factor Auth 공급자 목록을 표시할 **Multi-Factor Auth 공급자**를 선택합니다.
4. Multi-factor Auth 공급자를 선택하고 페이지의 맨 아래에 있는 **관리**를 클릭합니다. Azure Multi-Factor Authentication 관리 포털이 열립니다.
5. Azure Multi-Factor Authentication 관리 포털의 보고서 보기 아래에 **사기 행위 경고**를 클릭합니다.
6. 보고서에서 보려는 날짜 범위를 지정합니다. 또한 사용자 이름, 전화번호 및 사용자 상태를 지정할 수 있습니다.
7. **실행**을 클릭하면 사기 행위 경고 보고서가 표시됩니다. 보고서를 내보내려면 **CSV로 내보내기**를 클릭합니다.

## <a name="one-time-bypass"></a>일회성 바이패스
일회성 바이패스를 통해 사용자는 2단계 인증을 수행하지 않고 한 번에 인증할 수 있습니다. 바이패스는 임시적이며 지정된 시간(초) 이후 만료됩니다. 모바일 앱 또는 전화가 알림 또는 전화 통화를 받지 못하는 경우 일회성 바이패스를 사용하여 사용자가 원하는 리소스에 액세스하도록 할 수 있습니다.

### <a name="create-a-one-time-bypass"></a>일회성 바이패스 만들기
1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 이 페이지 맨 위에 있는 지침에 따라 MFA 관리 포털로 이동합니다.
3. 왼쪽에 옆에 **+**가 있는 Azure MFA 공급자 이름이 보이면 **+**를 클릭합니다. 서로 다른 MFA 서버 복제 그룹 및 Azure 기본 그룹이 표시됩니다. 적절한 그룹을 선택합니다.
4. 사용자 관리에서 **일회성 바이패스**를 클릭합니다.
5. 일회성 바이패스 페이지에서 **새 일회성 바이패스**를 클릭합니다.

  ![클라우드](./media/multi-factor-authentication-whats-next/create1.png)

6. 사용자 이름, 바이패스 기간 및 바이패스 이유를 입력합니다. **바이패스**를 클릭합니다.
7. 시간 제한이 즉시 적용되기 때문에 사용자는 일회성 바이패스가 만료되기 전에 로그인해야 합니다. 

### <a name="view-the-one-time-bypass-report"></a>일회성 바이패스 보고서 보기
1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 왼쪽에서 Active Directory를 선택합니다.
3. 맨 위에서 Multi-Factor Auth 공급자 목록을 표시할 **Multi-Factor Auth 공급자**를 선택합니다.
4. Multi-factor Auth 공급자를 선택하고 페이지의 맨 아래에 있는 **관리**를 클릭합니다. Azure Multi-Factor Authentication 관리 포털이 열립니다.
5. Azure Multi-factor Authentication 관리 포털의 왼쪽, 보고서 보기 아래에서 **일회성 바이패스**를 클릭합니다.
6. 보고서에서 보려는 날짜 범위를 지정합니다. 또한 사용자 이름, 전화번호 및 사용자 상태를 지정할 수 있습니다.
7. **실행**을 클릭하면 바이패스 보고서가 표시됩니다. 보고서를 내보내려면 **CSV로 내보내기**를 클릭합니다.

## <a name="custom-voice-messages"></a>사용자 지정 음성 메시지
사용자 지정 음성 메시지를 사용하면 2단계 인증으로 사용자 고유의 녹음/녹화 또는 인사말을 사용할 수 있습니다. Microsoft 기록을 추가하거나 바꾸는 데 사용자 지정 음성 메시지를 사용할 수 있습니다.

시작하기 전에 다음을 고려하세요.

* 지원되는 파일 형식은 .wav 및 .mp3입니다.
* 파일 크기는 5MB로 제한됩니다.
* 인증 메시지 길이는 20초 미만이어야 합니다. 20초보다 긴 메시지는 확인 시간이 다 소진되기 전에 사용자가 응답하기에는 시간이 부족합니다.

### <a name="set-up-a-custom-message"></a>사용자 지정 메시지 설정

사용자 지정 메시지 작성은 두 부분으로 이루어져 있습니다. 첫째, 메시지를 업로드한 다음 사용자를 위해 이를 켭니다.

사용자 지정 메시지를 업로드하려면

1. 지원되는 파일 형식 중 하나를 사용하여 사용자 지정 음성 메시지 만들기
2. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
3. 이 페이지 맨 위에 있는 지침에 따라 MFA 관리 포털로 이동합니다.
4. Azure Multi-Factor Authentication 관리 포털의 구성 섹션에서 **음성 메시지**를 클릭합니다.
5. 구성: 음성 메시지 페이지에서 **새 음성 메시지**를 클릭합니다.
   ![클라우드](./media/multi-factor-authentication-whats-next/custom1.png)
6. 구성: 새 음성 메시지 페이지에서 **사운드 파일 관리**를 클릭합니다.
   ![클라우드](./media/multi-factor-authentication-whats-next/custom2.png)
7. 구성: 사운드 파일 페이지에서 **사운드 파일 업로드**를 클릭합니다.
   ![클라우드](./media/multi-factor-authentication-whats-next/custom3.png)
8. 구성: 사운드 파일 업로드에서 **찾아보기**를 클릭하고 음성 메시지로 이동하여 **열기**를 클릭합니다.
9. 설명을 추가하고 **업로드**를 클릭합니다.
10. 음성 메시지가 업로드되면 메시지가 파일을 성공적으로 업로드했음을 확인합니다.

사용자를 위해 메시지를 켜려면

1. 왼쪽에서 **음성 메시지**를 클릭합니다.
2. 음성 메시지 섹션에서 **새 음성 메시지**를 클릭합니다.
3. 언어 드롭다운 메뉴에서 언어를 선택합니다.
4. 이 메시지가 특정 응용 프로그램용인 경우 응용 프로그램 상자에서 지정하십시오.
5. 메시지 유형 드롭다운 목록에서 새 사용자 지정 메시지로 재정의할 메시지 유형을 선택합니다.
6. 사운드 파일 드롭다운 목록에서 첫 번째 부분에서 업로드한 사운드 파일을 선택합니다.
7. **만들기**를 클릭합니다. 메시지는 성공적으로 음성 메시지를 만들었음을 확인합니다.
    ![클라우드](./media/multi-factor-authentication-whats-next/custom5.png)</center>

## <a name="caching-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication에서 캐싱
캐싱을 사용하면 특정 시간 기간을 설정하여 해당 시간 내에 후속 인증 시도가 자동으로 성공하도록 할 수 있습니다. 캐싱은 첫 번째 요청이 진행 중인 동안 VPN과 같은 온-프레미스 시스템이 여러 확인 요청을 전송하는 경우 사용됩니다. 사용자가 진행 중인 첫 번째 확인에 성공한 후에 자동으로 후속 요청이 성공할 수 있습니다. 

캐싱은 Azure AD 로그인에 사용하기 위한 것이 아닙니다.

### <a name="set-up-caching"></a>캐싱 설정 
1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 이 페이지 맨 위에 있는 지침에 따라 MFA 관리 포털로 이동합니다.
3. Azure Multi-Factor Authentication 관리 포털의 구성 섹션에서 **캐싱**을 클릭합니다.
4. 캐싱 구성 페이지에서 **새 캐시**를 클릭합니다.
5. 캐시 유형 및 캐시 시간(초)을 선택합니다. **만들기**를 클릭합니다.

<center>![클라우드](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>신뢰할 수 있는 IP
신뢰할 수 있는 IP는 관리되는 또는 페더레이션된 테넌트의 관리자가 2단계 인증을 바이패스하기 위해 사용할 수 있는 Azure MFA 기능입니다. 이 기능은 회사의 로컬 인트라넷에서 로그인하는 사용자를 위해 사용됩니다. 이 기능은 관리자용 무료 버전이 아닌 Azure Multi-Factor Authentication의 정식 버전에서 사용할 수 있습니다. Azure Multi-Factor Authentication의 정식 버전을 가져오는 방법에 대한 자세한 내용은 [Azure Multi-Factor Authentication](multi-factor-authentication.md)을 참조하세요.

| Azure AD 테넌트의 유형 | 사용 가능한 신뢰할 수 있는 IP 옵션 |
|:--- |:--- |
| 관리 |<li>특정 IP 주소 범위 – 관리자는 사용자 회사의 인트라넷에서 로그인하는 사용자에 대해 2단계 인증을 바이패스할 수 있는 IP 주소를 지정할 수 있습니다.</li> |
| 페더레이션 |<li>모든 페더레이션된 사용자 - 조직 내에서 로그인하는 모든 페더레이션된 사용자는 AD FS에서 발급한 클레임을 사용하여 2단계 인증을 바이패스합니다.</li><br><li>특정 IP 주소 범위 – 관리자는 사용자 회사의 인트라넷에서 로그인하는 사용자에 대해 2단계 인증을 바이패스할 수 있는 IP 주소를 지정할 수 있습니다. |

이 바이패스는 회사의 인트라넷 내부에서만 작동합니다. 

**회사 네트워크 내부 최종 사용자 환경:**

신뢰할 수 있는 IP를 사용할 수 없는 경우 브라우저 흐름에 2단계 인증이 필요하고 이전 리치 클라이언트 앱에 앱 암호가 필요합니다. 

신뢰할 수 있는 IP가 사용하도록 설정된 경우 브라우저 흐름에는 2단계 인증이 필요하지 *않습니다.* 앱 암호는 사용자가 앱 암호를 아직 만들지 않은 경우, 이전 리치 클라이언트 앱에는 필요하지 *않습니다*. 앱 암호를 사용 중인 경우 필요합니다. 

**회사 네트워크 외부 최종 사용자 환경:**

신뢰할 수 있는 IP 사용 여부에 관계없이 브라우저 흐름에 2단계 인증이 필요하고 이전 리치 클라이언트 앱에 앱 암호가 필요합니다. 

### <a name="to-enable-trusted-ips"></a>신뢰할 수 있는 IP를 활성화하려면
1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 이 문서의 시작 부분에 지침에 따라 MFA 서비스 설정 페이지로 이동합니다.
3. 서비스 설정 페이지의 신뢰할 수 있는 IP에는 두 가지 옵션이 있습니다.
   
   * **내 인트라넷에서 발생하는 페더레이션된 사용자의 요청** - 확인란을 선택합니다. 회사 네트워크에서 로그인 중인 모든 페더레이션된 사용자는 AD FS에서 발급한 클레임을 사용하여 2단계 인증을 바이패스합니다.
   * **공용 IP 중 특정 범위의 요청** - CIDR 표기법으로 제공된 텍스트 상자에 IP 주소를 입력합니다. 예를 들어 xxx.xxx.xxx.1 – xxx.xxx.xxx.254 범위의 IP 주소에 xxx.xxx.xxx.0/24, 또는 단일 IP 주소에 xxx.xxx.xxx.xxx/32입니다. 최대 50개의 IP 주소 범위를 입력할 수 있습니다. 이러한 IP 주소에서 로그인한 사용자는 2단계 인증을 바이패스합니다.
4. **Save**를 클릭합니다.
5. 업데이트를 적용하면 **닫기**를 클릭합니다.

![신뢰할 수 있는 IP](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>앱 암호
Office 2010 이전 및 Apple 메일 등의 일부 앱은 2단계 인증을 지원하지 않습니다. 이들 앱은 두 번째 인증을 허용하도록 구성되어 있지 않습니다. 이러한 앱을 사용하려면 기존의 암호 대신 "앱 암호"를 사용해야 합니다. 앱 암호를 사용하면 응용 프로그램이 2단계 인증을 바이패스하고 작업을 계속할 수 있습니다.

> [!NOTE]
> Office 2013 클라이언트에 대한 최신 인증
> 
> Outlook을 포함한 Office 2013 클라이언트 이상의 버전은 최신 인증 프로토콜을 지원하고 2단계 인증과 함께 사용할 수 있습니다. 이를 사용하면 이러한 클라이언트에 대한 앱 암호는 필요하지 않습니다.  자세한 내용은 [발표된 Office 2013 최신 인증 공개 미리 보기](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)를 참조하세요.

### <a name="important-things-to-know-about-app-passwords"></a>앱 암호에 대해 알아야 할 중요 사항
앱 암호에 대해 알아야 할 중요한 사항 목록은 다음과 같습니다.

* 앱 암호는 입력 상자에 앱당 한 번씩 입력해야 합니다. 사용자는 암호를 매번 추적하여 입력할 필요가 없습니다.
* 실제 암호는 자동으로 생성되고 사용자가 제공하지 않습니다. 자동으로 생성된 암호는 공격자가 추측하기 어렵고 더 안전합니다.
* 사용자당 40개의 암호로 제한되어 있습니다. 
* 암호를 캐시하고 온-프레미스 시나리오에서 사용하는 앱은 앱 암호가 조직 ID 외부에서 알려질 수 없으므로 실패할 수도 있습니다. 예제는 온-프레미스에 있는 Exchange 전자 메일이지만 보관된 메일은 클라우드에 있습니다. 동일한 암호는 작동하지 않습니다.
* 다단계 인증이 시작되면 일부 브라우저 이외의 클라이언트에 사용자 암호를 사용할 수 있습니다. 관리 작업에는 앱 암호를 사용할 수 없습니다. 강력한 암호로 PowerShell 스크립트를 실행할 서비스 계정을 만들고 해당 계정에 2단계 인증을 사용하지 않도록 설정해야 합니다.

> [!WARNING]
> 앱 암호는 클라이언트가 온-프레미스 및 클라우드 자동 검색 끝점과 통신하는 하이브리드 환경에서는 작동하지 않습니다. 도메인 암호가 온-프레미스를 인증하는 데 필요하고 앱 암호가 클라우드를 사용하여 인증하는 데 필요합니다.

### <a name="naming-guidance-for-app-passwords"></a>앱 암호에 대한 명명 지침
앱 암호 이름은 해당 이름이 사용될 장치를 반영해야 합니다. 예를 들어 브라우저 이외 앱이 있는 노트북이 있는 경우 Laptop 이름을 딴 앱 암호를 하나 만들고 해당 앱 암호를 사용합니다. 그런 다음 데스크톱 컴퓨터의 동일한 응용 프로그램에 대해 Desktop 이름을 딴 다른 앱 암호를 만듭니다. 

Microsoft는 응용 프로그램별로 하나의 앱 암호보다는 장치별로 하나의 앱 암호를 만드는 것을 권장합니다.

### <a name="federated-sso-app-passwords"></a>페더레이션된(SSO) 앱 암호
Azure AD는 온-프레미스 Windows Server Active Directory Domain Services(AD DS)로 페더레이션(Single Sign-On)을 지원합니다. 조직이 Azure AD를 사용하여 페더레이션되고 Azure Multi-Factor Authentication을 사용하려는 경우 앱 암호에 대해 정보가 중요합니다. 이 섹션은 페더레이션된 고객(SSO)에게만 적용됩니다.

* Azure AD에서 앱 암호를 확인하기 때문에 페더레이션을 바이패스합니다. 앱 암호를 설정할 때 페더레이션이 능동적으로 사용됩니다.
* 페더레이션된(SSO) 사용자의 경우 수동 흐름과 달리 ID 공급자(IdP)로 이동하지 않습니다. 암호는 조직 ID에 저장됩니다. 사용자가 회사를 떠나는 경우 해당 정보는 실제 시간으로 DirSync를 사용하는 조직 ID에 유입되어야 합니다. 계정 사용 안 함/삭제 설정은 동기화에 최대 3시간이 걸리며 Azure AD에서 앱 암호의 사용 안 함/삭제가 지연됩니다.
* 앱 암호를 사용할 경우 온-프레미스 클라이언트 Access Control 설정은 적용되지 않습니다.
* 온-프레미스 인증 로깅/감사 기능은 앱 암호에 사용할 수 없습니다
* 특정 고급 아키텍처 디자인은 클라이언트와 2단계 인증을 사용하는 경우 조직의 사용자 이름, 암호 및 앱 암호의 조합이 필요할 수 있습니다. 그러나 이것은 인증 위치에 따라 다릅니다. 온-프레미스 인프라에 대해 인증하는 클라이언트의 경우 조직의 사용자 이름과 암호를 사용합니다. Azure AD에 대해 인증하는 클라이언트의 경우 앱 암호를 사용합니다.

  예를 들어 다음과 같은 인스턴스로 구성된 아키텍처가 있다고 가정합니다.

  * Azure AD를 사용하여 Active Directory의 온-프레미스 인스턴스를 페더레이션하고 있습니다
  * 온라인 Exchange를 사용하고 있습니다
  * 특별히 온-프레미스인 Lync를 사용하고 있습니다
  * Azure Multi-Factor Authentication을 사용하고 있습니다

  ![검사](./media/multi-factor-authentication-whats-next/federated.png)

  이러한 경우 다음 단계를 따라야 합니다.

  * Lync에 로그인하는 경우 조직의 사용자 이름 및 암호를 사용합니다.
  * 온라인으로 Exchange에 연결하는 Outlook 클라이언트를 통해 주소록에 액세스하려는 경우 앱 암호를 사용합니다.

### <a name="allow-app-password-creation"></a>앱 암호 만들기 허용
기본적으로 사용자가 앱 암호를 만들 수는 없지만 기능을 직접 사용할 수 있습니다. 사용자가 앱 암호를 만들 수 있으려면 다음 절차를 수행합니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 이 문서의 시작 부분에 지침에 따라 MFA 서비스 설정 페이지로 이동합니다.
3. **사용자가 비 브라우저 앱에 로그인하도록 앱 암호를 만들 수 있습니다** 옆의 라디오 단추가 선택합니다.

![앱 암호 만들기](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>앱 암호 만들기
사용자가 초기 등록을 하는 동안 앱 암호를 만들 수 있습니다. 등록 프로세스가 앱 암호를 만들 수 있도록 하는 등록 프로세스의 끝에 옵션이 제공됩니다.

사용자는 등록 후 앱 암호를 만들 수도 있습니다. Azure Portal 또는 Office 365 포털에서 해당 설정을 변경하여 앱 암호를 만들 수 있습니다 사용자에 대한 자세한 내용과 세부 단계는 [Azure Multi-factor Authentication에서 앱 암호란](./end-user/multi-factor-authentication-end-user-app-passwords.md)을 참조하세요.

## <a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>사용자가 신뢰하는 장치에 대한 Multi-Factor Authentication 기억
사용자가 신뢰하는 장치 및 브라우저에 대한 Multi-Factor Authentication 기억은 모든 MFA 사용자에 대해 무료로 사용할 수 있는 기능입니다. 다단계 인증을 통해 사용자는 로그인 후 설정된 며칠 동안 MFA를 바이패스할 수 있습니다. 이 기능은 사용자가 동일한 장치에서 2단계 인증을 수행하는 횟수를 최소화함으로써 유용성을 향상시킬 수 있습니다.

그러나 계정 또는 장치가 손상된 경우 신뢰할 수 있는 장치의 MFA를 기억해두는 것이 보안에 도움이 될 수 있습니다. 회사 계정이 손상되거나 신뢰할 수 있는 장치를 분실 또는 도난당한 경우 [모든 장치에서 Multi-Factor Authentication을 복원](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user)해야 합니다. 이 작업은 모든 장치에서 신뢰할 수 있는 상태를 해지하고 사용자는 2단계 인증을 다시 수행해야 합니다. [2단계 인증을 위한 설정 관리](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted)의 내용에 따라 사용자에게 자신의 장치에서 MFA를 복원하도록 지시할 수도 있습니다.

### <a name="how-it-works"></a>작동 방법

사용자가 로그인 시 "**X**일 동안 다시 묻지 않음" 상자를 선택한 경우 브라우저에서 영구 쿠키를 설정하여 Multi-Factor Authentication을 작동합니다. 쿠키가 만료될 때까지는 해당 브라우저에서 MFA를 다시 요청하는 메시지가 사용자에게 표시되지 않습니다. 사용자가 동일한 장치에서 다른 브라우저를 열거나 쿠키를 지우는 경우 다시 확인하라는 메시지가 표시됩니다. 

"**X**일 동안 다시 묻지 않음" 확인란은 최신 인증의 지원 여부에 관계없이 비브라우저 앱에는 표시되지 않습니다. 이러한 앱에서는 1시간마다 새로운 액세스 토큰을 제공하는 새로 고침 토큰을 사용합니다. 새로 고침 토큰의 유효성이 확인되면 Azure AD가 2단계 인증을 수행한 마지막 시간이 구성된 일 수 내에 있는지 확인합니다. 

결론적으로 신뢰할 수 있는 장치에서 MFA를 기억하면 웹앱의 인증 횟수가 줄어듭니다(일반적으로는 인증할 때마다 요청). 하지만 최신 인증 클라이언트에서는 인증 횟수가 늘어납니다(일반적으로 90일마다 요청).

> [!NOTE]
>이 기능은 사용자가 Azure MFA Server 또는 타사 MFA 솔루션을 통해 AD FS에 대해 2단계 인증을 수행할 때 AD FS의 "로그인 유지" 기능과는 호환되지 않습니다. 사용자가 AD FS에서 "로그인 유지"를 선택하고 장치를 MFA에 대해 신뢰할 수 있는 것으로 표시한 경우 "MFA 기억" 일 수가 만료된 후에는 확인할 수 없습니다. Azure AD는 새로운 2단계 인증을 요청하지만 AD FS는 2단계 인증을 다시 수행하는 대신, 원본 MFA 클레임 및 날짜와 함께 토큰을 반환합니다. 이 프로세스에서는 Azure AD 및 AD FS 간의 확인 루프가 해제 설정됩니다. 

### <a name="enable-remember-multi-factor-authentication"></a>Multi-Factor Authentication 기억 사용
1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 이 문서의 시작 부분에 지침에 따라 MFA 서비스 설정 페이지로 이동합니다.
3. 서비스 설정 페이지의 사용자 장치 설정 관리 아래에서 **사용자가 신뢰하는 장치에 Multi-Factor Authentication을 기억하도록 허용** 상자를 선택합니다.
   ![장치 기억](./media/multi-factor-authentication-whats-next/remember.png)
4. 신뢰할 수 있는 장치가 2단계 인증을 바이패스하는 일수를 설정합니다. 기본값은 14일입니다.
5. **저장**을 클릭합니다.
6. **닫기**를 클릭합니다.

### <a name="mark-a-device-as-trusted"></a>장치를 신뢰할 수 있음으로 표시

이 기능을 사용하면 사용자는 **다시 묻지 않음**을 선택하여 로그인할 때 장치를 신뢰할 수 있음으로 표시할 수 있습니다.

![다시 묻지 않음 - 스크린샷](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>선택 가능한 확인 방법
사용자에 대해 어떤 인증 방법을 사용할지를 선택할 수 있습니다. 다음 표에서는 각 방법의 간략한 개요를 제공합니다.

사용자가 자신의 계정을 MFA를 등록하는 경우 사용하도록 설정한 옵션에서 해당하는 기본 확인 방법을 선택합니다. 등록 프로세스에 대한 지침은 [2단계 인증에 내 계정 설정](multi-factor-authentication-end-user-first-time.md)에서 다룹니다.

| 메서드 | 설명 |
|:--- |:--- |
| 휴대폰에 전화 걸기 |자동 음성 전화를 겁니다. 사용자가 전화를 받고 휴대폰 키패드에서 #을 눌러 인증합니다. 이 전화 번호는 온-프레미스 Active Directory와 동기화되지 않습니다. |
| 휴대폰에 문자 메시지 전송 |확인 코드를 포함하는 문자 메시지를 보냅니다. 사용자는 확인 코드로 문자 메시지에 응답하거나 또는 로그인 인터페이스에 확인 코드를 입력하도록 요구됩니다. |
| 모바일 앱을 통한 알림 |휴대폰이나 등록된 장치로 푸시 알림을 보냅니다. 사용자는 알림을 보고 **확인**을 선택하여 인증을 완료합니다. <br>[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073) 장치의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다. |
| 모바일 앱의 확인 코드 |Microsoft Authenticator 앱은 30초마다 새로운 OATH 확인 코드를 생성합니다. 사용자는 로그인 인터페이스에 이 확인 코드를 입력합니다.<br>[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073) 장치의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다. |

### <a name="how-to-enabledisable-authentication-methods"></a>인증 방법을 활성화/비활성화하는 방법
1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 이 문서의 시작 부분에 지침에 따라 MFA 서비스 설정 페이지로 이동합니다.
3. 서비스 설정 페이지의 확인 옵션에서 사용할 옵션을 선택/선택 취소합니다.
   ![확인 옵션](./media/multi-factor-authentication-whats-next/authmethods.png)
4. **저장**을 클릭합니다.
5. **닫기**를 클릭합니다.
