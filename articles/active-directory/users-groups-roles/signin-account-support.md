---
title: Azure AD 로그인 페이지에서 Microsoft 계정을 수락 합니까? Microsoft Docs
description: 화면 메시지에서 로그인 중에 사용자 이름 조회를 반영 하는 방법
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221ab7c50a84650f1b2adf3fdb2b284365795f42
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024278"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Azure Active Directory Microsoft 계정에 대 한 로그인 옵션

Azure Active Directory (Azure AD)에 대 한 Microsoft 365 로그인 페이지는 회사 또는 학교 계정 및 Microsoft 계정을 지원 하지만 사용자의 상황에 따라 하나 또는 둘 다 일 수 있습니다. 예를 들어 Azure AD 로그인 페이지는 다음을 지원 합니다.

* 두 계정 유형에 서의 로그인을 허용 하는 앱
* 게스트를 수락 하는 조직

## <a name="identification"></a>Identification(식별)
조직에서 사용 하는 로그인 페이지에서 사용자 이름 필드의 힌트 텍스트를 살펴보면 Microsoft 계정을 지원 하는지 알 수 있습니다. 힌트 텍스트가 "전자 메일, 휴대폰 또는 Skype" 인 경우 로그인 페이지는 Microsoft 계정을 지원 합니다.

![계정 로그인 페이지의 차이점](./media/signin-account-support/ui-prompt.png)

[추가 로그인 옵션은 개인 Microsoft 계정에 대해서만 작동](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) 하지만 회사 또는 학교 계정 리소스에 로그인 하는 데 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

[로그인 브랜딩 사용자 지정](../fundamentals/add-custom-domain.md)