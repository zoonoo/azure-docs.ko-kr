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
ms.openlocfilehash: d9ff01cf0180dae7f75d9753ba889d0caddad937
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775343"
---
이 섹션에서는 직접 피어 링에 대해 다음과 같은 수정 작업을 수행 하는 방법을 설명 합니다.

### <a name="add-direct-peering-connections"></a>직접 피어 링 연결 추가
1. 위쪽에서 **+ 연결 추가** 단추를 클릭 하 고 새 피어 링 연결을 구성 합니다.
    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 뷰](../media/setup-direct-modify-addconnection.png)
1. **직접 피어 링 연결** 양식을 입력 하 고 **저장**을 클릭 합니다. 피어 링 연결을 구성 하는 데 도움이 필요 하면 위의 "직접 피어 링 만들기 및 프로 비전" 섹션의 단계를 검토 하세요.
    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 뷰](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>직접 피어 링 연결 제거

연결 제거는 현재 포털에서 지원 되지 않습니다. [Microsoft 피어 링](mailto:peeringexperience@microsoft.com)에 문의 하세요.

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>활성 연결의 대역폭 업그레이드 또는 다운 그레이드
1. 수정 하려는 피어 링 연결을 클릭 한 다음 > **연결 편집** 단추를 클릭 **합니다.**
    > [!div class="mx-imgBorder"]
    > ![피어 링 연결 편집](../media/setup-direct-modify-editconnection.png)
1. 아래와 같이 대역폭을 수정 하 고 **저장**을 클릭 합니다.
    > [!div class="mx-imgBorder"]
    > ![피어 링 연결 수정 대역폭](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>활성 연결에 IPv4/IPv6 세션을 추가 합니다.
1. 수정 하려는 피어 링 연결을 클릭 한 다음 위와 같이 **...**  > **연결 편집** 단추를 클릭 합니다.
1. **세션 IPv4 접두사** 또는 **세션 IPv6 접두사** 정보를 추가 하 고 **저장**을 클릭 합니다.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>활성 연결에서 IPv4/IPv6 세션을 제거 합니다.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
