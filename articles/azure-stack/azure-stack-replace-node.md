---
title: Azure Stack 통합 시스템에서 크기 조정 단위 노드를 대체 합니다. | Microsoft Docs
description: Azure Stack 통합 시스템에서 실제 확장 단위 노드를 바꾸는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: mabrigg
ms.lastreviewed: 12/06/2018
ms.openlocfilehash: 83dff31ea32c4fe8797c3725a7e28490134bb27f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251591"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Azure Stack 통합 시스템에서 크기 조정 단위 노드를 대체 합니다.

*적용 대상: Azure Stack 통합 시스템*

이 문서는 Azure Stack 통합 시스템에서 물리적 컴퓨터 (배율 단위 노드 라고도 함)를 대체 하는 일반 프로세스를 설명 합니다. 원래 장비 제조업체 (OEM) 하드웨어 공급 업체를 기반으로 실제 확장 단위 노드 대체 단계는 달라질 수 합니다. 시스템에 관련 된 자세한 단계에 대 한 공급 업체의 필드 교체 장치 (FRU) 설명서를 참조 하세요.

다음 흐름 다이어그램에서는 전체 확장 단위 노드를 대체 하는 일반 FRU 프로세스를 보여 줍니다.

![대체 노드 프로세스에 대 한 흐름도](media/azure-stack-replace-node/replacenodeflow.png)

*이 작업이 필요 하지 않을 하드웨어의 물리적 조건에 기반 합니다.

> [!Note]  
> 종료 작업이 실패 하는 경우에 뒤에 중지 작업을 드레이닝 작업을 사용 하는 것이 좋습니다. 자세한 내용은 사용 가능한 노드 작업을 참조 하세요.  

## <a name="review-alert-information"></a>경고 정보를 검토 합니다.

확장 단위 노드에 다운 된 경우 다음 중요 한 경고를 받게 됩니다.

- 노드 네트워크 컨트롤러에 연결 되어 있지
- 가상 컴퓨터 배치에 액세스할 수 없는 노드
- 확장 단위 노드에 오프 라인 상태입니다.

![목록 아래로 배율 단위에 대 한 경고](media/azure-stack-replace-node/nodedownalerts.png)

열면 합니다 **배율 단위 노드가 오프 라인 상태인** 경고, 경고 설명에 액세스할 수 없는 배율 단위 노드를 포함 합니다. 하드웨어 수명 주기 호스트에서 실행 되는 OEM 전용 모니터링 솔루션에서 추가 경고가 나타날 수도 있습니다.

![노드가 오프 라인 경고의 세부 정보](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>배율 단위 노드 교체 프로세스

다음 단계를 크기 조정 단위 노드 교체 프로세스를 개략적으로 나와 있습니다. 시스템에 관련 된 자세한 단계에 대 한 OEM 하드웨어 공급 업체의 FRU 설명서를 참조 하세요. OEM에서 제공한 설명서를 참조 하지 않고 다음이 단계를 수행 하지 마세요.

1. 사용 된 **종료** 확장 단위 노드에 정상적으로 종료 하는 작업입니다. 이 작업이 필요 하지 않을 하드웨어의 물리적 조건에 기반 합니다. 

2. 가능성이 적은 경우 종료 작업 실패를 사용 합니다 [드레이닝](azure-stack-node-actions.md#drain) 배율 단위 노드 유지 관리 모드로 설정 하는 작업입니다. 이 작업이 필요 하지 않을 하드웨어의 물리적 조건에 기반 합니다.

   > [!NOTE]  
   > 어떤 경우 든 하나의 노드만 사용 하지 않도록 설정 및 수는 S2D를 중단 하지 않고 동시에 전원 꺼짐 (저장소 공간 다이렉트).

3. 배율 단위 노드 유지 관리 모드에서를 한 후 사용 합니다 [중지](azure-stack-node-actions.md#stop) 작업. 이 작업이 필요 하지 않을 하드웨어의 물리적 조건에 기반 합니다.

   > [!NOTE]  
   > 전원 끄기 작동 하지 않는 드물지만, 베이스 보드 관리 컨트롤러 (BMC) 웹 인터페이스를 대신 사용 합니다.

4. 물리적 컴퓨터를 대체 합니다. 일반적으로 OEM 하드웨어 공급 업체에 문의 하면 됩니다.
5. 사용 된 [복구](azure-stack-node-actions.md#repair) 배율 단위에 새 물리적 컴퓨터를 추가할 작업입니다.
6. 권한 있는 끝점을 사용 하 여 [가상 디스크 복구 상태를 확인할](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair)합니다. 새 데이터 드라이브를 사용 하 여 전체 저장소 복구 작업을 시스템 부하에 따라 여러 시간이 걸릴 수 있습니다 및 공간을 사용 합니다.
7. 복구 작업이 완료 된 후 모든 활성 경고가 자동으로 종료 된의 유효성을 검사 합니다.

## <a name="next-steps"></a>다음 단계

- 시스템 전원이 켜져 있지만 실제 디스크를 교체 하는 방법에 대 한 내용은 [디스크를 교체](azure-stack-replace-disk.md)합니다. 
- 시스템 전원이 필요로 하는 하드웨어 구성 요소를 교체 하는 방법에 대 한 내용은 [하드웨어 구성 요소를 교체](azure-stack-replace-component.md)합니다.
