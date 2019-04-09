---
title: Azure Active Directory의 조건부 액세스를 사용 하 여 클라우드 앱 액세스를 위해 앱 보호 정책을 요구 하는 방법 | Microsoft Docs
description: Azure Active Directory의 조건부 액세스를 사용 하 여 클라우드 앱 액세스를 위해 앱 보호 정책을 요구 하는 방법에 알아봅니다.
services: active-directory
keywords: 앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 4/4/2019
ms.author: joflore
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: f695d50e251d0104cf9f0d38fe4489a0e66dfe15
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288503"
---
# <a name="how-to-require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>방법: 조건부 액세스 (미리 보기)를 사용 하 여 클라우드 앱 액세스를 위해 앱 보호 정책 필요

직원은 개인 및 회사 작업에 대해 모바일 디바이스를 사용합니다. 직원이 생산적이 될 수 있도록 하는 반면 데이터 손실을 방지하려고 합니다. Azure Active Directory (Azure AD) 조건부 액세스를 사용 하 여 처음에 앱 보호 정책이 있는 클라이언트 앱에 클라우드 앱에 대 한 액세스를 제한 하 여 회사 데이터를 보호할 수 있습니다.

이 항목에서는 앱 보호 정책 데이터에 액세스 하기 전에 필요할 수 있는 조건부 액세스 정책을 구성 하는 방법에 설명 합니다.

## <a name="overview"></a>개요

[Azure AD 조건부 액세스](overview.md)를 사용하여 권한 있는 사용자가 리소스를 액세스하는 방법을 미세 조정할 수 있습니다. 예를 들어 클라우드 앱에 대한 액세스를 신뢰할 수 있는 디바이스로 제한할 수 있습니다.

