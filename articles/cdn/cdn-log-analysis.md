---
title: Azure CDN 사용 패턴 분석 | Microsoft Docs
description: 이 문서에서는 Azure CDN 제품에 사용할 수 있는 다양한 유형의 분석 보고서를 설명합니다.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: rli; v-deasim
ms.openlocfilehash: 3f475c5cc9b766ea9aa5bd39d4a378e8deed5e35
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Azure CDN 사용 패턴 분석

응용 프로그램에 대해 CDN을 사용하도록 설정한 후에는 CDN 사용을 모니터링하고, 전송 상태를 확인하고, 잠재적인 문제를 해결할 수 있습니다. Azure CDN은 다음과 같은 방식으로 이러한 기능을 제공합니다. 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Azure 진단 로그를 통한 코어 분석

코어 분석은 Verizon(표준 및 프리미엄) 및 Akamai(표준) CDN 프로필에 속하는 모든 CDN 끝점에 사용할 수 있습니다. Azure 진단 로그를 사용하면 코어 분석을 Azure 저장소, 이벤트 허브 또는 Log Analytics로 내보낼 수 있습니다. Log Analytics는 사용자 구성 및 사용자 지정이 가능한 그래프가 포함된 솔루션을 제공합니다. 자세한 내용은 [Azure 진단 로그](cdn-azure-diagnostic-logs.md)를 참조하세요.

## <a name="verizon-core-reports"></a>Verizon 코어 보고서

**Verizon의 Azure CDN 표준** 또는 **Verizon의 Azure CDN 프리미엄** 프로필을 사용하는 Azure CDN 사용자는 Verizon 보조 포털에서 Verizon 코어 보고서를 볼 수 있습니다. Verizon 코어 보고서는 Azure Portal에서 **관리** 옵션을 통해 액세스할 수 있고 다양한 그래프 및 보기를 제공합니다. 자세한 내용은 [Verizon의 코어 보고서](cdn-analyze-usage-patterns.md)를 참조하세요.

## <a name="verizon-custom-reports"></a>Verizon 사용자 지정 보고서

**Verizon의 Azure CDN 표준** 또는 **Verizon의 Azure CDN 프리미엄** 프로필을 사용하는 Azure CDN 사용자는 Verizon 보조 포털에서 Verizon 사용자 지정 보고서를 볼 수 있습니다. Verizon 사용자 지정 보고서는 Azure Portal에서 **관리** 옵션을 통해 액세스할 수 있습니다. Verizon 사용자 지정 보고서 페이지는 Azure CDN 프로필에 속한 에지 CName 각각에 전송된 적중 또는 데이터의 수를 보여줍니다. 시간에 따라 데이터를 HTTP 응답 코드 또는 캐시 상태별로 그룹화할 수 있습니다. 자세한 내용은 [Verizon의 사용자 지정 보고서](cdn-verizon-custom-reports.md)를 참조하세요.

## <a name="verizon-premium-reports"></a>Verizon 프리미엄 보고서

**Verizon의 Azure CDN Premium**을 사용하면 다음 보고서에도 액세스할 수 있습니다.
   * [고급 HTTP 보고서](cdn-advanced-http-reports.md)
   * [실시간 통계](cdn-real-time-stats.md)
   * [에지 노드 성능](cdn-edge-performance.md)

