---
title: "Azure 진단 구성 스키마 버전 목록 | Microsoft Docs"
description: "Azure Virtual Machines, VM Scale Sets, Service Fabric 및 Cloud Services에서 성능 카운터 컬렉션을 구성하는 데 사용됩니다."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/09/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: ac1ae46a165baad675c6b2445f91e05694eae6f9
ms.openlocfilehash: 5d0894430dc915fc46d753a0b672de3b51ce3888
ms.lasthandoff: 02/22/2017


---
# <a name="list-of-azure-diagnostics-versions"></a>Azure 진단 버전 목록
이 페이지는 Microsoft Azure SDK의 일부로 제공되는 Azure 진단 스키마의 버전의 색인입니다.  

> [!NOTE]
> Azure 진단은 Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric 및 Cloud Services에서 성능 카운터 및 기타 통계를 수집하는 데 사용되는 구성 요소입니다.  이 페이지는 이러한 서비스 중 하나를 사용하는 경우에만 해당됩니다.
>

Azure 진단은 Azure Monitor, Application Insights 및 Log Analytics와 같은 기타 Microsoft 진단 제품과 함께 사용됩니다.

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Azure SDK 및 진단 버전 전달 차트  

|Azure SDK 버전 | Azure 진단 버전 | 모델|  
|------------------|---------------------------|------|  
|1.x               |1.0                         | 플러그 인|  
|2.0 - 2.4         |1.0                         |"|  
|2.5               |1.2                         |확장|  
|2.6               |1.3                         |"|  
|2.7               |1.4                         |"|  
|2.8               |1.5                         |"|  
|2.9               |1.6                         |"|
|2.96              |1.7                         |"|



 Azure 진단 버전 1.0은 처음에는 플러그 인 모델로 제공되었습니다. 따라서 Azure SDK를 설치할 때 함께 제공되는 Azure 진단 버전을 사용할 수 있게 되었습니다.  

 SDK 2.5(진단 버전 1.2)부터는 Azure 진단이 확장 모델로 이동되었습니다. 새 기능을 활용하기 위한 도구는 최신 Azure SDK에서만 사용할 수 있었지만 진단을 사용하는 클라우드 서비스 또는 가상 컴퓨터는 Azure에서 직접 최신 전달 버전을 선택할 수 있습니다.  

 예를 들어 SDK 2.5를 사용하는 사용자는 최신 기능을 사용하는지에 관계없이 진단 1.5를 로드하게 됩니다.  

## <a name="azure-diagnostics-schemas-index"></a>Azure 진단 스키마 색인  
[진단 1.0 구성 스키마](azure-diagnostics-schema-1dot0.md)  

[진단 1.2 구성 스키마](azure-diagnostics-schema-1dot2.md)  

[진단 1.3 이상 구성 스키마](azure-diagnostics-schema-1dot3-and-later.md)  

