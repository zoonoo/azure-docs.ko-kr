---
title: 지정된 API에 대한 사용 권한을 선택하는 방법 | Microsoft Docs
description: 개발 중이거나 Azure AD에 등록 중인 사용자 지정 응용 프로그램의 인증 엔드포인트를 찾는 방법입니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: f6c2540d8f0bb49491a428b085d20067a36d970a
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44723926"
---
# <a name="how-to-select-permissions-for-a-given-api"></a>지정된 API에 대한 사용 권한을 선택하는 방법

[Azure Portal](https://portal.azure.com)에서 응용 프로그램의 인증 엔드포인트를 찾을 수 있습니다.

-   [Azure Portal](https://portal.azure.com)로 이동합니다.

-   왼쪽 탐색 창에서 **Azure Active Directory**를 클릭합니다.

-   **앱 등록**을 클릭하고 **엔드포인트**를 선택합니다.

-   그러면 테넌트의 모든 인증 엔드포인트가 나열되는 **엔드포인트** 페이지가 열립니다.

-   응용 프로그램 ID와 함께 사용하는 인증 프로토콜에 해당하는 엔드포인트를 사용하여 응용 프로그램에 해당하는 인증 요청을 만듭니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory 개발자 가이드](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#authentication-and-authorization-protocols)
