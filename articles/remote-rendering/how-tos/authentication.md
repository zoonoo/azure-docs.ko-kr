---
title: 인증
description: 인증이 작동 하는 방식을 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.openlocfilehash: fbc27f2fa3b7517151c4bcdbec5b146e83dd868e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681637"
---
# <a name="configure-authentication"></a>인증 구성

Azure 원격 렌더링은 [Azure 공간 앵커 (global.asa)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp)와 동일한 인증 메커니즘을 사용 합니다. 클라이언트는 REST Api를 성공적으로 호출 하도록 *AccountKey*, *Authenticationtoken*또는 *AccessToken* 를 설정 해야 합니다. *AccountKey* 는 Azure Portal의 원격 렌더링 계정에 대 한 "키" 탭에서 가져올 수 있습니다. *Authenticationtoken* 은 [ADAL 라이브러리](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)를 사용 하 여 가져올 수 있는 Azure AD 토큰입니다. *AccessToken* 는 Azure MIXED Reality STS (보안 토큰 서비스)에서 가져올 수 있는 MR 토큰입니다.

## <a name="authentication-for-deployed-applications"></a>배포 된 응용 프로그램에 대 한 인증

 계정 키를 사용 하는 것은 신속한 탑재를 위해 사용 하는 것이 좋습니다. 포함 된 계정 키를 사용 하 여 프로덕션에 응용 프로그램을 제공 하지 말고 사용자 기반 또는 서비스 기반 Azure AD 인증 방법을 사용 하는 것이 좋습니다.

 Azure AD 인증은 azure [공간 앵커 (global.asa)](https://docs.microsoft.com/azure/spatial-anchors/) 서비스의 [azure ad 사용자 인증](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) 섹션에 설명 되어 있습니다.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

서비스의 응용 프로그램, 서비스 또는 Azure AD 사용자에 게 부여 되는 액세스 수준을 제어 하기 위해 Azure 원격 렌더링 계정에 필요에 따라 다음과 같은 역할이 생성 됩니다.

* **원격 렌더링 관리자**: Azure 원격 렌더링을 위한 변환, 관리 세션, 렌더링 및 진단 기능을 사용자에 게 제공 합니다.
* **원격 렌더링 클라이언트**: Azure 원격 렌더링을 위한 관리 세션, 렌더링 및 진단 기능을 사용자에 게 제공 합니다.

## <a name="next-steps"></a>다음 단계

* [계정 만들기](create-an-account.md)
* [인증에 Azure 프런트 엔드 Api 사용](frontend-apis.md)
* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)
