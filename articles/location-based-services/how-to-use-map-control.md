---
title: Azure Location Based Services 맵 컨트롤 사용 방법 | Microsoft Docs
description: Azure Location Based Services 맵 컨트롤 클라이언트 쪽 Javascript 라이브러리 사용 방법을 알아봅니다.
services: location-based-services
author: kgremban
ms.author: kgremban
ms.date: 11/22/2017
ms.topic: article
ms.service: location-based-services
manager: timlt
ms.openlocfilehash: ee767c9461f79437ab49d2a919bb82e7de8feba7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-use-the-azure-location-based-services-map-control"></a>Azure Location Based Services 맵 컨트롤 사용 방법
맵 컨트롤 클라이언트 쪽 Javascript 라이브러리를 사용하면 맵 및 포함된 Azure Location Based Services 기능을 웹 또는 모바일 응용 프로그램에 렌더링할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건
Azure Location Based Services 계정 및 키 계정 만들기 및 구독 키 검색에 대한 자세한 내용은 [Azure Location Based Services 계정 및 키를 관리하는 방법](how-to-manage-account-keys.md)을 참조하세요. 

## <a name="create-a-new-map-in-a-web-page-using-the-map-control-api"></a>맵 컨트롤 API를 사용하여 웹 페이지에 새 맵 만들기
맵 컨트롤 클라이언트 쪽 Javascript 라이브러리를 사용하여 웹 페이지에 지도를 포함할 수 있습니다.

1. 새 파일을 만들고 이름을 MapSearch.html로 지정합니다.

2. 파일의 `<head>` 요소에 Azure Location Based Services 스타일 시트 및 스크립트 소스 참조를 추가합니다.

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
    ```
    
3. 브라우저에서 새 맵을 렌더링하려면 `<style>` 요소에 **#map** 참조를 추가합니다.

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. 맵 컨트롤을 초기화하기 위해 html 본문에 새 섹션을 정의하고 스크립트를 만듭니다. 스크립트에서 고유한 Azure Location Based Services 계정 키를 사용합니다. 

    ```html
    <div id="map">
        <script>
            var LBSAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": LBSAccountKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. 웹 브라우저에서 파일을 열고 렌더링된 맵을 봅니다.