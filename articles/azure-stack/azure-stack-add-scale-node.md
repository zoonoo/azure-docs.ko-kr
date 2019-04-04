---
title: Azure Stack 추가 노드 크기 조정 | Microsoft Docs
description: Azure Stack의 배율 단위에 노드를 추가 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 17da540bd6077b8e045f125fd3cf13dc0e043000
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882041"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack"></a>Azure Stack에서 확장할 단위 노드를 추가 합니다.

Azure Stack 운영자를 추가 하는 물리적 컴퓨터를 추가 하 여 기존 확장 단위의 전체 용량을 늘릴 수 있습니다. 물리적 컴퓨터는 배율 단위 노드 라고도 합니다. 추가한 새 확장 단위 노드에 각 CPU 종류, 메모리 및 디스크 번호 및 배율 단위에 이미 있는 노드에 대 한 크기의 유형이 같아야 합니다.

배율 단위 노드를 추가 하려면 Azure Stack에서 작동 하 고 하드웨어 장비 제조업체 (OEM)에서 도구를 실행 합니다. 새 물리적 컴퓨터에 기존 노드와 동일한 펌웨어 수준과 일치 하는지 확인 하려면 하드웨어 수명 주기 호스트 (HLH)에서 OEM 도구를 실행 합니다.

다음 흐름 다이어그램에서는 크기 조정 단위 노드를 추가 하는 일반적인 프로세스를 보여 줍니다.

![배율 단위 흐름 추가](media/azure-stack-add-scale-node/add-node-flow.png) &#42; *OEM 하드웨어 공급 업체는 물리적 서버 랙에 배치를 시행 하 고 펌웨어를 업데이트 하는 여부를 지원 계약에 따라 달라 집니다.*

새 노드를 추가 하는 작업은 몇 시간 또는 완료 일 걸릴 수 있습니다.

> [!Note]  
> 하지 않도록 다음 작업 중 하나가 추가 확장 단위 노드 작업이 이미 진행에서 하는 동안:
>
>  - Azure Stack을 업데이트 합니다.
>  - 인증서 회전
>  - Azure Stack 중지
>  - 복구 배율 단위 노드


## <a name="add-scale-unit-nodes"></a>노드 크기 조정 단위를 추가 합니다.

다음 단계는 노드를 추가 하는 방법에 개략적 합니다. OEM 제공 용량 확장 설명서를 첫 번째 참조 하지 않고 다음이 단계를 수행 하지 마십시오.

