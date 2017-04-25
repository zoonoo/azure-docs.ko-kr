---
title: "Content Delivery Network에 Web App 연결 | Microsoft Docs"
description: "Content Delivery Network에 Web App을 연결하여 에지 노드의 정적 파일을 제공합니다."
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 8194e669f943a6c47f02ae0d2a55e0e720420489
ms.lasthandoff: 04/18/2017

---
# <a name="connect-a-web-app-to-a-content-delivery-network"></a>Content Delivery Network에 Web App 연결

이 자습서에서는 Azure CDN POP 위치를 통해 Web App의 정적 파일을 제공하기 위해 Azure CDN 프로필 및 Azure CDN 끝점을 만듭니다.

> [!TIP]
> [Azure CDN POP 위치](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations)의 최신 목록을 검토합니다.
>

## <a name="step-1---login-to-azure-portal"></a>1단계 - Azure Portal에 로그인

먼저 즐겨찾는 브라우저를 열고 Azure [Portal](https://portal.azure.com)로 이동합니다.

## <a name="step-2---create-a-cdn-profile"></a>2단계 - CDN 프로필 만들기

왼쪽 탐색 창에서 **+ 새로 만들기** 단추를 클릭하고 **웹 + 모바일**을 클릭합니다. [웹 + 모바일] 범주 아래에서 **CDN**을 선택합니다.

**이름**, **위치**, **리소스 그룹**, **가격 책정 계층**을 지정한 다음 **만들기**를 클릭합니다.

왼쪽 탐색에서 리소스 그룹 허브를 열고 **myResourceGroup**을 선택합니다. 리소스 목록에서 **myCDNProfile**을 선택합니다.

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>3단계 - CDN 끝점 만들기

검색 상자 옆에 있는 명령의 **+ 끝점**을 클릭하면 끝점 생성 블레이드가 시작됩니다.

**이름**, **원본 유형**, **원본 호스트 이름**을 지정한 다음 **추가**를 클릭합니다.

끝점이 만들어지고 Content Delivery Network 끝점이 생성되면 상태가 **실행 중**으로 업데이트됩니다.

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---leveraging-cdn"></a>4단계 - CDN 활용

이제 끝점이 실행되므로 웹 서버에서 정적 파일을 찾은 다음 호스트 이름을 `http://<app_name>.azurewebsites.net/path/to-static-file`에서 `http://<endpoint_name>.azureedge.net/path/to-static-file`로 변경하여 팝 서버에서 콘텐츠를 사용할 수 있는지 유효성을 검사해 보겠습니다.

![app-service-web-url-to-cdn-endpoint-url](media/app-service-web-tutorial-content-delivery-network/app-service-web-url-to-cdn-endpoint-url.png)

## <a name="next-steps"></a>다음 단계


