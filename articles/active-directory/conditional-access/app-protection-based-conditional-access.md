---
title: Azure Active Directory의 조건부 액세스를 사용 하 여 클라우드 앱 액세스를 위해 앱 보호 정책 필요 | Microsoft Docs
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
ms.openlocfilehash: 2250449c0ef342332945b80cb10cb9a02885b259
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496933"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>조건부 액세스 (미리 보기)를 사용 하 여 클라우드 앱 액세스를 위해 앱 보호 정책 필요

직원은 개인 및 회사 작업에 대해 모바일 디바이스를 사용합니다. 직원이 생산적이 될 수 있도록 하는 반면 데이터 손실을 방지하려고 합니다. Azure Active Directory (Azure AD) 조건부 액세스를 사용 하 여 클라우드 앱에 대 한 액세스를 제한 하 여 회사 데이터를 보호할 수 있습니다. 먼저 앱 보호 정책을 사용 하 여 클라이언트 앱을 사용 합니다.

이 문서에서는 데이터에 액세스 하기는 앱 보호 정책이 필요할 수 있는 조건부 액세스 정책을 구성 하는 방법에 설명 합니다.

## <a name="overview"></a>개요

[Azure AD 조건부 액세스](overview.md)를 사용하여 권한 있는 사용자가 리소스를 액세스하는 방법을 미세 조정할 수 있습니다. 예를 들어 클라우드 앱에 대한 액세스를 신뢰할 수 있는 디바이스로 제한할 수 있습니다.

