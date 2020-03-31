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
ms.openlocfilehash: e3e87e59f5b3c95051d9ee53e4b8d87afe9d9ba8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774563"
---
이 섹션에서는 직접 피어링에 대해 다음과 같은 수정 작업을 수행하는 방법에 대해 설명합니다.

### <a name="add-exchange-peering-connections"></a>Exchange 피어링 연결 추가

1. 상단의 **+ 연결 추가** 버튼을 클릭하고 새 피어링 연결을 구성합니다.
    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 보기](../media/setup-exchange-modify-addconnection.png)
1. **Exchange 피어링 연결** 양식을 작성하고 **저장을**클릭합니다. 피어링 연결 구성에 대한 도움말을 보려면 위의 "직접 피어링 만들기 및 프로비전" 섹션의 단계를 검토합니다.
    > [!div class="mx-imgBorder"]
    > ![피어링 리소스 보기](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Exchange 피어링 연결 제거

1. 삭제하려는 피어링 연결을 클릭한 다음 다음 을 **클릭합니다.**  >  **연결** 버튼을 삭제합니다.
    > [!div class="mx-imgBorder"]
    > ![피어링 연결 삭제](../media/setup-exchange-modify-deleteconnection.png)
1. 강조 표시된 상자에 표시된 대로 리소스 ID를 **삭제 확인** 상자에 입력하고 **삭제를**클릭합니다.
    > [!div class="mx-imgBorder"]
    > ![피어링 연결 삭제확인](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>활성 연결에 IPv4/IPv6 세션 추가

1. 수정하려는 피어링 연결을 클릭한 다음 다음 을 **클릭합니다.**  >  **연결** 버튼을 편집합니다.
    > [!div class="mx-imgBorder"]
    > ![피어링 연결 편집](../media/setup-exchange-modify-editconnection.png)
1. **IPv4 주소** 또는 **IPv6 주소** 정보를 추가하고 **저장을**클릭합니다.
    > [!div class="mx-imgBorder"]
    > ![피어링 연결 수정](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>활성 연결에서 IPv4/IPv6 세션 제거

기존 연결에서 IPv4/IPv6 세션을 제거하는 것은 현재 포털에서 지원되지 않습니다. [연락처 마이크로 소프트 피어링](mailto:peeringexperience@microsoft.com).