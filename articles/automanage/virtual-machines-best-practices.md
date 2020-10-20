---
title: Virtual Machines 모범 사례에 대 한 Azure Automanage
description: 자동으로 등록 및 구성 되는 서비스에 대 한 virtual machines의 Azure Automanage 모범 사례에 대해 알아봅니다.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: dce076da08a7c31d7e2637dd5b8b29c9202ea10e
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206410"
---
# <a name="azure-automanage-for-virtual-machines-best-practices"></a>Virtual machines에 대 한 Azure Automanage 모범 사례


이러한 Azure 서비스는 가상 컴퓨터에 대해 Automanage를 사용 하는 경우 자동으로 등록 됩니다. [클라우드 채택 프레임 워크](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/azure-server-management)에서 찾을 수 있는 모범 사례 백서에 반드시 필요 합니다.

이러한 모든 서비스에 대해 자동으로 등록 하 고, 드리프트를 자동으로 구성 하 고, 드리프트가 검색 되 면 중재를 모니터링 합니다. 이 프로세스에 대해 자세히 알아보려면 [가상 컴퓨터에 대 한 Azure Automanage](automanage-virtual-machines.md)를 참조 하세요.


## <a name="participating-services"></a>참여 하는 서비스

|서비스    |Description    |지원 되는 프로필<sup>1</sup>    |기본 설정 지원<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|VM Insights 모니터링    |VM용 Azure Monitor는 실행 중인 프로세스 및 다른 리소스에 대 한 종속성을 포함 하 여 가상 컴퓨터의 성능 및 상태를 모니터링 합니다. 자세히 [알아보세요.](../azure-monitor/insights/vminsights-overview.md)    |Azure VM 모범 사례-프로덕션    |예    |
|Backup    |Azure Backup은 VM에서 의도하지 않은 데이터 소멸을 방지하기 위해 독립적이고 격리된 백업을 제공합니다. 자세히 [알아보세요.](../backup/backup-azure-vms-introduction.md) 요금은 보호 되는 Vm의 수와 크기를 기준으로 합니다. 자세히 [알아보세요.](https://azure.microsoft.com/pricing/details/backup/)    |Azure VM 모범 사례-프로덕션    |예    |
|Azure Security Center    |Azure Security Center은 데이터 센터의 보안 상태를 강화 하 고 클라우드의 하이브리드 워크 로드에서 고급 위협 방지를 제공 하는 통합 인프라 보안 관리 시스템입니다. 자세히 [알아보세요.](../security-center/security-center-intro.md)  Automanage는 VM이 Azure Security Center의 무료 계층 제품으로 상주 하는 구독을 구성 합니다. 구독이 이미 Azure Security Center 등록 하는 경우 자동 관리를 통해 다시 구성 되지 않습니다.    |Azure VM 모범 사례 – 프로덕션, Azure VM 모범 사례 – 개발/테스트    |예    |
|Microsoft 맬웨어 방지    |Azure용 Microsoft 맬웨어 방지 프로그램은 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별하고 제거하는 데 도움이 되는 평가판 실시간 보호 기능입니다. 알려진 악성 또는 원치 않는 소프트웨어가 Azure 시스템에서 스스로의 설치나 실행을 시도할 때 경고를 생성합니다. 자세히 [알아보세요.](../security/fundamentals/antimalware.md) |Azure VM 모범 사례 – 프로덕션, Azure VM 모범 사례 – 개발/테스트    |예    |
|업데이트 관리    |Azure Automation에서 업데이트 관리를 사용 하 여 가상 컴퓨터에 대 한 운영 체제 업데이트를 관리할 수 있습니다. 모든 에이전트 머신에서 사용 가능한 업데이트의 상태를 신속하게 평가하고 서버의 필수 업데이트를 설치하는 프로세스를 관리할 수 있습니다. 자세히 [알아보세요.](../automation/update-management/update-mgmt-overview.md)    |Azure VM 모범 사례 – 프로덕션, Azure VM 모범 사례 – 개발/테스트    |예    |
|변경 내용 추적 & 인벤토리    |변경 내용 추적 및 인벤토리는 변경 추적 및 인벤토리 기능을 결합하여 가상 머신 및 서버 인프라 변경 내용을 추적할 수 있도록 합니다. 이 서비스는 원치 않는 변경 내용을 진단 하 고 경고를 발생 시킬 수 있도록 사용자 환경의 서비스, 디먼 소프트웨어, 레지스트리 및 파일에서 변경 내용 추적을 지원 합니다. 인벤토리 지원을 사용하면 게스트 내 리소스를 쿼리하여 설치된 애플리케이션 및 기타 구성 항목을 볼 수 있습니다.  자세히 [알아보세요.](../automation/change-tracking/overview.md)    |Azure VM 모범 사례 – 프로덕션, Azure VM 모범 사례 – 개발/테스트    |예    |
|Azure Automation 계정    |Azure Automation은 인프라와 애플리케이션의 수명 주기 전반에 걸친 관리를 지원합니다. 자세히 [알아보세요.](../automation/automation-intro.md)    |Azure VM 모범 사례 – 프로덕션, Azure VM 모범 사례 – 개발/테스트    |예    |
|Log Analytics 작업 영역    |Azure Monitor는 Azure 리소스 및 데이터를 수집 하 고 집계 하 여 관리 경계 역할을 하는 컨테이너에 해당 하는 Log Analytics 작업 영역에 로그 데이터를 저장 합니다. 자세히 [알아보세요.](../azure-monitor/platform/design-logs-deployment.md)    |Azure VM 모범 사례 – 프로덕션, Azure VM 모범 사례 – 개발/테스트    |예    |


Automanage를 사용 하도록 설정 하는 <sup>경우 구성 프로필</sup> 을 사용할 수 있습니다. 자세히 [알아보세요.](automanage-virtual-machines.md#configuration-profiles) 구성 프로필의 기본 설정을 조정 하 고 모범 사례 제약 조건 내에서 고유한 기본 설정을 지정할 수도 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Portal에서 가상 컴퓨터에 대해 Automanage를 사용 하도록 설정 하세요.

> [!div class="nextstepaction"]
> [Azure Portal에서 가상 컴퓨터에 대해 Automanage를 사용 하도록 설정](quick-create-virtual-machines-portal.md)