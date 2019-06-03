---
title: Azure Time Series Insights 환경의 보존 기간을 구성하는 방법 | Microsoft Docs
description: 이 아티클에서는 Azure Time Series Insights 환경의 보존 기간을 구성하는 방법을 설명합니다.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: f5f34983d1818679450249aa40bbb325e743cc57
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239014"
---
# <a name="configuring-retention-in-time-series-insights"></a>Time Series Insights의 보존 기간 구성

이 아티클에서는 Azure Time Series Insights의 **데이터 보존 시간** 및 **저장 제한을 초과하는 동작**을 구성하는 방법을 설명합니다.

## <a name="summary"></a>요약

각 TSI(Time Series Insights) 환경에는 **데이터 보존 시간**을 구성하는 설정이 있습니다. 값의 범위는 1~400일입니다. 데이터는 환경 저장소 용량 또는 보존 기간(1-400) 중 더 빠른 시간을 기준으로 삭제됩니다.

각 TSI 환경에는 **저장 제한을 초과하는 동작**이라는 추가 설정이 있습니다. 이 설정은 환경의 최대 용량에 도달하면 수신 및 삭제 동작을 제어합니다. 두 가지 동작 중에서 선택할 수 있습니다.

- **이전 데이터 삭제**(기본값)
- **수신 일시 중지**

이러한 설정을 더 잘 이해하는 방법에 대한 자세한 내용은 [Time Series Insights의 보존 기간 이해](time-series-insights-concepts-retention.md)를 참조하세요.  

## <a name="configure-data-retention"></a>데이터 보존 구성하기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 기존 Time Series Insights 환경을 찾습니다. Azure Portal의 왼쪽에 있는 메뉴에서 **모든 리소스**를 선택합니다. Time Series Insights 환경을 선택합니다.

1. **설정** 제목 아래에서 **구성**을 선택합니다.

1. **데이터 보존 시간**을 선택하여 슬라이더 막대로 보존 기간을 구성하거나 텍스트 상자에 숫자를 입력합니다.

1. **용량** 설정은 데이터 이벤트의 최대 양과 데이터 저장에 사용되는 총 저장 용량에 영향을 주므로 이 구성을 참고하세요.

1. **저장 제한을 초과하는 동작** 설정을 전환합니다. **이전 데이터 삭제** 또는 **수신 일시 중지** 동작을 선택합니다.

1. **저장**을 선택하여 변경 내용을 구성합니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [Time Series Insights의 보존 기간 이해](time-series-insights-concepts-retention.md)를 참조하세요.