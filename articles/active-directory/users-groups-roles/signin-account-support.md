---
title: Azure AD 로그인 페이지에 Microsoft 계정 허용 하는 경우를 파악 하는 방법을 | Microsoft Docs
description: 화면의 어떻게 로그인 하는 동안 사용자 이름 조회를 반영 메시징
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d26ff0f9259e3531259673f94fe477444cc786b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60468222"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Azure Active Directory에서 Microsoft 계정에 대 한 로그인 옵션

작업 또는 학교 계정과 Microsoft 계정을 Azure Active Directory (Azure AD)에 대 한 Microsoft 365 로그인 페이지에서 지원 되지만 사용자의 상황에 따라이 하나 또는 다른 또는 둘 다 있습니다. 예를 들어, Azure AD 로그인 페이지를 지원합니다.

* 두 가지 유형의 계정에서 로그인 할 수 있는 앱
* 게스트를 허용 하는 조직

## <a name="identification"></a>Identification(식별)
조직에서 사용 하는 로그인 페이지 사용자 이름 필드의 힌트 텍스트를 확인 하 여 Microsoft 계정을 지원 하는지 확인할 수 있습니다. "전자 메일, 휴대폰 또는 Skype" 이라는 힌트 텍스트가 표시를 하는 경우 로그인 페이지는 Microsoft 계정을 지원 합니다.

![계정 로그인 페이지 간의 차이](./media/signin-account-support/ui-prompt.png)

[추가 로그인 옵션 개인 Microsoft 계정에 대해서만 작동](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) 있지만 회사 또는 학교 계정 리소스에 로그인에 대해 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

[브랜딩 사용자 지정 프로그램에 로그인](../fundamentals/add-custom-domain.md)