---
title: VM 인사이트 게스트 상태 문제 해결(미리 보기)
description: VM 인사이트 상태와 관련된 이슈가 있을 때 수행할 수 있는 문제 해결 단계를 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/25/2021
ms.openlocfilehash: 834c70e02ab25fa6dcadb5f6c997be09aaf5e353
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932780"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>VM 인사이트 게스트 상태 문제 해결(미리 보기)
이 문서에서는 VM 인사이트 상태와 관련된 이슈가 있을 때 수행할 수 있는 문제 해결 단계를 설명합니다.


## <a name="upgrade-available-message-is-still-displayed-after-upgrading-guest-health"></a>게스트 상태를 업그레이드 한 후에도 업그레이드 가능 메시지가 계속 표시됨 

- 글로벌 Azure에서 VM이 실행 중인지 확인합니다. Arc 사용 서버는 아직 지원되지 않습니다.
- [VM용 Azure Monitor 게스트 상태 사용(미리 보기)](vminsights-health-enable.md)에 설명된 대로 가상 머신의 지역 및 운영 체제 버전이 지원되는지 확인합니다.
- 게스트 상태 확장이 0 종료 코드를 표시하며 성공적으로 설치되었는지 확인합니다.
- Azure Monitor 에이전트 확장이 성공적으로 설치되었는지 확인합니다.
- 가상 머신에 시스템이 할당한 관리 ID가 사용되는지 확인합니다.
- 가상 머신에 사용자 할당 관리 ID가 지정되지 않았는지 확인합니다.
- 로캘이 *미국 영어* 인 Windows 가상 머신을 확인합니다. 현재 Azure Monitor 에이전트에서 지역화를 지원하지 않습니다.
- 가상 머신에서 네트워크 프록시를 사용하고 있지 않은지 확인합니다. Azure Monitor 에이전트는 현재 프록시를 지원하지 않습니다.
- 상태 확장 에이전트가 오류 없이 시작되었는지 확인합니다. 에이전트를 시작할 수 없으면 에이전트 상태가 손상되었을 수 있습니다. 에이전트 상태 폴더의 콘텐츠를 삭제하고 에이전트를 다시 시작합니다.
  - Linux의 경우: 디먼은 *vmGuestHealthAgent* 입니다. 상태 폴더는 */var/opt/vmGuestHealthAgent/* *입니다.
  - Windows의 경우: 서비스는 *VM 게스트 상태 에이전트* 입니다. 상태 폴더는 _%ProgramData%\Microsoft\VMGuestHealthAgent\\*_ 입니다.
- Azure Monitor 에이전트가 네트워크에 연결되었는지 확인합니다. 
  - 가상 머신에서 _<region>.handler.control.monitor.azure.com_ 을 ping해 봅니다. 예를 들어, 서유럽의 가상 머신인 경우 _westeurope.handler.control.monitor.azure.com:443_ 을 ping해 봅니다.
- 가상 머신이 Log Analytics 작업 영역과 같은 지역의 데이터 수집 규칙과 연결되어 있는지 확인합니다.
  -  DCR의 구조가 올바른지 확인하려면 [VM용 Azure Monitor 게스트 상태 사용(미리 보기)](vminsights-health-enable.md)에서 **DCR(데이터 수집 규칙) 만들기** 를 참조하세요. 세 개의 카운터를 샘플링하도록 설정된 *performanceCounters* 데이터 원본 섹션이 있고, 확장에 카운터를 보내도록 상태 확장 구성에 *inputDataSources* 섹션이 있는지 특히 주의합니다.
-  가상 머신에 게스트 상태 확장 오류가 있는지 확인합니다.
   -  Linux의 경우: _/var/log/azure/Microsoft.Azure.Monitor.VirtualMachines.GuestHealthLinuxAgent/*.log_ 의 로그를 확인합니다.
   -  Windows의 경우: _C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitor.VirtualMachines.GuestHealthWindowsAgent\{extension version}\*.log_ 의 로그를 확인합니다.
-  가상 머신에 Azure Monitor 에이전트 오류가 있는지 확인합니다.
   -  Linux의 경우: _/var/log/mdsd.*_ 의 로그를 확인합니다.
   -  Windows의 경우: _C:\WindowsAzure\Resources\*{vmName}.AMADataStore_ 의 로그를 확인합니다.
 



## <a name="error-message-that-no-data-is-available"></a>데이터를 사용할 수 없다는 오류 메시지 

![데이터 없음](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>가상 머신이 구성 요구 사항을 충족하는지 확인

1. 가상 컴퓨터가 Azure 가상 컴퓨터인지 확인합니다. 서버용 Azure Arc는 현재 지원되지 않습니다.
2. 가상 컴퓨터가 [지원되는 운영 체제](vminsights-health-enable.md?current-limitations.md)를 실행 중인지 확인합니다.
3. 가상 컴퓨터가 [지원되는 지역](vminsights-health-enable.md?current-limitations.md)에 설치되어 있는지 확인합니다.
4. Log Analytics 작업 영역이 [지원되는 지역](vminsights-health-enable.md?current-limitations.md)에 설치되어 있는지 확인합니다.

### <a name="verify-that-the-vm-is-properly-onboarded"></a>VM이 제대로 온보딩되었는지 확인
Azure Monitor 에이전트 확장 및 게스트 VM 상태 에이전트가 가상 머신에서 성공적으로 프로비저닝되었는지 확인합니다. Azure Portal의 가상 머신 메뉴에서 **확장** 을 선택하고 두 에이전트가 나열되었는지 확인합니다.

![VM 확장](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>가상 머신에서 시스템 할당 ID를 사용하도록 설정되어 있는지 확인
가상 머신에서 시스템 할당 ID를 사용하도록 설정되어 있는지 확인합니다. Azure Portal의 가상 머신 메뉴에서 **ID** 를 선택합니다. 사용자 관리 ID를 사용하도록 설정하면 시스템 관리 ID의 상태와 관계없이 Azure Monitor 에이전트가 구성 서비스와 통신할 수 없으며 게스트 상태 확장이 작동하지 않습니다.

![시스템 할당 ID](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>데이터 수집 규칙 확인
상태 확장을 데이터 원본으로 지정하는 데이터 수집 규칙이 가상 머신과 연결되어 있는지 확인합니다.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>권한 부족으로 인한 잘못된 요청에 관한 오류 메시지
이 오류는 **Microsoft.WorkloadMonitor** 리소스 공급자가 구독에 등록되지 않았음을 나타냅니다. 이 리소스 공급자 등록에 관한 세부 정보는 [Azure 리소스 공급자 및 형식](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)을 참조하세요. 

![잘못된 요청](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="health-shows-as-unknown-after-guest-health-is-enabled"></a>게스트 상태를 사용하도록 설정하고 나면 상태가 “알 수 없음”으로 표시됩니다.

### <a name="verify-that-performance-counters-on-windows-nodes-are-working-correctly"></a>Windows 노드의 성능 카운터가 제대로 작동하는지 확인 
게스트 상태는 노드에서 성능 카운터를 수집할 수 있는 에이전트를 사용합니다. 성능 카운터 라이브러리의 기본 세트가 손상되어 다시 빌드해야 할 수 있습니다. [성능 카운터 라이브러리 값을 수동으로 다시 빌드](/troubleshoot/windows-server/performance/rebuild-performance-counter-library-values)의 지침에 따라 성능 카운터를 다시 빌드합니다.





## <a name="next-steps"></a>다음 단계

- [VM 인사이트 게스트 상태 기능의 개요 알아보기](vminsights-health-overview.md)