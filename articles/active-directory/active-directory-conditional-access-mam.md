---
title: "Azure Active Directory에서 모바일 앱 관리를 통한 조건부 액세스 | Microsoft Docs"
description: "Azure Active Directory에서 모바일 앱 관리를 통한 조건부 액세스가 작동되는 방식을 알아봅니다."
services: active-directory
keywords: "앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: spunukol
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: c6bc39dc151c80ffe1306464da60a029e54cc6b1
ms.contentlocale: ko-kr
ms.lasthandoff: 09/05/2017

---
# <a name="conditional-access-with-mobile-app-management-in-azure-active-directory"></a>Azure Active Directory에서 모바일 앱 관리를 통한 조건부 액세스  

Intune 앱 보호 정책과 결합된 Azure Portal의 Azure AD(Active Directory) 앱 기반 조건부 액세스는 예를 들어 Exchange Online에 대한 액세스를 Outlook 앱으로 제한하는 등 Intune 앱 보호를 지원하는 모바일 앱으로 클라우드 앱에 대한 액세스를 제한하는 데 유용합니다. 이 지원을 통해 관리용으로 Intune MDM에 의해 등록되지 않은 장치는 여전히 회사 데이터를 보호할 수 있습니다.   

모바일 앱 관리 조건부 액세스를 사용하면 개인 및 회사 장치 모두에 대한 데이터를 보호하는 방법에 유연성을 제공하는 장치 기반 조건부 액세스 정책과 같은 다른 정책과 결합할 수 있습니다. 

##<a name="before-you-begin"></a>시작하기 전에

이 토픽은 다음 항목에 대해 잘 알고 있다고 가정합니다.

- [Azure Active Directory의 조건부 액세스](active-directory-conditional-access-azure-portal.md)에 대한 기본 개념.

- [조건부 액세스 정책을 구성하는](active-directory-conditional-access-azure-portal-get-started.md) 방법.


또한 [Azure Active Directory의 조건부 액세스 모범 사례](active-directory-conditional-access-best-practices.md)를 살펴봅니다.  



## <a name="prerequisites"></a>필수 조건

1.  앱 기반 조건부 액세스 정책을 만들기 전에 Enterprise Mobility + Security 또는 Azure Active Directory Premium 구독이 있어야 하며 사용자에게 EMS 또는 Azure AD에 대한 라이선스가 있어야 합니다. 
2.  모바일 앱 관리 정책을 사용하여 새 조건부 액세스를 만들기 전에 시나리오 및 마이그레이션 고려 사항을 검토해야 합니다.

## <a name="supported-platforms"></a>지원되는 플랫폼

-   iOS

-   Android

## <a name="approved-client-applications"></a>승인된 클라이언트 응용 프로그램 

- Microsoft Outlook

- Microsoft SharePoint

- Microsoft OneDrive

- Microsoft 팀

- Microsoft Word

- Microsoft Excel

- Microsoft PowerPoint


## <a name="exchange-online-policy"></a>Exchange Online 정책 

이 시나리오는 승인된 앱을 통한 Exchange Online에 대한 액세스를 위해 모바일 앱 관리 정책을 사용한 조건부 액세스로 구성됩니다.


### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음과 같이 사용자를 가정합니다.

- iOS 또는 Android에는 기본 메일 응용 프로그램을 사용하여 Exchange에 연결하도록 전자 메일을 구성합니다.

- 액세스는 Outlook 앱을 사용할 때만 가능하다는 것을 알리는 전자 메일을 수신합니다.

- 링크가 포함된 응용 프로그램을 다운로드합니다.

- Outlook 응용 프로그램을 열고 Azure AD 자격 증명으로 로그인합니다.

- 계속하려면 Authenticor(iOS) 또는 회사 포털(Android)을 설치하라는 메시지가 표시되었습니다.

- 응용 프로그램을 설치하고 Outlook 앱으로 돌아가서 계속할 수 있습니다.

- 장치를 등록하라는 메시지가 표시되었습니다.

- 전자 메일에 액세스할 수 있습니다.

회사 데이터에 액세스하는 시기에 Intune 앱 보호 정책이 활성화되어 있으면 사용자에게 응용 프로그램을 다시 시작하고 추가 PIN 등을 사용하라는 메시지가 표시될 수도 있습니다(응용 프로그램 및 플랫폼에 대해 구성된 경우).

