---
title: Azure Portal을 사용하여 HTTP에서 HTTPS로 리디렉션하는 Front Door 만들기
description: Azure Portal을 사용하여 HTTP에서 HTTPS로 리디렉션된 트래픽으로 Front Door를 만드는 방법을 알아봅니다.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: edc1ebe53969ceac0452818bee016e35de1e9322
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167841"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Azure Portal을 사용하여 HTTP에서 HTTPS로 리디렉션하는 Front Door 만들기

Azure Portal을 사용하여 TLS 종료를 위한 인증서로 [Front Door를 만들 수 있습니다](quickstart-create-front-door.md). 라우팅 규칙은 HTTP 트래픽을 HTTPS로 리디렉션하는 데 사용됩니다.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>기존 웹앱 리소스를 사용하여 Front Door 만들기

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

1. Azure Portal의 왼쪽 위 모서리에 있는 **리소스 만들기** 를 선택합니다.

1. 검색 창을 사용하여 **Front Door** 를 검색하고 리소스 종류를 찾으면 **만들기** 를 선택합니다.

1. *구독* 을 선택한 다음 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. **다음** 을 선택하여 구성 탭을 입력합니다.

    > [!NOTE]
    > UI에서 요청된 위치는 리소스 그룹에만 해당됩니다. Front Door 구성은 모든 [Azure Front Door의 POP 위치](front-door-faq.yml#what-are-the-pop-locations-for-azure-front-door-)에 배포됩니다.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-basics.png" alt-text="새 Front Door에 대한 기본 사항 구성":::

1. Front Door 구성은 기본 프런트 엔드 호스트를 추가하고, 백 엔드 풀에 백 엔드를 추가한 다음, 프런트 엔드 호스트의 라우팅 동작을 매핑하는 라우팅 규칙을 만드는 세 단계로 이루어집니다. _프런트 엔드 호스트_ 의 ' **+** ' 아이콘을 선택하여 프런트 엔드 호스트를 만듭니다.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer.png" alt-text="Front Door 구성 디자이너":::

1. Front Door의 기본 프런트 엔드 호스트에 대해 전역적으로 고유한 이름을 입력합니다. **추가** 를 선택하여 다음 단계를 계속 진행합니다.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-frontend-host.png" alt-text="프런트 엔드 호스트 추가":::

### <a name="create-backend-pool"></a>백 엔드 풀 만들기

1. _백 엔드 풀_ 의 ' **+** ' 아이콘을 선택하여 백 엔드 풀을 만듭니다. 백 엔드 풀의 이름을 입력하고 **백 엔드 추가** 를 선택합니다.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-backend-pool.png" alt-text="Front Door 구성 디자이너 백 엔드 풀":::

1. 백 엔드 호스트 유형을 _App Service_ 로 선택합니다. 웹앱이 호스트되는 구독을 선택한 다음 **백 엔드 호스트 이름** 드롭다운에서 특정 웹앱을 선택합니다.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-backend-pool.png" alt-text="백 엔드 풀에 백 엔드 추가":::

1. **추가** 를 선택하여 백 엔드를 저장하고 **추가** 를 다시 선택하여 백 엔드 풀 구성을 저장합니다. 

## <a name="create-http-to-https-redirect-rule"></a>HTTP에서 HTTPS로 리디렉션 규칙 만들기

1. *라우팅 규칙* 의 ' **+** ' 아이콘을 선택하여 경로를 만듭니다. 경로 이름(예: 'HttpToHttpsRedirect')을 입력한 다음 *허용된 프로토콜* 필드를 **'HTTPS만'** 으로 설정합니다. 적절한 *프런트 엔드/도메인* 이 선택되었는지 확인합니다.  

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-routing-rule.png" alt-text="Front Door 구성 디자이너 라우팅 규칙":::

1. *경로 세부 정보* 섹션에서 *경로 유형* 을 **리디렉션** 으로 설정합니다. *리디렉션 유형* **Found(302)** 로 설정되고 *리디렉션 프로토콜* 이 **HTTPS only** 로 설정되었는지 확인합니다. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-redirect-config-example.png" alt-text="HTTPS 리디렉션 경로에 HTTP 추가":::

1. **추가** 를 선택하여 HTTP에 대한 라우팅 규칙을 HTTPS 리디렉션에 저장합니다.

## <a name="create-forwarding-rule"></a>전달 규칙 만들기

1. 다른 라우팅 규칙을 추가하여 HTTPS 트래픽을 처리합니다. *라우팅 규칙* 에서 ' **+** ' 기호를 선택하고 경로에 대한 이름(예: 'DefaultForwardingRoute')을 제공합니다. 그런 다음 *허용된 프로토콜* 필드를 **HTTPS만** 으로 설정합니다. 적절한 *프런트 엔드/도메인* 이 선택되었는지 확인합니다.

1. 경로 정보 섹션에서 *경로 형식* 을 **전달** 로 설정합니다. 올바른 백 엔드 풀이 선택되고 *전달 프로토콜* 이 **HTTPS only** 로 설정되어 있는지 확인합니다. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-forward-route-example.png" alt-text="HTTPS 트래픽에 대한 전달 경로 추가" border="false":::

1. **추가** 를 선택하여 요청 전달에 대한 라우팅 규칙을 저장합니다.

1. **검토 + 만들기** 및 **만들기** 를 차례로 선택하여 Front Door 프로필을 만듭니다. 만든 다음 리소스로 이동합니다.

## <a name="next-steps"></a>다음 단계

- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
- [Front Door에서 URL 리디렉션](front-door-url-redirect.md)에 대해 자세히 알아보세요.
