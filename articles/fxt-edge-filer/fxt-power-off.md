---
title: 마이크로소프트 Azure FXT 가장자리 파일러 단위를 종료 하는 방법
description: Azure FXT 에지 파일러 노드의 시작 및 안전한 종료 절차
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 91445c4219abaa67385105f52515f52c87e2cf06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72255994"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Azure FXT 에지 파일러 하드웨어의 전원을 안전하게 끄는 방법

물리적 전원 단추를 사용하여 개별 노드를 전환할 수 있지만 정상적인 상황에서는 장치를 종료하는 데 사용하지 않아야 합니다.

Azure FXT Edge Filer 노드가 클러스터의 일부로 사용되면 클러스터 제어판 소프트웨어를 사용하여 하드웨어를 종료해야 합니다. 

> [!NOTE] 
> 가능한 데이터 손실 이나 손상을 방지 하려면 항상 제어판 소프트웨어를 사용 하 여 Azure FXT 가장자리 파일러를 종료 합니다. Microsoft 고객 서비스 및 지원팀에 지시가 없는 한 종료를 위해 물리적 전원 단추를 사용하지 마십시오.
> 
> 전기 비상 상황에서는 전원 코드를 분리하거나 데이터 센터의 전기 분리 메커니즘을 사용합니다.

## <a name="shut-down-a-node-from-the-control-panel"></a>제어판에서 노드 종료

다음 지침에 따라 Azure FXT Edge 파일러 노드의 전원을 안전하게 끄십시오.

1. 클러스터 제어판에 로그인합니다. (설정 [페이지 열기에서](fxt-cluster-create.md#open-the-settings-pages)길 찾기)
1. **설정** 탭을 클릭한 다음 **클러스터** > FXT 노드 페이지를**로드합니다.**
1. 클러스터 노드 목록에서 종료할 노드를 찾습니다. **작업** 열에서 **전원 해제** 버튼을 클릭합니다. 
1. 잠시 만요. 노드가 종료되고 자체 전원이 꺼집니다.

## <a name="next-steps"></a>다음 단계

* [모니터 Azure FXT Edge Filer 하드웨어 상태의](fxt-monitor.md)상태 LED 및 기타 표시기에 대해 알아봅니다.
* [연결 전원 케이블의](fxt-network-power.md#connect-power-cables)Azure FXT Edge 파일러 전원 공급 장치에 대해 자세히 알아보기.