[Intune 앱 보호 정책](https://docs.microsoft.com/intune/app-protection-policy)을 사용하여 회사의 데이터를 보호할 수 있습니다. Intune 앱 보호 정책에는 모바일 장치 관리 (MDM) 솔루션을 필요 하지 않습니다. 장치 관리 솔루션에 장치 등록 없이 사용 하 여 회사 데이터를 보호할 수 있습니다.

Azure Active Directory 조건부 액세스를 Intune 앱 보호 정책 받기로 Azure ad 보고 클라이언트 응용 프로그램에 클라우드 앱에 대 한 액세스를 제한 합니다. 예를 들어 있는 Intune 앱 보호 정책을 Outlook 앱에 Exchange Online에 대 한 액세스를 제한할 수 있습니다.

조건부 액세스 용어에서 이러한 클라이언트 앱 정책 보호 된 것으로 알려져 있는 *앱 보호 정책을*합니다.  

![조건부 액세스](./media/app-protection-based-conditional-access/05.png)

정책으로 보호 된 클라이언트 앱의 목록을 참조 하세요 [앱 보호 정책 요구 사항을](technical-reference.md#approved-client-app-requirement)합니다.

와 같은 다른 정책 사용 하 여 앱 보호-기반 조건부 액세스 정책을 결합할 수 있습니다 [장치 기반 조건부 액세스 정책을](require-managed-devices.md)합니다. 이러한 방식으로 개인 및 회사 장치에 대 한 데이터를 보호 하는 방법에 유연성을 제공할 수 있습니다.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>앱 보호 기반 조건부 액세스 요구 사항의 이점

보고서 경우 앱 보호 정책 Azure AD에 적용 되는 이제 Intune 관리 장치에 대 한 iOS 및 Android 용 Intune을 통해 보고 되는 준수와 유사 합니다. 조건부 액세스는 액세스 확인으로이 정책을 사용 합니다. 이 새 조건부 액세스 정책, 앱 보호 정책, 보안을 강화 합니다. 관리 오류에 대 한 예 보호:

- Intune 라이선스가 없는 사용자입니다.
- 사용자는 Intune 앱 보호 정책을 받을 수 없습니다.
- Intune 앱 보호 정책 앱 정책을 수신 하도록 구성 되지 않습니다.


## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 사용자가 다음에 대해 잘 알고 있다고 가정합니다.

- 합니다 [앱 보호 정책 요구 사항을](technical-reference.md#app-protection-policy-requirement) 기술 참조입니다.
- [승인된 클라이언트 앱 요구 사항](technical-reference.md#approved-client-app-requirement) 기술 참조.
- [Azure Active Directory의 조건부 액세스](overview.md)에 대한 기본 개념.
- [조건부 액세스 정책을 구성하는](app-based-mfa.md) 방법.


## <a name="prerequisites"></a>필수 조건

앱 보호 기반 조건부 액세스 정책을 만들려면 다음을 수행 해야 합니다.

- Enterprise Mobility + Security 또는 Azure Active Directory premium 구독을 Intune에 있어야 합니다.
- Enterprise Mobility + Security 또는 Azure AD에 대 한 사용자는 라이선스를 취득 했는지 + Intune.
- 클라이언트 앱을 앱 보호 정책을 받으려면 Intune에서 구성 되어 있는지 확인 하십시오.
- 사용자는 Intune 앱 보호 정책을 받으려면 Intune에서 구성 되어 있는지 확인 하십시오.

## <a name="app-protection-based-policy-for-exchange-online"></a>Exchange Online에 대 한 앱 보호 기반 정책

이 시나리오는 Exchange Online에 액세스 하기 위한 앱 보호 기반 조건부 액세스 정책으로 구성 됩니다.

### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음과 같이 사용자를 가정합니다.

- IOS 또는 Android에서 네이티브 메일 응용 프로그램을 사용 하 여 Exchange에 연결 하 여 전자 메일을 구성 합니다.
- Outlook 앱을 사용 하 여 액세스를 사용할 수 있는지 여부를 나타내는 전자 메일을 받습니다.
- 링크를 사용 하 여 응용 프로그램을 다운로드합니다.
- 가 Outlook 응용 프로그램 및 Azure AD 자격 증명으로 로그인 합니다.
- IOS 용 Microsoft Authenticator 또는 계속 사용 하 여 Android 용 Intune 회사 포털을 설치 하 라는 메시지가 표시 됩니다.
- 응용 프로그램을 설치 하 고 계속 하려면 Outlook 앱에 반환 합니다.
- 장치를 등록 하 라는 메시지가 표시 됩니다.
- Intune 앱 보호 정책을 받을 수 있습니다.
- 전자 메일에 액세스할 수 있습니다.

Intune 앱 보호 정책이 회사 데이터에 액세스 하려면 응용 프로그램에 있어야 합니다. 정책에는 사용자가 응용 프로그램을 다시 시작 하거나 추가 PIN을 사용 하 여 요청할 수 있습니다. 이 경우 응용 프로그램 및 플랫폼에 대 한 정책 구성 된 경우입니다.

### <a name="configuration"></a>구성

**1단계: Exchange Online에 대 한 Azure AD 조건부 액세스 정책 구성**

이 단계에서는 조건부 액세스 정책의 다음 구성 요소:

![조건부 액세스](./media/app-protection-based-conditional-access/01.png)

1. 조건부 액세스 정책의 이름을 입력 합니다.

2. 아래 **할당**의 **사용자 및 그룹**적어도 한 명의 사용자를 선택 하거나 각 조건부 액세스 정책에 대 한 그룹입니다.

3. **클라우드 앱**를 선택 **Office 365 Exchange Online**합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/07.png)

4. **조건을**, 구성 **장치 플랫폼** 하 고 **클라이언트 앱 (미리 보기)**:

    a. **장치 플랫폼**를 선택 **Android** 하 고 **iOS**합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/03.png)

    b. **클라이언트 앱 (미리 보기)** 를 선택 **모바일 앱 및 데스크톱 클라이언트** 하 고 **최신 인증 클라이언트**합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/91.png)

5. 아래 **액세스 제어**를 선택 **앱 보호 정책 (미리 보기)를 필요한**합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/05.png)
 

**2단계: Exchange online ActiveSync (EAS) 사용 하 여 Azure AD 조건부 액세스 정책 구성**

이 단계에서는 조건부 액세스 정책의 다음 구성 요소:

![조건부 액세스](./media/app-protection-based-conditional-access/06.png)

1. 조건부 액세스 정책의 이름을 입력 합니다.

2. 아래 **할당**의 **사용자 및 그룹**적어도 한 명의 사용자를 선택 하거나 각 조건부 액세스 정책에 대 한 그룹입니다.


3. **클라우드 앱**를 선택 **Office 365 Exchange Online**합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/07.png)

4. **조건을**, 구성 **클라이언트 앱 (미리 보기)** 합니다. 

    a. **클라이언트 앱 (미리 보기)** 를 선택 **모바일 앱 및 데스크톱 클라이언트** 하 고 **Exchange ActiveSync 클라이언트**합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/92.png)

    b. 아래 **액세스 제어**를 선택 **앱 보호 정책 (미리 보기)를 필요한**합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/05.png)


**3단계: IOS 및 Android 클라이언트 응용 프로그램에 대 한 Intune 앱 보호 정책 구성**