### <a name="configuration"></a>구성 

**1단계 - Exchange Online에 대한 Azure AD 조건부 액세스 정책 구성**

이 단계의 조건부 액세스 정책의 경우 다음 구성 요소를 구성해야 합니다.

![조건부 액세스](./media/active-directory-conditional-access-mam/01.png)

1. 조건부 액세스 정책의 **이름**.

2. **사용자 및 그룹**: 각 조건부 액세스 정책에는 선택한 사용자 또는 그룹이 하나 이상 있어야 합니다.

3. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online**을 선택해야 합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/07.png)

4. **조건:** **조건**으로 **장치 플랫폼** 및 **클라이언트 앱**을 선택해야 합니다.

    a. **장치 플랫폼**으로 **Android** 및 **iOS**를 선택합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/03.png)

    b. **클라이언트 앱**으로 **모바일 앱 및 데스크톱 앱**을 선택합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/04.png)

5. **액세스 제어**로 **승인된 클라이언트 앱 필요(미리 보기)**를 선택해야 합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/05.png)
 

**2단계 - EAS(Exchange Online with Active Sync)에 대한 Azure AD 조건부 액세스 정책 구성**

이 단계의 조건부 액세스 정책의 경우 다음 구성 요소를 구성해야 합니다.

![조건부 액세스](./media/active-directory-conditional-access-mam/06.png)

1. 조건부 액세스 정책의 **이름**.

2. **사용자 및 그룹**: 각 조건부 액세스 정책에는 선택한 사용자 또는 그룹이 하나 이상 있어야 합니다.


3. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online**을 선택해야 합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/07.png)

4. **조건:** **조건**으로 **클라이언트 앱**을 구성해야 합니다. 

    a. **클라이언트 앱**으로 **Exchange Active Sync**를 선택합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/08.png)

    b. **액세스 제어**로 **승인된 클라이언트 앱 필요(미리 보기)**를 선택해야 합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/05.png)


**3단계 - iOS 및 Android 클라이언트 응용 프로그램에 대한 Intune 앱 보호 정책 구성**


![조건부 액세스](./media/active-directory-conditional-access-mam/09.png)

자세한 정보는 [Microsoft Intune으로 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)를 참조하세요.


## <a name="exchange-online-and-sharepoint-online-policy"></a>Exchange Online 및 SharePoint Online 정책

이 시나리오는 승인된 앱을 통한 Exchange Online 및 SharePoint Online에 대한 액세스를 위해 모바일 앱 관리 정책을 사용한 조건부 액세스로 구성됩니다.

### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음과 같이 사용자를 가정합니다.

- SharePoint 앱을 사용하여 연결하고 해당 회사 사이트를 보려고 시도합니다.

- Outlook 앱 자격 증명과 동일한 자격 증명을 사용하여 로그인을 시도합니다.

- 다시 등록하지 않아도 리소스에 대한 액세스를 얻을 수 있습니다.


### <a name="configuration"></a>구성

**1단계 - Exchange Online 및 SharePoint Online에 대한 Azure AD 조건부 액세스 정책 구성**

이 단계의 조건부 액세스 정책의 경우 다음 구성 요소를 구성해야 합니다.

![조건부 액세스](./media/active-directory-conditional-access-mam/71.png)

1. 조건부 액세스 정책의 **이름**.

2. **사용자 및 그룹**: 각 조건부 액세스 정책에는 선택한 사용자 또는 그룹이 하나 이상 있어야 합니다.


3. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online** 및 **Office 365 SharePoint Online**을 선택해야 합니다. 

    ![조건부 액세스](./media/active-directory-conditional-access-mam/02.png)

4. **조건:** **조건**으로 **장치 플랫폼** 및 **클라이언트 앱**을 선택해야 합니다.

    a. **장치 플랫폼**으로 **Android** 및 **iOS**를 선택합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/03.png)

    b. **클라이언트 앱**으로 **모바일 앱 및 데스크톱 앱**을 선택합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/04.png)

5. **액세스 제어**로 **승인된 클라이언트 앱 필요(미리 보기)**를 선택해야 합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/05.png)




