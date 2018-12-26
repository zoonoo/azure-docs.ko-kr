---
title: Azure Active Directory B2C의 Multi-Factor Authentication | Microsoft Docs
description: Azure Active Directory B2C에서 보호하는 소비자 지향 응용 프로그램에서 Multi-Factor Authentication을 사용하는 방법입니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: eabae0f3575719c6cb93affefe0a393dd13d1439
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014009"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 다단계 인증 사용 | Microsoft Docs

Azure Active Directory(Azure AD) B2C는 [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)에 직접 통합하여 애플리케이션에서 등록 및 로그인하는 데 두 번째 보안 계층을 추가할 수 있습니다. 코드 작성 없이도 다단계 인증을 사용하도록 설정할 수 있습니다. 이미 등록 및 로그인 정책을 만든 경우에도 다단계 인증을 사용할 수 있습니다.

이 기능은 응용 프로그램에서 다음과 같은 시나리오를 처리하는 데 도움이 됩니다.

- 하나의 애플리케이션에 액세스하려는 경우 다단계 인증이 필요하지 않지만 다른 애플리케이션에 액세스하려면 다단계 인증이 필요합니다. 예를 들어 고객이 소셜 또는 로컬 계정을 사용하여 자동차 보험 애플리케이션에 로그인할 수 있지만 동일한 디렉터리에 등록된 주택 보험 애플리케이션에 액세스하기 전에 전화번호를 확인해야 합니다.
- 일반적으로 애플리케이션에 액세스하는 데 다단계 인증이 필요하지 않지만 그 안에 중요한 부분에 액세스하려면 다단계 인증이 필요합니다. 예를 들어 고객이 소셜 또는 로컬 계정으로 은행 애플리케이션에 로그인하고 계정 잔액을 확인할 수 있지만 유선 전송을 시도하기 전에 전화번호를 확인해야 합니다.

## <a name="set-multi-factor-authentication"></a>다단계 인증 설정

정책을 만들 때 다단계 인증을 사용하도록 설정하는 옵션이 있습니다.

![다단계 인증 설정](./media/active-directory-b2c-reference-mfa/add-policy.png)

**State**를 **On**으로 설정합니다.

정책에 **지금 실행**을 사용하여 환경을 확인할 수 있습니다. 다음 시나리오를 확인합니다.

다단계 인증 단계가 실행되기 전에 테넌트에 고객 계정이 만들어집니다. 이 단계에서 고객에게 자신의 전화 번호를 제공하고 확인하라는 메시지가 표시됩니다. 확인에 성공한 경우 전화번호는 나중에 사용하도록 고객 계정에 연결됩니다. 고객이 취소 또는 삭제하더라도 다음에 로그인하는 동안 다시 전화 번호를 확인하도록 요청할 수 있습니다(다단계 인증을 사용하도록 설정한 경우).

## <a name="add-multi-factor-authentication"></a>다단계 인증 추가

이전에 만든 정책에서 다단계 인증을 사용하도록 설정할 수 있습니다. 

다단계 인증을 사용하도록 설정하려면

1. 정책을 열고 **편집**을 선택합니다. 
2. **다단계 인증**을 선택합니다.
3. **State**를 **On**으로 설정합니다.
4. 페이지 위쪽에서 **저장**을 클릭합니다.


