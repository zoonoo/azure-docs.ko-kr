---
title: Azure AD에서 조건부 액세스로 Azure 관리에 대한 액세스 관리
description: Azure AD에서 조건부 액세스를 사용하여 Azure 관리에 대한 액세스를 관리하는 방법을 알아봅니다.
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
ms.openlocfilehash: 2a52635dbaa7a76034f3a535b099320a901e8c07
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758778"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>조건부 액세스로 Azure 관리에 대한 액세스 관리

> [!CAUTION]
> Azure 관리에 대한 액세스를 관리하는 정책을 설정하기 전에 조건부 액세스가 어떻게 작동하는지 이해해야 합니다. 포털에 대한 자신의 액세스를 차단할 수 있는 조건은 만들지 않아야 합니다.

Azure AD(Azure Active Directory)의 조건부 액세스는 사용자가 지정한 특정 조건을 기반으로 클라우드 앱에 대한 액세스를 제어합니다. 액세스를 허용하려면 정책의 요구 사항에 대한 부합 여부를 기준으로 액세스를 허용 또는 차단하는 조건부 액세스 정책을 만듭니다. 

일반적으로 조건부 액세스를 사용하여 클라우드 앱에 대한 액세스를 제어합니다. 특정 조건(예: 로그인 위험, 위치 또는 디바이스)을 기준으로 Azure Management에 대한 액세스를 제어하고 다단계 인증 등의 요구 사항을 시행하는 정책을 설정할 수도 있습니다.

Azure Management에 대한 정책을 만들려면 정책을 적용할 앱을 선택할 때 **클라우드 앱**에서 **Microsoft Azure Management**를 선택합니다.

![Azure 관리에 대한 조건부 액세스](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

사용자가 만든 정책은 다음을 비롯한 모든 Azure 관리 엔드포인트에 적용됩니다.

- Azure portal
- Azure Resource Manager 공급자
- 클래식 Service Management APIs
- Azure PowerShell
- Visual Studio 구독 관리자 포털
- Azure DevOps
- Azure Data Factory 포털

정책은 Azure Resource Manager API를 호출하는 Azure PowerShell에 적용됩니다. Microsoft Graph를 호출하는 [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)에는 적용되지 않습니다.

Microsoft Azure 관리에 대한 조건부 액세스를 사용하도록 설정하는 샘플 정책을 설정하는 방법에 대한 자세한 내용은 문서 [조건부 액세스: Azure 관리용 MFA 필요](../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md)를 참조하세요.
