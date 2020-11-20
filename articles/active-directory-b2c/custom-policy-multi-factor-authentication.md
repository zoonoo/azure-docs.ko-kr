---
title: Azure Active Directory B2C의 Multi-Factor Authentication | Microsoft Docs
description: Azure Active Directory B2C에서 보호하는 소비자 지향 애플리케이션에서 Multi-Factor Authentication을 사용하는 방법입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 39d23313f9d00ef48f64327dd10356a91b9d13b7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951896"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 다단계 인증 사용 | Microsoft Docs

Azure Active Directory B2C (Azure AD B2C)는 [AZURE AD Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) 와 직접 통합 되므로 응용 프로그램에서 등록 및 로그인 환경에 두 번째 보안 계층을 추가할 수 있습니다. 코드 작성 없이도 다단계 인증을 사용하도록 설정할 수 있습니다. 이미 가입 및 로그인 사용자 흐름을 만든 경우에도 다단계 인증을 사용할 수 있습니다.

이 기능을 통해 응용 프로그램은 다음과 같은 시나리오를 처리할 수 있습니다.

- 하나의 애플리케이션에 액세스하려는 경우 다단계 인증이 필요하지 않지만 다른 애플리케이션에 액세스하려면 다단계 인증이 필요합니다. 예를 들어 고객이 소셜 또는 로컬 계정을 사용하여 자동차 보험 애플리케이션에 로그인할 수 있지만 동일한 디렉터리에 등록된 주택 보험 애플리케이션에 액세스하기 전에 전화번호를 확인해야 합니다.
- 일반적으로 애플리케이션에 액세스하는 데 다단계 인증이 필요하지 않지만 그 안에 중요한 부분에 액세스하려면 다단계 인증이 필요합니다. 예를 들어 고객이 소셜 또는 로컬 계정으로 은행 애플리케이션에 로그인하고 계정 잔액을 확인할 수 있지만 유선 전송을 시도하기 전에 전화번호를 확인해야 합니다.

## <a name="set-multi-factor-authentication"></a>다단계 인증 설정

1. [Azure 포털](https://portal.azure.com)
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 사용 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 선택 합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C** 를 선택합니다. 또는 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. MFA를 사용 하도록 설정할 사용자 흐름을 선택 합니다. 예를 들어 *B2C_1_signinsignup* 합니다.
1. **속성** 을 선택합니다.
1. **다단계 인증** 섹션에서 원하는 **mfa 방법을** 선택한 다음 **mfa 적용** 에서 **Always on** 또는 **[조건부](conditional-access-user-flow.md) (권장)** 를 선택 합니다. 조건에 대해 [조건부 액세스 정책](conditional-access-identity-protection-setup.md) 정책을 만들고 정책을 적용할 앱을 지정 합니다. 
1. [저장]을 선택합니다. 이제이 사용자 흐름에 대해 MFA를 사용할 수 있습니다.

**사용자 흐름 실행** 을 사용하여 환경을 확인할 수 있습니다. 다음 시나리오를 확인합니다.

다단계 인증 단계가 실행되기 전에 테넌트에 고객 계정이 만들어집니다. 이 단계에서 고객에게 자신의 전화 번호를 제공하고 확인하라는 메시지가 표시됩니다. 확인에 성공한 경우 전화번호는 나중에 사용하도록 고객 계정에 연결됩니다. 고객이 취소 또는 삭제하더라도 다음에 로그인하는 동안 다시 전화 번호를 확인하도록 요청할 수 있습니다(다단계 인증을 사용하도록 설정한 경우).
