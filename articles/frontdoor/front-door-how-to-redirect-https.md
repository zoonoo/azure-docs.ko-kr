---
title: Azure Portal를 사용 하 여 HTTP에서 HTTPS로 리디렉션하는 프런트 도어 만들기
description: Azure Portal를 사용 하 여 HTTP에서 HTTPS로 리디렉션된 트래픽으로 프런트 도어를 만드는 방법에 대해 알아봅니다.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 19908b3cba63bc76a205097ef8d16e612d58503b
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91626645"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Azure Portal를 사용 하 여 HTTP에서 HTTPS로 리디렉션하는 프런트 도어 만들기

Azure Portal를 사용 하 여 TLS 종료를 위한 인증서를 사용 하 여 [Front 도어를 만들](quickstart-create-front-door.md) 수 있습니다. 라우팅 규칙은 HTTP 트래픽을 HTTPS로 리디렉션하는 데 사용 됩니다.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>기존 웹 앱 리소스를 사용 하 여 Front 도어 만들기

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

1. Azure Portal의 왼쪽 위 모서리에 있는 **리소스 만들기** 를 선택 합니다.

1. 검색 창을 사용 하 여 **전방 도어** 를 검색 하 고 리소스 종류를 찾았으면 **만들기**를 선택 합니다.

1. *구독* 을 선택한 다음 기존 리소스 그룹을 사용 하거나 새 리소스 그룹을 만듭니다. **다음** 을 선택 하 여 구성 탭을 입력 합니다.

    > [!NOTE]
    > UI에서 요청 된 위치는 리소스 그룹에만 해당 됩니다. 프런트 도어 구성은 모든 [Azure 전면 도어의 POP 위치](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)에 배포 됩니다.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-basics.png" alt-text="새 Front 문에 대 한 기본 사항 구성":::

1. 프런트 도어 구성은 기본 프런트 엔드 호스트를 추가 하 고 백 엔드 풀에 백 엔드를 추가한 다음, 프런트 엔드 호스트의 라우팅 동작을 매핑하는 라우팅 규칙을 만드는 세 단계로 이루어집니다. 프런트 엔드 **+** 호스트에서 ' ' 아이콘 _Frontend hosts_ 을 선택 하 여 프런트 엔드 호스트를 만듭니다.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer.png" alt-text="새 Front 문에 대 한 기본 사항 구성":::

1. 프런트 도어의 기본 프런트 엔드 호스트에 대해 전역적으로 고유한 이름을 입력 합니다. **추가** 를 선택 하 여 다음 단계를 계속 합니다.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-frontend-host.png" alt-text="새 Front 문에 대 한 기본 사항 구성":::

### <a name="create-backend-pool"></a>백 엔드 풀 만들기

1. 백 엔드 **+** 풀에서 ' ' 아이콘 _Backend pools_ 을 선택 하 여 백 엔드 풀을 만듭니다. 백 엔드 풀의 이름을 입력 하 고 **백 엔드 추가**를 선택 합니다.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-backend-pool.png" alt-text="새 Front 문에 대 한 기본 사항 구성":::

1. 백 엔드 호스트 유형을 _App service_로 선택 합니다. 웹 앱이 호스트 되는 구독을 선택한 다음 **백 엔드 호스트 이름**드롭다운에서 특정 웹 앱을 선택 합니다.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-backend-pool.png" alt-text="새 Front 문에 대 한 기본 사항 구성":::

1. **추가** 를 선택 하 여 백 엔드를 저장 하 고 **추가** 를 다시 선택 하 여 백 엔드 풀 구성을 저장 합니다. 

## <a name="create-http-to-https-redirect-rule"></a>HTTP에서 HTTPS로 리디렉션 규칙 만들기

1. **+** *라우팅 규칙* 에서 ' ' 아이콘을 선택 하 여 경로를 만듭니다. 경로 이름 (예: ' HttpToHttpsRedirect ')을 입력 한 다음 *허용 된 프로토콜* 필드를 **' HTTP l o n '** 으로 설정 합니다. 적절 한 *프런트 엔드/도메인이* 선택 되었는지 확인 합니다.  

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-routing-rule.png" alt-text="새 Front 문에 대 한 기본 사항 구성":::

1. *경로 세부 정보* 섹션에서 **리디렉션**으로 *경로 유형을* 설정 합니다. *리디렉션 유형* Get이 **발견 (302)** 으로 설정 되 고 *프로토콜* get이 **HTTPS로만**설정 되어 있는지 확인 합니다. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-redirect-config-example.png" alt-text="새 Front 문에 대 한 기본 사항 구성":::

1. **추가** 를 선택 하 여 HTTP에 대 한 라우팅 규칙을 HTTPS 리디렉션에 저장 합니다.

## <a name="create-forwarding-rule"></a>전달 규칙 만들기

1. 다른 라우팅 규칙을 추가 하 여 HTTPS 트래픽을 처리 합니다. **+** *라우팅 규칙* 에 대 한 ' ' 기호를 선택 하 고 경로 이름을 제공 합니다 (예: ' DefaultForwardingRoute '). 그런 다음 *허용 된 프로토콜* 필드를 **HTTPS로만**설정 합니다. 적절 한 *프런트 엔드/도메인이* 선택 되었는지 확인 합니다.

1. 경로 정보 섹션에서 *경로 형식을* **전달**으로 설정 합니다. 올바른 백 엔드 풀을 선택 하 고 *전달 프로토콜이* **HTTPS로만**설정 되어 있는지 확인 합니다. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-forward-route-example.png" alt-text="새 Front 문에 대 한 기본 사항 구성" border="false":::

1. **추가** 를 선택 하 여 요청 전달에 대 한 라우팅 규칙을 저장 합니다.

1. **검토 + 만들기** 및 **만들기**를 차례로 선택 하 여 Front 도어 프로필을 만듭니다. 만든 후 리소스로 이동 합니다.

## <a name="next-steps"></a>다음 단계

- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
- [전면 도어에서 URL 리디렉션](front-door-url-redirect.md)에 대해 자세히 알아보세요.
