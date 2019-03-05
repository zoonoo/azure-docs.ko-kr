---
title: Azure Stack에서 모니터링 데이터 사용 | Microsoft Docs
description: Azure Stack에서 모니터링 데이터를 사용 하기 위한 옵션에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2018
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: e2144f293c446a311a9c903ff620c8ef1d1d4efc
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341445"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Azure Stack에서 모니터링 데이터를 사용 하는 방법

*적용 대상: Azure Stack 통합 시스템*

Azure Monitor 파이프라인만을 사용 하 여 한 곳에서 모니터링 데이터 같은 전역 Azure에서 Azure Monitor를 찾을 수 있습니다. 하지만 모든 글로벌 Azure의 모니터링 데이터는 Azure Stack에서 사용할 수 있습니다. 이 문서에서는 프로그래밍 방식으로 서비스에서 모니터링 데이터를 수집할 수는 다양 한 방법의 요약을 찾을 수 있습니다.
 
## <a name="options-for-data-consumption"></a>데이터 사용 옵션

| 데이터 형식 | Category | 지원되는 서비스 | 액세스 방법 |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Azure Monitor 플랫폼 수준 메트릭 | 메트릭 | [Azure Stack에서 Azure Monitor를 사용 하 여 지원 되는 메트릭](azure-stack-metrics-supported.md) | REST API |
| 게스트 OS 메트릭 (예를 들어 성능 카운트)를 계산 합니다. | 메트릭 | Windows 및 Linux Virtual Machines | 저장소 테이블 또는 blob:<br>Windows 또는 Linux Azure 진단 <br>이벤트 허브:<br>Windows Azure 진단 |
| Storage 메트릭 | 메트릭 | Azure Storage | 저장소 테이블:<br>저장소 분석 |
| 활동 로그 | 이벤트 | 모든 Azure 서비스 | REST API:<br>Azure Monitor 이벤트 API |
| 게스트 OS 로그 (예를 들어, IIS, ETW, syslog)를 계산 합니다. | 이벤트 | Windows 및 Linux Virtual Machines | 저장소 테이블 또는 blob:<br>Windows 또는 Linux Azure 진단 <br>이벤트 허브:<br>Windows Azure 진단 |
| 저장소 로그 | 이벤트 | Azure Storage | 저장소 테이블:<br>저장소 분석 |

## <a name="next-steps"></a>다음 단계

에 대해 자세히 알아보세요 [Azure Stack에서 Azure monitor](azure-stack-metrics-azure-data.md)합니다.
