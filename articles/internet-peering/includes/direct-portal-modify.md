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
ms.openlocfilehash: 3894bf046ed4ee3f068e43dbc5bc5b7f2a1002b4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681050"
---
이 섹션에서는 Direct 피어링에 대해 다음과 같은 수정 작업을 수행하는 방법을 설명합니다.

### <a name="add-direct-peering-connections"></a>직접 피어링 연결 추가
1. + **연결 추가** 단추를 선택하고 새 피어링 연결을 구성합니다.
    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 보기](../media/setup-direct-modify-addconnection.png)

1. **직접 피어링 연결** 양식을 작성하고 **저장을**선택합니다. 피어링 연결을 구성하는 데 도움이 되면 "직접 피어링 만들기 및 프로비전" 섹션의 단계를 검토합니다.
    > [!div class="mx-imgBorder"]
    > ![직접 피어링 연결 양식](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>직접 피어링 연결 제거

연결을 제거하는 것은 현재 Azure 포털에서 지원되지 않습니다. 자세한 내용은 [Microsoft 피어링](mailto:peeringexperience@microsoft.com)에 문의하십시오.

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>활성 연결에서 대역폭 업그레이드 또는 다운그레이드
1. 수정할 피어링 연결을 선택한 다음 **...**  >  **연결을 편집합니다.**
    > [!div class="mx-imgBorder"]
    > ![연결 편집](../media/setup-direct-modify-editconnection.png)

1. 슬라이더를 이동하여 대역폭을 수정한 다음 **저장을**선택합니다.
    > [!div class="mx-imgBorder"]
    > ![대역폭 수정](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>활성 연결에 IPv4 또는 IPv6 세션 정보 추가
1. 수정할 피어링 연결을 선택한 다음 **...**  > 1단계에 표시된 대로 **연결을 편집합니다.**
1. **세션 IPv4 접두사** 또는 **세션 IPv6 접두사** 정보를 입력하고 **저장을**선택합니다.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>활성 연결에서 IPv4 또는 IPv6 세션 정보 제거
**세션 IPv4 접두사** 또는 **세션 IPv6 접두사** 정보를 제거하는 것은 현재 포털에서 지원되지 않습니다. 자세한 내용은 [Microsoft 피어링](mailto:peeringexperience@microsoft.com)에 문의하십시오.
