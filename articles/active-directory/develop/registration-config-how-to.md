---
title: 엔드포인트 구성
description: 개발 중이거나 Azure AD에 등록 중인 사용자 지정 애플리케이션의 인증 엔드포인트를 찾는 방법입니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: 043dc4f6e57620f58a1cf5f76db755703421800f
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82778739"
---
# <a name="how-to-configure-endpoints"></a>엔드포인트 구성 방법

[Azure Portal](https://portal.azure.com)에서 애플리케이션의 인증 엔드포인트를 찾을 수 있습니다.

-   [Azure Portal](https://portal.azure.com)로 이동 합니다.

-   왼쪽 탐색 창에서 **Azure Active Directory**를 클릭합니다.

-   **앱 등록**을 클릭하고 **엔드포인트**를 선택합니다.

-   그러면 테넌트의 모든 인증 엔드포인트가 나열되는 **엔드포인트** 페이지가 열립니다.

-   애플리케이션 ID와 함께 사용하는 인증 프로토콜에 해당하는 엔드포인트를 사용하여 애플리케이션에 해당하는 인증 요청을 만듭니다.

**국가별 클라우드** (예: Azure ad 중국, 독일 및 미국 정부)에는 고유한 앱 등록 포털과 Azure AD 인증 끝점이 있습니다. [국가별 클라우드 개요](authentication-national-cloud.md)에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory 개발자 가이드](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)