[Intune 앱 보호 정책](https://docs.microsoft.com/intune/app-protection-policy)을 사용하여 회사의 데이터를 보호할 수 있습니다. Intune 앱 보호 정책은 디바이스 관리 솔루션에 디바이스를 등록하거나 등록하지 않고 회사의 데이터를 보호할 수 있도록 하는 MDM(모바일 디바이스 관리) 솔루션이 필요하지 않습니다.

Azure Active Directory 조건부 액세스를 Intune 앱 보호 정책 받기로 Azure ad 보고 클라이언트 응용 프로그램에 클라우드 앱에 대 한 액세스를 제한 합니다. 예를 들어 있는 Intune 앱 보호 정책을 Outlook 앱에 Exchange Online에 대 한 액세스를 제한할 수 있습니다.

조건부 액세스 용어에서 이러한 클라이언트 앱 정책 보호 된 것으로 알려져 있는 **앱 보호 정책을**합니다.  

![조건부 액세스](./media/app-protection-based-conditional-access/05.png)

정책 목록을 보호 된 클라이언트 앱을 참조 하세요 [앱 보호 정책 요구 사항을](technical-reference.md#approved-client-app-requirement)합니다.

와 같은 다른 정책 사용 하 여 앱 보호-기반 조건부 액세스 정책을 결합할 수 있습니다 [장치 기반 조건부 액세스 정책을](require-managed-devices.md) 개인 및 회사 장치에 대 한 데이터를 보호 하는 방법에 유연성을 제공 합니다.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>앱 보호 기반 조건부 액세스 요구 사항의 이점

관리 것과 유사한 준수 iOS 및 Android에 대 한 Intune에 의해 보고 이제 Intune 장치 보고서를 Azure AD 조건부 액세스는 액세스 검사를 사용할 수 있도록 앱 보호 정책이 적용 된 경우. 이 새 조건부 액세스 정책과 **앱 보호 정책을** 와 같은 관리 오류 로부터 보호 하 여 보안을 강화 합니다.

- Intune 라이선스가 없는 사용자
- Intune 앱 보호 정책을 받을 수 없는 사용자
- Intune 앱 보호 정책 앱에 정책을 수신 하도록 구성 되지 않았습니다


## <a name="before-you-begin"></a>시작하기 전에

이 토픽은 다음 항목에 대해 잘 알고 있다고 가정합니다.

- 합니다 [앱 보호 정책 요구 사항을](technical-reference.md#app-protection-policy-requirement) 기술 참조입니다.

- [승인된 클라이언트 앱 요구 사항](technical-reference.md#approved-client-app-requirement) 기술 참조.

- [Azure Active Directory의 조건부 액세스](overview.md)에 대한 기본 개념.

- [조건부 액세스 정책을 구성하는](app-based-mfa.md) 방법.


## <a name="prerequisites"></a>필수 조건

앱 보호 기반 조건부 액세스 정책을 만들려면.
- Enterprise Mobility + Security 또는 Azure Active Directory premium 구독 + Intune가
- 사용자는 EMS 또는 Azure AD에 대 한 라이선스를 취득 확인 + Intune
- 클라이언트 앱을 앱 보호 정책을 받으려면 Intune에서 구성 되었는지 확인
- 사용자는 Intune 앱 보호 정책을 받으려면 Intune에서 구성 되었는지 확인

## <a name="app-protection-based-policy-for-exchange-online"></a>Exchange Online에 대 한 앱 보호 기반 정책

이 시나리오는 Exchange Online에 액세스 하기 위한 앱 보호 기반 조건부 액세스 정책으로 구성 됩니다.

### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음과 같이 사용자를 가정합니다.

- iOS 또는 Android에는 기본 메일 애플리케이션을 사용하여 Exchange에 연결하도록 이메일을 구성합니다.

- 액세스는 Outlook 앱을 사용할 때만 가능하다는 것을 알리는 전자 메일을 수신합니다.

- 링크가 포함된 애플리케이션을 다운로드합니다.

- Outlook 애플리케이션을 열고 Azure AD 자격 증명으로 로그인합니다.

- 계속하려면 Authenticator(iOS) 또는 회사 포털(Android)을 설치하라는 메시지가 표시되었습니다.

- 애플리케이션을 설치하고 Outlook 앱으로 돌아가서 계속할 수 있습니다.

- 디바이스를 등록하라는 메시지가 표시되었습니다.

- Intune 앱 보호 정책을 받을 수합니다

- 전자 메일에 액세스할 수 있습니다.

Intune 앱 보호 정책이 회사 데이터에 액세스 하려면 응용 프로그램에 있어야 하며 응용 프로그램을 다시 시작, (응용 프로그램 및 플랫폼에 대해 구성 된) 경우 추가 PIN 등을 사용 하 여 사용자를 묻는 메시지가 나타납니다.

### <a name="configuration"></a>구성

**1 단계-Exchange Online에 대 한 Azure AD 조건부 액세스 정책 구성**

이 단계의 조건부 액세스 정책의 경우 다음 구성 요소를 구성해야 합니다.

![조건부 액세스](./media/app-protection-based-conditional-access/01.png)

1. 조건부 액세스 정책의 **이름**.

2. **사용자 및 그룹**: 각 조건부 액세스 정책에는 선택한 사용자 또는 그룹이 하나 이상 있어야 합니다.

3. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online**을 선택해야 합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/07.png)

4. **조건:** **조건**으로 **디바이스 플랫폼** 및 **클라이언트 앱**을 구성해야 합니다.

    a. **디바이스 플랫폼**으로 **Android** 및 **iOS**를 선택합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/03.png)

    b. **클라이언트 앱(미리 보기)** 으로 **모바일 앱 및 데스크톱 앱** 및 **최신 인증 클라이언트**를 선택합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/91.png)

5. 로 **액세스 제어**, 해야 **앱 보호 정책 (미리 보기) 필요** 선택 합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/05.png)
 

**2 단계-Exchange online Active Sync (EAS) 사용 하 여 Azure AD 조건부 액세스 정책 구성**

이 단계의 조건부 액세스 정책의 경우 다음 구성 요소를 구성해야 합니다.

![조건부 액세스](./media/app-protection-based-conditional-access/06.png)

1. 조건부 액세스 정책의 **이름**.

2. **사용자 및 그룹**: 각 조건부 액세스 정책에는 선택한 사용자 또는 그룹이 하나 이상 있어야 합니다.


3. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online**을 선택해야 합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/07.png)

4. **조건:** **조건**으로 **클라이언트 앱(미리 보기)** 을 구성해야 합니다. 

    a. **클라이언트 앱(미리 보기)** 으로 **모바일 앱 및 데스크톱 클라이언트** 및 **Exchange ActiveSync 클라이언트**를 선택합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/92.png)

    b. 로 **액세스 제어**, 해야 **앱 보호 정책 (미리 보기) 필요** 선택 합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/05.png)