1. 새 물리적 서버 랙에 배치 하 고 적절 하 게 케이블로 연결 합니다. 
2. 물리적 스위치 포트를 사용 하도록 설정 하 고 해당 하는 경우 액세스 제어 목록 (Acl)를 조정 합니다.
3. 베이스 보드 관리 컨트롤러 (BMC)에서 올바른 IP 주소를 구성 하 고 OEM 제공 설명서 당 BIOS 설정을 모두 적용 합니다.
4. 현재 펌웨어 기준선을는 HLH에서 실행 되는 하드웨어 제조업체에서 제공 하는 도구를 사용 하 여 모든 구성 요소에 적용 됩니다.
5. Azure Stack 관리 포털에서 노드 추가 작업을 실행 합니다.
6. 노드 추가 작업에 성공 했는지 확인 합니다. 이렇게 하려면 다음을 확인 합니다 [ **상태** 배율 단위의](#monitor-add-node-operations)합니다. 

## <a name="add-the-node"></a>노드 추가

새 노드를 추가 하는 관리 포털 또는 PowerShell을 사용할 수 있습니다. 노드 추가 작업 먼저 사용 가능한 계산 용량으로 새 규모 단위 노드를 추가 하 고 저장소 용량을 자동으로 확장 합니다. Azure Stack은 하이퍼 수렴 형 시스템 이므로 용량을 자동으로 확장 위치 *계산* 하 고 *저장소* 함께 확장 합니다.

### <a name="use-the-admin-portal"></a>관리자 포털 사용

1. Azure Stack 운영자로 서 Azure Stack 관리 포털에 로그인 합니다.
2. 이동할 **+ 리소스 만들기** > **용량** > **배율 단위 노드**합니다.
   ![배율 단위 노드](media/azure-stack-add-scale-node/select-node1.png)
3. 에 **노드 추가** 창을 *지역*를 선택한 후는 *배율 단위* 노드를 추가 하려면. 도 지정 합니다 *BMC IP 주소* 추가 하는 배율 단위 노드에 대 한 합니다. 만 한 번에 하나의 노드를 추가할 수 있습니다.
   ![노드 자세히 보기를 추가 합니다.](media/azure-stack-add-scale-node/select-node2.png)
 

### <a name="use-powershell"></a>PowerShell 사용

사용 된 **새로 만들기-AzsScaleUnitNodeObject** 노드를 추가 하는 cmdlet.  

다음 샘플 PowerShell 스크립트 중 하나를 사용 하기 전에 값을 바꿉니다 *노드 이름* 하 고 *IP 주소* Azure Stack 환경에서 값을 사용 하 여 합니다.

  > [!Note]  
  > 노드 이름을 지정할 때 보다 작거나 15 자로 이름을 유지 해야 합니다. 공백을 포함 하거나 다음 문자를 포함 하는 이름을 사용할 수 없습니다: `\`, `/`, `:`, `*`를 `?`를 `"`, `<`를 `>`, `|`, `\`, `~`, `!`, `@`, `#`, `$`, `%`, `^`, `&`, `(`, `)`, `{`, `}`, `_`.

**노드를 추가 합니다.**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

## <a name="monitor-add-node-operations"></a>모니터 추가 노드 작업 
노드 추가 작업의 상태를 가져오려면 관리자 포털 또는 PowerShell을 사용할 수 있습니다. 추가 노드 작업 완료 하는 일에 몇 시간이 걸릴 수 있습니다.

### <a name="use-the-admin-portal"></a>관리자 포털 사용 
새 노드 추가 모니터링 하려면 관리 포털에서의 배율 단위를 검토 하거나 단위 노드 개체의 크기를 조정 합니다. 이렇게 하려면로 이동 **하위 지역 관리** > **단위**합니다. 배율 단위 또는 배율 단위 노드를 검토 하려면 다음을 선택 합니다. 

### <a name="use-powershell"></a>PowerShell 사용
배율 단위 및 노드 크기 조정 단위에 대 한 상태는 다음과 같이 PowerShell을 사용 하 여 검색할 수 있습니다.
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>노드 추가 작업에 대 한 상태 
**배율 단위:**

|상태               |설명  |
|---------------------|---------|
|실행 중              |배율 단위에 모든 노드 적극적으로 참여 하 합니다.|
|중지됨              |확장 단위 노드에 다운 되었거나 연결할 수 없습니다.|
|확장            |하나 이상의 배율 단위 노드 용량을 계산 하는 대로 현재 추가 됩니다.|
|저장소 구성  |계산 용량 확장 되 고 실행 중인 저장소 구성 합니다.|
|업데이트 관리 필요 |하나 이상의 노드 크기 조정 단위 복구 해야 하는 오류가 검색 되었습니다.|


**배율 단위 노드:**

|상태                |설명  |
|----------------------|---------|
|실행 중               |노드 배율 단위에 적극적으로 참여 합니다.|
|중지됨               |노드를 사용할 수 없는 경우|
|추가하는 중                |노드는 배율 단위에 추가 되 고 적극적으로 됩니다.|
|복구 중             |노드는 적극적으로 복구 하는 중입니다.|
|유지 관리           |노드 일시 중지 되 고 활성 사용자 워크 로드가 없습니다 실행입니다. |
|업데이트 관리 필요  |노드를 복구 해야 하는 오류가 감지 되었습니다.|


## <a name="troubleshooting"></a>문제 해결
노드를 추가할 때 나타나는 일반적인 문제는 다음과 같습니다. 

**시나리오 1:**  배율 단위 노드 추가 작업이 실패 하지만 하나 이상의 노드가 중지 됨 상태로 나열 됩니다.  
- 업데이트 관리: 하나 이상의 노드를 복구 하려면 복구 작업을 사용 합니다. 작업을 단일 복구만 한 번에 실행할 수 있습니다.

**시나리오 2:** 하나 이상의 배율 단위 노드 추가 되었지만 저장소 확장에 실패 했습니다. 이 시나리오에서는 배율 단위 노드 개체 실행의 상태를 보고 하지만 구성 저장소 작업이 시작 되지 않습니다.  
- 업데이트 관리: 다음 PowerShell cmdlet을 실행 하 여 저장소 상태를 검토 하려면 권한 있는 끝점을 사용 합니다.
  ```powershell
     Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
  ```
 
**시나리오 3:** 실패 한 저장소 스케일 아웃 작업을 나타내는 경고를 받았습니다.  
- 업데이트 관리: 이 경우 저장소 구성 작업에 실패 했습니다. 이 문제는 지원에 문의 해야 합니다.


## <a name="next-steps"></a>다음 단계 
[공용 IP 주소 추가](azure-stack-add-ips.md) 
