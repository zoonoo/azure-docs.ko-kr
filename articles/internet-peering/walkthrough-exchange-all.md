---
title: Exchange 피어 링 연습
titleSuffix: Azure
description: Exchange 피어 링 연습
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: bb761afa6d8953b441b6c9541c43b73031719494
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775174"
---
# <a name="exchange-peering-walkthrough"></a>Exchange 피어 링 연습

이 섹션에서는 Exchange 피어 링을 설정 하 고 관리 하기 위해 수행 해야 하는 단계를 설명 합니다.

## <a name="create-an-exchange-peering"></a>Exchange 피어 링 만들기
> [!div class="mx-imgBorder"]
> ![Exchange 피어 링 워크플로 및 연결 상태를](./media/exchange-peering.png)

Exchange 피어 링을 프로 비전 하려면 다음 단계를 따라야 합니다.
1. Microsoft [피어 링 정책](https://peering.azurewebsites.net/peering) 을 검토 하 여 Exchange 피어 링에 대 한 요구 사항을 파악 합니다.
1. [Peeringdb](https://www.peeringdb.com/net/694) 에서 Microsoft 피어 링 위치 및 피어 링 기능 id 찾기
1. 자세한 내용은 [PowerShell을 사용 하 여 exchange 피어 링 만들기 및 수정](howto-exchange-powershell.md) 문서에 설명 된 지침을 사용 하 여 피어 링 위치에 대 한 교환 피어 링을 요청 합니다.
1. 피어 링 요청을 제출 하면 Microsoft에서 요청을 검토 하 고 필요한 경우 사용자에 게 연락 합니다.
1. 승인 되 면 연결 상태가 승인 됨으로 변경 됩니다.
1. 최종 사용자에 게 BGP 세션을 구성 하 고 Microsoft에 알림
1. 모든 정책 거부 및 종단 간 유효성 검사를 사용 하 여 BGP 세션을 프로 비전 합니다.
1. 성공 하면 피어 링 연결 상태가 활성 임을 알리는 알림을 받게 됩니다.
1. 그러면 새 피어 링을 통해 트래픽이 허용 됩니다.

연결 상태는 표준 [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) 세션 상태와 혼동 되지 않습니다.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>레거시 Exchange 피어 링을 Azure 리소스로 변환
레거시 Exchange 피어 링을 Azure 리소스로 변환 하려면 다음 단계를 따라야 합니다.
1. [레거시 Exchange 피어 링을 Azure 리소스로 변환](howto-legacy-exchange-powershell.md) 의 지침을 따릅니다.
1. 변환 요청을 제출 하면 Microsoft에서 요청을 검토 하 고 필요한 경우 사용자에 게 연락 합니다.
1. 승인 되 면 연결 상태가 활성으로 표시 된 Exchange 피어 링이 표시 됩니다.

## <a name="deprovision-exchange-peering"></a>Exchange 피어 링 프로 비전 해제
[Microsoft 피어](mailto:peering@microsoft.com) 링에 문의 하 여 Exchange 피어 링을 프로 비전 해제 합니다.

프로 비전 해제에 대해 Exchange 피어 링이 설정 된 경우에는 연결 상태가 **pendingremove** 로 표시 됩니다.

> [!NOTE]
> 연결 상태가 ProvisioningStarted 이거나 ProvisioningCompleted 인 경우 PowerShell cmdlet을 실행 하 여 Exchange 피어 링을 삭제 하면 작업이 실패 합니다.

## <a name="next-steps"></a>다음 단계

* [Microsoft와의 피어 링을 설정 하기 위한 필수 조건](prerequisites.md)에 대해 알아봅니다.
