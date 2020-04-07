---
title: 인증
description: 인증 작동 방식 설명
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.openlocfilehash: fbc27f2fa3b7517151c4bcdbec5b146e83dd868e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681637"
---
# <a name="configure-authentication"></a>인증 구성

Azure 원격 렌더링은 [ASA(Azure 공간 앵커)와](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp)동일한 인증 메커니즘을 사용합니다. 클라이언트는 REST API를 성공적으로 호출하기 위해 *AccountKey,* *AuthenticationToken*또는 *AccessToken을* 설정해야 합니다. *AccountKey는* Azure 포털의 원격 렌더링 계정에 대한 "키" 탭에서 가져올 수 있습니다. *인증 토큰은* [ADAL 라이브러리를](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)사용하여 얻을 수 있는 Azure AD 토큰입니다. *AccessTokenAzure* 혼합 현실 보안 토큰 서비스 (STS)에서 얻을 수 있는 MR 토큰입니다.

## <a name="authentication-for-deployed-applications"></a>배포된 응용 프로그램에 대한 인증

 빠른 온보딩에는 계정 키를 사용하는 것이 좋지만 개발/프로토타이핑 중에는 사용하는 것이 좋습니다. 응용 프로그램에 포함된 계정 키를 사용하여 프로덕션으로 제공하지 말고 대신 사용자 기반 또는 서비스 기반 Azure AD 인증 방법을 사용하는 것이 좋습니다.

 Azure AD 인증은 [ASA(Azure 공간 앵커)](https://docs.microsoft.com/azure/spatial-anchors/) 서비스의 [Azure AD 사용자 인증](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) 섹션에 설명되어 있습니다.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

서비스의 응용 프로그램, 서비스 또는 Azure AD 사용자에게 부여된 액세스 수준을 제어하기 위해 Azure 원격 렌더링 계정에 대해 필요에 따라 할당할 수 있도록 다음 역할이 만들어졌습니다.

* **원격 렌더링 관리자**: Azure 원격 렌더링에 대한 변환, 세션, 렌더링 및 진단 기능을 사용자에게 제공합니다.
* **원격 렌더링 클라이언트**: Azure 원격 렌더링에 대한 관리 세션, 렌더링 및 진단 기능을 사용자에게 제공합니다.

## <a name="next-steps"></a>다음 단계

* [계정 만들기](create-an-account.md)
* [인증을 위해 Azure 프런트 엔드 API 사용](frontend-apis.md)
* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)
