---
title: Azure AD 로그인 페이지에서 Microsoft 계정을 수락합니까 | 마이크로 소프트 문서
description: 로그인 하는 동안 사용자 이름 조회를 반영 하는 화면 메시징
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024278"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Azure 활성 디렉터리에서 Microsoft 계정에 대한 로그인 옵션

Azure Active Directory(Azure AD)에 대한 Microsoft 365 로그인 페이지는 직장 또는 학교 계정 및 Microsoft 계정을 지원하지만 사용자의 상황에 따라 하나 또는 둘 중 하나 또는 둘 다일 수 있습니다. 예를 들어 Azure AD 로그인 페이지는 다음을 지원합니다.

* 두 유형의 계정 모두에서 로그인을 허용하는 앱
* 게스트를 수락하는 조직

## <a name="identification"></a>Identification(식별)
조직에서 사용하는 로그인 페이지가 사용자 이름 필드의 힌트 텍스트를 확인하여 Microsoft 계정을 지원하는지 확인할 수 있습니다. 힌트 텍스트에 "전자 메일, 전화 또는 Skype"라고 표시되면 로그인 페이지가 Microsoft 계정을 지원합니다.

![계정 로그인 페이지 간의 차이](./media/signin-account-support/ui-prompt.png)

[추가 로그인 옵션은 개인 Microsoft 계정에서만 작동하지만](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) 직장 또는 학교 계정 리소스에 로그인하는 데 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

[로그인 브랜딩 사용자 지정](../fundamentals/add-custom-domain.md)