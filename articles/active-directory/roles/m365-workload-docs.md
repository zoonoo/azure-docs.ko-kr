---
title: Microsoft 365 서비스 전반의 관리자 역할 문서 - Azure AD | Microsoft Docs
description: Azure Active Directory에서 Microsoft 365 서비스에 대한 관리자 역할의 콘텐츠 및 API 참조 확인
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
ms.openlocfilehash: ae5761c4a31662f447a107e6b5eb89bcf02f70c8
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055538"
---
# <a name="roles-for-microsoft-365-services-in-azure-active-directory"></a>Azure Active Directory의 Microsoft 365 서비스에 대한 역할

Azure AD(Azure Active Directory)의 관리자 역할을 사용하여 Microsoft 365의 모든 제품을 관리할 수 있습니다. 제품과 관련된 추가 역할이 제공되는 제품도 있습니다. 각 제품에서 지원하는 역할에 대한 내용은 아래 표를 참조하세요. 역할 보안 플랜에 대한 지침은 [Azure AD에서 하이브리드 및 클라우드 배포에 대한 권한 있는 액세스 보안](security-planning.md)을 참조하세요.

## <a name="where-to-find-content"></a>콘텐츠를 확인할 수 있는 위치

Microsoft 365 서비스 | 역할 콘텐츠 | API 콘텐츠
---------------------- | ------------------ | -----------------
Office 365 및 Microsoft 365 비즈니스 플랜의 관리자 역할 | [Microsoft 365 관리자 역할](/office365/admin/add-users/about-admin-roles) | 사용할 수 없음
Azure AD(Azure Active Directory) 및 Azure AD ID 보호| [Azure AD 관리자 역할](permissions-reference.md) | [그래프 API](/graph/api/overview)<br>[역할 할당 가져오기](/graph/api/directoryrole-list)
Exchange Online| [Exchange 역할 기반 액세스 제어](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [Exchange용 PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry)<br>[역할 할당 가져오기](/powershell/module/exchange/role-based-access-control/get-rolegroup)
SharePoint Online | [Azure AD 관리자 역할](permissions-reference.md)<br>또한 [Microsoft 365의 SharePoint 관리자 역할에 대한 정보](/sharepoint/sharepoint-admin-role) | [그래프 API](/graph/api/overview)<br>[역할 할당 가져오기](/graph/api/directoryrole-list)
Teams/비즈니스용 Skype | [Azure AD 관리자 역할](permissions-reference.md) | [그래프 API](/graph/api/overview)<br>[역할 할당 가져오기](/graph/api/directoryrole-list)
보안 및 준수 센터(Office 365 Advanced Threat Protection, Exchange Online Protection, Information Protection) | [Office 365 관리자 역할](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry)<br>[역할 할당 가져오기](/powershell/module/exchange/role-based-access-control/get-rolegroup)
보안 점수 | [Azure AD 관리자 역할](permissions-reference.md) | [그래프 API](/graph/api/overview)<br>[역할 할당 가져오기](/graph/api/directoryrole-list)
규정 준수 관리자 | [준수 관리자 역할](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | 사용할 수 없음
Azure Information Protection | [Azure AD 관리자 역할](permissions-reference.md) | [그래프 API](/graph/api/overview)<br>[역할 할당 가져오기](/graph/api/directoryrole-list)
Microsoft Cloud App Security | [역할 기반 액세스 제어](/cloud-app-security/manage-admins) | [API 참조](/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Azure ATP 역할 그룹](/azure-advanced-threat-protection/atp-role-groups) | 사용할 수 없음
Windows Defender Advanced Threat Protection | [Windows Defender ATP 역할 기반 액세스 제어](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | 사용할 수 없음
Privileged Identity Management | [Azure AD 관리자 역할](permissions-reference.md) | [그래프 API](/graph/api/overview)<br>[역할 할당 가져오기](/graph/api/directoryrole-list)
Intune | [Intune 역할 기반 액세스 제어](/intune/role-based-access-control) | [그래프 API](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta&preserve-view=true)<br>[역할 할당 가져오기](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta&preserve-view=true)
Managed Desktop | [Azure AD 관리자 역할](permissions-reference.md) | [그래프 API](/graph/api/overview)<br>[역할 할당 가져오기](/graph/api/directoryrole-list)

## <a name="next-steps"></a>다음 단계

* [Azure AD 관리자 역할을 할당 또는 제거하는 방법](manage-roles-portal.md)
* [Azure AD 관리자 역할 참조](permissions-reference.md)