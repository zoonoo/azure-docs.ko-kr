---
title: Exchange 피어링 연습
titleSuffix: Azure
description: Exchange 피어링 연습
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: bb761afa6d8953b441b6c9541c43b73031719494
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775174"
---
# <a name="exchange-peering-walkthrough"></a>Exchange 피어링 연습

이 섹션에서는 Exchange 피어링을 설정하고 관리하기 위해 따라야 하는 단계를 설명합니다.

## <a name="create-an-exchange-peering"></a>Exchange 피어링 만들기
> [!div class="mx-imgBorder"]
> ![피어링 워크플로 및 연결 상태 교환](./media/exchange-peering.png)

Exchange 피어링을 프로비전하려면 다음 단계를 따라야 합니다.
1. Microsoft 피어링 정책을 검토하여 Exchange [피어링에](https://peering.azurewebsites.net/peering) 대한 요구 사항을 이해합니다.
1. [피어링DB에서](https://www.peeringdb.com/net/694) Microsoft 피어링 위치 및 피어링 기능 ID 찾기
1. 자세한 내용은 PowerShell 문서를 사용하여 Exchange [피어링 만들기 및 수정의](howto-exchange-powershell.md) 지침을 사용하여 피어링 위치에 대한 Exchange 피어링을 요청합니다.
1. 피어링 요청을 제출한 후 Microsoft는 요청을 검토하고 필요한 경우 귀하에게 연락합니다.
1. 승인되면 연결 상태가 승인됨으로 변경됩니다.
1. 끝에 BGP 세션을 구성하고 Microsoft에 알립니다.
1. DENY ALL 정책을 사용하여 BGP 세션을 프로비전하고 종단 간 유효성을 검사합니다.
1. 성공하면 피어링 연결 상태가 활성이라는 알림을 받게 됩니다.
1. 그러면 새 피어링을 통해 트래픽이 허용됩니다.

연결 상태는 표준 [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) 세션 상태와 혼동해서는 안 됩니다.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>레거시 Exchange 피어링을 Azure 리소스로 변환
레거시 Exchange 피어링을 Azure 리소스로 변환하려면 다음 단계를 따라야 합니다.
1. 레거시 Exchange [피어링에서 Azure 리소스로 변환의 지침에](howto-legacy-exchange-powershell.md) 따라
1. 변환 요청을 제출한 후 Microsoft는 요청을 검토하고 필요한 경우 귀하에게 연락합니다.
1. 승인되면 연결 상태가 활성으로 Exchange 피어링이 표시됩니다.

## <a name="deprovision-exchange-peering"></a>프로비저닝 해제 교환 피어링
프로비저닝 해제Exchange 피어링을 위해 [Microsoft 피어링에](mailto:peering@microsoft.com) 문의하십시오.

프로비저닝 해제를 위해 Exchange 피어링이 설정된 경우 연결 상태가 **보류 해제됨으로** 표시됩니다.

> [!NOTE]
> PowerShell cmdlet을 실행하여 연결 상태가 프로비저닝시작 또는 프로비저닝일 때 Exchange 피어링을 삭제하면 작업이 실패합니다.

## <a name="next-steps"></a>다음 단계

* [Microsoft를 사용하여 피어링을 설정하는 필수 구성 방법에](prerequisites.md)대해 알아봅니다.
