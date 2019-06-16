---
title: Azure Active Directory에서 조건부 액세스로 Azure management에 대 한 액세스 관리
description: Azure AD의 조건부 액세스를 사용 하 여 Azure 관리에 대 한 액세스를 관리 하는 방법을 알아봅니다.
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
ms.date: 01/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 4ee58835a5055f5382cefa3a049424ffe97a01c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122952"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>조건부 액세스를 사용 하 여 Azure management에 대 한 액세스 관리

Azure Active Directory (Azure AD)에서 조건부 액세스는 클라우드 지정 하는 특정 조건에 따라 앱에 대 한 액세스를 제어 합니다. 액세스를 허용 하려면 정책의 요구 사항을 충족 여부에 따라 액세스를 허용할지 여부를 지정 하는 조건부 액세스 정책을 만들어야 합니다. 

일반적으로 클라우드 앱에 대 한 액세스를 제어 하려면 조건부 액세스를 사용 합니다. 특정 조건(예: 로그인 위험, 위치 또는 디바이스)을 기준으로 Azure Management에 대한 액세스를 제어하고 다단계 인증 등의 요구 사항을 시행하는 정책을 설정할 수도 있습니다.

Azure Management에 대한 정책을 만들려면 정책을 적용할 앱을 선택할 때 **클라우드 앱**에서 **Microsoft Azure Management**를 선택합니다.

![Azure 관리에 대 한 조건부 액세스](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

만든 정책은 Azure Portal, Azure Resource Manager 공급자, 클래식 Service Management API, Azure PowerShell 및 Visual Studio 구독 관리자 포털 등을 비롯한 모든 Azure Management 엔드포인트에 적용됩니다. 정책은 Azure Resource Manager API를 호출하는 Azure PowerShell에 적용됩니다. Microsoft Graph를 호출하는 [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)에는 적용되지 않습니다.

> [!CAUTION]
> Azure 관리에 대 한 액세스를 관리 하는 정책을 설정 하기 전에 조건부 액세스의 작동 방식을 이해 해야 합니다. 포털에 대한 자신의 액세스를 차단할 수 있는 조건은 만들지 않아야 합니다.

설정 및 조건부 액세스를 사용 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Active Directory의 조건부 액세스](../active-directory/active-directory-conditional-access-azure-portal.md)합니다.