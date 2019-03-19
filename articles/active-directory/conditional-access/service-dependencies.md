---
title: Azure Active Directory 조건부 액세스의 서비스 종속성 이란? | Microsoft Docs
description: Azure Active Directory 조건부 액세스에서 조건을 사용하여 정책을 트리거하는 방법을 알아봅니다.
services: active-directory
keywords: 앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/14/2019
ms.author: markvi
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e871c241c92fbdac00223df94dde203745b8edae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58166103"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Azure Active Directory 조건부 액세스의 서비스 종속성 이란? 


조건부 액세스 정책을 사용 하 여 웹 사이트 및 서비스에 액세스 요구 사항을 지정할 수 있습니다. 예를 들어, multi-factor authentication (MFA)을 필요한 액세스 요구를 포함할 수 있습니다 또는 [관리 되는 장치](require-managed-devices.md)합니다. 


사이트 또는 서비스에 직접 액세스 하면 관련 정책의 영향 평가 하기 위해 일반적으로 쉽습니다. 예를 들어, 구성 된 SharePoint Online에 대 한 MFA를 요구 하는 정책을 경우 SharePoint 웹 포털에 각 로그인에 MFA 적용 됩니다. 그러나이 아닌 경우 항상 간단 되므로 다른 클라우드 앱에 대 한 종속성을 사용 하 여 클라우드 앱 정책의 영향을 평가 하려면 예를 들어, Microsoft Teams SharePoint online을 활용합니다. 현재이 시나리오에서 Microsoft Teams 액세스 하면은 또한 SharePoint MFA 정책이 적용 합니다.   


## <a name="policy-enforcement"></a>정책 적용 

구성 서비스 종속성에 있는 경우 초기 바인딩된 또는 런타임에 바인딩된 적용을 사용 하 여 정책을 적용할 수 있습니다. 

**초기 바인딩된 정책 적용** 의미 사용자 호출한 응용 프로그램에 액세스 하기 전에 종속 서비스가 정책을 충족 해야 합니다. 예를 들어, 사용자는 MS 팀에 로그인 하기 전에 SharePoint 정책을 충족 해야 합니다. 

**런타임에 바인딩된 정책 적용** 호출한 응용 프로그램에 사용자가 로그인 한 후 발생 합니다. 응용 프로그램 요청을 다운스트림 서비스에 대 한 토큰을 호출 하는 경우 적용으로 지연 됩니다. 예제에는 플래너 및 SharePoint 액세스 Office.com에 액세스 하는 MS 팀 포함 됩니다. 

아래 다이어그램에서는 MS 팀 서비스 종속성을 보여 줍니다. 실선 화살표 Planner 나타냅니다 바인딩된 적용에 대 한 초기 바인딩 적용 파선된 화살표를 나타냅니다. 



![MS 팀 서비스 종속성](./media/service-dependencies/01.png)



  

모범 사례로, 관련된 앱 및 가능 하면 서비스에서 일반적인 정책을 설정 해야 합니다. 일관 된 보안 상태에 있는 최상의 사용자 환경을 제공 합니다. 예를 들어 Exchange Online에서 일반적인 정책 설정, SharePoint Online, MS 팀과 비즈니스용 Skype을 크게 줄여줍니다 다운스트림 서비스에 적용 되 고 다른 정책에서 발생할 수 있는 예기치 않은 프롬프트. 

다음 표에서 클라이언트 앱을 충족 해야 하는 추가 서비스 종속성을 나열  

| 클라이언트 앱         | 다운스트림 서비스                          | 적용 |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure 관리 (포털 및 API) | 초기 바인딩 |
| Microsoft 클래스 룸 | Exchange                                    | 초기 바인딩 |
|                     | SharePoint                                  | 런타임에 바인딩  |
| Microsoft 팀     | Exchange                                    | 초기 바인딩 |
|                     | MS Planner                                  | 런타임에 바인딩  |
|                     | SharePoint                                  | 초기 바인딩 |
|                     | 비즈니스 온라인용 Skype                   | 초기 바인딩 |
| Office 포털       | Exchange                                    | 런타임에 바인딩  |
|                     | SharePoint                                  | 런타임에 바인딩  |
| Outlook의 그룹      | Exchange                                    | 초기 바인딩 |
|                     | SharePoint                                  | 초기 바인딩 |
| PowerApps           | Microsoft Azure 관리 (포털 및 API) | 초기 바인딩 |
|                     | Windows Azure Active Directory              | 초기 바인딩 |
| Project             | Dynamics CRM                                | 초기 바인딩 |
| 비즈니스용 Skype  | Exchange                                    | 초기 바인딩 |
| Visual Studio       | Microsoft Azure 관리 (포털 및 API) | 초기 바인딩 |



## <a name="next-steps"></a>다음 단계

사용자 환경에서 조건부 액세스를 구현하는 방법은 [Azure Active Directory에서 조건부 액세스 배포 계획](plan-conditional-access.md)을 참조하세요.
