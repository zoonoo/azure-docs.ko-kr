---
title: Microsoft 365 서비스에서 관리자 역할 문서-Azure AD | Microsoft Docs
description: Azure Active Directory에서 Microsoft 365 services의 관리자 역할에 대 한 콘텐츠 및 API 참조를 찾습니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5579e0d2094c3dc596e8e50992ae6bc31818ff87
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743069"
---
# <a name="roles-for-microsoft-365-services-in-azure-active-directory"></a>Azure Active Directory의 Microsoft 365 서비스에 대 한 역할

Microsoft 365의 모든 제품은 Azure Active Directory (Azure AD)의 관리 역할로 관리할 수 있습니다. 제품과 관련된 추가 역할이 제공되는 제품도 있습니다. 각 제품에서 지원하는 역할에 대한 내용은 아래 표를 참조하세요. 위임 문제와 관련한 일반 설명은 [Azure Active Directory의 역할 위임 계획](concept-delegation.md)에서 확인할 수 있습니다.

## <a name="where-to-find-content"></a>콘텐츠를 확인할 수 있는 위치

Microsoft 365 서비스 | 역할 콘텐츠 | API 콘텐츠
---------------------- | ------------------ | -----------------
Office 365 및 Microsoft 365 비즈니스 플랜의 관리자 역할 | [Microsoft 365 관리자 역할](/office365/admin/add-users/about-admin-roles?view=o365-worldwide&preserve-view=true) | 사용할 수 없음
Azure AD(Azure Active Directory) 및 Azure AD ID 보호| [Azure AD 관리자 역할](permissions-reference.md) | [그래프 API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true&preserve-view=true)<br>[역할 할당 가져오기](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Exchange Online| [Exchange 역할 기반 액세스 제어](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [Exchange용 PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps&preserve-view=true)<br>[역할 할당 가져오기](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps&preserve-view=true)
SharePoint Online | [Azure AD 관리자 역할](permissions-reference.md)<br>또한 [Microsoft 365의 SharePoint 관리자 역할에 대 한 정보](/sharepoint/sharepoint-admin-role) | [그래프 API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[역할 할당 가져오기](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Teams/비즈니스용 Skype | [Azure AD 관리자 역할](permissions-reference.md) | [그래프 API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[역할 할당 가져오기](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
보안 및 준수 센터(Office 365 Advanced Threat Protection, Exchange Online Protection, Information Protection) | [Office 365 관리자 역할](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps&preserve-view=true)<br>[역할 할당 가져오기](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps&preserve-view=true)
보안 점수 | [Azure AD 관리자 역할](permissions-reference.md) | [그래프 API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[역할 할당 가져오기](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
규정 준수 관리자 | [준수 관리자 역할](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | 사용할 수 없음
Azure Information Protection | [Azure AD 관리자 역할](permissions-reference.md) | [그래프 API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[역할 할당 가져오기](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Microsoft Cloud App Security | [역할 기반 액세스 제어](/cloud-app-security/manage-admins) | [API 참조](/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Azure ATP 역할 그룹](/azure-advanced-threat-protection/atp-role-groups) | 사용할 수 없음
Windows Defender Advanced Threat Protection | [Windows Defender ATP 역할 기반 액세스 제어](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | 사용할 수 없음
Privileged Identity Management | [Azure AD 관리자 역할](permissions-reference.md) | [그래프 API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[역할 할당 가져오기](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Intune | [Intune 역할 기반 액세스 제어](/intune/role-based-access-control) | [그래프 API](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta&preserve-view=true)<br>[역할 할당 가져오기](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta&preserve-view=true)
Managed Desktop | [Azure AD 관리자 역할](permissions-reference.md) | [그래프 API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[역할 할당 가져오기](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)

## <a name="next-steps"></a>다음 단계

* [Azure AD 관리자 역할을 할당 또는 제거하는 방법](manage-roles-portal.md)
* [Azure AD 관리자 역할 참조](permissions-reference.md)