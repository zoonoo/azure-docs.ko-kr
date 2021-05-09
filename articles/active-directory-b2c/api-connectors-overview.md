---
title: Azure AD B2C의 API 커넥터 정보
description: Azure AD(Azure Active Directory) API 커넥터를 사용하여 web API를 통해 등록 사용자 흐름을 사용자 지정하고 확장할 수 있습니다.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: d7a3301cb6ec10e75979d0e1fdfad52c7103a2aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102611524"
---
# <a name="use-api-connectors-to-customize-and-extend-sign-up-user-flows"></a>API 커넥터를 사용하여 등록 사용자 흐름 사용자 지정 및 확장

> [!IMPORTANT]
> 등록을 위한 API 커넥터는 Azure AD B2C의 퍼블릭 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="overview"></a>개요 
개발자 또는 IT 관리자는 API 커넥터를 사용하여 웹 API와 등록 사용자 흐름을 통합함으로써 등록 환경을 사용자 지정하고 외부 시스템과 통합할 수 있습니다. 예를 들어 API 커넥터를 사용하여 다음을 수행할 수 있습니다.

- **사용자 입력 데이터 유효성 검사**. 잘못된 형식이나 유효하지 않은 사용자 데이터의 유효성을 검사합니다. 예를 들어 외부 데이터 저장소의 기존 데이터 또는 허용되는 값 목록에 대해 사용자가 제공한 데이터의 유효성을 검사할 수 있습니다. 유효하지 않은 경우 사용자에게 유효한 데이터를 제공하도록 요청하거나 사용자가 등록 흐름을 계속할 수 없도록 차단할 수 있습니다.
- **사용자 지정 승인 워크플로와 통합** 합니다. 계정 생성을 관리 및 제한하기 위해 사용자 지정 승인 시스템에 연결합니다.
- **사용자 특성을 덮어씁니다**. 사용자로부터 수집된 특성에 값 서식을 다시 지정하거나 할당합니다. 예를 들어 사용자가 이름을 모두 소문자 또는 대문자로 입력한 경우 이름의 첫 번째 문자만을 대문자로 시작하도록 서식을 지정할 수 있습니다. 
- **ID 검증을 수행** 합니다. ID 검증 서비스를 사용하여 계정 생성 결정에 보안 수준을 강화합니다.
- **사용자 지정 비즈니스 논리 실행**. 클라우드 시스템에서 다운스트림 이벤트를 트리거하여 푸시 알림을 전송하고, 회사 데이터베이스를 업데이트하고, 권한을 관리하고, 데이터베이스를 감사하고, 기타 사용자 지정 작업을 수행할 수 있습니다.

API 커넥터는 HTTP 엔드포인트 URL 및 API 호출 인증을 정의하여 API 엔드포인트를 호출하는 데 필요한 정보를 Azure Active Directory에 제공합니다. API 커넥터를 구성한 후에는 사용자 흐름에서 특정 단계에 해당 커넥터를 사용할 수 있습니다. 사용자가 가입 흐름에서 해당 단계에 도달하면 API 커넥터가 호출되고 API에 대한 HTTP POST 요청으로 구체화하여 사용자 정보(“클레임”)를 JSON 본문의 키-값 쌍으로 전송합니다. API 응답은 사용자 흐름의 실행에 영향을 줄 수 있습니다. 예를 들어 API 응답은 사용자가 등록하지 못하도록 차단할 수 있으며, 사용자에게 정보를 다시 입력하도록 요청하거나 사용자 특성을 덮어쓰고 추가할 수 있습니다.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>사용자 흐름에서 API 커넥터를 사용할 수 있는 위치

다음과 같이 사용자 흐름에는 API 커넥터를 사용할 수 있는 두 개의 위치가 있습니다.

- ID 공급자를 사용하여 로그인한 후
- 사용자를 만들기 전

> [!IMPORTANT]
> 두 경우 모두, 사용자 로그인이 아닌 사용자 **등록** 중에 API 커넥터가 호출됩니다.

### <a name="after-signing-in-with-an-identity-provider"></a>ID 공급자를 사용하여 로그인한 후

API 커넥터는 로그인 프로세스의 해당 절차에서 ID 공급자(예: Google, Facebook, Azure AD)를 사용한 사용자 인증 후 즉시 호출됩니다. 이 단계는 사용자 특성을 수집하기 위해 사용자에게 표시되는 형식인 ***특성 모음 페이지*** 앞에 나옵니다. 이 단계는 사용자가 로컬 계정으로 등록하는 경우에는 호출되지 않습니다. 다음은 이 단계에서 사용할 수 있는 API 커넥터 시나리오의 예입니다.

- 사용자가 제공한 메일 또는 페더레이션 ID를 사용하여 기존 시스템에서 클레임을 조회합니다. 기존 시스템에서 해당 클레임을 반환하고 특성 모음 페이지를 미리 채운 다음 토큰에서 반환할 수 있도록 합니다.
- 소셜 ID를 토대로 허용 또는 차단 목록을 구현합니다.

### <a name="before-creating-the-user"></a>사용자를 만들기 전

등록 프로세스 내 이 단계에서, API 커넥터는 포함된 경우 특성 모음 페이지 다음에 호출됩니다. 이 단계는 항상 사용자 계정 생성 전에 호출됩니다. 다음은 등록 중에 이 시점에서 사용할 수 있는 시나리오의 예입니다.

- 사용자 입력 데이터의 유효성을 검사하고 데이터를 다시 제출하도록 사용자에게 요청합니다.
- 사용자가 입력한 데이터를 기반으로 사용자 등록을 차단합니다.
- ID 검증을 수행합니다.
- 사용자에 대한 기존 데이터를 외부 시스템을 쿼리하여 애플리케이션 토큰에 반환하거나 Azure AD에 저장합니다.


## <a name="next-steps"></a>다음 단계
- [사용자 흐름에 API 커넥터를 추가](add-api-connector.md)하는 방법을 알아봅니다.
- [샘플](code-samples.md#api-connectors)을 시작하세요.
<!-- - Learn how to [add a custom approval system to self-service sign-up](add-approvals.md) -->