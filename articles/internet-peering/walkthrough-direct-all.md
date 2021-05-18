---
title: 직접 피어링 연습
titleSuffix: Azure
description: 직접 피어링 연습
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 6a1fa7f582511a2089bab2694f9f5ec47634ff54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592384"
---
# <a name="direct-peering-walkthrough"></a>직접 피어링 연습

이 섹션에서는 직접 피어링을 설정하고 관리하기 위해 수행해야 하는 단계를 설명합니다.

## <a name="create-a-direct-peering"></a>직접 피어링 만들기
> [!div class="mx-imgBorder"]
> ![직접 피어링 워크플로 및 연결 상태](./media/direct-peering.png)

직접 피어링을 프로비전하려면 다음 단계를 수행해야 합니다:
1. 직접 피어링에 대한 요구 사항을 이해하려면 Microsoft [피어링 정책](https://peering.azurewebsites.net/peering)을 검토하세요.
1. 피어링 요청을 제출하려면 [직접 피어링 만들기 또는 수정](howto-direct-powershell.md)의 지침을 수행합니다.
1. 피어링 요청을 제출하면 Microsoft에서 등록된 이메일 주소로 연락하여 LOA(승인 문서) 또는 기타 정보를 제공합니다.
1. 피어링 요청이 승인되면 연결 상태가 ProvisioningStarted로 변경됩니다.
1. 그러려면 다음 작업을 수행해야 합니다.
    1. LOA에 따라 배선 완료
    1. (선택 사항)169.254.0.0/16을 사용하여 링크 테스트 수행
    1. BGP 세션을 구성한 다음 알려주세요.
1. Microsoft는 모두 거부 정책을 사용하여 BGP 세션을 프로비전하고 종단 간 유효성을 검사합니다.
1. 성공하면 피어링 연결 상태가 활성이라는 알림이 수신됩니다.
1. 그러면 새 피어링을 통해 트래픽이 허용됩니다.

연결 상태를 표준 [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) 세션 상태와 혼동하지 마세요.

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>레거시 직접 피어링을 Azure 리소스로 변환
레거시 직접 피어링을 Azure 리소스로 변환하려면 다음 단계를 수행합니다:
1. [레거시 직접 피어링을 Azure 리소스로 변환](howto-legacy-direct-powershell.md)의 지침을 따릅니다
1. 변환 요청을 제출하면 Microsoft에서 요청을 검토하고 필요한 경우 연락 드립니다.
1. 승인되면 연결 상태가 활성인 직접 피어링이 표시됩니다.

## <a name="deprovision-direct-peering"></a>직접 피어링 프로비전 해제
직접 피어링의 프로비전을 해제하려면 [Microsoft 피어링](mailto:peering@microsoft.com) 팀에 문의하세요.

프로비전 해제를 위해 직접 피어링이 설정되면 연결 상태가 **PendingRemove** 로 표시됨

> [!NOTE]
> ConnectionState가 ProvisioningStarted 또는 ProvisioningCompleted일 때 PowerShell cmdlet을 실행하여 직접 피어링을 삭제하면 작업이 실패합니다.

## <a name="next-steps"></a>다음 단계

* [Microsoft와 피어링을 설정하기 위한 사전 요구 사항](prerequisites.md)에 대해 알아봅니다.
