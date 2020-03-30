---
title: 직접 피어링 연습
titleSuffix: Azure
description: 직접 피어링 연습
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d88fcfc4d3e073bf544f2ca0f4d01dbe305b45da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775512"
---
# <a name="direct-peering-walkthrough"></a>직접 피어링 연습

이 섹션에서는 직접 피어링을 설정하고 관리하기 위해 따라야 하는 단계를 설명합니다.

## <a name="create-a-direct-peering"></a>직접 피어링 만들기
> [!div class="mx-imgBorder"]
> ![직접 피어링 워크플로 및 연결 상태](./media/direct-peering.png)

Direct 피어링을 프로비전하려면 다음 단계를 따라야 합니다.
1. Microsoft 피어링 정책을 검토하여 직접 [피어링에](https://peering.azurewebsites.net/peering) 대한 요구 사항을 이해합니다.
1. [직접 피어링 만들기 또는 수정의](howto-direct-powershell.md) 지침에 따라 피어링 요청을 제출합니다.
1. 피어링 요청을 제출한 후 Microsoft는 등록된 전자 메일 주소를 사용하여 LOA(승인 서) 또는 기타 정보를 제공하기 위해 연락을 합니다.
1. 피어링 요청이 승인되면 프로비저닝시작에 대한 연결 상태가 변경됩니다.
1. 그러려면 다음 작업을 수행해야 합니다.
    1. LOA에 따른 완전한 배선
    1. (선택적으로) 169.254.0.0/16을 사용하여 링크 테스트를 수행합니다.
    1. BGP 세션을 구성한 다음 알려주시기 않습니다.
1. Microsoft는 모든 정책을 거부하여 BGP 세션을 프로비전하고 종단 간 유효성을 검사합니다.
1. 성공하면 피어링 연결 상태가 활성이라는 알림을 받게 됩니다.
1. 그러면 새 피어링을 통해 트래픽이 허용됩니다.

연결 상태는 표준 [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) 세션 상태와 혼동해서는 안 됩니다.

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>레거시 직접 피어링을 Azure 리소스로 변환
레거시 직접 피어링을 Azure 리소스로 변환하려면 다음 단계를 따라야 합니다.
1. 레거시 직접 [피어링에서 Azure 리소스로 변환의 지침에](howto-legacy-direct-powershell.md) 따라
1. 변환 요청을 제출한 후 Microsoft는 요청을 검토하고 필요한 경우 귀하에게 연락합니다.
1. 승인되면 연결 상태가 활성으로 직접 피어링이 표시됩니다.

## <a name="deprovision-direct-peering"></a>프로비저닝 해제 직접 피어링
[Microsoft 피어링](mailto:peering@microsoft.com) 팀에 문의하여 직접 피어링을 프로비전 해제합니다.

프로비저닝 해제에 대해 직접 피어링이 설정된 경우 연결 상태가 **보류 해제됨으로** 표시됩니다.

> [!NOTE]
> PowerShell cmdlet을 실행하여 ConnectionState가 프로비저닝 시작 또는 프로비저닝일 때 직접 피어링을 삭제하면 작업이 실패합니다.

## <a name="next-steps"></a>다음 단계

* [Microsoft를 사용하여 피어링을 설정하는 필수 구성 방법에](prerequisites.md)대해 알아봅니다.
