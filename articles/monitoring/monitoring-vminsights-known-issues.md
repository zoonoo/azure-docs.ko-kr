---
title: VM용 Azure Monitor의 알려진 문제 | Microsoft Docs
description: VM용 Azure Monitor는 Azure VM 운영 체제의 상태 및 성능 모니터링뿐만 아니라 응용 프로그램 구성 요소 및 종속성의 자동 검색도 다른 리소스와 결합하고, 이러한 항목 간의 통신을 매핑하는 Azure의 솔루션입니다. 이 문서에서는 알려진 문제에 대해 설명합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2018
ms.author: magoedte
ms.openlocfilehash: 6d1f1d1ae07ec32262f655fd6ed7205a70e252f4
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385094"
---
# <a name="known-issues-with-azure-monitor-for-vms"></a>VM용 Azure Monitor의 알려진 문제

VM용 Azure Monitor의 상태 기능과 관련하여 알려진 문제는 다음과 같습니다.

- 단일 VM에서 온보딩이 시작되고 완료된 경우에도 상태 기능은 Log Analytics 작업 영역에 연결된 모든 VM에 온보딩됩니다.
- Azure VM이 제거되거나 삭제되어 더 이상 존재하지 않는 경우 해당 VM은 3-7일 동안 VM 목록 보기에 표시됩니다. 또한 제거되거나 삭제된 VM의 상태를 클릭하면 **상태 진단** 보기가 실행되고, 이어서 로드 루프로 들어갑니다. 삭제된 VM 이름을 선택하면 해당 VM이 삭제되었다는 메시지가 있는 블레이드가 시작됩니다.
- 이 릴리스에서는 상태 조건의 기간 및 빈도를 수정할 수 없습니다. 
- 상태 조건은 비활성화할 수 없습니다. 
- 온보딩 후 데이터가 Azure Monitor -> Virtual Machines -> 상태 또는 VM 리소스 블레이드 -> 인사이트에 표시되기까지 시간이 걸릴 수 있습니다
- 상태 진단 환경에서는 다른 보기보다 더 빠르게 업데이트하므로 보기 간에 전환할 때 정보 지연이 발생할 수 있습니다.  
- VM용 Azure Monitor 상태에 제공되는 경고 요약은 모니터링되는 Azure VM에서 검색된 상태 문제에 대해 실행된 경고에만 해당됩니다.
- 단일 VM의 **경고 목록** 보기 페이지와 Azure Monitor에서는 지난 30일 동안 모니터 조건이 "실행됨"으로 설정된 경고를 보여 줍니다.  이러한 경고는 구성할 수 없습니다. 그러나 요약을 클릭한 후 **경고 목록** 보기 페이지가 시작되면 모니터 조건과 시간 범위 모두에 대한 필터 값을 변경할 수 있습니다.
- **경고 목록** 보기 페이지에서 솔루션과 관련된 특정 구성인 **리소스 종류**, **리소스** 및 **서비스 모니터링** 필터는 수정하지 않는 것이 좋습니다(이 목록 보기에는 Azure Monitor -> 경고 목록 보기와 비교하여 일부 추가 필드가 표시됨).    
- Linux VM의 **상태 진단** 보기에는 사용자 정의 VM 이름 대신 VM의 전체 도메인 이름이 있습니다.
- VM을 종료하면 상태 조건 중 일부가 위험 상태로 업데이트되고, 나머지 일부는 VM의 순 상태가 위험 상태에 있는 정상 상태로 업데이트됩니다.
- 상태 경고 심각도는 수정할 수 없으며, 사용하거나 사용하지 않도록 설정할 수만 있습니다.  또한 일부 심각도는 상태 조건의 상태에 따라 업데이트됩니다.
- 상태 조건 인스턴스의 설정을 수정하면 VM에서 동일한 유형의 모든 상태 조건 인스턴스에 대해 동일한 설정이 수정됩니다. 예를 들어 C: 논리 디스크에 해당하는 사용 가능한 디스크 공간 상태 조건 인스턴스의 임계값이 수정되면 이 임계값은 동일한 VM에 대해 검색 및 모니터링되는 다른 모든 논리 디스크에 적용됩니다.   
- 정상 상태가 이미 **실행 중** 또는 **사용 가능**으로 설정되어 있으므로 Windows VM을 대상으로 하는 다음 상태 조건에 대한 임계값은 수정할 수 없습니다. [워크로드 모니터 API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager)에서 쿼리할 때 다음과 같은 경우 정상 상태에는 서비스 또는 엔터티에 대해 *threshold* 값이 **4**인 **LessThan** 또는 **GreaterThan**의 *comparisonOperator* 값이 표시됩니다.
   - DNS 클라이언트 서비스 상태 – 서비스가 실행 중이 아님 
   - DHCP 클라이언트 서비스 상태 – 서비스가 실행 중이 아님 
   - RPC 서비스 상태 – 서비스가 실행 중이 아님 
   - Windows 방화벽 서비스 상태 – 서비스가 실행 중이 아님
   - Windows 이벤트 로그 서비스 상태 – 서비스가 실행 중이 아님 
   - 서버 서비스 상태 – 서비스가 실행 중이 아님 
   - Windows 원격 관리 서비스 상태 – 서비스가 실행 중이 아님 
   - 파일 시스템 오류 또는 손상 - 논리 디스크를 사용할 수 없음

