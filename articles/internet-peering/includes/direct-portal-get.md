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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75775083"
---
1. 리소스 **그룹으로** 이동하여 **피어링** 리소스를 만드는 동안 선택한 리소스 그룹을 클릭합니다. 리소스 그룹이 너무 많은 경우 *필터* 필드를 사용할 수 있습니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 그룹](../media/setup-direct-get-resourcegroup.png)

1. 만든 **피어링** 리소스를 클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 보기](../media/setup-direct-get-open.png)

1. **개요** 페이지에는 상위 수준 정보가 표시됩니다. 아래에 강조 표시된 정보를 관찰합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 보기](../media/setup-direct-get-overview.png)

1. 왼쪽에서 **ASN 정보를** 클릭하여 PeerAsn을 만드는 동안 제출된 정보를 볼 수 있습니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 보기](../media/setup-direct-get-asninfo.png)

1. 왼쪽에서 **연결을**클릭합니다. 맨 위에 는 지하철 내의 여러 시설에서 ASN과 Microsoft 간의 피어링 연결 요약을 확인합니다. 위에서 강조 표시된 대로 가운데 창의 **연결을** 클릭하여 **개요** 페이지에서 연결 요약에 도달할 수도 있습니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 보기](../media/setup-direct-get-connectionssummary.png)

    * **연결 상태는** 피어링 연결 설정의 상태에 해당합니다. 이 필드에 표시된 상태는 [직접 피어링 연습에](../walkthrough-direct-all.md) 표시된 상태 다이어그램을 따릅니다.
    * **IPv4 세션 상태** 및 **IPv6 세션 상태는** 각각 IPv4 및 IPv6 BGP 세션 상태에 해당합니다.  
    * 상단에서 행을 선택하면 하단의 ***연결*** 섹션에 각 연결에 대한 세부 정보가 표시됩니다. 화살표 표시를 클릭하여 하위 섹션 ***구성,*** ***IPv4 주소*** 및 ***IPv6 주소를*** 확장할 수 있습니다.
