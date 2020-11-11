---
title: Power BI 및 Excel과 Log Analytics 통합
description: Log Analytics에서 Power BI로 결과를 보내는 방법
ms.subservice: logs
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: a4e2faf87ac3dddf91e6945343a46b02df72db0a
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507502"
---
# <a name="log-analytics-integration-with-power-bi"></a>Power BI와 Log Analytics 통합

이 문서에서는 Log Analytics에서 Microsoft Power BI에 데이터를 제공 하 여 시각적으로 매력적인 보고서와 대시보드를 만드는 방법에 대해 집중적으로 설명 합니다. 

## <a name="background"></a>배경 

Azure Monitor 로그는 수집 로그에 대 한 종단 간 솔루션을 제공 하는 플랫폼입니다. [Azure Monitor Log Analytics](../platform/data-platform.md#) 는 이러한 로그를 쿼리 하는 인터페이스입니다. Log Analytics를 비롯 한 전체 Azure Monitor 데이터 플랫폼에 대 한 자세한 내용은 [Azure Monitor data platform](../platform/data-platform.md)을 참조 하십시오. 

Microsoft Power BI는 Microsoft의 데이터 시각화 플랫폼입니다. 시작 하는 방법에 대 한 자세한 내용은 [Power BI의 홈 페이지](https://powerbi.microsoft.com/)를 참조 하세요. 


일반적으로 무료 Power BI 기능을 사용 하 여 시각적으로 매력적인 보고서와 대시보드를 통합 하 고 만들 수 있습니다.

고급 기능을 통해 Power BI Pro 또는 프리미엄 계정을 구입 해야 할 수 있습니다. 이러한 기능으로는 다음이 포함됩니다. 
 - 작업 공유 
 - 예약 된 새로 고침
 - Power BI 앱 
 - 데이터 흐름 및 증분 새로 고침 

자세한 내용은 [Power BI 가격 책정 및 기능에 대 한 자세한](https://powerbi.microsoft.com/pricing/) 정보를 참조 하세요. 

## <a name="integrating-queries"></a>쿼리 통합  

Power BI는 [M 쿼리 언어](/powerquery-m/power-query-m-language-specification/) 를 기본 쿼리 언어로 사용 합니다. 

Log Analytics 쿼리를 M으로 내보내고 Power BI에서 직접 사용할 수 있습니다. 성공한 쿼리를 실행 한 후 Log Analytics UI 위쪽 작업 모음의 **내보내기** 단추에서 **Power BI로 내보내기 (M 쿼리)** 를 선택 합니다.

:::image type="content" source="media/log-powerbi-excel/export-query2.png" alt-text="내보내기 옵션 메뉴 풀 다운을 보여 주는 Log Analytics 쿼리" border="true":::

Log Analytics Power BI에서 직접 사용할 수 있는 M 코드를 포함 하는 .txt 파일을 만듭니다.

## <a name="connecting-your-logs-to-a-dataset"></a>데이터 집합에 로그 연결 

Power BI 데이터 집합은 보고 및 시각화에 사용할 수 있는 데이터 원본입니다. Log Analytics 쿼리를 데이터 집합에 연결 하려면 Log Analytics에서 내보낸 M 코드를 Power BI의 빈 쿼리로 복사 합니다. 

자세한 내용은 [데이터 집합 Power BI 이해](/power-bi/service-datasets-understand/)를 참조 하세요. 

## <a name="collect-data-with-power-bi-dataflows"></a>Power BI 데이터 흐름를 사용 하 여 데이터 수집 

Power BI 데이터 흐름를 사용 하 여 데이터를 수집 하 고 저장할 수도 있습니다. 자세한 내용은 [Power BI 데이터 흐름](/power-bi/service-dataflows-overview)를 참조 하세요.

데이터 흐름은 데이터를 수집 하 고 준비 하는 데 도움이 되도록 설계 된 "클라우드 ETL"의 유형입니다. 데이터 집합은 다양 한 엔터티를 연결 하 고 요구 사항에 맞게 모델링할 수 있도록 설계 된 "모델"입니다.

## <a name="incremental-refresh"></a>증분 새로 고침 

Power BI 데이터 집합 및 Power BI 데이터 흐름에는 모두 증분 새로 고침 옵션이 있습니다. Power BI 데이터 흐름 및 Power BI 데이터 집합은이 기능을 지원 하지만이 기능을 사용 하려면 Power BI Premium 필요 합니다.  


증분 새로 고침은 작은 쿼리를 실행 하 고 쿼리를 실행할 때 모든 데이터를 다시 수집 하는 대신 실행 당 적은 양의 데이터를 업데이트 합니다. 많은 양의 데이터를 저장할 수 있지만 쿼리가 실행 될 때마다 새 데이터 증분을 추가 하는 옵션이 있습니다. 이 동작은 오래 실행 되는 보고서에 적합 합니다.

증분 새로 고침은 결과 집합에 *날짜/시간* 필드의 존재를 사용 하는 Power BI 합니다. 증분 새로 고침을 구성 하기 전에 Log Analytics 쿼리 결과 집합에 하나 이상의 *날짜/시간* 필드를 포함 해야 합니다. 

증분 새로 고침을 구성 하는 방법 및 방법에 대 한 자세한 내용은 [Power BI 데이터 집합 및 증분 새로 고침](/power-bi/service-premium-incremental-refresh) [Power BI 데이터 흐름 및 증분 새로](/power-bi/service-dataflows-incremental-refresh)고침을 참조 하세요.

## <a name="reports-and-dashboards"></a>보고서 및 대시보드

Power BI로 데이터가 전송 된 후에는 Power BI를 사용 하 여 보고서 및 대시보드를 만들 수 있습니다.

자세한 내용은 [첫 번째 Power BI 모델 및 보고서를 만드는 방법에 대 한 가이드](/learn/modules/build-your-first-power-bi-report/)를 참조 하세요.  

## <a name="excel-integration"></a>Excel 통합

Power BI에서 사용 되는 것과 동일한 M 통합을 사용 하 여 Excel 스프레드시트와 통합할 수 있습니다. 자세한 내용은 [excel과 통합 하는 방법에 대](https://support.microsoft.com/office/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a) 한이 가이드를 참조 하 여 Log Analytics에서 내보낸 M 쿼리를 붙여넣습니다.

[Log Analytics 및 Excel 통합](log-excel.md) 에서 추가 정보를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Log Analytics 쿼리](log-query-overview.md)를 시작 합니다.