![조건부 액세스](./media/app-protection-based-conditional-access/09.png)

자세한 내용은 [Microsoft Intune로 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)합니다.



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Exchange Online에 대 한 앱 보호 기반 또는 준수 장치 정책

이 시나리오는 Exchange Online에 액세스 하기 위한 앱 보호 기반 또는 준수 장치 조건부 액세스 정책으로 구성 됩니다.


### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음을 가정합니다.
 
- 사용자가 회사 장치 없이 이미 등록 됩니다.
- 응용 프로그램 리소스에 액세스 하는 장치를 등록 해야를 보호 하는 사용자에 게 등록 및 앱을 사용 하 여 Azure AD에 등록 되지 않습니다.
- 앱 보호 응용 프로그램을 사용 하는 등록 된 사용자는 장치를 다시 등록 하지 않아도 됩니다.
- 사용자 등록 없으면 Intune 앱 보호 정책을 받을 수 있습니다.
- 사용자 등록 없으면 Outlook 및 Intune 앱 보호 정책을 사용 하 여 전자 메일에 액세스할 수 있습니다.
- 사용자는 장치를 등록 한 경우 outlook 전자 메일을 액세스할 수 있습니다.


### <a name="configuration"></a>구성

**1단계: Exchange Online에 대 한 Azure AD 조건부 액세스 정책 구성**

이 단계에서는 조건부 액세스 정책의 다음 구성 요소:

![조건부 액세스](./media/app-protection-based-conditional-access/62.png)

1. 조건부 액세스 정책의 이름을 입력 합니다.

2. 아래 **할당**의 **사용자 및 그룹**적어도 한 명의 사용자를 선택 하거나 각 조건부 액세스 정책에 대 한 그룹입니다.

3. **클라우드 앱**를 선택 **Office 365 Exchange Online**합니다. 

     ![조건부 액세스](./media/app-protection-based-conditional-access/07.png)

4. **조건을**, 구성 **장치 플랫폼** 하 고 **클라이언트 앱 (미리 보기)** 합니다. 
 
    a. **장치 플랫폼**를 선택 **Android** 하 고 **iOS**합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/03.png)

    b. **클라이언트 앱 (미리 보기)** 를 선택 **모바일 앱 및 데스크톱 클라이언트** 하 고 **최신 인증 클라이언트**합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/91.png)

5. 아래 **액세스 제어**, 다음 옵션을 선택 합니다.

   - **디바이스를 준수 상태로 표시해야 함**

   - **앱 보호 정책(미리 보기) 필요**

   - **선택된 컨트롤 중 하나 필요**   
 
     ![조건부 액세스](./media/app-protection-based-conditional-access/11.png)



**2단계: ActiveSync를 사용 하 여 Exchange Online에 대 한 Azure AD 조건부 액세스 정책 구성**

이 단계에서는 조건부 액세스 정책의 다음 구성 요소:

![조건부 액세스](./media/app-protection-based-conditional-access/06.png)

1. 조건부 액세스 정책의 이름을 입력 합니다.

2. 아래 **할당**의 **사용자 및 그룹**적어도 한 명의 사용자를 선택 하거나 각 조건부 액세스 정책에 대 한 그룹입니다.

3. **클라우드 앱**를 선택 **Office 365 Exchange Online**합니다. 

    ![조건부 액세스](./media/app-protection-based-conditional-access/07.png)

4. **조건을**, 구성 **클라이언트 앱 (미리 보기)** 합니다. 

    **클라이언트 앱 (미리 보기)** 를 선택 **모바일 앱 및 데스크톱 클라이언트** 하 고 **Exchange ActiveSync 클라이언트**합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/92.png)

5. 아래 **액세스 제어**, 다음 옵션을 선택 합니다.

   - **디바이스를 준수 상태로 표시해야 함**

   - **앱 보호 정책(미리 보기) 필요**

   - **선택된 컨트롤 중 하나 필요**

     ![조건부 액세스](./media/app-protection-based-conditional-access/11.png)



**3단계: IOS 및 Android 클라이언트 응용 프로그램에 대 한 Intune 앱 보호 정책 구성**


![조건부 액세스](./media/app-protection-based-conditional-access/09.png)

자세한 내용은 [Microsoft Intune로 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)합니다.





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Exchange Online에 대 한 앱 보호 기반 및 준수 장치 정책

이 시나리오는 Exchange Online에 액세스 하기 위한 앱을 기반으로 보호 및 준수 장치 조건부 액세스 정책으로 구성 됩니다.


### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음과 같이 사용자를 가정합니다.
 
-   IOS 또는 Android에서 네이티브 메일 응용 프로그램을 사용 하 여 Exchange에 연결 하 여 전자 메일을 구성 합니다.
-   액세스 하려면 해당 장치를 등록할 나타내는 전자 메일을 받습니다.
-   Intune 회사 포털을 다운로드 하 고 포털에 로그인 합니다.
-   메일을 확인 하 고 Outlook 앱을 사용 하 라는 메시지가 표시 됩니다.
-   Outlook 앱을 다운로드합니다.
-   Outlook 앱을 열고 등록에 사용 되는 자격 증명을 입력 합니다.
-   Intune 앱 보호 정책을 받을 수 있습니다.
-   Outlook 및 Intune 앱 보호 정책을 사용 하 여 전자 메일에 액세스할 수 있습니다.

회사 데이터에 액세스 하기에 Intune 앱 보호 정책이 활성화 됩니다. 정책에는 사용자가 응용 프로그램을 다시 시작 하거나 추가 PIN을 사용 하 여 요청할 수 있습니다. 이 경우 응용 프로그램 및 플랫폼에 대 한 정책 구성 된 경우입니다.


### <a name="configuration"></a>구성

**1단계: Exchange Online에 대 한 Azure AD 조건부 액세스 정책 구성**

이 단계에서는 조건부 액세스 정책의 다음 구성 요소:

![조건부 액세스](./media/app-protection-based-conditional-access/01.png)

1. 조건부 액세스 정책의 이름을 입력 합니다.

2. 아래 **할당**의 **사용자 및 그룹**적어도 한 명의 사용자를 선택 하거나 각 조건부 액세스 정책에 대 한 그룹입니다.

3. **클라우드 앱**를 선택 **Office 365 Exchange Online**합니다. 

     ![조건부 액세스](./media/app-protection-based-conditional-access/07.png)

4. **조건을**, 구성 **장치 플랫폼** 하 고 **클라이언트 앱 (미리 보기)** 합니다. 
 
    a. **장치 플랫폼**를 선택 **Android** 하 고 **iOS**합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/03.png)

    b. **클라이언트 앱 (미리 보기)** 를 선택 **모바일 앱 및 데스크톱 클라이언트** 하 고 **최신 인증 클라이언트**합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/91.png)

5. 아래 **액세스 제어**, 다음 옵션을 선택 합니다.

   - **디바이스를 준수 상태로 표시해야 함**

   - **앱 보호 정책(미리 보기) 필요**

   - **선택된 컨트롤이 모두 필요**   
 
     ![조건부 액세스](./media/app-protection-based-conditional-access/13.png)



**2단계: ActiveSync를 사용 하 여 Exchange Online에 대 한 Azure AD 조건부 액세스 정책 구성**

이 단계에서는 조건부 액세스 정책의 다음 구성 요소:

![조건부 액세스](./media/app-protection-based-conditional-access/06.png)

1. 조건부 액세스 정책의 이름을 입력 합니다.

2. 아래 **할당**의 **사용자 및 그룹**적어도 한 명의 사용자를 선택 하거나 각 조건부 액세스 정책에 대 한 그룹입니다.

3. **클라우드 앱**를 선택 **Office 365 Exchange Online**합니다. 

    ![조건부 액세스](./media/app-protection-based-conditional-access/07.png)

4. **조건을**, 구성 **클라이언트 앱 (미리 보기)** 합니다. 

    **클라이언트 앱 (미리 보기)** 를 선택 **모바일 앱 및 데스크톱 클라이언트** 하 고 **Exchange ActiveSync 클라이언트**합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/92.png)

5. 아래 **액세스 제어**, 다음 옵션을 선택 합니다.

   - **디바이스를 준수 상태로 표시해야 함**

   - **앱 보호 정책(미리 보기) 필요**

   - **선택된 컨트롤이 모두 필요**   
 
     ![조건부 액세스](./media/app-protection-based-conditional-access/13.png)




**3단계: IOS 및 Android 클라이언트 응용 프로그램에 대 한 Intune 앱 보호 정책 구성**


![조건부 액세스](./media/app-protection-based-conditional-access/09.png)

자세한 내용은 [Microsoft Intune로 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)합니다.


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online 및 SharePoint Online에 대 한 앱 보호 또는 앱 기반 정책

이 시나리오는 Exchange Online 및 SharePoint Online에 액세스 하기 위한 앱 보호 기반 또는 승인 된 앱 정책으로 구성 됩니다.


### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음과 같이 사용자를 가정합니다.

- 하나는 클라이언트 응용 프로그램 구성 앱 보호 정책 요구 사항 또는 승인 된 앱 요구 사항을 지 원하는 앱 목록에 있습니다.  
- 앱 보호 정책 요구 사항을 충족 하 고 Intune 앱 보호 정책을 받을 수 있는 클라이언트 응용 프로그램을 사용 합니다.
- Intune 앱 보호 정책을 지 원하는 승인 된 앱 정책 요구 사항을 충족 하는 클라이언트 응용 프로그램을 사용 합니다.
- 전자 메일 또는 문서에 액세스 하는 응용 프로그램을 엽니다.
- 가 Outlook 응용 프로그램 및 Azure AD 자격 증명으로 로그인 합니다.
- 설치 되어 있지 않은 경우 사용 하 여 iOS 용 Microsoft Authenticator 또는 Android 용 Intune 회사 포털을 설치 하 라는 메시지가 표시 됩니다.
- 응용 프로그램을 설치 수 계속 하려면 Outlook 앱에 반환 합니다.
- 장치를 등록 하 라는 메시지가 표시 됩니다.
- Intune 앱 보호 정책을 받을 수 있습니다.
- Outlook 및 Intune 앱 보호 정책을 사용 하 여 전자 메일에 액세스할 수 있습니다.
- 사이트 및 앱 보호 정책 요구 사항에 없는 앱을 사용 하 여 문서에 액세스할 수 있지만 승인 된 앱 요구 사항을 나열 합니다.

Intune 앱 보호 정책이 필요 하며 회사 데이터에 액세스 하기 정책에는 사용자가 응용 프로그램을 다시 시작 하거나 추가 PIN을 사용 하 여 요청할 수 있습니다. 이 경우 응용 프로그램 및 플랫폼에 대 한 정책 구성 된 경우입니다.

**설명**

- 앱 보호 및 앱 기반 조건부 액세스 정책을 모두 지원 하려는 경우이 시나리오를 사용할 수 있습니다.
- 이 *또는* 정책, 앱 보호 정책 요구 사항 사용 하 여 앱에 대 한 액세스 승인 된 클라이언트 앱 요구 사항 먼저 평가 됩니다.

### <a name="configuration"></a>구성

**1단계: Exchange Online에 대 한 Azure AD 조건부 액세스 정책 구성**

이 단계에서는 조건부 액세스 정책의 다음 구성 요소:

![조건부 액세스](./media/app-protection-based-conditional-access/62.png)

1. 조건부 액세스 정책의 이름을 입력 합니다.

2. 아래 **할당**의 **사용자 및 그룹**적어도 한 명의 사용자를 선택 하거나 각 조건부 액세스 정책에 대 한 그룹입니다.

3. **클라우드 앱**를 선택 **Office 365 Exchange Online**합니다. 

     ![조건부 액세스](./media/app-protection-based-conditional-access/02.png)

4. **조건을**, 구성 **장치 플랫폼** 하 고 **클라이언트 앱 (미리 보기)** 합니다. 
 
    a. **장치 플랫폼**를 선택 **Android** 하 고 **iOS**합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/03.png)

    b. **클라이언트 앱 (미리 보기)** 를 선택 **모바일 앱 및 데스크톱 클라이언트** 하 고 **최신 인증 클라이언트**합니다.

    ![조건부 액세스](./media/app-protection-based-conditional-access/91.png)

5. 아래 **액세스 제어**, 다음 옵션을 선택 합니다.

   - **승인된 클라이언트 앱 필요**

   - **앱 보호 정책(미리 보기) 필요**

   - **선택된 컨트롤 중 하나 필요**
 
     ![조건부 액세스](./media/app-protection-based-conditional-access/12.png)


**2단계: IOS 및 Android 클라이언트 응용 프로그램에 대 한 Intune 앱 보호 정책 구성**


![조건부 액세스](./media/app-protection-based-conditional-access/09.png)

자세한 내용은 [Microsoft Intune로 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)합니다.




## <a name="next-steps"></a>다음 단계

- 조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory 조건부 액세스를 사용하는 특정 앱에 MFA 요구](app-based-mfa.md)를 참조하세요.
- 사용자 환경에 대 한 조건부 액세스 정책 구성 준비 인 경우 참조 [Azure Active Directory에서 조건부 액세스 모범 사례](best-practices.md)합니다. 