---
title: Approved client apps with Conditional Access - Azure Active Directory
description: Learn how to require approved client apps for cloud app access with Conditional Access in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: c173d0e17166911e28fea3d1c5820879d17af4a8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381112"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>How To: Require approved client apps for cloud app access with Conditional Access 

직원은 개인 및 회사 작업에 대해 모바일 디바이스를 사용합니다. 직원이 생산적이 될 수 있도록 하는 반면 데이터 손실을 방지하려고 합니다. With Azure Active Directory (Azure AD) Conditional Access, you can restrict access to your cloud apps to approved client apps that can protect your corporate data.  

이 항목에서는 승인된 클라이언트 앱을 필요로 하는 조건부 액세스 정책을 구성하는 방법을 설명합니다.

## <a name="overview"></a>개요

With [Azure AD Conditional Access](overview.md), you can fine-tune how authorized users can access your resources. 예를 들어 클라우드 앱에 대한 액세스를 신뢰할 수 있는 디바이스로 제한할 수 있습니다.

[Intune 앱 보호 정책](https://docs.microsoft.com/intune/app-protection-policy)을 사용하여 회사의 데이터를 보호할 수 있습니다. Intune 앱 보호 정책은 디바이스 관리 솔루션에 디바이스를 등록하거나 등록하지 않고 회사의 데이터를 보호할 수 있도록 하는 MDM(모바일 디바이스 관리) 솔루션이 필요하지 않습니다.

Azure Active Directory Conditional Access enables you to limit access to your cloud apps to client apps that support Intune app protection policies. 예를 들어 Exchange Online에 대한 액세스를 Outlook 앱으로 제한할 수 있습니다.

In the Conditional Access terminology, these client apps are known as **approved client apps**.  

![조건부 액세스](./media/app-based-conditional-access/05.png)

승인된 클라이언트 앱의 목록은 [승인된 클라이언트 앱 요구 사항](technical-reference.md#approved-client-app-requirement)을 참조하세요.

You can combine app-based Conditional Access policies with other policies such as [device-based Conditional Access policies](require-managed-devices.md) to provide flexibility in how to protect data for both personal and corporate devices.

## <a name="before-you-begin"></a>시작하기 전에

이 토픽은 다음 항목에 대해 잘 알고 있다고 가정합니다.

- [승인된 클라이언트 앱 요구 사항](technical-reference.md#approved-client-app-requirement) 기술 참조.
- The basic concepts of [Conditional Access in Azure Active Directory](overview.md).
- How to [configure a Conditional Access policy](app-based-mfa.md).
- The [migration of Conditional Access policies](best-practices.md#policy-migration).

## <a name="prerequisites"></a>전제 조건

To create an app-based Conditional Access policy, you must have an Enterprise Mobility + Security or an Azure Active Directory premium subscription, and the users must be licensed for EMS or Azure AD. 

## <a name="exchange-online-policy"></a>Exchange Online 정책 

This scenario consists of an app-based Conditional Access policy for access to Exchange Online.

### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음과 같이 사용자를 가정합니다.

- iOS 또는 Android에는 기본 메일 애플리케이션을 사용하여 Exchange에 연결하도록 이메일을 구성합니다.
- 액세스는 Outlook 앱을 사용할 때만 가능하다는 것을 알리는 전자 메일을 수신합니다.
- 링크가 포함된 애플리케이션을 다운로드합니다.
- Outlook 애플리케이션을 열고 Azure AD 자격 증명으로 로그인합니다.
- 계속하려면 Authenticator(iOS) 또는 회사 포털(Android)을 설치하라는 메시지가 표시되었습니다.
- 애플리케이션을 설치하고 Outlook 앱으로 돌아가서 계속할 수 있습니다.
- 디바이스를 등록하라는 메시지가 표시되었습니다.
- 전자 메일에 액세스할 수 있습니다.

Any Intune app protection policies are activated at the time the access corporate data and may prompt the user to restart the application, use an additional PIN etc. (if configured for the application and platform).

### <a name="configuration"></a>구성 

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online**을 선택해야 합니다.
1. **조건:** **조건**으로 **디바이스 플랫폼** 및 **클라이언트 앱**을 선택해야 합니다.
   1. **디바이스 플랫폼**으로 **Android** 및 **iOS**를 선택합니다.
   1. **클라이언트 앱(미리 보기)** 으로 **모바일 앱 및 데스크톱 앱** 및 **최신 인증 클라이언트**를 선택합니다.
1. **액세스 제어**로 **승인된 클라이언트 앱 필요(미리 보기)** 를 선택해야 합니다.

   ![조건부 액세스](./media/app-based-conditional-access/05.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online**을 선택해야 합니다.
1. **조건:** **조건**으로 **클라이언트 앱(미리 보기)** 을 구성해야 합니다. 
   1. **클라이언트 앱(미리 보기)** 으로 **모바일 앱 및 데스크톱 클라이언트** 및 **Exchange ActiveSync 클라이언트**를 선택합니다.
   1. **액세스 제어**로 **승인된 클라이언트 앱 필요(미리 보기)** 를 선택해야 합니다.

      ![조건부 액세스](./media/app-based-conditional-access/05.png)

**3단계 - iOS 및 Android 클라이언트 애플리케이션에 대한 Intune 앱 보호 정책 구성**

자세한 정보는 [Microsoft Intune으로 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)를 참조하세요.

## <a name="exchange-online-and-sharepoint-online-policy"></a>Exchange Online 및 SharePoint Online 정책

This scenario consists of a Conditional Access with mobile app management policy for access to Exchange Online and SharePoint Online with approved apps.

### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음과 같이 사용자를 가정합니다.

- SharePoint 앱을 사용하여 연결하고 해당 회사 사이트를 보려고 시도합니다.
- Attempts to sign in with the same credentials as the Outlook app credentials
- 다시 등록하지 않아도 리소스에 대한 액세스를 얻을 수 있습니다.

### <a name="configuration"></a>구성

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online and SharePoint Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online** 및 **Office 365 SharePoint Online**을 선택해야 합니다. 
1. **조건:** **조건**으로 **디바이스 플랫폼** 및 **클라이언트 앱**을 선택해야 합니다.
   1. **디바이스 플랫폼**으로 **Android** 및 **iOS**를 선택합니다.
   1. **클라이언트 앱(미리 보기)** 으로 **모바일 앱 및 데스크톱 클라이언트** 및 **최신 인증 클라이언트**를 선택합니다.
1. **액세스 제어**로 **승인된 클라이언트 앱 필요(미리 보기)** 를 선택해야 합니다.

   ![조건부 액세스](./media/app-based-conditional-access/05.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online**을 선택해야 합니다. 온라인 
1. **조건:** **조건**으로 **클라이언트 앱**을 구성해야 합니다.
   1. **클라이언트 앱(미리 보기)** 으로 **모바일 앱 및 데스크톱 클라이언트** 및 **Exchange ActiveSync 클라이언트**를 선택합니다.
   1. **액세스 제어**로 **승인된 클라이언트 앱 필요(미리 보기)** 를 선택해야 합니다.

      ![조건부 액세스](./media/app-based-conditional-access/05.png)

**3단계 - iOS 및 Android 클라이언트 애플리케이션에 대한 Intune 앱 보호 정책 구성**

![조건부 액세스](./media/app-based-conditional-access/09.png)

자세한 정보는 [Microsoft Intune으로 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)를 참조하세요.

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online 및 SharePoint Online에 대한 앱 기반 또는 준수 디바이스 정책

This scenario consists of an app-based or compliant device Conditional Access policy for access to Exchange Online.

### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음을 가정합니다.
 
- Some users are already enrolled (with or without corporate devices)
- 앱 보호 애플리케이션을 사용하여 Azure AD에 등록되지 않은 사용자는 리소스에 액세스하려면 디바이스를 등록해야 합니다.
- 앱 보호 애플리케이션을 사용하는 등록된 사용자는 디바이스에 다시 등록할 필요가 없습니다.

### <a name="configuration"></a>구성

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online and SharePoint Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online** 및 **Office 365 SharePoint Online**을 선택해야 합니다. 
1. **조건:** **조건**으로 **디바이스 플랫폼** 및 **클라이언트 앱**을 구성해야 합니다. 
   1. **디바이스 플랫폼**으로 **Android** 및 **iOS**를 선택합니다.
   1. **클라이언트 앱(미리 보기)** 으로 **모바일 앱 및 데스크톱 클라이언트** 및 **최신 인증 클라이언트**를 선택합니다.
1. **액세스 제어**로 다음을 선택해야 합니다.
   - **디바이스를 준수 상태로 표시해야 함**
   - **승인된 클라이언트 앱(미리 보기) 필요**
   - **선택된 컨트롤 중 하나가 필요**   
 
      ![조건부 액세스](./media/app-based-conditional-access/11.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online**을 선택해야 합니다. 
1. **조건:** **조건**으로 **클라이언트 앱**을 구성해야 합니다. 
   1. **클라이언트 앱(미리 보기)** 으로 **모바일 앱 및 데스크톱 클라이언트** 및 **Exchange ActiveSync 클라이언트**를 선택합니다.
1. **액세스 제어**로 **승인된 클라이언트 앱 필요(미리 보기)** 를 선택해야 합니다.
 
   ![조건부 액세스](./media/app-based-conditional-access/11.png)

**3단계 - iOS 및 Android 클라이언트 애플리케이션에 대한 Intune 앱 보호 정책 구성**

![조건부 액세스](./media/app-based-conditional-access/09.png)

자세한 정보는 [Microsoft Intune으로 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)를 참조하세요.

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online 및 SharePoint Online에 대한 앱 기반 및 준수 디바이스 정책

This scenario consists of an app-based and compliant device Conditional Access policy for access to Exchange Online.

### <a name="scenario-playbook"></a>시나리오 플레이 북

이 시나리오는 다음과 같이 사용자를 가정합니다.
 
- iOS 또는 Android에는 기본 메일 애플리케이션을 사용하여 Exchange에 연결하도록 이메일을 구성합니다.
- 액세스하려면 디바이스를 등록해야 함을 알리는 전자 메일을 수신합니다.
- 회사 포털을 다운로드하고 회사 포털에 로그인합니다.
- 메일을 확인하고 Outlook 앱을 사용하도록 요청받았습니다.
- Outlook 앱을 다운로드합니다.
- Outlook 앱을 열고가 등록에 사용되는 자격 증명을 입력합니다.
- 사용자는 전자 메일에 액세스할 수 있습니다.

회사 데이터에 액세스하는 시기에 Intune 앱 보호 정책이 활성화되어 있으면 사용자에게 애플리케이션을 다시 시작하고 추가 PIN 등을 사용하라는 메시지가 표시될 수도 있습니다(애플리케이션 및 플랫폼에 대해 구성된 경우).

### <a name="configuration"></a>구성

**Step 1 - Configure an Azure AD Conditional Access policy for Exchange Online and SharePoint Online**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online** 및 **Office 365 SharePoint Online**을 선택해야 합니다. 
1. **조건:** **조건**으로 **디바이스 플랫폼** 및 **클라이언트 앱**을 구성해야 합니다. 
   1. **디바이스 플랫폼**으로 **Android** 및 **iOS**를 선택합니다.
   1. **클라이언트 앱(미리 보기)** 으로 **모바일 앱 및 데스크톱 앱** 및 **최신 인증 클라이언트**를 선택합니다.
1. **액세스 제어**로 다음을 선택해야 합니다.
   - **디바이스를 준수 상태로 표시해야 함**
   - **승인된 클라이언트 앱(미리 보기) 필요**
   - **선택된 컨트롤이 모두 필요**   
 
      ![조건부 액세스](./media/app-based-conditional-access/13.png)

**Step 2 - Configure an Azure AD Conditional Access policy for Exchange Online with Active Sync (EAS)**

For the Conditional Access policy in this step, you need to configure the following components:

1. The **Name** of your Conditional Access policy.
1. **Users and groups**: Each Conditional Access policy must have at least one user or group selected.
1. **클라우드 앱:** 클라우드 앱으로 **Office 365 Exchange Online**을 선택해야 합니다. 
1. **조건:** **조건**으로 **클라이언트 앱(미리 보기)** 을 구성해야 합니다. 
   1. **클라이언트 앱(미리 보기)** 으로 **모바일 앱 및 데스크톱 클라이언트** 및 **Exchange ActiveSync 클라이언트**를 선택합니다.

   ![조건부 액세스](./media/app-based-conditional-access/92.png)

1. **액세스 제어**로 다음을 선택해야 합니다.
   - **디바이스를 준수 상태로 표시해야 함**
   - **승인된 클라이언트 앱(미리 보기) 필요**
   - **선택된 컨트롤이 모두 필요**   

**3단계 - iOS 및 Android 클라이언트 애플리케이션에 대한 Intune 앱 보호 정책 구성**

![조건부 액세스](./media/app-based-conditional-access/09.png)

자세한 정보는 [Microsoft Intune으로 앱 및 데이터 보호](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)를 참조하세요.

## <a name="next-steps"></a>다음 단계

조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory 조건부 액세스를 사용하는 특정 앱에 MFA 요구](app-based-mfa.md)를 참조하세요.

사용자 환경에 대한 조건부 액세스 정책을 구성할 준비가 완료된 경우 Azure Active Directory의 [조건부 액세스 모범 사례](best-practices.md)를 참조하세요. 
