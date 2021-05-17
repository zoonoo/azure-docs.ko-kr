---
title: Azure Front Door 경로 구성
description: 이 문서에서는 도메인과 원본 그룹 간의 경로를 구성하는 방법을 보여 줍니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 94c22ffd423c32ba5f489298267464ca36abaecd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099536"
---
# <a name="configure-an-azure-front-door-standardpremium-route"></a>Azure Front Door 표준/프리미엄 경로 구성

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

이 문서에서는 기존 엔드포인트의 Azure Front Door(AFD) 경로를 만드는 데 사용되는 각 설정을 설명합니다. 기존 Azure Front Door 엔드포인트에 사용자 지정 도메인 및 원본을 추가한 후 도메인과 원본 간의 연결을 정의하도록 경로를 구성하여 도메인과 원본 간의 트래픽을 라우팅해야 합니다.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Azure Front Door 경로를 구성하기 전에 현재 엔드포인트 내에서 하나 이상의 원본 그룹과 하나 이상의 사용자 지정 도메인을 만들어야 합니다. 

원본 그룹을 설정하려면 [새 Azure Front Door 표준/프리미엄 원본 그룹 만들기](how-to-create-origin.md)를 참조하세요. 

## <a name="create-a-new-azure-front-door-standardpremium-route"></a>새 Azure Front Door 표준/프리미엄 경로 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Front Door 표준/프리미엄 프로필로 이동합니다.

1. **설정** 에서 **Endpoint Manager** 를 선택합니다.
   
    :::image type="content" source="../media/how-to-configure-route/select-endpoint-manager.png" alt-text="Front Door Endpoint Manager 설정의 스크린샷." lightbox="../media/how-to-configure-route/select-endpoint-manager-expanded.png":::

1. 그런 다음 경로를 구성하려는 엔드포인트에서 **엔드포인트 편집** 을 선택합니다.
   
    :::image type="content" source="../media/how-to-configure-route/select-edit-endpoint.png" alt-text="엔드포인트 편집을 선택하는 스크린샷.":::

1. **엔드포인트 편집** 페이지가 나타납니다. 경로에서 **+ 추가** 를 선택합니다.
    
    :::image type="content" source="../media/how-to-configure-route/select-add-route.png" alt-text="엔드포인트 편집 페이지에서 경로 추가의 스크린샷.":::    
    
1. **경로 추가** 페이지에서 다음 정보를 입력하거나 선택합니다.

    :::image type="content" source="../media/how-to-configure-route/add-route-page.png" alt-text="경로 추가 페이지의 스크린샷." lightbox="../media/how-to-configure-route/add-route-page-expanded.png"::: 

    | 설정 | 값 |
    | --- | --- |
    | 이름 | 새 경로의 고유한 이름을 입력합니다. |   
    | 도메인| 유효성이 검사되고 다른 경로에 연결되지 않은 도메인을 하나 이상 선택합니다. |
    | 일치할 패턴  | 이 경로에 허용되는 모든 URL 경로 패턴을 구성합니다. 예를 들어 이를 `/images/*`로 설정하면 URL `www.contoso.com/images/*`에서 모든 요청을 허용할 수 있습니다. AFD는 정확한 일치를 기준으로 트래픽을 먼저 확인하고, 정확히 일치하는 경로가 없는 경우 일치하는 와일드 카드 경로를 찾습니다. 일치하는 경로가 있는 회람 규칙이 없을 경우 요청을 거부하고 400: 잘못된 요청 오류 HTTP 응답을 반환합니다. |
    | 허용되는 프로토콜 | 클라이언트가 요청할 때 Azure Front Door가 허용할 프로토콜을 지정합니다. |
    | 리디렉션 | HTTP 요청을 사용하여 들어오는 요청에 HTTPS를 적용할지 여부를 지정합니다. |
    | 원본 그룹 | 원본으로 돌아가기 요청이 발생할 때 전달할 원본 그룹을 선택합니다. |
    | 원본 경로 | 캐시하려는 리소스의 경로를 입력합니다. 도메인에서 어떤 리소스도 캐시할 수 있도록 허용하려면 이 설정을 비워 둡니다. |
    | 전달 프로토콜 | 전달 요청에 사용되는 프로토콜을 선택합니다. |
    | 캐싱 | Azure Front Door에 정적 콘텐츠 캐싱을 사용하도록 설정하려면 이 옵션을 선택합니다. |
    | 규칙 | 이 경로에 적용될 규칙 집합을 선택합니다. 규칙을 구성하는 방법에 대한 자세한 내용은 [Azure Front Door 규칙 집합 구성](how-to-configure-rule-set.md)을 참조하세요. | 

1. **추가** 를 선택하여 새 경로를 만듭니다. 경로는 엔드포인트의 경로 목록에 표시됩니다.
    
    :::image type="content" source="../media/how-to-configure-route/route-list-page.png" alt-text="경로 목록의 스크린샷.":::  
    
## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경로를 삭제하려면 경로를 선택하고 **삭제** 를 선택합니다. 

:::image type="content" source="../media/how-to-configure-route/route-delete-page.png" alt-text="경로 삭제 방법의 스크린샷.":::  

## <a name="next-steps"></a>다음 단계
사용자 지정 도메인에 대해 자세히 알아보려면 Azure Front Door 엔드포인트에 사용자 지정 도메인을 추가하는 방법에 대한 자습서로 이동하세요.

> [!div class="nextstepaction"]
> [사용자 지정 도메인 추가]()
