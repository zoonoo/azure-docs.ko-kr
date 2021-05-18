---
title: Azure AD 앱 등록에 대한 엔드포인트 가져오기
titleSuffix: Microsoft identity platform
description: 개발 중이거나 Azure AD에 등록 중인 사용자 지정 애플리케이션의 인증 엔드포인트를 찾는 방법입니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 778523869715916bf1e4c32af59ea7a0081df91b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103314"
---
# <a name="how-to-discover-endpoints"></a>엔드포인트 검색 방법

[Azure Portal](https://portal.azure.com)에서 애플리케이션의 인증 엔드포인트를 찾을 수 있습니다.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. **Azure Active Directory** 를 선택합니다.
1. **관리** 에서 **앱 등록** 을 선택한 다음 최상위 메뉴에서 **엔드포인트** 를 선택합니다.

    **엔드포인트** 페이지가 표시되고 테넌트의 인증 엔드포인트가 표시됩니다.
    
    **애플리케이션 (클라이언트) ID** 와 함께 사용하는 인증 프로토콜과 일치하는 엔드포인트를 사용하여 애플리케이션에 해당하는 인증 요청을 만듭니다.

**국가별 클라우드**(예: Azure ad 중국, 독일 및 미국 정부)에는 고유한 앱 등록 포털 및 Azure AD 인증 엔드포인트가 있습니다. [국가별 클라우드 개요](authentication-national-cloud.md)에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

다른 Azure 환경의 엔드포인트에 대한 자세한 내용은 [국가별 클라우드 개요](authentication-national-cloud.md)를 참조하세요.
