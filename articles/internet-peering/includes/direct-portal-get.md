---
title: 포함 파일
titleSuffix: Azure
description: 포함 파일
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3507aacc68de25f7368cbe3cda917077564c56eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96356247"
---
1. **리소스 그룹** 으로 이동하여 **피어링** 리소스를 만들 때 선택한 리소스 그룹을 선택합니다. 리소스 그룹이 너무 많은 경우 **필터** 상자를 사용합니다.

    > [!div class="mx-imgBorder"]
    > ![리소스 그룹](../media/setup-direct-get-resourcegroup.png)

1. 사용자가 만든 **피어링** 리소스를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![왼쪽 창에서 개요 페이지가 선택됩니다. PeeringResourceGroup에 대한 정보를 보여줍니다. 피어링 목록에서 AshburnPeering가 강조 표시됩니다.](../media/setup-direct-get-open.png)

1. **개요** 페이지에는 여기에 표시된 것처럼 높은 수준의 정보가 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 개요 창](../media/setup-direct-get-overview.png)

1. 왼쪽에서 **ASN 정보** 를 선택하여 PeerAsn을 만들 때 제출된 정보를 확인합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 ASN 정보](../media/setup-direct-get-asninfo.png)

1. 왼쪽에서 **연결** 을 선택합니다. 화면 위쪽에는 메트로 내의 여러 시설에서 ASN과 Microsoft 간의 피어링 연결 요약이 표시됩니다. 표시된 것처럼 창 가운데에서 **연결** 을 선택하여 **개요** 페이지에서 연결 요약에 액세스할 수도 있습니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 연결](../media/setup-direct-get-connectionssummary.png)

    * **연결 상태** 는 피어링 연결 설정의 상태에 해당합니다. 이 필드에 표시되는 상태는 [직접 피어링 연습](../walkthrough-direct-all.md)에 표시된 상태 다이어그램을 따릅니다.
    * **IPv4 세션 상태** 및 **IPv6 세션 상태** 는 각각 IPv4 및 IPv6 BGP 세션 상태에 해당합니다. 
    * 화면 위쪽에 있는 행을 선택하면 아래쪽의 **연결** 섹션에 각 연결에 대한 세부 정보가 표시됩니다. 화살표를 선택하여 **구성**, **IPv4 주소** 및 **IPv6 주소** 를 확장합니다.