**3 단계-iOS 및 Android 클라이언트 응용 프로그램에 대 한 Intune 앱 보호 정책 구성**


![조건부 액세스](./media/app-protection-based-conditional-access/09.png)

자세한 정보는 [Microsoft Intune으로 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)를 참조하세요.



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Exchange Online에 대 한 앱 보호 기반 또는 준수 장치 정책

이 시나리오는 Exchange Online에 액세스 하기 위한 앱 보호 기반 또는 준수 장치 조건부 액세스 정책으로 구성 됩니다.


### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음을 가정합니다.
 
- 일부 사용자가 (유무와 관계 없이 회사 장치)에 이미 등록

- 앱 보호 애플리케이션을 사용하여 Azure AD에 등록되지 않은 사용자는 리소스에 액세스하려면 디바이스를 등록해야 합니다.

- 앱 보호 애플리케이션을 사용하는 등록된 사용자는 디바이스에 다시 등록할 필요가 없습니다.

- 사용자는 Intune 앱 보호 정책을 받을 수 없으면 등록

- 사용자는 Intune 앱 보호 정책을 Outlook와 메일에 액세스할 수 없으면 등록

- 장치를 등록 한 경우 사용자가 outlook 전자 메일을 액세스할 수 있습니다.


### <a name="configuration"></a>구성

**1 단계-Exchange Online에 대 한 Azure AD 조건부 액세스 정책 구성**

이 단계의 조건부 액세스 정책의 경우 다음 구성 요소를 구성해야 합니다.

![조건부 액세스](./media/app-protection-based-conditional-access/62.png)

1. 조건부 액세스 정책의 **이름**.

2. **사용자 및 그룹**: 각 조건부 액세스 정책에는 선택한 사용자 또는 그룹이 하나 이상 있어야 합니다.

3. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online**을 선택해야 합니다. 

     ![조건부 액세스](./media/app-protection-based-conditional-access/07.png)

4. **조건:** **조건**으로 **디바이스 플랫폼** 및 **클라이언트 앱**을 구성해야 합니다. 
 
    a. **디바이스 플랫폼**으로 **Android** 및 **iOS**를 선택합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/03.png)

    b. **클라이언트 앱(미리 보기)** 으로 **모바일 앱 및 데스크톱 클라이언트** 및 **최신 인증 클라이언트**를 선택합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/91.png)

5. **액세스 제어**로 다음을 선택해야 합니다.

   - **디바이스를 준수 상태로 표시해야 함**

   - **앱 보호 정책(미리 보기) 필요**

   - **선택된 컨트롤 중 하나 필요**   
 
     ![조건부 액세스](./media/app-protection-based-conditional-access/11.png)



**2 단계-Exchange online Active Sync (EAS) 사용 하 여 Azure AD 조건부 액세스 정책 구성**

이 단계의 조건부 액세스 정책의 경우 다음 구성 요소를 구성해야 합니다.

![조건부 액세스](./media/app-protection-based-conditional-access/06.png)

1. 조건부 액세스 정책의 **이름**.

2. **사용자 및 그룹**: 각 조건부 액세스 정책에는 선택한 사용자 또는 그룹이 하나 이상 있어야 합니다.

3. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online**을 선택해야 합니다. 

    ![조건부 액세스](./media/app-protection-based-conditional-access/07.png)

4. **조건:** **조건**으로 **클라이언트 앱**을 구성해야 합니다. 

    **클라이언트 앱(미리 보기)** 으로 **모바일 앱 및 데스크톱 클라이언트** 및 **Exchange ActiveSync 클라이언트**를 선택합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/92.png)

5. **액세스 제어**로 다음을 선택해야 합니다.

   - **디바이스를 준수 상태로 표시해야 함**

   - **앱 보호 정책(미리 보기) 필요**

   - **선택된 컨트롤 중 하나 필요**   
    ![조건부 액세스](./media/app-protection-based-conditional-access/11.png)



**3 단계-iOS 및 Android 클라이언트 응용 프로그램에 대 한 Intune 앱 보호 정책 구성**


![조건부 액세스](./media/app-protection-based-conditional-access/09.png)

자세한 정보는 [Microsoft Intune으로 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)를 참조하세요.





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Exchange Online에 대 한 앱 보호 기반 및 준수 장치 정책

