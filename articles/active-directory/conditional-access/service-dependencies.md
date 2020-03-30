---
title: 조건부 액세스 서비스 종속성 - Azure Active Directory
description: Azure Active Directory 조건부 액세스에서 조건을 사용하여 정책을 트리거하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b39238575c05d35a2d87999e08c49c0c77e99bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74380023"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Azure Active 디렉터리 조건부 액세스의 서비스 종속성이란 무엇입니까? 

조건부 액세스 정책을 사용하여 웹 사이트 및 서비스에 대한 액세스 요구 사항을 지정할 수 있습니다. 예를 들어 액세스 요구 사항에는 MFA(다단계 인증) 또는 [관리되는 장치가](require-managed-devices.md)포함될 수 있습니다. 

사이트 나 서비스에 직접 액세스하는 경우 관련 정책의 영향을 평가하기가 쉽습니다. 예를 들어 SharePoint Online 에 대해 MFA가 구성된 정책이 있는 경우 SharePoint 웹 포털에 로그인할 때마다 MFA가 적용됩니다. 그러나 다른 클라우드 앱에 종속된 클라우드 앱이 있기 때문에 정책의 영향을 평가하는 것이 항상 간단하지는 않습니다. 예를 들어 Microsoft 팀은 SharePoint Online의 리소스에 대한 액세스를 제공할 수 있습니다. 따라서 현재 시나리오에서 Microsoft 팀에 액세스하면 SharePoint MFA 정책도 적용됩니다.   

## <a name="policy-enforcement"></a>정책 적용 

서비스 종속성을 구성된 경우 초기 바인딩 또는 라그 바운드 적용을 사용하여 정책이 적용될 수 있습니다. 

- **조기 바인딩된 정책 적용은** 사용자가 호출 앱에 액세스하기 전에 종속 서비스 정책을 충족해야 함을 의미합니다. 예를 들어 사용자는 MS Teams에 로그인하기 전에 SharePoint 정책을 충족해야 합니다. 
- 사용자가 호출 앱에 서명한 후 **늦게 바인딩된 정책 적용이** 발생합니다. 적용은 앱 요청을 호출할 때 다운스트림 서비스에 대한 토큰으로 연기됩니다. 예를 들어 플래너에 액세스하는 MS 팀과 공유지점에 액세스하는 Office.com 있습니다. 

아래 다이어그램은 MS Teams 서비스 종속성을 보여 줍니다. 솔리드 화살표는 플래너의 파선 화살표가 초기 바인딩 적용을 나타내며, 이는 늦은 바인딩 적용을 나타냅니다. 

![MS 팀 서비스 종속성](./media/service-dependencies/01.png)

가장 좋은 방법은 가능하면 관련 앱 및 서비스 간에 공통 정책을 설정해야 합니다. 일관된 보안 태세를 갖추면 최상의 사용자 환경을 제공합니다. 예를 들어 Exchange Online, SharePoint Online, Microsoft 팀 및 비즈니스용 Skype에서 공통 정책을 설정하면 다운스트림 서비스에 적용되는 다른 정책에서 발생할 수 있는 예기치 않은 프롬프트가 크게 줄어듭니다. 

아래 표에는 클라이언트 앱이 충족해야 하는 추가 서비스 종속성이 나열되어 있습니다.  

| 클라이언트 앱         | 다운스트림 서비스                          | 적용 |
| :--                 | :--                                         | ---         | 
| Azure 데이터 레이크     | 마이크로소프트 Azure 관리(포털 및 API) | 초기 바인딩 |
| 마이크로소프트 교실 | Exchange                                    | 초기 바인딩 |
|                     | SharePoint                                  | 초기 바인딩 |
| Microsoft 팀     | Exchange                                    | 초기 바인딩 |
|                     | MS 플래너                                  | 늦은 바인딩  |
|                     | SharePoint                                  | 초기 바인딩 |
|                     | 비즈니스 온라인용 Skype                   | 초기 바인딩 |
| 사무실 포털       | Exchange                                    | 늦은 바인딩  |
|                     | SharePoint                                  | 늦은 바인딩  |
| Outlook 그룹      | Exchange                                    | 초기 바인딩 |
|                     | SharePoint                                  | 초기 바인딩 |
| PowerApps           | 마이크로소프트 Azure 관리(포털 및 API) | 초기 바인딩 |
|                     | Windows Azure Active Directory              | 초기 바인딩 |
| Project             | Dynamics CRM                                | 초기 바인딩 |
| 비즈니스용 Skype  | Exchange                                    | 초기 바인딩 |
| Visual Studio       | 마이크로소프트 Azure 관리(포털 및 API) | 초기 바인딩 |
| Microsoft Forms     | Exchange                                    | 초기 바인딩 |
|                     | SharePoint                                  | 초기 바인딩 |
| Microsoft To-Do     | Exchange                                    | 초기 바인딩 |

## <a name="next-steps"></a>다음 단계

사용자 환경에서 조건부 액세스를 구현하는 방법을 알아보려면 [Azure Active Directory에서 조건부 액세스 배포 계획을 참조하세요.](plan-conditional-access.md)
