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
ms.devlang: multiple
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 0f4eaaefb7d2080218e19574621a4962e61057c3
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394310"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Azure Functions에 대한 Application Insights 지원 기능

Azure Functions는 ILogger 인터페이스를 통해 사용할 수 있는 Application Insights와의 [기본 제공 통합](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)을 제공합니다. 다음은 현재 지원되는 기능 목록입니다. Azure Functions 가이드에서 [시작](https://github.com/Azure/Azure-Functions/wiki/App-Insights) 방법을 검토하세요.

## <a name="supported-features"></a>지원되는 기능

| Azure 기능                       | V1                | V2(Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.7.2**         |
| | | | 
| **자동 컬렉션**        |                 |                   |               
| &bull; 요청                     | yes             | yes               | 
| &bull; 예외                   | yes             | yes               | 
| &bull; 종속성                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | yes               | 
| | | | 
| **지원되는 기능**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | yes             | yes               | 
| &bull; 샘플링                     | yes             | yes               | 
| &bull; 하트비트                   |                 | yes               | 
| | | | 
| **상관 관계**                       |                   |                   |               
| &bull; ServiceBus                     |                   | yes               | 
| &bull; EventHub                       |                   | yes               | 
| | | | 
| **구성 가능 여부**                      |                   |                   |           
| &bull;완전히 구성 가능합니다.<br/>지침을 보려면 [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852)를 참조하세요.<br/>모든 옵션에 대해서는 [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration)를 참조하세요.               |                   | yes                   | 


## <a name="sampling"></a>샘플링

Azure Functions를 사용하면 기본적으로 구성에서 샘플링을 사용하도록 설정할 수 있습니다. 자세한 내용은 [샘플링 구성](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling)을 참조하세요.
