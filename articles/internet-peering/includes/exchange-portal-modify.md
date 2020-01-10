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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774563"
---
이 섹션에서는 직접 피어 링에 대해 다음과 같은 수정 작업을 수행 하는 방법을 설명 합니다.

### <a name="add-exchange-peering-connections"></a>Exchange 피어 링 연결 추가

1. 위쪽에서 **+ 연결 추가** 단추를 클릭 하 고 새 피어 링 연결을 구성 합니다.
    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 뷰](../media/setup-exchange-modify-addconnection.png)
1. **Exchange 피어 링 연결** 양식을 작성 하 고 **저장**을 클릭 합니다. 피어 링 연결을 구성 하는 데 도움이 필요 하면 위의 "직접 피어 링 만들기 및 프로 비전" 섹션의 단계를 검토 하세요.
    > [!div class="mx-imgBorder"]
    > ![피어 링 리소스 뷰](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Exchange 피어 링 연결 제거

1. 삭제할 피어 링 연결을 클릭 한 다음 > **연결 삭제** 단추를 클릭 합니다 **.**
    > [!div class="mx-imgBorder"]
    > ![피어 링 연결 삭제](../media/setup-exchange-modify-deleteconnection.png)
1. 강조 표시 된 상자에 표시 된 대로 **삭제 확인** 상자에 리소스 ID를 입력 하 고 **삭제**를 클릭 합니다.
    > [!div class="mx-imgBorder"]
    > ![피어 링 연결 DeleteConfirm](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>활성 연결에 IPv4/IPv6 세션 추가

1. 수정 하려는 피어 링 연결을 클릭 한 다음 > **연결 편집** 단추를 클릭 **합니다.**
    > [!div class="mx-imgBorder"]
    > ![피어 링 연결 편집](../media/setup-exchange-modify-editconnection.png)
1. **IPv4 주소** 또는 **IPv6 주소** 정보를 추가 하 고 **저장**을 클릭 합니다.
    > [!div class="mx-imgBorder"]
    > ![피어 링 연결 수정](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>활성 연결에서 IPv4/IPv6 세션 제거

기존 연결에서 IPv4/IPv6 세션을 제거 하는 기능은 현재 포털에서 지원 되지 않습니다. [Microsoft 피어 링](mailto:peeringexperience@microsoft.com)에 문의 하세요.