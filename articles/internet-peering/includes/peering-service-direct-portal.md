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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687039"
---
1. Azure 피어링 서비스에 대해 사용할 피어링 연결을 선택합니다. 그런 다음 선택 **...**  >  **연결을 편집합니다.**
    > [!div class="mx-imgBorder"]
    > ![피어링 연결 편집 연결](../media/setup-direct-modify-editconnection.png)
1. **피어링 서비스에 대해 사용**중에서 **사용 옵션을** 선택한 다음 **저장을**선택합니다.
    > [!div class="mx-imgBorder"]
    > ![피어링 연결 사용 피어링 서비스](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. **개요** 화면에서 배포 세부 정보가 표시됩니다. 배포가 완료되면 **리소스로 이동을**선택합니다.
    > [!div class="mx-imgBorder"]
    > ![배포가 완료되었습니다.](../media/setup-direct-modify-overview-deployment-complete.png)

1. 등록된 **접두사** 창에서 **등록된 접두사 추가를**선택합니다.
    > [!div class="mx-imgBorder"]
    > ![등록된 접두사 추가](../media/setup-direct-modify-add-registered-prefix.png)
1. **이름과** 접두사를 선택하고 **저장을**선택하여 **접두사를** 등록합니다.
    > [!div class="mx-imgBorder"]
    >  ![접두사 등록](../media/setup-direct-modify-register-a-prefix.png) 

1. 접두사를 만든 후 **등록된 접두사**목록에 표시됩니다. 자세한 내용을 보려면 접두사 **이름을** 선택합니다.
    > [!div class="mx-imgBorder"]
    > ![등록된 접두사 및 연결](../media/setup-direct-modify-registered-prefixes.png)
1. 등록된 접두사 페이지에는 각 접두사에 대한 **접두사 키가** 포함된 전체 세부 정보가 표시됩니다. 이 키는 공급자 ISP에서 이 접두사를 할당한 고객에게 제공되어야 합니다. 그런 다음 고객은 이 키를 사용하여 구독 내에서 접두사를 등록할 수 있습니다.
    > [!div class="mx-imgBorder"]
    > ![접두사 키가 있는 접두사](../media/setup-direct-modify-registered-prefix-detail.png)