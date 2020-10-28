---
title: Virtual machines 용 Azure Automanage FAQ
description: 가상 컴퓨터에 대 한 Azure Automanage에 대해 자주 묻는 질문에 대 한 대답입니다.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: 89ad5c2b973a74bc9fa0f6ca2f59f92a26b666e7
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891464"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Vm에 대 한 Azure Automanage 질문과 대답

이 문서에서는 [가상 머신에 대 한 Azure Automanage](automanage-virtual-machines.md)에 대해 가장 일반적인 질문 중 일부에 대 한 답변을 제공 합니다.

Azure 문제와 관련된 정보가 이 문서에 없을 경우 [MSDN 및 Stack Overflow에서 Azure 포럼](https://azure.microsoft.com/support/forums/)을 방문합니다. 이러한 포럼이나 [@AzureSupport Twitter](https://twitter.com/AzureSupport) 의 에 문제를 게시할 수 있습니다. Azure 지원 요청을 제출할 수도 있습니다. 지원 요청을 제출 하려면 [Azure 지원 페이지](https://azure.microsoft.com/support/options/)에서 **지원 받기** 를 선택 합니다.


## <a name="azure-automanage-for-virtual-machines"></a>가상 컴퓨터에 대 한 Azure Automanage

**Azure Automanage를 사용 하도록 설정 하는 데 필요한 모든 필수 구성 요소는 무엇 인가요?**

Azure Automanage를 사용 하도록 설정 하기 위한 필수 구성 요소는 다음과 같습니다.
- Windows Server Vm에만 해당
- Vm이 실행 되 고 있어야 함
- Vm은 지원 되는 지역에 있어야 합니다.
- 사용자에 게 올바른 사용 권한이 있어야 합니다.
- 확장 되지 않은 집합 Vm만
- 지금은 automanage에서 샌드박스 구독을 지원 하지 않습니다.

**Automanage를 사용 하도록 설정 하는 데 필요한 Azure RBAC 권한은 무엇입니까?**

기존 Automanage 계정을 사용 하 여 VM에서 Automanage를 사용 하도록 설정 하는 경우 VM이 있는 리소스 그룹에 대 한 참가자 역할이 필요 합니다.

를 사용 하도록 설정할 때 새 Automanage 계정을 사용 하는 경우 소유자 역할 또는 구독에 대 한 참가자 + 사용자 액세스 관리자 역할이 있어야 합니다.


**지원되는 지역은 어떻게 되나요?**

유럽 서부, 미국 동부, 미국 서 부 2, 캐나다 중부, 미국 서 부 및 지역의 Vm이 지원 됩니다.


**Azure Automanage가 자동화 하는 기능은 무엇 인가요?**

자동 관리는 [여기](virtual-machines-best-practices.md)에 나열 된 서비스를 VM의 수명 주기 내내 등록, 구성 및 모니터링 합니다.

**Azure는 Azure Arc 사용 Vm을 사용 하 여 작업을 자동으로 관리 하나요?**

Automanage는 현재 Arc 사용 Vm을 지원 하지 않습니다.

**Azure Automanage에서 구성을 사용자 지정할 수 있나요?**

고객은 구성 기본 설정을 통해 Azure Backup 보존 등의 특정 서비스에 대 한 설정을 사용자 지정할 수 있습니다. 변경할 수 있는 설정의 전체 목록은 [여기](virtual-machines-best-practices.md)에서 설명서를 참조 하세요.


**Azure Automanage는 Linux 및 Windows Vm 모두에서 작동 하나요?**

현재 Automanage는 Windows Server Azure Vm을 지원 합니다.


**Vm 집합에만 Automanage를 선택적으로 적용할 수 있나요?**

자동 관리는 선택한 새 및 기존 Vm에서 클릭 및 가리키기 간소화를 사용 하 여 사용 하도록 설정할 수 있습니다. Automanage도 언제 든 지 사용 하지 않도록 설정할 수 있습니다.


**Azure는 가상 머신 확장 집합의 지원 Vm을 자동으로 관리 하나요?**

아니요, Azure Automanage는 현재 가상 머신 확장 집합의 Vm을 지원 하지 않습니다.


**Azure Automanage 비용은 얼마나 되나요?**

Azure Automanage는 공개 미리 보기에서 추가 비용 없이 사용할 수 있습니다. Azure Backup와 같은 연결 된 Azure 리소스에는 비용이 발생 합니다.


**Azure 정책을 통해 Automanage를 적용할 수 있나요?**

예, 자동 관리를 정의 된 범위 내의 모든 Vm에 자동으로 적용 하는 기본 제공 정책이 있습니다. 또한 Automanage 계정과 함께 구성 프로필 (DevTest 또는 Production)을 지정 합니다. Azure 정책을 통한 Automanage 사용에 대 한 자세한 내용은 [여기](virtual-machines-policy-enable.md)를 참조 하세요.


**Automanage 계정 이란?**

Automanage 계정은 자동화 된 작업이 수행 되는 보안 컨텍스트 또는 id를 제공 하는 MSI (관리 서비스 ID)입니다.


**Automanage를 사용 하도록 설정 하면 선택한 VM 외에도 추가 Vm에 영향을 미칩니까?**

VM이 기존 Log Analytics 작업 영역에 연결 된 경우 해당 작업 영역을 다시 사용 하 여 변경 내용 추적, 인벤토리 및 업데이트 관리 솔루션을 적용 합니다. 해당 작업 영역에 연결 된 모든 Vm은 해당 솔루션을 사용할 수 있습니다.


**내 VM의 구성 프로필을 변경할 수 있나요?**

이번에는 해당 VM에 대해 Automanage를 사용 하지 않도록 설정한 후 원하는 구성 프로필 및 기본 설정으로 Automanage를 다시 사용 하도록 설정 해야 합니다.


**내 VM이 이미 서비스에 대해 구성 되어 있는 경우 (예: 업데이트 관리) 자동으로 다시 구성 합니다.**
아니요, Automanage는 다시 구성 하지 않습니다. 드리프트를 위해 해당 서비스와 연결 된 리소스를 모니터링 하기 시작 합니다.


**Automanage 포털에서 VM의 상태가 실패 인 이유는 무엇 인가요?**

상태가 *실패* 로 표시 되는 경우 VM이 있는 리소스 그룹을 통해 배포 문제를 해결할 수 있습니다. **리소스 그룹** 으로 이동 하 여 리소스 그룹을 선택 하 고 **배포** 를 클릭 한 다음 오류 정보와 함께 *실패* 상태를 확인 합니다.

**자동 관리에 대 한 문제 해결 지원을 받으려면 어떻게 해야 하나요?**

[기술 지원 사례 티켓](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 입력할 수 있습니다. **서비스** 옵션의 경우 *모니터링 및 관리* 섹션에서 *자동 관리* 를 검색 하 고 선택 합니다.


## <a name="next-steps"></a>다음 단계

Azure Portal에서 가상 컴퓨터에 대해 Automanage를 사용 하도록 설정 하세요.

> [!div class="nextstepaction"]
> [Azure Portal에서 가상 컴퓨터에 대해 Automanage를 사용 하도록 설정](quick-create-virtual-machines-portal.md)