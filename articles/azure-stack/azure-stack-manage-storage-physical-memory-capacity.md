---
title: Azure Stack에 대 한 실제 메모리 용량 관리 | Microsoft Docs
description: 모니터링 하 고 Azure Stack에 대 한 사용 가능한 저장소 공간을 관리 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/28/2018
ms.author: mabrigg
ms.reviewer: Thomas.Roettinger
ms.openlocfilehash: a914d20f61b5b632e792ca29f6c201964db4a203
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452142"
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Azure Stack에 대 한 실제 메모리 용량 관리

*적용 대상: Azure Stack 통합 시스템*

Azure Stack에 대 한 사용 가능한 총 메모리 용량을 늘리려면 추가 메모리를 추가할 수 있습니다. Azure Stack에서 물리적 서버는 라고도 하는 *확장 단위 노드에*합니다. 배율 단위는 모든 노드를 단일 배율 단위는 멤버에는 동일한 양의 메모리 있어야 합니다.

> [!note]  
> 계속 하기 전에 있는지 하드웨어 제조업체의 설명서를 참조 하십시오.는 제조업체는 실제 메모리 업그레이드를 지원 합니다. OEM 하드웨어 공급 업체 지원 계약이 공급 업체 물리적 서버 랙에 배치 및 장치 펌웨어 업데이트를 수행 하는 필요할 수 있습니다.

다음 흐름 다이어그램에서는 각 배율 단위 노드로 메모리를 추가 하는 일반적인 프로세스를 보여 줍니다.

![각 배율 단위 노드로 메모리를 추가 합니다.](media\azure-stack-manage-storage-physical-capacity\process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>기존 노드에 메모리를 추가 합니다.
다음 단계를 추가 메모리가 프로세스의 대략적인 개요를 제공합니다. 

> [!Warning]  
OEM에서 제공한 설명서를 참조 하지 않고 다음이 단계를 수행 하지 마세요.

> [!Warning]  
전체 배율 단위를 종료 해야 합니다 롤링 메모리 업그레이드를 지원 되지 않습니다.

1. 에 설명 된 단계를 사용 하 여 Azure Stack을 중지 합니다 [Start 및 stop Azure Stack](azure-stack-start-and-stop.md) 문서.
2. 하드웨어 제조업체의 설명서를 사용 하 여 각 물리적 컴퓨터의 메모리를 업그레이드 합니다.
3. Azure Stack의 단계를 사용 하 여 시작 합니다 [Start 및 stop Azure Stack](azure-stack-start-and-stop.md) 문서.

## <a name="next-steps"></a>다음 단계

 - 참조 찾기, 복구 및 비즈니스 요구에 따라 저장소 용량을 회수 하는 Azure Stack에서 저장소 계정을 관리 하는 방법에 알아보려면 [Azure Stack의 storage 계정 관리](azure-stack-manage-storage-accounts.md)합니다.
 - Azure Stack 클라우드 운영자는 모니터링 하 고 Azure Stack 배포의 저장소 용량을 관리에 대해 알아보려면 [Azure Stack에 대 한 저장소 용량을 관리할](azure-stack-manage-storage-shares.md)합니다. 