이 시나리오는 Exchange Online에 액세스 하기 위한 앱을 기반으로 보호 및 준수 장치 조건부 액세스 정책으로 구성 됩니다.


### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음과 같이 사용자를 가정합니다.
 
-   iOS 또는 Android에는 기본 메일 애플리케이션을 사용하여 Exchange에 연결하도록 이메일을 구성합니다.
-   액세스하려면 디바이스를 등록해야 함을 알리는 전자 메일을 수신합니다.
-   회사 포털을 다운로드하고 회사 포털에 로그인합니다.
-   메일을 확인하고 Outlook 앱을 사용하도록 요청받았습니다.
-   Outlook 앱을 다운로드합니다.
-   Outlook 앱을 열고가 등록에 사용되는 자격 증명을 입력합니다.
-   Intune 앱 보호 정책을 받을 수합니다
-   Outlook 및 Intune 앱 보호 정책을 사용 하 여 전자 메일에 액세스할 수

Intune 앱 보호 정책이 회사 데이터에 액세스 하기 전에 활성화 되 고 응용 프로그램을 다시 시작, (응용 프로그램 및 플랫폼에 대해 구성 된) 경우 추가 PIN 등을 사용 하 여 사용자를 묻는 메시지가 나타납니다.


### <a name="configuration"></a>구성

**1 단계-Exchange Online에 대 한 Azure AD 조건부 액세스 정책 구성**

이 단계의 조건부 액세스 정책의 경우 다음 구성 요소를 구성해야 합니다.

![조건부 액세스](./media/app-protection-based-conditional-access/01.png)

1. 조건부 액세스 정책의 **이름**.

2. **사용자 및 그룹**: 각 조건부 액세스 정책에는 선택한 사용자 또는 그룹이 하나 이상 있어야 합니다.

3. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online**을 선택해야 합니다. 

     ![조건부 액세스](./media/app-protection-based-conditional-access/07.png)

4. **조건:** **조건**으로 **디바이스 플랫폼** 및 **클라이언트 앱**을 구성해야 합니다. 
 
    a. **디바이스 플랫폼**으로 **Android** 및 **iOS**를 선택합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/03.png)

    b. **클라이언트 앱(미리 보기)** 으로 **모바일 앱 및 데스크톱 앱** 및 **최신 인증 클라이언트**를 선택합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/91.png)

5. **액세스 제어**로 다음을 선택해야 합니다.

   - **디바이스를 준수 상태로 표시해야 함**

   - **앱 보호 정책(미리 보기) 필요**

   - **선택된 컨트롤이 모두 필요**   
 
     ![조건부 액세스](./media/app-protection-based-conditional-access/13.png)



**2 단계-Exchange online Active Sync (EAS) 사용 하 여 Azure AD 조건부 액세스 정책 구성**

이 단계의 조건부 액세스 정책의 경우 다음 구성 요소를 구성해야 합니다.

![조건부 액세스](./media/app-protection-based-conditional-access/06.png)

1. 조건부 액세스 정책의 **이름**.

2. **사용자 및 그룹**: 각 조건부 액세스 정책에는 선택한 사용자 또는 그룹이 하나 이상 있어야 합니다.

3. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online**을 선택해야 합니다. 

    ![조건부 액세스](./media/app-protection-based-conditional-access/07.png)

4. **조건:** **조건**으로 **클라이언트 앱(미리 보기)** 을 구성해야 합니다. 

    **클라이언트 앱(미리 보기)** 으로 **모바일 앱 및 데스크톱 클라이언트** 및 **Exchange ActiveSync 클라이언트**를 선택합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/92.png)

5. **액세스 제어**로 다음을 선택해야 합니다.

   - **디바이스를 준수 상태로 표시해야 함**

   - **승인 된 클라이언트 앱 (미리 보기) 필요**

   - **선택된 컨트롤이 모두 필요**   
 
     ![조건부 액세스](./media/app-protection-based-conditional-access/13.png)




**3 단계-iOS 및 Android 클라이언트 응용 프로그램에 대 한 Intune 앱 보호 정책 구성**


![조건부 액세스](./media/app-protection-based-conditional-access/09.png)

자세한 정보는 [Microsoft Intune으로 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)를 참조하세요.


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online 및 SharePoint Online에 대 한 앱 보호 또는 앱 기반 정책