**2단계 - EAS(Exchange Online with Active Sync)에 대한 Azure AD 조건부 액세스 정책 구성**

이 단계의 조건부 액세스 정책의 경우 다음 구성 요소를 구성해야 합니다.

![조건부 액세스](./media/active-directory-conditional-access-mam/06.png)

1. 조건부 액세스 정책의 **이름**.

2. **사용자 및 그룹**: 각 조건부 액세스 정책에는 선택한 사용자 또는 그룹이 하나 이상 있어야 합니다.

3. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online**을 선택해야 합니다. 온라인 

    ![조건부 액세스](./media/active-directory-conditional-access-mam/07.png)

4. **조건:** **조건**으로 **클라이언트 앱**을 구성해야 합니다.

    a. **클라이언트 앱**으로 **Exchange Active Sync**를 선택합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/08.png)

    b. **액세스 제어**로 **승인된 클라이언트 앱 필요(미리 보기)**를 선택해야 합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/05.png)




**3단계 - iOS 및 Android 클라이언트 응용 프로그램에 대한 Intune 앱 보호 정책 구성**


![조건부 액세스](./media/active-directory-conditional-access-mam/09.png)

자세한 정보는 [Microsoft Intune으로 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)를 참조하세요.


## <a name="mobile-application-management-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online 및 SharePoint Online에 대한 모바일 응용 프로그램 관리 또는 준수 장치 정책

이 시나리오는 승인된 앱을 통한 Exchange Online에 대한 액세스를 위해 모바일 앱 관리 또는 준수 장치 정책을 사용한 조건부 액세스로 구성됩니다.


### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음을 가정합니다.
 
- 일부 사용자가 이미 등록되어 있습니다(회사 장치 포함 또는 제외).

- 앱 보호 응용 프로그램을 사용하여 Azure AD에 등록되지 않은 사용자는 리소스에 액세스하려면 장치를 등록해야 합니다.

- 앱 보호 응용 프로그램을 사용하는 등록된 사용자는 장치에 다시 등록할 필요가 없습니다.


### <a name="configuration"></a>구성

**1단계 - Exchange Online 및 SharePoint Online에 대한 Azure AD 조건부 액세스 정책 구성**

이 단계의 조건부 액세스 정책의 경우 다음 구성 요소를 구성해야 합니다.

![조건부 액세스](./media/active-directory-conditional-access-mam/62.png)

1. 조건부 액세스 정책의 **이름**.

2. **사용자 및 그룹**: 각 조건부 액세스 정책에는 선택한 사용자 또는 그룹이 하나 이상 있어야 합니다.

3. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online** 및 **Office 365 SharePoint Online**을 선택해야 합니다. 

     ![조건부 액세스](./media/active-directory-conditional-access-mam/02.png)

4. **조건:** **조건**으로 **장치 플랫폼** 및 **클라이언트 앱**을 구성해야 합니다. 
 
    a. **장치 플랫폼**으로 **Android** 및 **iOS**를 선택합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/03.png)

    b. **클라이언트 앱**으로 **모바일 앱 및 데스크톱 앱**을 선택합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/04.png)

5. **액세스 제어**로 다음을 선택해야 합니다.

    - **장치를 준수 상태로 표시해야 함**

    - **승인된 클라이언트 앱(미리 보기) 필요**

    - **선택된 컨트롤 중 하나가 필요**   
 
    ![조건부 액세스](./media/active-directory-conditional-access-mam/11.png)



**2단계 - EAS(Exchange Online with Active Sync)에 대한 Azure AD 조건부 액세스 정책 구성**

이 단계의 조건부 액세스 정책의 경우 다음 구성 요소를 구성해야 합니다.

![조건부 액세스](./media/active-directory-conditional-access-mam/61.png)

1. 조건부 액세스 정책의 **이름**.

2. **사용자 및 그룹**: 각 조건부 액세스 정책에는 선택한 사용자 또는 그룹이 하나 이상 있어야 합니다.

3. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online**을 선택해야 합니다. 

    ![조건부 액세스](./media/active-directory-conditional-access-mam/07.png)

4. **조건:** **조건**으로 **클라이언트 앱**을 구성해야 합니다. 

    **클라이언트 앱*으로 **Exchange Active Sync**를 선택합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/08.png)

