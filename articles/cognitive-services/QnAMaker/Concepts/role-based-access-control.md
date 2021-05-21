---
title: 다른 사용자와 협업 - QnA Maker
description: Azure 역할 기반 액세스 제어를 사용하여 다른 작성자 및 편집자와 협업하는 방법을 알아봅니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: 512ae470d3c9068b9d284095a18013fc84dbf36e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232548"
---
# <a name="collaborate-with-other-authors-and-editors"></a>다른 작성자 및 편집자와 협업

QnA Maker 리소스에 배치된 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 다른 작성자 및 편집자와 협업합니다.

## <a name="access-is-provided-on-the-qna-maker-resource"></a>액세스 권한은 QnA Maker 리소스에 제공됩니다.

모든 권한은 QnA Maker 리소스에 배치된 사용 권한에 의해 제어됩니다. 이러한 권한은 읽기, 쓰기, 게시 및 전체 액세스에 맞게 조정됩니다. QnA Maker 리소스에 대한 [RBAC 액세스를 업데이트](../how-to/manage-qna-maker-app.md)하여 여러 사용자 간의 협업을 허용할 수 있습니다.

이 Azure RBAC 기능에는 다음이 포함됩니다.
* AAD(Azure Active Directory)는 소유자 및 기여자에 대한 키 기반 인증을 통해 이전 버전과 100% 호환됩니다. 고객은 요청에 키 기반 인증 또는 Azure RBAC 기반 인증을 사용할 수 있습니다.
* 제어가 기술 자료 수준이 아닌 리소스 수준에 있기 때문에 리소스의 모든 기술 자료에 작성자와 편집자를 빠르게 추가합니다.

> [!NOTE]
> 리소스에 대한 사용자 지정 하위 도메인을 추가해야 합니다. 기본적으로 [사용자 지정 하위 도메인](../../cognitive-services-custom-subdomains.md)이 있어야 합니다. 그렇지 않은 경우 추가하세요.

## <a name="access-is-provided-by-a-defined-role"></a>정의된 역할에 의해 액세스가 제공됩니다.

[!INCLUDE [Azure RBAC permissions table](../includes/role-based-access-control.md)]

## <a name="authentication-flow"></a>인증 흐름

다음 다이어그램에서는 작성자의 관점에서 QnA Maker 포털에 로그인하고 작성 API를 사용하는 흐름을 보여줍니다.

> [!div class="mx-imgBorder"]
> ![다음 다이어그램에서는 작성자의 관점에서 QnA Maker 포털에 로그인하고 작성 API를 사용하는 흐름을 보여줍니다.](../media/qnamaker-how-to-collaborate-knowledge-base/rbac-flow-from-portal-to-service.png)

|단계|설명|
|--|--|
|1|포털에서 QnA Maker 리소스에 대한 토큰을 가져옵니다.|
|2|포털은 키 대신 토큰을 전달하는 적절한 QnA Maker 작성 API(APIM)를 호출합니다.|
|3|QnA Maker API에서 토큰의 유효성을 검사합니다.|
|4 |QnA Maker API에서 QnAMaker 서비스를 호출합니다.|

[작성 API](../index.yml)를 호출하려는 경우 인증을 설정하는 방법에 대해 자세히 알아보세요.

## <a name="authenticate-by-qna-maker-portal"></a>QnA Maker 포털에서 인증

QnA Maker 포털을 사용하여 작성 및 협업하는 경우 협업자의 리소스에 적절한 역할을 추가하면 QnA Maker 포털에서 모든 액세스 권한을 관리할 수 있습니다.

## <a name="authenticate-by-qna-maker-apis-and-sdks"></a>QnA Maker API 및 SDK를 통해 인증

REST 또는 SDK를 통해 API를 사용하여 작성 및 협업하는 경우 인증을 관리하는 [서비스 주체를 만들](../../authentication.md#assign-a-role-to-a-service-principal)어야 합니다.

## <a name="next-step"></a>다음 단계

* 언어 및 클라이언트 애플리케이션에 대한 기술 자료 디자인
