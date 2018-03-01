---
title: "Azure Log Analytics의 데이터 보존 비용 관리 | Microsoft Docs"
description: "Azure Portal에서 Log Analytics 작업 영역에 대한 가격 책정 요금제 및 데이터 보존 정책을 변경하는 방법을 알아봅니다."
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 5143abdde715424a41a53bb661db342acf817e0c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="manage-cost-of-data-retention-with-your-log-analytics-workspace"></a>Log Analytics 작업 영역으로 데이터 보존 비용 관리
선택한 요금제에 따라 Log Analytics을 등록할 때 연결된 소스에서 생성된 데이터를 작업 영역에 저장하는 기간이 제한됩니다.  이 문서에서는 여러 다른 기간 동안 이 데이터의 보존 비용에 영향을 미칠 수 있는 고려 사항과 이러한 제한을 구성하는 방법을 중점적으로 설명합니다.   

Log Analytics는 원본 온-프레미스, 클라우드 및 하이브리드 환경에서 대량의 데이터를 사용할 수 있으므로 특정 기간 동안 해당 데이터를 저장하는 비용은 다음 요소에 따라 크게 달라질 수 있습니다.

* 데이터를 수집하는 원본 시스템, 인프라 구성 요소, 클라우드 리소스 등의 수
* 메시지 큐, 로그, 이벤트, 보안 관련 데이터 또는 성능 메트릭과 같이 원본에서 생성된 데이터 형식
* 이러한 원본에서 생성된 데이터의 양 
* 사용하도록 설정된 관리 솔루션의 수, 데이터 원본 및 수집 빈도

> [!NOTE]
> 수집한 데이터의 예상 크기에 대해서는 각 솔루션에 대한 설명서를 참조하세요.   

*무료* 요금제를 사용 중인 경우 데이터는 7일 동안 보존되는 것으로 제한됩니다.  *독립 실행형* 또는 *유료* 계층의 경우 수집된 데이터를 마지막 31일 동안 사용할 수 있습니다.  

*무료* 요금제를 사용하며 허용되는 양이 계속 초과될 경우 이 제한을 초과해서 데이터를 수집하도록 작업 영역을 유료 요금제로 변경할 수 있습니다. 

> [!NOTE]
> 유료 계층에 대해 더 긴 보존 기간을 선택한 경우 요금이 부과됩니다. 언제든지 요금제 유형을 변경할 수 있습니다. 가격 책정에 대한 자세한 내용은 [가격 책정 정보](https://azure.microsoft.com/pricing/details/log-analytics/)를 참조하세요. 

## <a name="change-the-data-retention-period"></a>데이터 보존 기간 변경 

1. [Azure 포털](http://portal.azure.com)에 로그인합니다. 
2. **모든 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택합니다.
3. Log Analytics 구독 창의 목록에서 수정할 작업 영역을 선택 합니다.
4. 작업 영역 페이지의 왼쪽 창에서 **보존**을 클릭합니다.
5. 작업 영역 보존 창에서 슬라이더를 이동하여 일 수를 늘리거나 줄인 다음 **저장**을 클릭합니다.  *무료* 계층에서 작업 중인 경우 데이터 보존 기간을 수정할 수 없으며 이 설정을 제어하기 위해 유료 계층으로 업그레이드해야 합니다.<br><br> ![작업 영역 데이터 보존 설정 변경](media/log-analytics-manage-cost/manage-cost-change-retention.png)

## <a name="next-steps"></a>다음 단계  

수집되는 데이터의 양, 데이터를 전송하는 원본 및 전송되는 여러 데이터 형식을 파악하여 소비 및 비용을 관리하려면 [Log Analytics에서 데이터 사용 현황 분석](log-analytics-usage.md)을 참조하세요.