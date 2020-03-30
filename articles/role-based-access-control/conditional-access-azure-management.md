---
title: Azure AD에서 조건부 액세스를 사용하여 Azure 관리에 대한 액세스 관리
description: Azure AD에서 조건부 액세스를 사용하여 Azure 관리에 대한 액세스를 관리하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: f3341f1c30a1581b8507652c322c00581e3972aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137402"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>조건부 액세스를 사용하여 Azure 관리에 대한 액세스 관리

> [!CAUTION]
> Azure 관리에 대한 액세스를 관리하는 정책을 설정하기 전에 조건부 Access의 작동 방식을 이해해야 합니다. 포털에 대한 자신의 액세스를 차단할 수 있는 조건은 만들지 않아야 합니다.

Azure Active Directory(Azure AD)의 조건부 액세스는 지정한 특정 조건에 따라 클라우드 앱에 대한 액세스를 제어합니다. 액세스를 허용하려면 정책의 요구 사항이 충족되는지 여부에 따라 액세스를 허용하거나 차단하는 조건부 액세스 정책을 만듭니다. 

일반적으로 조건부 액세스를 사용하여 클라우드 앱에 대한 액세스를 제어합니다. 특정 조건(예: 로그인 위험, 위치 또는 디바이스)을 기준으로 Azure Management에 대한 액세스를 제어하고 다단계 인증 등의 요구 사항을 시행하는 정책을 설정할 수도 있습니다.

Azure Management에 대한 정책을 만들려면 정책을 적용할 앱을 선택할 때 **클라우드 앱**에서 **Microsoft Azure Management**를 선택합니다.

![Azure 관리에 대한 조건부 액세스](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

만드는 정책은 다음을 포함하여 모든 Azure 관리 끝점에 적용됩니다.

- Azure portal
- Azure 리소스 관리자 공급자
- 클래식 서비스 관리 API
- Azure PowerShell
- 비주얼 스튜디오 구독 관리자 포털
- Azure DevOps
- Azure 데이터 팩토리 포털

정책은 Azure Resource Manager API를 호출하는 Azure PowerShell에 적용됩니다. Microsoft Graph를 호출하는 [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)에는 적용되지 않습니다.


조건부 액세스를 설정하고 사용하는 방법에 대한 자세한 내용은 [Azure Active Directory의 조건부 액세스를](../active-directory/active-directory-conditional-access-azure-portal.md)참조하십시오.
