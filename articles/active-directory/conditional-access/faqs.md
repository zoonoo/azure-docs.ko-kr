---
title: Azure Active Directory 조건부 액세스 Faq | Microsoft Docs
description: Azure Active Directory에서 조건부 액세스에 대 한 자주 묻는 질문에 답변을 받을 수 있습니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad0494868c494b488a238a81e504c58552813907
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508983"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Azure Active Directory 조건부 액세스 Faq

## <a name="which-applications-work-with-conditional-access-policies"></a>조건부 액세스 정책을 사용 하 여 응용 프로그램 작동?

조건부 액세스 정책과 함께 작동 하는 응용 프로그램에 대 한 자세한 내용은 [응용 프로그램 및 Azure Active Directory에서 조건부 액세스 규칙을 사용 하는 브라우저](technical-reference.md)합니다.

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>B2B 공동 작업과 게스트 사용자에 대 한 조건부 액세스 정책이 적용 됩니까?

B2B(Business-to-Business) 협업 사용자에 대한 정책이 적용됩니다. 그러나 경우에 따라 사용자가 정책 요구 사항을 충족하지 못할 수 있습니다. 예를 들어 게스트 사용자의 조직이 다단계 인증을 지원하지 않는 경우가 있습니다. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>SharePoint Online 정책도 비즈니스용 OneDrive에 적용됩니까?

예. SharePoint Online 정책은 비즈니스용 OneDrive에도 적용됩니다.

## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Word나 Outlook 등의 클라이언트 앱에 정책을 설정할 수 없는 이유는 무엇입니까?

조건부 액세스 정책을 서비스에 액세스 하기 위한 요구 사항을 설정 합니다. 해당 서비스에 인증하는 경우에 적용됩니다. 클라이언트 애플리케이션에서 직접 정책이 설정되는 것이 아니라 클라이언트가 서비스를 호출할 때 적용됩니다. 예를 들어 SharePoint에서 설정된 정책은 SharePoint를 호출하는 클라이언트에 적용됩니다. Exchange에서 설정된 정책은 Outlook에 적용됩니다.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>서비스 계정에는 조건부 액세스 정책을 적용 되나요?

조건부 액세스 정책은 모든 사용자 계정에 적용 됩니다. 여기에는 서비스 계정으로 사용되는 사용자 계정이 포함됩니다. 종종 무인 모드로 실행 되는 서비스 계정에는 조건부 액세스 정책의 요구 사항을 만족할 수 없습니다. 예를 들어 다단계 인증이 필요할 수 있습니다. 조건부 액세스 정책 관리 설정을 사용 하 여 서비스 계정은 정책에서 제외할 수 있습니다. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>Graph Api는 조건부 액세스 정책을 구성 하는 데 사용할 수 있습니까?

현재는 사용할 수 없습니다. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>지원되지 않는 디바이스 플랫폼에 대한 기본 제외 정책은 무엇인가요?

현재 iOS 및 Android 장치 사용자에 게 조건부 액세스 정책은 선택적으로 적용 됩니다. 다른 장치 플랫폼에서 응용 프로그램에 기본적으로 영향을 받지 iOS 및 Android 장치에 대 한 조건부 액세스 정책. 테넌트 관리자는 지원되지 않는 플랫폼의 사용자에 대해 액세스를 차단하도록 전역 정책을 재정의할 수 있습니다.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Microsoft Teams 대 한 조건부 액세스 정책을 어떻게 작동 하나요?

Microsoft Teams는 모임, 일정, 파일 공유 등의 핵심 생산성 시나리오를 위해 Exchange Online 및 SharePoint Online을 많이 사용합니다. 이러한 클라우드 앱에 대해 설정 된 조건부 액세스 정책은 Microsoft Teams 직접 사용자가 로그인 할 때 Microsoft Teams 적용 합니다.

Microsoft Teams 지원 됩니다 개별적으로 Azure Active Directory 조건부 액세스 정책에서 클라우드 앱으로. 클라우드 앱에 설정 된 조건부 액세스 정책은 사용자가 로그인 할 때 Microsoft Teams 적용 합니다. 그러나 Exchange Online 및 SharePoint Online 등의 다른 앱에 대한 정확한 정책이 없으면 사용자가 해당 리소스에 계속하여 직접 액세스할 수 있습니다.

Windows 및 Mac용 Microsoft Teams 데스크톱 클라이언트는 최신 인증을 지원합니다. 최신 인증은 ADAL(Azure Active Directory Authentication Library) 기반의 로그인을 플랫폼 전체의 Microsoft Office 클라이언트 애플리케이션에 제공합니다.

## <a name="next-steps"></a>다음 단계

- 사용자 환경에 대 한 조건부 액세스 정책을 구성 하려면 참조는 [Azure Active Directory의 조건부 액세스 모범 사례](best-practices.md)합니다. 
