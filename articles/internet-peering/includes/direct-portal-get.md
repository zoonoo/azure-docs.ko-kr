---
title: 파일 포함
titleSuffix: Azure
description: 포함 파일
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e50f53c65c330265d3d9ec1b1804a7910b632b1d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678842"
---
1. **리소스 그룹**으로 이동 하 여 **피어 링** 리소스를 만들 때 선택한 리소스 그룹을 선택 합니다. 너무 많은 리소스 그룹이 있는 경우 **필터** 상자를 사용 합니다.

    > [!div class="mx-imgBorder"]
    > ![리소스 그룹](../media/setup-direct-get-resourcegroup.png)

1. 만든 **피어 링** 리소스를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 뷰](../media/setup-direct-get-open.png)

1. **개요** 페이지에는 여기에 표시 된 것 처럼 높은 수준의 정보가 표시 됩니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 개요 창](../media/setup-direct-get-overview.png)

1. 왼쪽에서 **asn 정보** 를 선택 하 여 peerasn을 만들 때 제출 된 정보를 확인 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 ASN 정보](../media/setup-direct-get-asninfo.png)

1. 왼쪽에서 **연결**을 선택 합니다. 화면 위쪽에는 metro 내의 여러 시설에서 ASN과 Microsoft 간의 피어 링 연결 요약이 표시 됩니다. 표시 된 것 처럼 창의 가운데에서 **연결** 을 선택 하 여 **개요** 페이지에서 연결 요약에 액세스할 수도 있습니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 연결](../media/setup-direct-get-connectionssummary.png)

    * **연결 상태** 는 피어 링 연결 설정의 상태에 해당 합니다. 이 필드에 표시 되는 상태는 [직접 피어 링 연습](../walkthrough-direct-all.md)에 표시 된 상태 다이어그램을 따릅니다.
    * **Ipv4 세션 상태** 및 **ipv6 세션 상태** 는 각각 ipv4 및 ipv6 BGP 세션 상태에 해당 합니다. 
    * 화면 위쪽에서 행을 선택 하면 아래쪽의 **연결** 섹션에 각 연결에 대 한 세부 정보가 표시 됩니다. 화살표를 선택 하 여 **구성**, **IPv4 주소**및 **IPv6 주소**를 확장 합니다.
