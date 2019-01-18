---
title: Azure Stack에서 크기 조정 단위 노드 작업 | Microsoft Docs
description: 노드 상태 보기 및 사용 된 전원 전원 켜기, 해제 및 Azure Stack 통합 시스템에서 노드 작업을 다시 시작 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 1/14/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 170cf458496d91a28260296e2aba803d76fbc06b
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54388824"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Azure Stack에서 크기 조정 단위 노드 작업

*적용 대상: Azure Stack 통합 시스템*

이 문서에서는 배율 단위의 상태를 확인 하는 방법을 설명 합니다. 단위의 노드를 볼 수 있습니다. 있습니다 수 전원에 전원 같은 노드 작업 실행, 종료, 드레이닝, 다시 시작 및 복구 합니다. 일반적으로 부분 또는 노드를 복구 하는 데 필드 교체 하는 동안 이러한 노드의 작업을 사용 합니다.

> [!Important]  
> 이 문서에서 설명 하는 모든 노드 작업 한 번에 하나의 노드를 대상으로 해야 합니다.

## <a name="view-the-node-status"></a>노드 상태 보기

관리자 포털의 배율 단위와 연결 된 해당 노드의 상태를 볼 수 있습니다.

배율 단위의 상태를 보려면:

1. 에 **하위 지역 관리** 타일에서 지역을 선택 합니다.
2. 왼쪽 아래 **인프라 리소스**를 선택 **배율 단위**합니다.
3. 결과에서 배율 단위를 선택 합니다.
4. 왼쪽 아래 **일반**를 선택 **노드**합니다.

  다음 정보를 보려면

  - 개별 노드 목록
  - 작업 상태 (아래 목록 참조)
  - 전원 상태 (실행 중 또는 중지)
  - 서버 모델
  - 베이스 보드 관리 컨트롤러 (BMC)의 IP 주소
  - 총 코어 수
  - 총 메모리 양

![배율 단위는 상태](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>노드 작동 상태

| 상태 | 설명 |
|----------------------|-------------------------------------------------------------------|
| 실행 중 | 노드 배율 단위에 적극적으로 참여 합니다. |
| 중지됨 | 노드를 사용할 수 없는 경우 |
| 추가하는 중 | 노드는 배율 단위에 추가 되 고 적극적으로 됩니다. |
| 복구 중 | 노드는 적극적으로 복구 하는 중입니다. |
| 유지 관리 | 노드 일시 중지 되 고 활성 사용자 워크 로드가 없습니다 실행입니다. |
| 업데이트 관리 필요 | 노드를 복구 해야 하는 오류가 감지 되었습니다. |

## <a name="scale-unit-node-actions"></a>크기 조정 단위 노드 작업

배율 단위 노드에 대 한 정보를 볼 때와 같은 노드 작업을 수행할 수도 있습니다.
 - 시작 및 중지 (에 따라 현재 전원 상태)
 - 사용 하지 않도록 설정 하 고 (작업 상태)에 따라 다시 시작
 - 복구
 - Shutdown

노드의 작동 상태를 사용할 수 있는 옵션을 결정 합니다.

Azure Stack PowerShell 모듈을 설치 해야 합니다. 이러한 cmdlet에는 **Azs.Fabric.Admin** 모듈입니다. 를 설치 하거나 Azure Stack 용 PowerShell의 설치를 확인 하려면 참조 [Azure Stack 용 PowerShell 설치](azure-stack-powershell-install.md)합니다.

## <a name="stop"></a>중지

합니다 **중지** 작업 노드를 해제 합니다. 전원 단추를 누르면 처럼 동일한 됩니다. 운영 체제 종료 신호를 보내지 않습니다. 계획 된 중지 작업에 대 한 종료 작업을 먼저 시도 항상. 

이 작업은 일반적으로 노드 응답이 없는 상태 이며 더 이상 요청에 응답할 때 사용 됩니다.

중지 작업을 실행 하려면 관리자 권한 PowerShell 프롬프트를 열고 다음 cmdlet을 실행 합니다.

```PowerShell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

드문 경우에 중지 작업 작동 하지 않습니다, 작업을 다시 시도 및를 두 번째로 실패 하는 경우 BMC 웹 인터페이스를 대신 사용 합니다.

자세한 내용은 [중지 AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/stop-azsscaleunitnode)합니다.

## <a name="start"></a>시작

합니다 **시작** 노드에서 작업을 설정 합니다. 전원 단추를 누르면 처럼 동일한 됩니다. 
 
시작 작업을 실행 하려면 관리자 권한 PowerShell 프롬프트를 열고 다음 cmdlet을 실행 합니다.

```PowerShell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

드문 경우에 시작 작업 작동 하지 않습니다, 작업을 다시 시도 및를 두 번째로 실패 하는 경우 BMC 웹 인터페이스를 대신 사용 합니다.

자세한 내용은 [시작 AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/start-azsscaleunitnode)합니다.

## <a name="drain"></a>드레이닝

합니다 **드레이닝** 작업이 해당 특정 배율 단위의 나머지 노드로 모든 활성 워크 로드를 이동 합니다.

이 작업은 일반적으로 전체 노드를 교체와 같은 부분 필드 교체 하는 동안 사용 됩니다.

> [!Important]
> 노드 드레이닝 작업을 사용 하 여 사용자 알렸습니다, 계획 된 유지 관리 기간 동안 있는지 확인 합니다. 조건에 따라 활성 작업 중단이 발생할 수 있습니다.

드레이닝 작업을 실행 하려면 관리자 권한 PowerShell 프롬프트를 열고 다음 cmdlet을 실행 합니다.

```PowerShell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

자세한 내용은 [사용 안 함-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/disable-azsscaleunitnode)합니다.

## <a name="resume"></a>다시 시작

합니다 **재개** 작업을 사용할 수 없는 노드를 다시 시작 하 고 워크 로드 배치에 대 한 활성 표시 합니다. 노드에서 실행 중이 던 이전 워크 로드가 장애 복구 수행 합니다. (노드 드레이닝 작업을 사용 하는 경우 해야 전원 끄기 합니다. 켜면 노드 다시, 워크 로드 배치에 대 한 활성으로 표시 되지 않습니다. 준비가 되 면 사용 해야 다시 시작 작업 활성으로 노드를 표시 합니다.)

다시 시작 작업을 실행 하려면 관리자 권한 PowerShell 프롬프트를 열고 다음 cmdlet을 실행 합니다.

```PowerShell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

자세한 내용은 [Enable AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode)합니다.

## <a name="repair"></a>복구

합니다 **복구** 작업 노드를 복구 합니다. 다음 시나리오 중 하나에 사용 합니다.
 - 전체 노드 교체 (유무와 관계 없이 새 데이터 디스크)
 - 하드웨어 구성 요소 오류 후 (필드 교체 장치 (FRU) 설명서에서 advise) 하는 경우 대체 합니다.

> [!Important]  
> 노드 또는 개별 하드웨어 구성 요소를 교체 해야 하는 경우 정확한 단계는 OEM 하드웨어 공급 업체의 FRU 설명서를 참조 하십시오. FRU 설명서는 하드웨어 구성 요소 교체 후 복구 작업을 실행 해야 하는지 여부를 지정 합니다. 

복구 작업을 실행 하는 경우 BMC IP 주소를 지정 해야 합니다. 

복구 작업을 실행 하려면 관리자 권한 PowerShell 프롬프트를 열고 다음 cmdlet을 실행 합니다.

  ````PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ````

## <a name="next-steps"></a>다음 단계

Azure Stack 패브릭 관리자 모듈에 대 한 자세한 내용은 참조 하세요 [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0)합니다.