이 시나리오는 Exchange Online 및 SharePoint Online에 액세스 하기 위한 앱 보호 기반 또는 승인 된 앱 정책으로 구성 됩니다.


### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음과 같이 사용자를 가정합니다.

- 클라이언트 응용 프로그램을 어느 구성 앱 보호 정책 요구 사항 또는 승인 된 앱 요구 사항을 지 원하는 앱 목록에 있습니다.  
- 앱 보호 정책 요구 사항을 충족 하는 사용자는 클라이언트 응용 프로그램에는 Intune 앱 보호 정책을 받을 수 있습니다.
- Intune 앱 보호 정책을 지 원하는 승인 된 앱 정책 요구 사항을 충족 하는 클라이언트 응용 프로그램을 사용 하는 사용자
- 전자 메일 또는 문서에 액세스 하는 응용 프로그램을 엽니다.
- Outlook 애플리케이션을 열고 Azure AD 자격 증명으로 로그인합니다.
- 계속 하려면 Authenticator (iOS) 또는 회사 포털 (Android)를 설치 하 라는 메시지가 표시 (설치 되지 않은 경우)
- 애플리케이션을 설치하고 Outlook 앱으로 돌아가서 계속할 수 있습니다.
- 디바이스를 등록하라는 메시지가 표시되었습니다.
- Intune 앱 보호 정책을 받을 수합니다
- Outlook 및 Intune 앱 보호 정책을 사용 하 여 전자 메일에 액세스할 수
- 사이트/앱 보호 정책 요구 사항에 없는 앱을 사용 하 여 문서에 액세스할 수 있지만 승인 된 앱 요구 사항에 나열 됩니다.

Intune 앱 보호 정책이 회사 데이터에 액세스 하기 전에 필요한 및 응용 프로그램을 다시 시작, (응용 프로그램 및 플랫폼에 대해 구성 된) 경우 추가 PIN 등을 사용 하 여 사용자 라는 메시지가 표시 될 수 있습니다.

**설명**

- 앱 보호 및 앱 기반 조건부 액세스 정책을 모두 지원 하려는 경우이 시나리오를 사용할 수 있습니다.

- 이 *또는* 정책, 앱 **앱 보호 정책** 액세스에 대 한 요구 사항 평가 먼저 **승인 된 클라이언트 앱** 요구 사항.

### <a name="configuration"></a>구성

**1 단계-Exchange Online에 대 한 Azure AD 조건부 액세스 정책 구성**

이 단계의 조건부 액세스 정책의 경우 다음 구성 요소를 구성해야 합니다.

![조건부 액세스](./media/app-protection-based-conditional-access/62.png)

1. 조건부 액세스 정책의 **이름**.

2. **사용자 및 그룹**: 각 조건부 액세스 정책에는 선택한 사용자 또는 그룹이 하나 이상 있어야 합니다.

3. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online**을 선택해야 합니다. 

     ![조건부 액세스](./media/app-protection-based-conditional-access/02.png)

4. **조건:** **조건**으로 **디바이스 플랫폼** 및 **클라이언트 앱**을 구성해야 합니다. 
 
    a. **디바이스 플랫폼**으로 **Android** 및 **iOS**를 선택합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/03.png)

    b. **클라이언트 앱(미리 보기)** 으로 **모바일 앱 및 데스크톱 앱** 및 **최신 인증 클라이언트**를 선택합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/91.png)

5. **액세스 제어**로 다음을 선택해야 합니다.

   - **승인된 클라이언트 앱 필요**

   - **앱 보호 정책(미리 보기) 필요**

   - **선택된 컨트롤 중 하나 필요**   
 
     ![조건부 액세스](./media/app-protection-based-conditional-access/12.png)


**2 단계-iOS 및 Android 클라이언트 응용 프로그램에 대 한 Intune 앱 보호 정책 구성**


![조건부 액세스](./media/app-protection-based-conditional-access/09.png)

자세한 정보는 [Microsoft Intune으로 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)를 참조하세요.




## <a name="next-steps"></a>다음 단계

조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory 조건부 액세스를 사용하는 특정 앱에 MFA 요구](app-based-mfa.md)를 참조하세요.

사용자 환경에 대한 조건부 액세스 정책을 구성할 준비가 완료된 경우 [Azure Active Directory의 조건부 액세스 모범 사례](best-practices.md)를 참조하세요. 