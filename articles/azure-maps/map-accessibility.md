---
title: Azure Maps로 액세스 가능한 응용 프로그램 만들기 | Microsoft Docs
description: Azure Maps를 사용하여 액세스 가능한 응용 프로그램을 작성하는 방법
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 05/18/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: 537a8c80dc0d1fcb2f536d0e30200de19a2111a4
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867055"
---
# <a name="building-an-accessible-application"></a>액세스 가능한 응용 프로그램 작성

이 문서에서는 화면 판독기에서 사용할 수 있는 지도 응용 프로그램을 작성하는 방법을 보여줍니다.

## <a name="understand-the-code"></a>코드 이해

<iframe height='500' scrolling='no' title='액세스 가능한 응용 프로그램 만들기' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>액세스 가능한 응용 프로그램 만들기</a>를 참조하세요.
</iframe>

지도에는 몇 가지 내게 필요한 옵션 기능이 미리 구축되어 있습니다. 사용자는 키보드를 사용하여 지도를 탐색할 수 있고 화면 판독기를 실행 중이면 지도가 사용자에게 상태 변경 사항을 알립니다. 예를 들어 지도를 확대하거나 축소하면 지도의 새로운 위도, 경도, 확대 및 지역 정보가 사용자에게 제공됩니다. 기본 지도에 추가되는 모든 정보에는 화면 판독기 사용자를 위한 텍스트 정보가 있어야 합니다. [팝업](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) 사용이 이런 기능을 구현하는 한가지 방법입니다. 위의 검색 예에서는 지도에 배치된 모든 핀에 대한 텍스트 정보 팝업이 지도에 추가됩니다. [팝업](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)의 attach 메서드를 사용하면 지도에 팝업을 시각적으로 표시하지 않고도 화면 판독기에서 팝업을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 팝업 클래스 및 메서드에 대해 자세히 알아봅니다.

* [팝업](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [attach](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#attach)
    * [remove](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#remove)
    * [열기](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [닫기](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

[코드 샘플 페이지](http://aka.ms/AzureMapsSamples)에서 다른 매핑 시나리오를 확인해 보세요.