5. **액세스 제어**로 **승인된 클라이언트 앱 필요(미리 보기)**를 선택해야 합니다.
 
    ![조건부 액세스](./media/active-directory-conditional-access-mam/11.png)




**3단계 - iOS 및 Android 클라이언트 응용 프로그램에 대한 Intune 앱 보호 정책 구성**


![조건부 액세스](./media/active-directory-conditional-access-mam/09.png)

자세한 정보는 [Microsoft Intune으로 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)를 참조하세요.





## <a name="mobile-application-management-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online 및 SharePoint Online에 대한 모바일 응용 프로그램 관리 및 준수 장치 정책

이 시나리오는 승인된 앱을 통한 Exchange Online에 대한 액세스를 위해 모바일 앱 관리 및 준수 장치 정책을 사용한 조건부 액세스로 구성됩니다.

### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음과 같이 사용자를 가정합니다.
 
-   iOS 또는 Android에는 기본 메일 응용 프로그램을 사용하여 Exchange에 연결하도록 전자 메일을 구성합니다.
-   액세스하려면 장치를 등록해야 함을 알리는 전자 메일을 수신합니다.
-   회사 포털을 다운로드하고 회사 포털에 로그인합니다.
-   메일을 확인하고 Outlook 앱을 사용하도록 요청받았습니다.
-   Outlook 앱을 다운로드합니다.
-   Outlook 앱을 열고가 등록에 사용되는 자격 증명을 입력합니다.
-   사용자는 전자 메일에 액세스할 수 있습니다.

회사 데이터에 액세스하는 시기에 Intune 앱 보호 정책이 활성화되어 있으면 사용자에게 응용 프로그램을 다시 시작하고 추가 PIN 등을 사용하라는 메시지가 표시될 수도 있습니다(응용 프로그램 및 플랫폼에 대해 구성된 경우).


### <a name="configuration"></a>구성

**1단계 - Exchange Online 및 SharePoint Online에 대한 Azure AD 조건부 액세스 정책 구성**

이 단계의 조건부 액세스 정책의 경우 다음 구성 요소를 구성해야 합니다.

![조건부 액세스](./media/active-directory-conditional-access-mam/62.png)

1. 조건부 액세스 정책의 **이름**.

2. **사용자 및 그룹**: 각 조건부 액세스 정책에는 선택한 사용자 또는 그룹이 하나 이상 있어야 합니다.

3. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online** 및 **Office 365 SharePoint Online**을 선택해야 합니다. 

     ![조건부 액세스](./media/active-directory-conditional-access-mam/02.png)

4. **조건:** **조건**으로 **장치 플랫폼** 및 **클라이언트 앱**을 구성해야 합니다. 
 
    a. **장치 플랫폼**으로 **Android** 및 **iOS**를 선택합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/03.png)

    b. **클라이언트 앱**으로 **모바일 앱 및 데스크톱 앱**을 선택합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/04.png)

5. **액세스 제어**로 다음을 선택해야 합니다.

    - **장치를 준수 상태로 표시해야 함**

    - **승인된 클라이언트 앱(미리 보기) 필요**

    - **선택된 컨트롤 중 하나가 필요**   
 
    ![조건부 액세스](./media/active-directory-conditional-access-mam/11.png)



**2단계 - EAS(Exchange Online with Active Sync)에 대한 Azure AD 조건부 액세스 정책 구성**

이 단계의 조건부 액세스 정책의 경우 다음 구성 요소를 구성해야 합니다.

![조건부 액세스](./media/active-directory-conditional-access-mam/61.png)

1. 조건부 액세스 정책의 **이름**.

2. **사용자 및 그룹**: 각 조건부 액세스 정책에는 선택한 사용자 또는 그룹이 하나 이상 있어야 합니다.

3. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online**을 선택해야 합니다. 

    ![조건부 액세스](./media/active-directory-conditional-access-mam/07.png)

4. **조건:** **조건**으로 **클라이언트 앱**을 구성해야 합니다. 

    **클라이언트 앱**으로 **Exchange Active Sync**를 선택합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/08.png)

