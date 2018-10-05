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
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: 819c3e74355cf80c7a998abb8b02b10c9e077059
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47062771"
---
# <a name="known-issues-with-azure-monitor-for-vms"></a>VM용 Azure Monitor의 알려진 문제

VM용 Azure Monitor의 상태 기능과 관련하여 알려진 문제는 다음과 같습니다.

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
- DNS 클라이언트 서비스 상태와 같은 일부 Windows 상태 조건의 정상 상태는 컨텍스트에 따라 서비스 또는 엔터티의 **실행 중**, **사용 가능** 상태로 이미 잠겨 있으므로 이러한 상태 조건에 대한 임계값은 수정할 수 없습니다.  대신, 값이 숫자 4로 표시되며, 이후 릴리스에서는 실제 표시 문자열로 변환됩니다.  
- 일부 Linux 상태 조건에 대한 임계값은 이미 비정상 상태에서 트리거하도록 설정되어 있으므로 논리적 디스크 상태처럼 수정할 수 없습니다.  이러한 조건은 온라인/오프라인 또는 켜기/끄기인지 여부를 나타내며, 1 또는 0 값을 표시하여 동일하게 나타냅니다.
- Azure Monitor -> Virtual Machines -> 상태 -> 미리 선택된 구독 및 리소스 그룹이 있는 모든 목록 보기를 사용하는 동안 리소스 그룹의 리소스 그룹 필터를 업데이트하면 목록 보기에 **결과 없음**이 표시됩니다.  Azure Monitor -> Virtual Machines -> 상태 탭으로 돌아가서 원하는 구독과 리소스 그룹을 선택한 다음, 목록 보기로 이동합니다.

## <a name="next-steps"></a>다음 단계
[VM용 Azure Monitor 등록](monitoring-vminsights-onboard.md)을 검토하여 가상 머신의 모니터링을 사용하기 위한 요구 사항과 방법을 알아보세요.