- 정상 상태가 이미 **true**로 설정되어 있으므로 다음 Linux 상태 조건에 대한 임계값은 수정할 수 없습니다.  워크로드 모니터링 API에서 엔터티에 대해 쿼리할 때 정상 상태에는 컨텍스트에 따라 **LessThan** 및 *threshold* 값이 **1**인 *comparisonOperator*가 표시됩니다.
   - 논리 디스크 상태 - 논리 디스크가 온라인/사용 가능이 아님
   - 디스크 상태 - 디스크가 온라인/사용 가능이 아님
   - 네트워크 어댑터 상태 - 네트워크 어댑터가 사용 안 함임  

- Windows의 **총 CPU 사용률** 상태 조건은 포털에서 **4와 같지 않음**이라는 임계값을 표시하고, CPU 사용률이 95% 이상이고 시스템 큐 길이가 15보다 클 때 워크로드 모니터링 API에서 쿼리된 경우를 나타냅니다. 이 상태 조건은 이 릴리스에서 수정할 수 없습니다.  
- 포털 또는 워크로드 모니터 API가 즉시 업데이트될 수 있더라도 임계값 업데이트와 같은 구성 변경을 적용하는 데 최대 30분이 걸립니다.  
- 개별 프로세서 및 논리 프로세서 수준 상태 조건은 Windows에서 사용할 수 없으며, Windows VM에는 **총 CPU 사용률**만 사용할 수 있습니다.  
- 각 상태 조건에 대해 정의된 경고 규칙은 Azure Portal에서 공개되지 않습니다. [워크로드 모니터 API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager)에서만 상태 경고 규칙을 사용하거나 사용하지 않도록 구성할 수 있습니다.  
- Azure Portal에서는 상태 경고에 대한 [Azure Monitor 작업 그룹](../monitoring-and-diagnostics/monitoring-action-groups.md)을 할당할 수 없습니다. 알림 설정 API를 사용하여 상태 경고가 발생할 때마다 트리거되도록 작업 그룹을 구성해야 합니다. 현재 VM에 대해 작업 그룹을 할당할 수 있습니다. 따라서 VM에 대해 발생된 모든 *상태 경고*에서 동일한 작업 그룹을 트리거했습니다. 기존의 Azure 경고와 같이 모든 상태 경고 규칙에 대해 별도의 작업 그룹이라는 개념이 없습니다. 또한 상태 경고가 트리거되면 이메일 또는 SMS를 보내 알리도록 구성된 작업 그룹만 지원됩니다. 

## <a name="next-steps"></a>다음 단계
[VM용 Azure Monitor 등록](monitoring-vminsights-onboard.md)을 검토하여 가상 머신의 모니터링을 사용하기 위한 요구 사항과 방법을 알아보세요.