---
title: Azure Application Insights - Azure Functions 지원 기능 | Microsoft Docs
description: Azure Functions에 대한 Application Insights 지원 기능
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 06feece050835b2b9188eb702210770b44a6b49c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55185817"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Azure Functions에 대한 Application Insights 지원 기능

Azure Functions는 ILogger 인터페이스를 통해 사용할 수 있는 Application Insights와의 [기본 제공 통합](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)을 제공합니다. 다음은 현재 지원되는 기능 목록입니다. Azure Functions 가이드에서 [시작](https://github.com/Azure/Azure-Functions/wiki/App-Insights) 방법을 검토하세요.

## <a name="supported-features"></a>지원되는 기능

| Azure 기능                       | V1                | V2(Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.7.2**         |
| | | | 
| **자동 컬렉션**        |                 |                   |               
| &bull; 요청                     | 예             | 예               | 
| &bull; 예외                   | 예             | 예               | 
| &bull; 종속성                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | 예               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | 예               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | 예               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | 예               | 
| | | | 
| **지원되는 기능**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | 예             | 예               | 
| &nbsp;&nbsp;&nbsp;&mdash; 컨트롤 채널 보호|                 | 예               | 
| &bull; 샘플링                     | 예             | 예               | 
| &bull; 하트비트                   |                 | 예               | 
| | | | 
| **상관 관계**                       |                   |                   |               
| &bull; ServiceBus                     |                   | 예               | 
| &bull; EventHub                       |                   | 예               | 
| | | | 
| **구성 가능 여부**                      |                   |                   |           
| &bull;완전히 구성 가능합니다.<br/>지침을 보려면 [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852)를 참조하세요.<br/>모든 옵션에 대해서는 [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration)를 참조하세요.               |                   | 예                   | 


## <a name="live-metrics--secure-control-channel"></a>라이브 메트릭 및 컨트롤 채널 보호

지정한 사용자 지정 필터 조건이 Application Insights SDK의 라이브 메트릭 구성 요소에 다시 전송됩니다. 필터는 customerid와 같은 잠재적으로 중요한 정보를 포함할 수 있습니다. 비밀 API 키를 사용해 채널 보안을 유지할 수 있습니다. 해당 지침은 [컨트롤 채널 보호](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)를 참조하세요.

## <a name="sampling"></a>샘플링

Azure Functions를 사용하면 기본적으로 구성에서 샘플링을 사용하도록 설정할 수 있습니다. 자세한 내용은 [샘플링 구성](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling)을 참조하세요.
