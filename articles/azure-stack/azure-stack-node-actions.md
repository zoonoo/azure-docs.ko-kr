---
title: Azure Stack에서 크기 조정 단위 노드 작업 | Microsoft Docs
description: 노드 상태를 보고 하 고는 전원 켜기, 전원 끄기, 드레이닝을 사용 하는 Azure Stack 통합 시스템에 대 한 노드 작업을 다시 시작 하는 방법을 알아봅니다.
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
ms.date: 10/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: a792bc083c3a2c78b24d5895c34420b86b0863bb
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959770"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Azure Stack에서 크기 조정 단위 노드 작업

*적용 대상: Azure Stack 통합 시스템*

이 문서에서는 배율 단위와 해당 관련된 노드에의 상태를 보는 방법 및 사용 가능한 노드 작업을 사용 하는 방법을 설명 합니다. 전원 켜기, 전원 끄기 포함 하 드레이닝 다시 시작 및 복구 하는 노드 작업 합니다. 일반적으로 부분 또는 노드 복구 시나리오에 대 한 필드 교체 하는 동안 이러한 노드의 작업을 사용합니다.

> [!Important]  
> 이 문서에서 설명 하는 모든 노드 작업은 한 번에 대상 하나의 노드만 해야 합니다.


## <a name="view-the-node-status"></a>노드 상태 보기

관리자 포털의 배율 단위와 연결 된 해당 노드의 상태를 쉽게 볼 수 있습니다.

배율 단위의 상태를 보려면:

1. 에 **하위 지역 관리** 타일에서 지역을 선택 합니다.
2. 왼쪽 아래 **인프라 리소스**를 선택 **배율 단위**합니다.
3. 결과에서 배율 단위를 선택 합니다.
 
여기에서 다음 정보를 볼 수 있습니다.

- 지역 이름입니다. 지역 이름을 사용 하 여 참조 됩니다 **-위치** PowerShell 모듈에서.
- 시스템의 종류
- 총 논리적 코어
- 총 메모리
- 개별 노드 및 해당 상태 목록 어느 **실행** 또는 **중지**

![각 노드에 대 한 실행 상태를 표시 하는 크기 조정 단위 타일](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-node-information"></a>노드 정보 보기

개별 노드를 선택 하면 다음 정보를 볼 수 있습니다.

- 지역 이름
- 서버 모델
- 베이스 보드 관리 컨트롤러 (BMC)의 IP 주소
- 작동 상태
- 총 코어 수
- 총 메모리 양
 
![각 노드에 대 한 실행 상태를 표시 하는 크기 조정 단위 타일](media/azure-stack-node-actions/NodeActions.PNG)

또한 여기에서 크기 조정 단위 노드 작업을 수행할 수 있습니다.

## <a name="scale-unit-node-actions"></a>크기 조정 단위 노드 작업

배율 단위 노드에 대 한 정보를 볼 때와 같은 노드 작업을 수행할 수도 있습니다.

- 드레이닝 및 다시 시작
- 복구

노드의 작동 상태를 사용할 수 있는 옵션을 결정 합니다.

### <a name="power-off"></a>전원 끄기

합니다 **전원을 끄고** 작업 노드를 해제 합니다. 전원 단추를 누르면 처럼 동일한 됩니다. 만약 **되지** 운영 체제 종료 신호를 보내기. 계획 된 작업 끄지, 먼저 확장 단위 노드를 드레이닝 해야 합니다.

이 작업은 일반적으로 노드 응답이 없는 상태 이며 더 이상 요청에 응답할 때 사용 됩니다.

> [!Important] 
> 이 기능은 PowerShell을 통해 사용할 수만 있습니다. 나중에 다시 Azure Stack 관리자 포털에서 제공 됩니다.


전원 끄기 PowerShell 통해를 실행 합니다.

````PowerShell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```` 

전원 끄기 작동 하지 않는 드물지만, BMC 웹 인터페이스를 대신 사용 합니다.

### <a name="power-on"></a>전원 켜기

합니다 **전원 켜기** 노드에서 작업을 설정 합니다. 전원 단추를 누르면 처럼 동일한 됩니다. 

> [!Important] 
> 이 기능은 PowerShell을 통해 사용할 수만 있습니다. 나중에 다시 Azure Stack 관리자 포털에서 제공 됩니다.

PowerShell 통해 작업에서 기능을 실행:

````PowerShell
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
````

작업의 전원 작동 하지 않는 드물지만, BMC 웹 인터페이스를 대신 사용 합니다.

### <a name="drain"></a>드레이닝

합니다 **드레이닝** 작업이 해당 특정 배율 단위의 나머지 노드 간에 배포 하 여 모든 활성 작업 evacuates 합니다.

이 작업은 일반적으로 전체 노드를 교체와 같은 부분 필드 교체 하는 동안 사용 됩니다.

> [!IMPORTANT]  
> 사용자 알렸습니다, 계획 된 유지 관리 기간 중에 노드를 드레이닝 수 있는지 확인 합니다. 조건에 따라 활성 작업 중단이 발생할 수 있습니다.

PowerShell 통해 드레이닝 작업 실행:

  ````PowerShell
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>다시 시작

합니다 **Resume** 작업 배출 된 노드를 다시 시작 하 고 워크 로드 배치에 대 한 활성 표시 합니다. 노드에서 실행 중이 던 이전 워크 로드가 장애 복구 수행 합니다. (노드 및 다음 전원 끄기, 노드가 다시 전원을 경우 밖에 없었고 경우 표시 되어 있지 않습니다 워크 로드 배치에 대 한 활성으로 합니다. 준비가 되 면 사용 해야 다시 시작 작업 활성으로 노드를 표시 합니다.)

PowerShell 통해 다시 시작 작업을 실행 합니다.

  ````PowerShell
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>복구

합니다 **복구** 작업 노드를 복구 합니다. 다음 시나리오 중 하나에 사용 합니다.

- 전체 노드 교체 (유무와 관계 없이 새 데이터 디스크)
- 하드웨어 구성 요소 오류 후 (필드 교체 장치 (FRU) 설명서에서 advise) 하는 경우 대체 합니다.

> [!IMPORTANT]  
> 노드 또는 개별 하드웨어 구성 요소를 교체 해야 하는 경우 정확한 단계는 OEM 하드웨어 공급 업체의 FRU 설명서를 참조 하십시오. FRU 설명서는 하드웨어 구성 요소 교체 후 복구 작업을 실행 해야 하는지 여부를 지정 합니다.  

복구 작업을 실행 하는 경우 BMC IP 주소를 지정 해야 합니다. 

PowerShell 통해 복구 작업을 실행 합니다.

  ````PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ````

## <a name="next-steps"></a>다음 단계

Azure Stack 패브릭 관리자 모듈에 대 한 자세한 내용은 참조 하세요 [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.5.0)합니다.
