---
title: VM용 Azure Monitor(미리 보기)의 알려진 문제 | Microsoft Docs
description: 이 문서에서는 Azure VM 운영 체제의 상태 및 성능 모니터링을 결합하는 Azure의 솔루션인 VM용 Azure Monitor의 알려진 문제를 설명합니다. VM용 Azure Monitor는 애플리케이션 구성 요소 및 다른 리소스와의 종속성을 자동으로 검색하고 이들 간의 통신을 매핑합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/26/2018
ms.author: magoedte
ms.openlocfilehash: c329e1fa80c6647bb78b11917ecd012461e62ea4
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790506"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>VM용 Azure Monitor(미리 보기)의 알려진 문제

이 문서에서는 Azure VM 운영 체제의 상태 및 성능 모니터링을 결합하는 Azure의 솔루션인 VM용 Azure Monitor의 알려진 문제를 설명합니다. 

상태 기능과 관련하여 알려진 문제는 다음과 같습니다.

- 상태 기능은 Log Analytics 작업 영역에 연결된 모든 VM에 대해 사용됩니다. 단일 VM에서 작업이 시작되고 종료되는 경우에도 마찬가지입니다.
- Linux VM의 경우 단일 VM 보기의 상태 조건을 나열하는 페이지의 제목에는 사용자 정의 VM 이름 대신 VM의 전체 도메인 이름이 포함됩니다.  
- 지원되는 메서드를 사용하여 VM에 대한 모니터링을 사용하지 않도록 설정한 후에 다시 배포하려는 경우 동일한 작업 영역에 배포해야 합니다. 새 작업 영역을 사용하고 해당 VM의 상태를 보려고 하면 비정상적인 동작이 표시될 수 있습니다.
- 제거하거나 삭제한 Azure VM은 얼마 동안 VM 목록 보기에 표시됩니다. 또한 제거하거나 삭제한 VM의 상태를 클릭하면 **상태 진단** 보기가 열리고 로딩 루프가 시작됩니다. 삭제한 VM의 이름을 선택하면 VM이 삭제되었다는 내용의 메시지가 포함된 창이 열립니다.
- 이 릴리스에서는 상태 조건의 기간 및 빈도를 수정할 수 없습니다. 
- 상태 조건을 사용하지 않도록 설정할 수 없습니다. 
- 배포 후에 **Azure Monitor** > **Virtual Machines** > **상태** 창 또는 **VM 리소스** > **인사이트** 창에 데이터가 표시되기까지 시간이 걸릴 수 있습니다.
- 상태 진단 환경은 다른 보기보다 빠르게 업데이트됩니다. 보기 간에 전환할 때 정보가 지연될 수 있습니다. 
- VM용 Azure Monitor에 포함된 경고 요약은 모니터링된 Azure VM에서 검색된 상태 문제로 인해 발생하는 경고만 표시합니다.
- VM을 종료하면 일부 상태 조건은 *위험*으로 업데이트되고, 다른 상태 조건은 *정상*으로 업데이트됩니다. 순 VM 상태는 *위험*으로 표시됩니다.
- 상태 경고 심각도는 수정할 수 없으며, 사용하거나 사용하지 않도록 설정할 수만 있습니다. 또한 일부 심각도는 상태 조건의 상태에 따라 업데이트됩니다.   
- 상태 조건 인스턴스의 설정을 수정하면 VM에서 동일한 유형의 모든 상태 조건 인스턴스가 수정됩니다. 예를 들어 C: 논리 디스크에 해당하는 사용 가능한 디스크 공간 상태 조건 인스턴스의 임계값을 수정할 경우 이 임계값은 동일한 VM에 대해 검색 및 모니터링되는 다른 모든 논리 디스크에 적용됩니다.  
- 상태가 *실행 중* 또는 *사용 가능*으로 설정되어 있는 Windows VM을 대상으로 하는 상태 조건의 임계값은 수정할 수 없습니다. [워크로드 모니터 API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager)에서 상태를 쿼리할 때 다음과 같은 경우 서비스 또는 엔터티에 대해 *comparisonOperator* 값 **LessThan** 또는 **GreaterThan**과 *threshold* 값 **4**가 표시됩니다.
   - DNS 클라이언트 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - DHCP 클라이언트 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - RPC 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - Windows 방화벽 서비스 상태 – 서비스가 실행 중이 아닙니다.
   - Windows 이벤트 로그 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - 서버 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - Windows 원격 관리 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - 파일 시스템 오류 또는 손상 - 논리 디스크를 사용할 수 없습니다.

- 상태가 이미 *true*로 설정되어 있는 다음 Linux 상태 조건의 임계값은 수정할 수 없습니다. 워크로드 모니터링 API에서 엔터티에 대해 쿼리할 때 컨텍스트에 따라 상태에 *comparisonOperator* 값 **LessThan** 및 *threshold* 값 **1**이 표시됩니다.
   - 논리 디스크 상태 - 논리 디스크가 온라인/사용 가능 상태가 아님
   - 디스크 상태 - 디스크가 온라인/사용 가능 상태가 아님
   - 네트워크 어댑터 상태 - 네트워크 어댑터가 사용 안 함임  

- 포털 또는 워크로드 모니터 API에서 즉시 업데이트할 수 있더라도 임계값 업데이트와 같은 구성 변경은 적용하는 데 최대 30분이 걸립니다. 
- 개별 프로세서 및 논리적 프로세서 수준 상태 조건은 Windows에서 사용할 수 없습니다. Windows VM에 대해서는 총 CPU 사용률만 제공됩니다. 
- 각 상태 조건에 대해 정의된 경고 규칙은 Azure Portal에 표시되지 않습니다. [워크로드 모니터 API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)에서만 상태 경고 규칙을 사용하거나 사용하지 않도록 설정할 수 있습니다. 
- Azure Portal에서는 상태 경고에 대해 [Azure Monitor 작업 그룹](../../azure-monitor/platform/action-groups.md)을 할당할 수 없습니다. 알림 설정 API를 통해서만 상태 경고가 발생할 때마다 트리거되도록 작업 그룹을 구성할 수 있습니다. 현재, VM에 대해 발생한 모든 *상태 경고*가 동일한 작업 그룹을 트리거하도록 VM에 대해 작업 그룹을 할당할 수 있습니다. 기존의 Azure 경고와 달리, 각 상태 경고 규칙에 대한 별도의 작업 그룹이라는 개념이 없습니다. 또한 상태 경고가 트리거될 때 메일 또는 SMS 알림을 제공하도록 구성된 작업 그룹만 지원됩니다. 

## <a name="next-steps"></a>다음 단계
가상 머신의 모니터링을 사용하기 위한 요구 사항과 메서드를 이해하려면 [VM용 Azure Monitor 배포](vminsights-onboard.md)를 검토하세요.
