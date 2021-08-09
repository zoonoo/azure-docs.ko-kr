---
title: Exchange 피어링 연습
titleSuffix: Azure
description: Exchange 피어링 연습
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 97430d86cba1107ef67264a181925d2a4d7c12c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586825"
---
# <a name="exchange-peering-walkthrough"></a>Exchange 피어링 연습

이 섹션에서는 Exchange 피어링을 설정하고 관리하기 위해 수행해야 하는 단계를 설명합니다.

## <a name="create-an-exchange-peering"></a>Exchange 피어링 만들기
> [!div class="mx-imgBorder"]
> ![Exchange 피어링 워크플로 및 연결 상태](./media/exchange-peering.png)

Exchange 피어링을 프로비저닝하려면 다음 단계를 수행해야 합니다.
1. Exchange 피어링에 대한 요구 사항을 이해하려면 Microsoft [피어링 정책](https://peering.azurewebsites.net/peering)을 검토하세요.
1. [PeeringDB](https://www.peeringdb.com/net/694)에서 Microsoft 피어링 위치 및 피어링 시설 ID 찾기
1. [PowerShell을 사용하여 Exchange 피어링 만들기 및 수정](howto-exchange-powershell.md) 문서의 지침에 따라 피어링 위치에 대한 Exchange 피어링을 요청합니다.
1. 피어링 요청을 제출하면 Microsoft에서 요청을 검토하고 필요한 경우 연락 드립니다.
1. 승인되면 연결 상태가 승인됨으로 변경됩니다.
1. 사용자 측에서 BGP 세션을 구성하고 Microsoft에 알립니다.
1. Microsoft에서 모두 거부 정책을 사용하여 BGP 세션을 프로비저닝하고 엔드투엔드 유효성을 검사합니다.
1. 성공하면 피어링 연결 상태가 활성이라는 알림이 수신됩니다.
1. 그러면 새 피어링을 통해 트래픽이 허용됩니다.

연결 상태를 표준 [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) 세션 상태와 혼동하지 마세요.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>레거시 Exchange 피어링을 Azure 리소스로 변환
레거시 Exchange 피어링을 Azure 리소스로 변환하려면 다음 단계를 수행합니다.
1. [레거시 Exchange 피어링을 Azure 리소스로 변환](howto-legacy-exchange-powershell.md)의 지침을 따릅니다.
1. 변환 요청을 제출하면 Microsoft에서 요청을 검토하고 필요한 경우 연락 드립니다.
1. 승인되면 연결 상태가 활성인 Exchange 피어링이 표시됩니다.

## <a name="deprovision-exchange-peering"></a>Exchange 피어링의 프로비전 해제
[Microsoft 피어링](mailto:peering@microsoft.com)에 문의하여 Exchange 피어링의 프로비전을 해제합니다.

Exchange 피어링의 프로비전 해제가 설정되면 연결 상태가 **PendingRemove** 로 표시됩니다.

> [!NOTE]
> 연결 상태가 ProvisioningStarted 또는 ProvisioningCompleted일 때 PowerShell cmdlet을 실행하여 Exchange 피어링을 삭제하면 작업이 실패합니다.

## <a name="next-steps"></a>다음 단계

* [Microsoft와 피어링을 설정하기 위한 사전 요구 사항](prerequisites.md)에 대해 알아봅니다.
