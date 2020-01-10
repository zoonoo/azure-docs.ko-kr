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
ms.openlocfilehash: 2e8938f270ed175c687d975b0b248275ad92f8e4
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775083"
---
1. **리소스 그룹** 으로 이동 하 여 **피어 링** 리소스를 만드는 동안 선택한 리소스 그룹을 클릭 합니다. 너무 많은 리소스 그룹이 있는 경우 *필터* 필드를 사용할 수 있습니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 그룹](../media/setup-direct-get-resourcegroup.png)

1. 만든 **피어 링** 리소스를 클릭 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 뷰](../media/setup-direct-get-open.png)

1. **개요** 페이지에는 높은 수준의 정보가 표시 됩니다. 아래에 강조 표시 된 정보를 확인 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 뷰](../media/setup-direct-get-overview.png)

1. PeerAsn을 만드는 동안 전송 된 정보를 보려면 왼쪽에서 **ASN 정보** 를 클릭 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 뷰](../media/setup-direct-get-asninfo.png)

1. 왼쪽에서 **연결**을 클릭 합니다. Metro 내의 여러 시설에서 ASN과 Microsoft 간의 피어 링 연결에 대 한 요약을 확인 하세요. 위에 강조 표시 된 가운데 창에서 **연결** 을 클릭 하 여 **개요** 페이지의 연결 요약에도 도착할 수 있습니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 뷰](../media/setup-direct-get-connectionssummary.png)

    * **연결 상태** 는 피어 링 연결 설정 상태에 해당 합니다. 이 필드에 표시 되는 상태는 [직접 피어 링 연습](../walkthrough-direct-all.md) 에 표시 된 상태 다이어그램을 따릅니다.
    * **Ipv4 세션 상태** 및 **ipv6 세션 상태** 는 각각 ipv4 및 ipv6 BGP 세션 상태에 해당 합니다.  
    * 위쪽에서 행을 선택 하면 아래쪽의 ***연결*** 섹션에 각 연결에 대 한 세부 정보가 표시 됩니다. 화살표 표시를 클릭 하 여 하위 섹션 ***구성***, ***IPv4 주소*** 및 ***IPv6 주소*** 를 확장할 수 있습니다.
