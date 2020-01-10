---
title: 직접 피어 링 연습
titleSuffix: Azure
description: 직접 피어 링 연습
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d88fcfc4d3e073bf544f2ca0f4d01dbe305b45da
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775512"
---
# <a name="direct-peering-walkthrough"></a>직접 피어 링 연습

이 섹션에서는 직접 피어 링을 설정 하 고 관리 하기 위해 수행 해야 하는 단계를 설명 합니다.

## <a name="create-a-direct-peering"></a>직접 피어 링 만들기
> [!div class="mx-imgBorder"]
> ![직접 피어 링 워크플로 및 연결 상태](./media/direct-peering.png)

직접 피어 링을 프로 비전 하려면 다음 단계를 따라야 합니다.
1. Microsoft [피어 링 정책](https://peering.azurewebsites.net/peering) 을 검토 하 여 직접 피어 링에 대 한 요구 사항을 파악 합니다.
1. [직접 피어 링 만들기 또는 수정](howto-direct-powershell.md) 의 지침에 따라 피어 링 요청을 제출 합니다.
1. 피어 링 요청을 제출 하면 Microsoft에서 등록 된 전자 메일 주소를 사용 하 여 LOA (인증의 문자) 또는 기타 정보를 제공 하 게 됩니다.
1. 피어 링 요청이 승인 되 면 연결 상태가 ProvisioningStarted로 변경 됩니다.
1. 그러려면 다음 작업을 수행해야 합니다.
    1. LOA에 따라 배선 완료
    1. (선택 사항) 169.254.0.0/16을 사용 하 여 링크 테스트 수행
    1. BGP 세션을 구성한 다음 알려 주세요.
1. Microsoft는 모든 정책을 거부 하 고 종단 간 유효성을 검사 하 여 BGP 세션을 프로 비전 합니다.
1. 성공 하면 피어 링 연결 상태가 활성 임을 알리는 알림을 받게 됩니다.
1. 그러면 새 피어 링을 통해 트래픽이 허용 됩니다.

연결 상태는 표준 [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) 세션 상태와 혼동 되지 않습니다.

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>레거시 직접 피어 링을 Azure 리소스로 변환
레거시 직접 피어 링을 Azure 리소스로 변환 하려면 다음 단계를 따라야 합니다.
1. [레거시 직접 피어 링을 Azure 리소스로 변환](howto-legacy-direct-powershell.md) 의 지침을 따릅니다.
1. 변환 요청을 제출 하면 Microsoft에서 요청을 검토 하 고 필요한 경우 사용자에 게 연락 합니다.
1. 승인 되 면 연결 상태가 활성으로 표시 되는 직접 피어 링이 표시 됩니다.

## <a name="deprovision-direct-peering"></a>직접 피어 링 프로 비전 해제
[Microsoft 피어 링](mailto:peering@microsoft.com) 팀에 문의 하 여 직접 피어 링을 프로 비전 해제 합니다.

프로 비전 해제에 대 한 직접 피어 링이 설정 된 경우에는 연결 상태를 **pendingremove** 로 볼 수 있습니다.

> [!NOTE]
> ConnectionState가 ProvisioningStarted 또는 ProvisioningCompleted 일 때 PowerShell cmdlet을 실행 하 여 직접 피어 링을 삭제 하면 작업이 실패 합니다.

## <a name="next-steps"></a>다음 단계

* [Microsoft와의 피어 링을 설정 하기 위한 필수 조건](prerequisites.md)에 대해 알아봅니다.
