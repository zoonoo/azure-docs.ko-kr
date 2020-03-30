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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775343"
---
이 섹션에서는 직접 피어링에 대해 다음과 같은 수정 작업을 수행하는 방법에 대해 설명합니다.

### <a name="add-direct-peering-connections"></a>직접 피어링 연결 추가
1. 상단의 **+ 연결 추가** 버튼을 클릭하고 새 피어링 연결을 구성합니다.
    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 보기](../media/setup-direct-modify-addconnection.png)
1. **직접 피어링 연결** 양식을 작성하고 **저장을**클릭합니다. 피어링 연결 구성에 대한 도움말을 보려면 위의 "직접 피어링 만들기 및 프로비전" 섹션의 단계를 검토합니다.
    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 보기](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>직접 피어링 연결 제거

연결 제거는 현재 포털에서 지원되지 않습니다. [연락처 마이크로 소프트 피어링](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>활성 연결에서 대역폭 업그레이드 또는 다운그레이드
1. 수정하려는 피어링 연결을 클릭한 다음 다음 을 **클릭합니다.**  >  **연결** 버튼을 편집합니다.
    > [!div class="mx-imgBorder"]
    > ![피어링 연결 편집](../media/setup-direct-modify-editconnection.png)
1. 아래 와 같이 대역폭을 수정한 다음 **저장을**클릭합니다.
    > [!div class="mx-imgBorder"]
    > ![피어링 연결 수정 대역폭](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>활성 연결에 IPv4/IPv6 세션을 추가합니다.
1. 수정하려는 피어링 연결을 클릭한 다음 다음 을 **클릭합니다.**  > 위와 같이 연결 버튼을 **편집합니다.**
1. **세션 IPv4 접두사** 또는 **세션 IPv6 접두사** 정보를 추가하고 **저장을**클릭합니다.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>활성 연결에서 IPv4/IPv6 세션을 제거합니다.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
