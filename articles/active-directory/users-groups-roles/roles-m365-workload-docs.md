---
title: Microsoft 365 워크로드용 관리자 역할 콘텐츠 - Azure AD | Microsoft Docs
description: Azure Active Directory에서 Microsoft 365 워크로드의 관리자 역할용 콘텐츠 및 API 참조를 확인할 수 있습니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 9ef400a5c7b42f6782fefa28e2351b09d8667861
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470249"
---
# <a name="administrator-roles-for-microsoft-365-workloads"></a>Microsoft 365 워크로드용 관리자 역할

Azure AD의 관리 역할을 사용해 Microsoft 365의 모든 제품을 관리할 수 있습니다. 제품과 관련된 추가 역할이 제공되는 제품도 있습니다. 각 제품에서 지원하는 역할에 대한 내용은 아래 표를 참조하세요. 위임 문제와 관련한 일반 설명은 [Azure Active Directory의 역할 위임 계획](roles-concept-delegation.md)에서 확인할 수 있습니다.

## <a name="where-to-find-content"></a>콘텐츠를 확인할 수 있는 위치

Microsoft 365 워크로드 | 역할 콘텐츠 | API 콘텐츠
---------------------- | ------------------ | -----------------
Office 365 및 Microsoft 365 비즈니스 플랜의 관리자 역할 | [Office 365 관리자 역할](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | 사용할 수 없음
Azure AD(Azure Active Directory) 및 Azure AD ID 보호| [Azure AD 관리자 역할](directory-assign-admin-roles.md) | [그래프 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[역할 할당 가져오기](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Exchange 역할 기반 액세스 제어](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [Exchange용 PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[역할 할당 가져오기](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Azure AD 관리자 역할](directory-assign-admin-roles.md)<br>[Office 365의 SharePoint 관리자 역할 정보](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [그래프 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[역할 할당 가져오기](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Teams/비즈니스용 Skype | [Azure AD 관리자 역할](directory-assign-admin-roles.md) | [그래프 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[역할 할당 가져오기](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
보안 및 준수 센터(Office 365 Advanced Threat Protection, Exchange Online Protection, Information Protection) | [Office 365 관리자 역할](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[역할 할당 가져오기](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
보안 점수 | [Azure AD 관리자 역할](directory-assign-admin-roles.md) | [그래프 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[역할 할당 가져오기](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
준수 관리자 | [준수 관리자 역할](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | 사용할 수 없음
Azure Information Protection | [Azure AD 관리자 역할](directory-assign-admin-roles.md) | [그래프 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[역할 할당 가져오기](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [역할 기반 액세스 제어](https://docs.microsoft.com/cloud-app-security/manage-admins) | [API 참조](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Azure ATP 역할 그룹](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | 사용할 수 없음
Windows Defender Advanced Threat Protection | [Windows Defender ATP 역할 기반 액세스 제어](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | 사용할 수 없음
Privileged Identity Management | [Azure AD 관리자 역할](directory-assign-admin-roles.md) | [그래프 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[역할 할당 가져오기](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Intune 역할 기반 액세스 제어](https://docs.microsoft.com/intune/role-based-access-control) | [그래프 API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[역할 할당 가져오기](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Managed Desktop | [Azure AD 관리자 역할](directory-assign-admin-roles.md) | [그래프 API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[역할 할당 가져오기](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>다음 단계

* [Azure AD 관리자 역할을 할당 또는 제거하는 방법](directory-manage-roles-portal.md)
* [Azure AD 관리자 역할 참조](directory-assign-admin-roles.md)
