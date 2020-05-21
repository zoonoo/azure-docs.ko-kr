---
title: 다른 사용자와 공동 작업-QnA Maker
description: ''
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: 080e6549579675e27486e6173d5907d92bbaad70
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83724924"
---
# <a name="collaborate-with-other-authors-and-editors"></a>다른 작성자 및 편집기와 공동 작업

QnA Maker 리소스에 배치 된 RBAC (역할 기반 액세스 제어)를 사용 하 여 다른 작성자 및 편집기와 공동 작업 합니다.

## <a name="access-is-provided-on-the-qna-maker-resource"></a>QnA Maker 리소스에 대 한 액세스가 제공 됩니다.

모든 권한은 QnA Maker 리소스에 배치 된 사용 권한에 의해 제어 됩니다. 이러한 권한은 읽기, 쓰기, 게시 및 모든 권한으로 정렬 됩니다.

이 RBAC 기능에는 다음이 포함 됩니다.
* AAD (Azure Active Directory)는 소유자 및 참가자에 대 한 키 기반 인증과 호환 되는 100%입니다. 고객은 요청에서 키 기반 인증 또는 RBAC 기반 인증을 사용할 수 있습니다.
* 제어가 기술 자료 수준이 아닌 리소스 수준에 있기 때문에 리소스의 모든 기술 자료에 작성자와 편집기를 빠르게 추가 합니다.

## <a name="access-is-provided-by-a-defined-role"></a>정의 된 역할에서 액세스를 제공 합니다.

[!INCLUDE [RBAC permissions table](../includes/role-based-access-control.md)]

## <a name="authentication-flow"></a>인증 흐름

다음 다이어그램에서는 작성자의 관점에서 QnA Maker 포털에 로그인 하 고 제작 Api를 사용 하는 흐름을 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![다음 다이어그램에서는 작성자의 관점에서 QnA Maker 포털에 로그인 하 고 제작 Api를 사용 하는 흐름을 보여 줍니다.](../media/qnamaker-how-to-collaborate-knowledge-base/rbac-flow-from-portal-to-service.png)

|단계|설명|
|--|--|
|1|포털에서 QnA Maker 리소스에 대 한 토큰을 가져옵니다.|
|2|포털은 키 대신 토큰을 전달 하는 적절 한 APIM (QnA Maker authoring API)를 호출 합니다.|
|3|QnA Maker API는 토큰의 유효성을 검사 합니다.|
|4 |QnA Maker API QnAMaker 서비스를 호출 합니다.|

[제작 api](../How-To/collaborate-knowledge-base.md)를 호출 하려는 경우 인증을 설정 하는 방법에 대해 자세히 알아보세요.

## <a name="authenticate-by-qna-maker-portal"></a>QnA Maker 포털에서 인증

QnA Maker 포털을 사용 하 여 작성 하 고 공동 작업 하는 경우 공동 작업을 [위해 리소스에 적절 한 역할을 추가](../How-To/collaborate-knowledge-base.md)하면 QnA Maker 포털에서 모든 액세스 권한을 관리 합니다.

## <a name="authenticate-by-qna-maker-apis-and-sdks"></a>QnA Maker Api 및 Sdk를 통해 인증

REST 또는 Sdk를 통해 Api를 사용 하 여 작성 하 고 공동 작업 하는 경우 인증을 관리 하는 [서비스 주체를 만들어야](../../authentication.md#assign-a-role-to-a-service-principal) 합니다.

## <a name="next-step"></a>다음 단계

* [언어](design-language-culture.md) 및 [클라이언트 응용 프로그램](integration-with-other-applications.md) 에 대 한 기술 자료 디자인
