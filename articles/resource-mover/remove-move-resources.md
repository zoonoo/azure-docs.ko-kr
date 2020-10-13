---
title: Azure 리소스 이동의 이동 컬렉션에서 리소스 제거
description: Azure 리소스 이동의 이동 컬렉션에서 리소스를 제거 하는 방법에 대해 알아봅니다.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 241ccbda67f7a2518d0c44a0d362673922ad4284
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89653128"
---
# <a name="remove-resources-from-a-move-collection"></a>이동 컬렉션에서 리소스 제거

이 문서에서는 [Azure 리소스](overview.md)이동의 이동 컬렉션에서 리소스를 제거 하는 방법을 설명 합니다. 컬렉션 이동은 azure 지역 간에 Azure 리소스를 이동할 때 사용 됩니다.

## <a name="remove-a-resource-portal"></a>리소스 제거 (포털)

리소스 이동 기 포털에서 다음과 같이를 제거 합니다.

1. **여러 지역**에서 컬렉션에서 제거 하려는 리소스를 선택 > **제거**합니다.

    ![제거 하도록 선택 하는 단추](./media/remove-move-resources/portal-select-resources.png)

1. **리소스 제거**에서 **제거**를 클릭 합니다.

    ![이동 컬렉션에서 리소스를 제거 하도록 선택 하는 단추](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-resource-powershell"></a>리소스 제거 (PowerShell)

다음과 같이 PowerShell을 사용 하 여 컬렉션에서 리소스 (이 예제에서는 PSDemoVM 컴퓨터)를 제거 합니다.

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus - Name PSDemoVM
```
**예상 출력** 
 ![ 이동 컬렉션에서 리소스를 제거한 후 출력 텍스트](./media/remove-move-resources/remove-resource.png)

## <a name="remove-a-collection-powershell"></a>컬렉션 제거 (PowerShell)

다음과 같이 PowerShell을 사용 하 여 전체 이동 컬렉션을 제거 합니다.

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus 
```
**예상 출력** 
 ![ 이동 컬렉션을 제거한 후 출력 텍스트](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>제거 후 VM 리소스 상태

이동 컬렉션에서 VM 리소스를 제거할 때 발생 하는 작업은 테이블에 요약 된 대로 리소스 상태에 따라 달라 집니다.

###  <a name="remove-vm-state"></a>VM 상태 제거
**리소스 상태** | **VM** | **네트워킹**
--- | --- | --- 
**컬렉션 이동에 추가 됨** | 컬렉션 이동에서 삭제 합니다. | 컬렉션 이동에서 삭제 합니다. 
**종속성 해결 됨/준비 보류 중** | 컬렉션 이동에서 삭제  | 컬렉션 이동에서 삭제 합니다. 
**준비 중**<br/> (또는 진행 중인 다른 모든 상태) | 오류가 발생 하 여 삭제 작업이 실패 합니다.  | 오류가 발생 하 여 삭제 작업이 실패 합니다.
**준비 실패** | 이동 컬렉션에서 삭제 합니다.<br/>복제본 디스크를 포함 하 여 대상 지역에서 만든 모든 항목을 삭제 합니다. <br/><br/> 이동 중에 만들어진 인프라 리소스는 수동으로 삭제 해야 합니다. | 이동 컬렉션에서 삭제 합니다.  
**이동 시작 보류 중** | 컬렉션 이동에서 삭제 합니다.<br/><br/> VM, 복제본 디스크 등을 포함 하 여 대상 지역에서 만든 모든 항목을 삭제 합니다.  <br/><br/> 이동 중에 만들어진 인프라 리소스는 수동으로 삭제 해야 합니다. | 컬렉션 이동에서 삭제 합니다.
**이동 시작 실패** | 컬렉션 이동에서 삭제 합니다.<br/><br/> VM, 복제본 디스크 등을 포함 하 여 대상 지역에서 만든 모든 항목을 삭제 합니다.  <br/><br/> 이동 중에 만들어진 인프라 리소스는 수동으로 삭제 해야 합니다. | 컬렉션 이동에서 삭제 합니다.
**커밋 보류 중** | 대상 리소스가 먼저 삭제 되도록 이동을 취소 하는 것이 좋습니다.<br/><br/> 리소스가 **이동 보류 중** 상태로 다시 이동 하 여 해당 위치에서 계속할 수 있습니다. | 대상 리소스가 먼저 삭제 되도록 이동을 취소 하는 것이 좋습니다.<br/><br/> 리소스가 **이동 보류 중** 상태로 다시 이동 하 여 해당 위치에서 계속할 수 있습니다. 
**커밋 실패** | 대상 리소스를 먼저 삭제 하려면를 삭제 하는 것이 좋습니다.<br/><br/> 리소스가 **이동 보류 중** 상태로 다시 이동 하 여 해당 위치에서 계속할 수 있습니다. | 대상 리소스가 먼저 삭제 되도록 이동을 취소 하는 것이 좋습니다.<br/><br/> 리소스가 **이동 보류 중** 상태로 다시 이동 하 여 해당 위치에서 계속할 수 있습니다.
**삭제 완료** | 리소스가 **이동 보류 중** 상태로 돌아갑니다.<br/><br/> 대상 VM, 복제본 디스크, 자격 증명 모음 등에 생성 된 항목을 비롯 하 여 이동 컬렉션에서 삭제 됩니다.  <br/><br/> 이동 중에 만들어진 인프라 리소스는 수동으로 삭제 해야 합니다. <br/><br/> 이동 중에 만들어진 인프라 리소스는 수동으로 삭제 해야 합니다. |  리소스가 **이동 보류 중** 상태로 돌아갑니다.<br/><br/> 이동 컬렉션에서 삭제 됩니다.
**삭제 실패** | 대상 리소스를 먼저 삭제 하도록 이동을 취소 하는 것이 좋습니다.<br/><br/> 그러면 리소스가 **이동 보류 중** 상태로 전환 되 고 해당 위치에서 계속할 수 있습니다. | 대상 리소스를 먼저 삭제 하도록 이동을 취소 하는 것이 좋습니다.<br/><br/> 그러면 리소스가 **이동 보류 중** 상태로 전환 되 고 해당 위치에서 계속할 수 있습니다.
**원본 삭제 보류 중** | 이동 컬렉션에서 삭제 되었습니다.<br/><br/> 대상 지역에 생성 된 항목은 삭제 되지 않습니다.  | 이동 컬렉션에서 삭제 되었습니다.<br/><br/> 대상 지역에 생성 된 항목은 삭제 되지 않습니다.
**원본 삭제 실패** | 이동 컬렉션에서 삭제 되었습니다.<br/><br/> 대상 지역에 생성 된 항목은 삭제 되지 않습니다. | 이동 컬렉션에서 삭제 되었습니다.<br/><br/> 대상 지역에 생성 된 항목은 삭제 되지 않습니다.

## <a name="sql-resource-state-after-removing"></a>제거한 후 SQL 리소스 상태

이동 컬렉션에서 Azure SQL 리소스를 제거 하면 수행 되는 작업은 테이블에 요약 된 대로 리소스 상태에 따라 달라 집니다.

**리소스 상태** | **SQL** 
--- | --- 
**컬렉션 이동에 추가 됨** | 컬렉션 이동에서 삭제 합니다. 
**종속성 해결 됨/준비 보류 중** | 컬렉션 이동에서 삭제 
**준비 중**<br/> (또는 진행 중인 다른 모든 상태)  | 오류가 발생 하 여 삭제 작업이 실패 합니다. 
**준비 실패** | 컬렉션 이동에서 삭제<br/><br/>대상 지역에서 만든 모든 항목을 삭제 합니다. 
**이동 시작 보류 중** |  컬렉션 이동에서 삭제<br/><br/>대상 지역에서 만든 모든 항목을 삭제 합니다. 이 시점에 SQL 데이터베이스가 있으며 삭제 됩니다. 
**이동 시작 실패** | 컬렉션 이동에서 삭제<br/><br/>대상 지역에서 만든 모든 항목을 삭제 합니다. SQL 데이터베이스는이 시점에 존재 하며 삭제 해야 합니다. 
**커밋 보류 중** | 대상 리소스가 먼저 삭제 되도록 이동을 취소 하는 것이 좋습니다.<br/><br/> 리소스가 **이동 보류 중** 상태로 다시 이동 하 여 해당 위치에서 계속할 수 있습니다.
**커밋 실패** | 대상 리소스가 먼저 삭제 되도록 이동을 취소 하는 것이 좋습니다.<br/><br/> 리소스가 **이동 보류 중** 상태로 다시 이동 하 여 해당 위치에서 계속할 수 있습니다. 
**삭제 완료** |  리소스가 **이동 보류 중** 상태로 돌아갑니다.<br/><br/> SQL 데이터베이스를 포함 하 여 대상에서 생성 된 것과 함께 이동 컬렉션에서 삭제 됩니다. 
**삭제 실패** | 대상 리소스를 먼저 삭제 하도록 이동을 취소 하는 것이 좋습니다.<br/><br/> 그러면 리소스가 **이동 보류 중** 상태로 전환 되 고 해당 위치에서 계속할 수 있습니다. 
**원본 삭제 보류 중** | 이동 컬렉션에서 삭제 되었습니다.<br/><br/> 대상 지역에 생성 된 항목은 삭제 되지 않습니다. 
**원본 삭제 실패** | 이동 컬렉션에서 삭제 되었습니다.<br/><br/> 대상 지역에 생성 된 항목은 삭제 되지 않습니다. 

## <a name="next-steps"></a>다음 단계

리소스 이동 기를 사용 하 여 [VM을](tutorial-move-region-virtual-machines.md) 다른 지역으로 이동 해 보세요.