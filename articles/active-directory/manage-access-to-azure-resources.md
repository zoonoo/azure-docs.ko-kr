---
title: "Azure Active Directory를 사용하여 Azure 리소스에 대한 액세스 관리"
description: "Azure Active Directory의 다양한 기능을 사용하여 Azure 리소스에 대한 액세스를 관리하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: bryanla
ms.assetid: f66abf54-3809-436c-92b6-018e1179780e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2017
ms.author: skwan
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 17879d69b95186848a81e4ff4afdf8fc0d1a511e
ms.contentlocale: ko-kr
ms.lasthandoff: 09/26/2017

---

# <a name="manage-access-to-azure-resources-with-azure-active-directory"></a>Azure Active Directory를 사용하여 Azure 리소스에 대한 액세스 관리

클라우드 리소스에 대한 ID 및 액세스 관리는 클라우드를 사용하는 모든 조직에서 중요한 기능입니다.  Azure AD(Azure Active Directory)는 Microsoft Azure의 ID 및 액세스 시스템입니다.  

Azure AD는 Azure 리소스에 대한 액세스를 관리하기 위한 다음과 같은 기능을 제공합니다.

|||
|---|---|
| [Azure AD 테넌트와 구독 사이의 관계](active-directory-understanding-resource-access.md) | Azure AD가 어떻게 Azure 구독에 대한 사용자 및 그룹의 신뢰할 수 있는 소스인지 자세히 알아보세요. |
| [RBAC(역할 기반 액세스 제어)](role-based-access-control-what-is.md) | 사용자, 그룹 또는 서비스 주체에 할당된 역할을 통해 세분화된 액세스 관리를 제공합니다. |
| [RBAC로 Privileged Identity Management](pim-azure-resource.md) | 권한 있는 역할을 Just-In-Time에 할당하여 높은 권한의 액세스를 제어합니다. |
| [Azure 관리에 대한 조건부 액세스](conditional-access-azure-management.md) | Azure 관리 끝점에 대한 액세스를 허용 또는 차단하도록 조건부 액세스 정책을 설정합니다. |
| [MSI(관리되는 서비스 ID)](msi-overview.md) | Virtual Machines와 같은 Azure 리소스에 사용자 고유의 ID를 제공하므로 코드에 자격 증명을 넣지 않아도 됩니다. |

 