5. **액세스 제어**로 다음을 선택해야 합니다.

    - **장치를 준수 상태로 표시해야 함**

    - **승인된 클라이언트 앱(미리 보기) 필요**

    - **선택된 컨트롤이 모두 필요**   
 
    ![조건부 액세스](./media/active-directory-conditional-access-mam/64.png)




**3단계 - iOS 및 Android 클라이언트 응용 프로그램에 대한 Intune 앱 보호 정책 구성**


![조건부 액세스](./media/active-directory-conditional-access-mam/09.png)

자세한 정보는 [Microsoft Intune으로 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)를 참조하세요.



## <a name="migration-considerations"></a>마이그레이션 고려 사항

Azure 클래식 포털에서 구성된 정책이 있는 경우 다음 이유로 인해 Azure Portal로 마이그레이션해야 합니다.


- Azure 클래식 포털 정책 및 Azure Portal 정책에 있는 사용자는 두 정책의 요구 사항을 충족해야 합니다. 

- 기존 정책을 마이그레이션하지 않는 경우 액세스 권한을 부여하는 정책을 구현할 수 없게 됩니다.


## <a name="migration-from-the-azure-classic-portal"></a>Azure 클래식 포털에서 마이그레이션

이 시나리오에서는 

- 사용자 [Azure 클래식 포털](https://manage.windowsazure.com)에서 다음을 구성했습니다.

    - SharePoint Online

    ![조건부 액세스](./media/active-directory-conditional-access-mam/14.png)

    - 장치 기반 조건부 액세스 정책

    ![조건부 액세스](./media/active-directory-conditional-access-mam/15.png)

- Azure Portal에서 모바일 응용 프로그램 관리 조건부 액세스 정책을 구성하려고 합니다. 
 

### <a name="configuration"></a>구성 

- 장치 기반 조건부 액세스 정책 검토

- Azure Portal로 마이그레이션 

- 모바일 응용 프로그램 관리 조건부 액세스 정책 추가


## <a name="migrating-from-intune"></a>Intune에서 마이그레이션 

이 시나리오에서는

- [Intune](https://portal.azure.com/#blade/Microsoft_Intune/SummaryBlade )에서 구성된 Exchange Online 또는 SharePoint Online 중 하나에 대한 모바일 응용 프로그램 관리 조건부 액세스 정책이 있습니다.

    ![조건부 액세스](./media/active-directory-conditional-access-mam/15.png)

- Azure Portal에서 모바일 응용 프로그램 관리 조건부 액세스를 사용하여 마이그레이션하려고 합니다.


### <a name="configuration"></a>구성 
 
- 장치 기반 조건부 액세스 정책 검토

- Azure Portal로 마이그레이션 

- Intune에서 Exchange Online 또는 SharePoint Online에 대해 구성된 모바일 응용 프로그램 관리 조건부 액세스 정책을 검토합니다.

- 장치 기반 컨트롤 외에 **승인된 응용 프로그램 필요**를 위한 컨트롤을 추가합니다. 
 

## <a name="migrating-from-the-azure-classic-portal-and-intune"></a>Azure 클래식 포털 및 Intune에서 마이그레이션

이 시나리오에서는

- 다음이 구성되어 있습니다.

    - **Azure 클래식 포털:** 장치 기반 조건부 

    - **Intune:** 모바일 응용 프로그램 관리 조건부 액세스 정책 
    
- Azure Portal에서 모바일 응용 프로그램 관리 조건부 액세스 정책을 사용하여 두 정책을 마이그레이션하려고 합니다.


### <a name="configuration"></a>구성

- 장치 기반 조건부 액세스 정책 검토

- Azure Portal로 마이그레이션 

- Intune에서 Exchange Online 또는 SharePoint Online에 대해 구성된 모바일 응용 프로그램 관리 조건부 액세스 정책을 검토합니다.

- 장치 기반 외에 **승인된 응용 프로그램 필요**를 위한 컨트롤을 추가합니다. 




## <a name="next-steps"></a>다음 단계

조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory에서 조건부 액세스 시작](active-directory-conditional-access-azure-portal-get-started.md)을 참조하세요.

사용자 환경에 대한 조건부 액세스 정책을 구성할 준비가 완료된 경우 [Azure Active Directory의 조건부 액세스 모범 사례](active-directory-conditional-access-best-practices.md)를 참조하세요. 
