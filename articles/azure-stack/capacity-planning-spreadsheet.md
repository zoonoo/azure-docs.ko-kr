---
title: 용량 spreadheet Azure Stack에 대 한 계획 | Microsoft Docs
description: 용량 계획 스프레드시트 Azure Stack 배포에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.openlocfilehash: 5ebddbf1fea49fbf868d15a544a18e5a8c6369fd
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078310"
---
# <a name="azure-stack-capacity-planner"></a>Azure Stack Capacity Planner
Azure Stack Capacity Planner 스프레드시트 Azure Stack 리소스 용량 계획에 사용 되는 경우 Capacity planner 컴퓨팅 리소스의 다양 한 할당을 디자인 하 고 어떻게 이러한 적합 한 다양 한 하드웨어 제품에서 참조 하는 기능을 제공 합니다. Azure Stack 계산기의 사용에 대 한 자세한 지침은 아래 제공 됩니다.

## <a name="worksheet-descriptions"></a>워크시트 설명
다음은 간단한 요약이에서 다운로드할 수 있는 Azure Stack Capacity Planner 스프레드시트에 포함 된 워크시트 [ http://aka.ms/azstackcapacityplanner ](http://aka.ms/azstackcapacityplanner):

|탭 이름|설명|
|-----|-----|
|버전-고 지 사항|계산기, 버전 번호 및 릴리스 날짜의 용도 대 한 간략 한 개요입니다.|
|지침|Azure Stack Capacity Planner의 사용에 대 한 자세한 지침을 제공 합니다.|
|DefinedSolutionSKUs|최대 5 개의 하드웨어 정의 포함 하는 다중 열 테이블입니다. 이 시트에 있는 항목은 예입니다. 의도 사용자가 사용할 것으로 간주 되 고 시스템 구성을 구매에 대 한 세부 정보를 변경 됩니다.|
|DefineByVMFootprint|컬렉션의 다양 한 크기 및 Vm의 수량을 만들어서 적절 한 하드웨어를 SKU를 찾습니다.|
|DefineByWorkloadFootprint|Azure Stack 워크 로드의 컬렉션을 만들어서 적절 한 하드웨어를 SKU를 찾습니다.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs 지침
이 워크시트는 최대 5 개의 하드웨어 정의 예제가 포함 되어 있습니다. 세부 정보를 사용 하 여에 대 한 것으로 간주 되 고 시스템 구성을 변경 하거나 구매 합니다.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>권한이 부여 된 하드웨어 파트너가 제공 하는 하드웨어 선택
Azure Stack 솔루션 파트너에 의해 설치 된 소프트웨어를 사용 하 여 통합된 시스템으로 전달 됩니다. 이러한 솔루션 파트너를 자체적으로 신뢰할 수 있는 다양 한 Azure Stack 솔루션 용량의 토론을 마무리 하려면 용량 계획 도구 및 해당 도구를 사용 해야 해야 합니다.

### <a name="multiple-ways-to-model-computing-resources"></a>컴퓨팅 리소스를 모델링 하는 여러 방법
Azure Stack planner 내에서 모델링 하는 리소스에 사용 되는 기본 구성 요소는 Azure Stack virtual machines (Vm)의 다양 한 크기입니다. 이러한 Vm 범위에서 가장 작은 크기의 "기본 0", "Standard_Fsv2." 현재 가장 큰 vm 필요에 따라 서로 다른 Vm의 다양 한 수량 수 만드는 데 사용할 두 가지 방법으로이 도구를 사용 하 여 컴퓨팅 리소스 할당 합니다.

1. Planner 사용자는 특정 하드웨어 제품을 선택 하 고이 하드웨어 리소스 내에 맞는 다양 한 리소스의 조합을 표시 합니다. 

2. Planner 사용자 VM 할당의 특정 조합을 만들고 사용 가능한 하드웨어 Sku는이 VM 구성을 지원할 수 있는 Azure 리소스 계산기는 지정할 수 있도록 합니다.

이 도구에서는 VM 리소스를 할당 하기 위한 두 가지 방법 또는 VM 리소스 할당의 하나의 단일 컬렉션으로 최대 6 개의 서로 다른 워크 로드 구성 컬렉션입니다. 각 워크 로드 구성에는 사용 가능한 VM 리소스의 다른 할당을 포함할 수 있습니다. 아래를 만들고 각 할당 모델을 사용 하는 자세한 정보가 제공 됩니다. 아닌 백그라운드에서 포함 된 값만 셀에 회색으로 표시 하거나이 워크시트에서 SKU 풀 다운 목록에서 수정 해야 합니다. 음영 처리 된 셀에서 변경 내용을 리소스 계산 중단 될 수 있습니다.


## <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint 지침
다양 한 크기 및 Vm의 수량을 단일 컬렉션을 사용 하 여 모델을 만들려면 "DefineByVMFootprint" 탭을 선택 하 고이 일련의 단계를 수행 합니다.

1. 이 워크시트의 오른쪽 위 모서리를 사용 하 여 제공 된 드롭다운 목록 상자 컨트롤 번호를 초기 선택 (4에서 12 사이의) 서버의 각 하드웨어 시스템 (SKU)에서 설치 하려는 합니다. 이 서버 수가 미치는 전반적인 참조 하는 모델링 프로세스 중 언제 든 지 수정할 수 있습니다 리소스 할당 모델에 대 한 리소스를 사용할 수 있습니다.
2. 특정 하드웨어 구성에 대 한 다양 한 VM 리소스 할당을 모델링 하려는 경우 페이지의 오른쪽 상단 모서리에서 "현재 SKU" 레이블 바로 아래 파란색 드롭다운 목록 상자를 찾습니다. 이 목록 상자 풀다운합니다 하 고 원하는 하드웨어 SKU를 선택 합니다.
3. 이제 모델에 다양 한 크기의 Vm을 추가 시작할 준비가 되었습니다. 특정 VM 유형을 포함 하려면 해당 VM 항목의 왼쪽에 파란색 윤곽선이 있는 상자에 수량 값을 입력 합니다.

  > [!NOTE]
  > 각 VM을 처음에 할당 된 저장소 크기를 사용 하 여 시작합니다. 저장소 크기 목록 상자를 사용 하 여 표시 되 고 각 Azure Stack VM에 대 한 저장소 리소스의 원하는 수준에 맞게 수정할 수 있습니다. 사용 하려는 저장소 크기를 지정 하지 않은 경우에 페이지의 오른쪽에 있는 "사용 가능한 저장소 구성" 목록에 포함 된 10 초기 크기 중 하나를 수정 하 여 추가할 수 있습니다.<br><br>각 VM을 처음에 할당 된 로컬 임시 저장소를 사용 하 여 시작합니다. 임시 저장소의 씬 프로 비전을 반영 하도록 로컬 temp 수를 최대 허용 되는 임시 저장소 용량을 포함 하 여 메뉴 드롭다운에 아무 것도 변경할 수 있습니다.

4. Vm에 추가한 대로 변경 하는 사용 가능한 SKU 리소스를 보여주는 차트에 표시 됩니다. 이 옵션을 사용 하면 모델링 프로세스 동안 다양 한 크기 및 Vm의 수량을 추가 하는 효과 볼 수 있습니다. 변경의 결과 표시 하는 다른 방법은 직접 사용할 수 있는 Vm 목록 아래에 나열 된 사용 및 계속 사용할 수 있는 번호를 시청 하려면 것입니다. 이러한 번호는 현재 선택 된 하드웨어 SKU 기반으로 하는 예상된 값을 반영 합니다.
5. Vm 집합을 만든 후에 "현재 SKU" 레이블 바로 아래 페이지의 오른쪽 위 모서리에 있는 "제안 된 SKU" 단추를 클릭 하 여 제안 된 하드웨어 SKU를 찾을 수 있습니다. 이 단추를 사용 하 여 다음 VM 구성을 수정 고 볼 수 있습니다 하드웨어 각 구성을 지원 합니다.


## <a name="definebyworkloadfootprint-instructions"></a>DefineByWorkloadFootprint 지침
Azure 스택 워크 로드의 컬렉션을 사용 하 여 모델을 만들려면 "DefineByWorkloadFootprint" 탭을 선택 하 고이 일련의 단계를 수행 합니다. Azure 스택 워크 로드는 사용 가능한 VM 리소스를 사용 하 여 생성 됩니다.   

> [!TIP]
> Azure Stack VM에 대해 제공 된 저장소 크기를 변경 하려면 이전 섹션의 3 단계에서 메모를 참조 하세요.

1. 이 페이지의 오른쪽 위 모서리로를 사용 하 여 제공 된 드롭다운 목록 상자 컨트롤 번호를 초기 선택 (4에서 12 사이의) 서버의 각 하드웨어 시스템 (SKU)에서 설치 하려는 합니다.
2. 특정 하드웨어 구성에 대 한 다양 한 VM 리소스 할당을 모델링 하려는 경우 페이지의 오른쪽 상단 모서리에서 "현재 SKU" 레이블 바로 아래 파란색 드롭다운 목록 상자를 찾습니다. 이 목록 상자 풀다운합니다 하 고 원하는 하드웨어 SKU를 선택 합니다.
3. DefineByVMFootprint 지침의 3 단계에서 설명한 것 처럼 각 DefineByVMFootprint 페이지에서 원하는 Azure Stack Vm에 대 한 적절 한 저장소 크기를 선택 합니다. VM 당 저장소 크기는 DefineByVMFootprint 시트에 정의 됩니다.
4. DefineByWorkloadFootprint 페이지의 왼쪽 위에 starting, 작업 내에 포함 된 각 VM 유형에 대해 quantity를 입력 하 여 최대 6 개의 서로 다른 워크 로드 형식에 대 한 구성을 만듭니다. 이 작업의 이름 바로 아래 열에 숫자 값을 배치 하 여 이루어집니다. 이 특정 구성에서 지원 되는 워크 로드 유형에 맞게 워크 로드 이름을 수정할 수 있습니다.
5. "Quantity" 레이블 바로 아래 열 맨 아래에 값을 입력 하 여 각 작업 유형의 특정 수량을 포함할 수 있습니다.
6. 워크 로드 유형 및 수량을 만든 후 클릭 "제안 된 SKU"는 "현재 SKU" 레이블 바로 아래 페이지의 오른쪽 위에 있는 단추 하면 가장 작은 SKU이이 전체를 지원 하기 위해 충분 한 리소스를 사용 하 여 표시할 워크 로드의 구성입니다.
7. 추가 모델링 하드웨어 SKU 선택 하는 서버의 수를 수정 하 여 수행할 수 있습니다 또는 워크 로드 구성 내에서 수량을 VM 할당을 변경 합니다. 연결 된 그래프에는 변경 내용을 전체 리소스 소비량에 미치는 영향을 보여 주는 피드백을 즉시 표시 됩니다.
8. 만족 스 러 우면 변경 내용으로, 다시 "제안 된 SKU" 단추를 클릭 하면 새 구성에 대 한 제안 된 SKU 표시 됩니다.


## <a name="next-steps"></a>다음 단계
에 대 한 자세한는 [Azure Stack에 대 한 데이터 센터 통합 고려 사항](azure-stack-datacenter-integration.md)
