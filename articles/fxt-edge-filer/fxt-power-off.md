---
title: Microsoft Azure FXT Edge 필터가 단위를 종료 하는 방법
description: 시작 및 Azure FXT Edge 필터가 노드를 안전 하 게 종료 하는 절차
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 8d779547040da9855409d3408827af2e4acd17a7
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542872"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Azure FXT Edge 필터가 하드웨어 끄지 때 안전 하 게 하는 방법

실제 전원 단추를 사용 하 여 개별 노드에서 전환할 수 있습니다, 있지만 정상적인 단위를 종료 하지 사용 해야 있습니다.

Azure FXT Edge 필터가 노드를 클러스터의 일부로 사용 되 면 하드웨어를 종료 하려면 클러스터 컨트롤 패널 소프트웨어를 사용 해야 합니다. 

> [!NOTE] 
> 가능한 데이터 손실 또는 손상을 방지 하려면 항상 Azure FXT Edge 파일 러를 종료 하려면 제어판 소프트웨어를 사용 합니다. 이렇게 하려면 Microsoft 고객 서비스 및 지원에서 지시 하지 않으면 종료에 대 한 실제 전원 단추를 사용 하지 마세요.
> 
> 전기 긴급 상황에서는 전원 코드를 연결 끊기 또는 데이터를 사용 하 여 center의 전기 메커니즘을 분리 합니다.

## <a name="shut-down-a-node-from-the-control-panel"></a>제어판에서 노드 종료

Azure FXT Edge 필터가 노드를 안전 하 게 전원을에 이러한 지침을 따릅니다.

1. 제어판 클러스터에 로그인 합니다. (에 설명 된 지침 [설정 페이지를 열려면](fxt-cluster-create.md#open-the-settings-pages))
1. 클릭 합니다 **설정을** 탭을 선택한 다음 부하를 **클러스터** > **FXT 노드** 페이지입니다.
1. 클러스터 노드 목록에서 종료 하려는 것을 찾습니다. 클릭 합니다 **전원을 끄고** 단추 해당 **작업** 열입니다. 
1. 몇 분 정도 기다립니다. 노드가 종료 되 고 전원을 자체입니다.

## <a name="next-steps"></a>다음 단계

* 상태 Led 및 다른 표시기에 대해 알아봅니다 [모니터 Azure FXT Edge 필터가 하드웨어 상태](fxt-monitor.md)합니다.
* 제공 하는 Azure FXT Edge 필터가 전원에 대해 자세히 알아보세요 [전원 케이블 연결](fxt-network-power.md#connect-power-cables)합니다.
