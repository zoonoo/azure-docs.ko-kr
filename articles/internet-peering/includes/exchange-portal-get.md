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
ms.openlocfilehash: 000971878e24c46892aaef1fa0c65237a4219883
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81678577"
---
**피어 링** 리소스가 성공적으로 배포 되 면 다음 단계에 따라 해당 리소스를 볼 수 있습니다.

1. **리소스 그룹**으로 이동 하 여 **피어 링** 리소스를 만들 때 선택한 리소스 그룹을 선택 합니다. 너무 많은 리소스 그룹이 있는 경우 **필터** 상자를 사용 합니다.

    > [!div class="mx-imgBorder"]
    > ![리소스 그룹](../media/setup-direct-get-resourcegroup.png)

1. 만든 **피어 링** 리소스를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 뷰](../media/setup-direct-get-open.png)

1. **개요** 페이지에는 여기에 표시 된 것 처럼 높은 수준의 정보가 표시 됩니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 개요 창](../media/setup-exchange-get-overview.png)

1. 왼쪽에서 **asn 정보** 를 선택 하 여 peerasn을 만들 때 제출 된 정보를 확인 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 ASN 정보](../media/setup-direct-get-asninfo.png)

1. 왼쪽에서 **연결**을 선택 합니다. 화면 위쪽에는 metro 내의 여러 시설에서 ASN과 Microsoft 간의 피어 링 연결 요약이 표시 됩니다. 표시 된 것 처럼 가운데 창에서 **연결** 을 선택 하 여 **개요** 페이지에서 연결 요약에 액세스할 수도 있습니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 연결](../media/setup-exchange-get-connectionssummary.png)

    * **연결 상태** 는 피어 링 연결 설정의 상태에 해당 합니다. 이 필드에 표시 되는 상태는 [Exchange 피어 링 연습](../walkthrough-exchange-all.md)에 표시 된 상태 다이어그램을 따릅니다.
    * **Ipv4 세션 상태** 및 **ipv6 세션 상태** 는 각각 ipv4 및 ipv6 BGP 세션 상태에 해당 합니다.  
    * 화면 위쪽에서 행을 선택 하면 아래쪽의 **연결** 섹션에 각 연결에 대 한 세부 정보가 표시 됩니다. 화살표를 선택 하 여 **구성**, **IPv4 주소**및 **IPv6 주소**를 확장 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 뷰](../media/setup-exchange-get-connectionsipv4.png)
