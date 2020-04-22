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
ms.openlocfilehash: 000971878e24c46892aaef1fa0c65237a4219883
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678577"
---
**피어링** 리소스가 성공적으로 배포된 후 다음 단계에 따라 볼 수 있습니다.

1. 리소스 **그룹으로**이동하여 **피어링** 리소스를 만들 때 선택한 리소스 그룹을 선택합니다. 리소스 그룹이 너무 많은 경우 **필터** 상자를 사용합니다.

    > [!div class="mx-imgBorder"]
    > ![리소스 그룹](../media/setup-direct-get-resourcegroup.png)

1. 만든 **피어링** 리소스를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 보기](../media/setup-direct-get-open.png)

1. **개요** 페이지에는 여기에 표시된 대로 상위 수준 정보가 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 개요 창](../media/setup-exchange-get-overview.png)

1. 왼쪽에서 **ASN 정보를** 선택하여 PeerAsn을 만들 때 제출된 정보를 봅니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 ASN 정보](../media/setup-direct-get-asninfo.png)

1. 왼쪽에서 **연결을**선택합니다. 화면 상단에는 지하철 내의 여러 시설에서 ASN과 Microsoft 간의 피어링 연결 요약이 표시됩니다. 그림과 같이 중앙 **창에서** 연결을 선택하여 **개요** 페이지에서 연결 요약에 액세스할 수도 있습니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 연결](../media/setup-exchange-get-connectionssummary.png)

    * **연결 상태는** 피어링 연결 설정의 상태에 해당합니다. 이 필드에 표시된 상태는 [Exchange 피어링 연습에](../walkthrough-exchange-all.md)표시된 상태 다이어그램을 따릅니다.
    * **IPv4 세션 상태** 및 **IPv6 세션 상태는** 각각 IPv4 및 IPv6 BGP 세션 상태에 해당합니다.  
    * 화면 상단에서 행을 선택하면 아래쪽의 **연결** 섹션에 각 연결에 대한 세부 정보가 표시됩니다. **구성,** **IPv4 주소**및 **IPv6 주소를**확장하려면 화살표를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 보기](../media/setup-exchange-get-connectionsipv4.png)
