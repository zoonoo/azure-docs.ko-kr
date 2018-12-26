---
title: Azure Stack 배율 단위 노드에 하드웨어 구성 요소 교체 | Microsoft Docs
description: Azure Stack 통합 시스템 하드웨어 구성 요소를 교체 하는 방법에 알아봅니다.
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
ms.date: 09/10/2018
ms.author: mabrigg
ms.openlocfilehash: df9470813f3f9c3bff58882879c06e7b7b0fc15b
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379607"
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>Azure Stack 배율 단위 노드에 하드웨어 구성 요소 교체

*적용 대상: Azure Stack 통합 시스템*

이 문서에서는 비 핫 스왑 가능한는 하드웨어 구성 요소를 대체 하는 일반 프로세스를 설명 합니다. 원래 장비 제조업체 (OEM) 하드웨어 공급 업체를 기반으로 단계가 달라 집니다 실제 대체 합니다. Azure Stack 통합 시스템에 관련 된 자세한 단계에 대 한 공급 업체의 필드 교체 장치 (FRU) 설명서를 참조 하세요.

비 핫 스왑 가능한 구성 요소는 다음과 같습니다.

- CPU*
- 메모리 *
- 획득 한 마더보드/베이스 보드 관리 컨트롤러 (BMC) / 비디오 카드
- 디스크 컨트롤러/호스트 버스 어댑터 (HBA) / 백플레인
- 네트워크 어댑터 (NIC)
- 운영 체제 디스크 *
- 데이터 드라이브 (핫 스왑, 예를 들어 pci-e 추가 카드를 지원 하지 않는 드라이브) *

* 이러한 구성 요소는 핫 스왑을 지원할 수 있습니다 하지만 공급 업체 구현에 따라 달라질 수 있습니다. 자세한 단계에 대 한 OEM 공급 업체의 FRU 설명서를 참조 하세요.

다음 흐름 다이어그램에서는 비 핫 스왑 가능한 하드웨어 구성 요소를 교체 하는 일반 FRU 프로세스를 보여 줍니다.

![구성 요소 대체 흐름을 보여 주는 흐름 다이어그램](media/azure-stack-replace-component/replacecomponentflow.PNG)

*이 작업이 필요 하지 않을 하드웨어의 물리적 조건에 기반 합니다.

* * 지원 계약을 기반으로 한 OEM 하드웨어 공급 업체에 문의 구성 요소 교체 및 펌웨어 다를 수 업데이트를 수행 하는 여부

## <a name="review-alert-information"></a>경고 정보를 검토 합니다.

Azure Stack의 상태 및 모니터링 시스템 저장소 공간 다이렉트에 의해 제어 되는 데이터 드라이브 및 네트워크 어댑터의 상태를 추적 합니다. 다른 하드웨어 구성 요소를 추적 하지 않습니다. 다른 모든 하드웨어 구성 요소를 사용 하는 경고는 모니터링 하드웨어 수명 주기 호스트에서 실행 되는 솔루션 공급 업체 관련 하드웨어에서 발생 합니다.  

## <a name="component-replacement-process"></a>구성 요소 교체 프로세스

다음 구성 요소 교체 프로세스의 대략적인 개요를 제공합니다. OEM 제공 FRU 설명서를 참조 하지 않고 다음이 단계를 수행 하지 마세요.

1. 사용 된 [드레이닝](azure-stack-node-actions.md#scale-unit-node-actions) 배율 단위 노드 유지 관리 모드로 설정 하는 작업입니다. 이 작업이 필요 하지 않을 하드웨어의 물리적 조건에 기반 합니다.

   > [!NOTE]
   > 어떤 경우 든 하나의 노드만 드레이닝 및 수는 S2D를 중단 하지 않고 동시에 전원 꺼짐 (저장소 공간 다이렉트).

2. 배율 단위 노드 유지 관리 모드를 사용 하 여 합니다 [전원을 끄고](azure-stack-node-actions.md#scale-unit-node-actions) 작업 합니다. 이 작업이 필요 하지 않을 하드웨어의 물리적 조건에 기반 합니다.

   > [!NOTE]
   > 전원 끄기 작동 하지 않는 드물지만, 베이스 보드 관리 컨트롤러 (BMC) 웹 인터페이스를 대신 사용 합니다.

3. 손상 된 하드웨어 구성 요소를 대체 합니다. OEM 하드웨어 공급 업체의 구성 요소 교체를 수행 하는지 여부를 지원 계약에 따라 달라질 수 있습니다.  
4. 펌웨어를 업데이트 합니다. 하드웨어 수명 주기 호스트를 사용 하 여 대체 하드웨어 구성 요소에 승인 된 펌웨어 수준이 적용 되도록 하 고 공급 업체별 펌웨어 업데이트 프로세스를 수행 합니다. OEM 하드웨어 공급 업체가이 단계를 수행 하는지 여부를 지원 계약에 따라 달라질 수 있습니다.  
5. 사용 된 [복구](azure-stack-node-actions.md#scale-unit-node-actions) 배율 단위를 배율 단위 노드를 다시 가져오는 작업입니다.
6. 권한 있는 끝점을 사용 하 여 [가상 디스크 복구 상태를 확인할](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair)합니다. 새 데이터 드라이브를 사용 하 여 전체 저장소 복구 작업을 시스템 부하에 따라 여러 시간이 걸릴 수 있습니다 및 공간을 사용 합니다.
7. 복구 작업이 완료 된 후 모든 활성 경고가 자동으로 종료 된의 유효성을 검사 합니다.

## <a name="next-steps"></a>다음 단계

- 핫 스왑 가능한 실제 디스크를 교체 하는 방법에 대 한 내용은 [디스크를 교체](azure-stack-replace-disk.md)합니다.
- 실제 노드를 교체 하는 방법에 대 한 내용은 [배율 단위 노드를 대체](azure-stack-replace-node.md)합니다.
