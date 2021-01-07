---
title: 포함 파일
titleSuffix: Azure
description: 포함 파일
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81687039"
---
1. Azure 피어 링 서비스를 사용 하도록 설정 하려는 피어 링 연결을 선택 합니다. 그런 후 **...를 선택**  >  합니다. **연결 편집**
    > [!div class="mx-imgBorder"]
    > ![피어 링 연결 편집 연결](../media/setup-direct-modify-editconnection.png)
1. **피어 링 서비스에 사용**에서 **사용** 을 선택한 다음 **저장**을 선택 합니다.
    > [!div class="mx-imgBorder"]
    > ![피어 링 연결에서 피어 링 서비스 사용](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. **개요** 화면에 배포 세부 정보가 표시 됩니다. 배포가 완료 되 면 **리소스로 이동**을 선택 합니다.
    > [!div class="mx-imgBorder"]
    > ![배포가 완료 되었습니다.](../media/setup-direct-modify-overview-deployment-complete.png)

1. 등록 된 **접두사** 창에서 **등록 된 접두사 추가**를 선택 합니다.
    > [!div class="mx-imgBorder"]
    > ![등록 된 접두사 추가](../media/setup-direct-modify-add-registered-prefix.png)
1. **이름** 및 **접두사** 를 선택 하 고 **저장**을 선택 하 여 접두사를 등록 합니다.
    > [!div class="mx-imgBorder"]
    >  ![접두사 등록](../media/setup-direct-modify-register-a-prefix.png) 

1. 접두사가 만들어지면 **등록 된 접두사**목록에 표시 됩니다. 자세한 내용을 보려면 접두사의 **이름을** 선택 합니다.
    > [!div class="mx-imgBorder"]
    > ![등록 된 접두사 및 연결](../media/setup-direct-modify-registered-prefixes.png)
1. 등록 된 접두사 페이지에서 각 접두사에 대 한 **접두사 키** 를 포함 하는 전체 세부 정보를 볼 수 있습니다. 공급자 ISP에서이 접두사를 할당 받은 고객에 게이 키를 제공 해야 합니다. 그러면 고객은이 키를 사용 하 여 해당 구독 내에 접두사를 등록할 수 있습니다.
    > [!div class="mx-imgBorder"]
    > ![접두사 키로 접두사](../media/setup-direct-modify-registered-prefix-detail.png)