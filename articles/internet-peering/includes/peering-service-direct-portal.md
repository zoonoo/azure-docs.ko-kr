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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "81687039"
---
1. Azure Peering Service에 대해 사용 설정하려는 피어링 연결을 선택합니다. 그런 다음, **...**  > **연결 편집** 을 선택합니다.
    > [!div class="mx-imgBorder"]
    > ![피어링 연결 편집 연결](../media/setup-direct-modify-editconnection.png)
1. **피어링 서비스에 사용** 에서 **사용** 을 선택하고 **저장** 을 선택합니다.
    > [!div class="mx-imgBorder"]
    > ![피어링 연결 피어링 서비스 사용](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. **개요** 화면에 배포 세부 정보가 표시됩니다. 배포가 완료된 후 **리소스로 이동** 을 선택합니다.
    > [!div class="mx-imgBorder"]
    > ![배포가 완료됨](../media/setup-direct-modify-overview-deployment-complete.png)

1. **등록된 접두사** 창에서 **등록된 접두사 추가** 를 설치합니다.
    > [!div class="mx-imgBorder"]
    > ![등록된 접두사 추가](../media/setup-direct-modify-add-registered-prefix.png)
1. **이름** 과 **접두사** 를 선택하고 **저장** 을 선택하여 접두사를 등록합니다.
    > [!div class="mx-imgBorder"]
    >  ![접두사 등록](../media/setup-direct-modify-register-a-prefix.png) 

1. 접두사를 만든 후에는 **등록된 접두사** 목록에 접두사가 표시됩니다. 접두사의 **이름** 을 선택하면 세부 정보가 표시됩니다.
    > [!div class="mx-imgBorder"]
    > ![등록된 접두사 및 연결](../media/setup-direct-modify-registered-prefixes.png)
1. 등록된 접두사 페이지에서 각 접두사에 대한 **접두사 키** 를 포함한 전체 세부 정보가 표시됩니다. 공급자 ISP에서 이 접두사가 할당된 고객에게 이 키를 제공해야 합니다. 고객은 이 키를 사용하여 해당 구독 내에 접두사를 등록할 수 있습니다.
    > [!div class="mx-imgBorder"]
    > ![접두사 키가 포함된 접두사](../media/setup-direct-modify-registered-prefix-detail.png)