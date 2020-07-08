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
ms.openlocfilehash: 3894bf046ed4ee3f068e43dbc5bc5b7f2a1002b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81681050"
---
이 섹션에서는 직접 피어 링에 대해 다음과 같은 수정 작업을 수행 하는 방법을 설명 합니다.

### <a name="add-direct-peering-connections"></a>직접 피어 링 연결 추가
1. **+ 연결 추가** 단추를 선택 하 고 새 피어 링 연결을 구성 합니다.
    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 뷰](../media/setup-direct-modify-addconnection.png)

1. **직접 피어 링 연결** 양식을 작성 하 고 **저장**을 선택 합니다. 피어 링 연결을 구성 하는 방법에 대 한 도움말을 보려면 "직접 피어 링 만들기 및 프로 비전" 섹션의 단계를 검토 하세요.
    > [!div class="mx-imgBorder"]
    > ![직접 피어 링 연결 폼](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>직접 피어 링 연결 제거

연결 제거는 현재 Azure Portal에서 지원 되지 않습니다. 자세한 내용은 [Microsoft 피어 링](mailto:peeringexperience@microsoft.com)에 문의 하세요.

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>활성 연결의 대역폭 업그레이드 또는 다운 그레이드
1. 수정 하려는 피어 링 연결을 선택 하 **고 ...를 선택**  >  합니다. **연결 편집**
    > [!div class="mx-imgBorder"]
    > ![연결 편집](../media/setup-direct-modify-editconnection.png)

1. 슬라이더를 이동 하 여 대역폭을 수정 하 고 **저장**을 선택 합니다.
    > [!div class="mx-imgBorder"]
    > ![대역폭 수정](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>활성 연결에 대 한 IPv4 또는 IPv6 세션 정보 추가
1. 수정 하려는 피어 링 연결을 선택 하 **고 ...를 선택**  >  합니다. 1 단계와 같이 **연결을 편집** 합니다.
1. **Session IPv4 접두사** 또는 **세션 IPv6 접두사** 정보를 입력 하 고 **저장**을 선택 합니다.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>활성 연결에 대 한 IPv4 또는 IPv6 세션 정보 제거
**세션 IPv4 접두사** 또는 **세션 IPv6 접두사** 정보는 현재 포털에서 제거할 때 지원 되지 않습니다. 자세한 내용은 [Microsoft 피어 링](mailto:peeringexperience@microsoft.com)에 문의 하세요.
