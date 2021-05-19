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
ms.openlocfilehash: c3212acd5015edbb639b8904b885c718d654e5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96356264"
---
이 섹션에서는 직접 피어링에 대해 다음과 같은 수정 작업을 수행하는 방법을 설명합니다.

### <a name="add-direct-peering-connections"></a>직접 피어링 연결 추가
1. **+ 연결 추가** 단추를 선택하고 새 피어링 연결을 구성합니다.
    > [!div class="mx-imgBorder"]
    > ![AshburnPeering - 연결 페이지에 연결 목록이 표시됩니다. \+ 연결 추가 단추가 강조 표시됩니다.](../media/setup-direct-modify-addconnection.png)

1. **직접 피어링 연결** 양식을 작성하고 **저장** 을 선택합니다. 피어링 연결을 구성하는 방법에 대한 도움말을 보려면 "직접 피어링 만들기 및 프로비전" 섹션의 단계를 검토하세요.
    > [!div class="mx-imgBorder"]
    > ![직접 피어링 연결 양식](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>직접 피어링 연결 제거

연결 제거는 현재 Azure Portal에서 지원되지 않습니다. 자세한 내용은 [Microsoft 피어링](mailto:peeringexperience@microsoft.com)에 문의하세요.

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>활성 연결에서 대역폭 업그레이드 또는 다운그레이드
1. 수정하려는 피어링 연결을 선택한 다음, **...**  > **연결 편집** 을 선택합니다.
    > [!div class="mx-imgBorder"]
    > ![연결 편집](../media/setup-direct-modify-editconnection.png)

1. 슬라이더를 이동하여 대역폭을 수정한 다음, **저장** 을 선택합니다.
    > [!div class="mx-imgBorder"]
    > ![대역폭 수정](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>활성 연결에 IPv4 또는 IPv6 세션 정보 추가
1. 수정하려는 피어링 연결을 선택한 다음, 1단계에 표시된 대로 **...**  > **연결 편집** 을 선택합니다.
1. **세션 IPv4 접두사** 또는 **세션 IPv6 접두사** 정보를 입력하고 **저장** 을 선택합니다.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>활성 연결에서 IPv4 또는 IPv6 세션 정보 제거
**세션 IPv4 접두사** 또는 **세션 IPv6 접두사** 정보 제거는 현재 포털에서 지원되지 않습니다. 자세한 내용은 [Microsoft 피어링](mailto:peeringexperience@microsoft.com)에 문의하세요.
