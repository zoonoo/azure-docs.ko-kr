---
title: Azure Application Insights의 레거시 엔터프라이즈 가격 책정 요금제 정보 | Microsoft Docs
description: Application Insights에서 원격 분석을 관리하고 비용을 모니터링합니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 6e7591ccf0f21099474a08dda088422c377135f6
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="enterprise-plan-details"></a>엔터프라이즈 계획 세부 정보

Application Insights에는 두 가지 가격 책정 요금제가 있습니다. 기본 요금제는 [기본](app-insights-pricing.md)이라고 하며, 주로 수집된 데이터 볼륨에 대한 추가 비용 및 청구서 없이 엔터프라이즈 요금제와 동일한 기능이 모두 포함됩니다. Operations Management Suite를 사용하는 경우 노드당 요금과 함께 일일 데이터 허용량이 있는 엔터프라이즈 요금제를 선택해야 하며 포함된 허용량 외에 수집된 데이터에 대해 요금을 청구합니다.

해당 통화 및 지역의 현재 가격은 [Application Insights 가격 책정 페이지](http://azure.microsoft.com/pricing/details/application-insights/)를 참조하세요.

## <a name="heres-how-the-enterprise-plan-works"></a>엔터프라이즈 요금제의 작동 방식은 다음과 같습니다.

* 엔터프라이즈 계획의 모든 앱에 대한 원격 분석을 보내는 노드 당 비용을 지불합니다.
 * *노드*는 앱을 호스팅하는 실제/가상 서버 컴퓨터 또는 PaaS(Platform-as-a-Service) 역할 인스턴스입니다.
 * 개발 컴퓨터, 클라이언트 브라우저 및 모바일 장치는 노드로 계산되지 않습니다.
 * 원격 분석을 보내는 여러 구성 요소(예: 웹 서비스 및 백 엔드 작업자)가 앱에 있는 경우 개별적으로 집계됩니다.
 * [라이브 메트릭 스트림](app-insights-live-stream.md) 데이터는 가격 책정에 계산되지 않습니다.* 요금은 구독을 통해 앱 기준이 아니라 노드 기준으로 부과됩니다. 12개 앱에 대해 원격 분석을 보내는 노드가 5개 있는 경우 5개 노드에 대한 요금이 부과됩니다.
* 요금이 매월 견적되지만 노드에서 앱의 원격 분석을 보내는 모든 시간에 대해서만 부과됩니다. 시간당 요금은 견적된 월별 요금/744(월 31일 기준의 시간 수)입니다.
* 시간별로 감지되는 각 노드에 대해 1일 200MB의 데이터 볼륨 할당이 제공됩니다. 사용되지 않은 데이터 할당은 다음 날로 이월되지 않습니다.
 * 엔터프라이즈 가격 책정 옵션을 선택하면 각 구독은 해당 구독의 Application Insights 리소스에 원격 분석을 보내는 노드 수에 따라 일일 데이터 허용량을 받습니다. 따라서 하루 종일 데이터를 전송하는 5개의 노드가 있는 경우 해당 구독의 모든 Application Insights 리소스에 풀링된 1GB 허용량이 적용됩니다. 모든 노드에서 포함된 데이터를 공유하기 때문에 특정 노드에서 다른 노드보다 더 많은 데이터를 보내는 것은 중요하지 않습니다. 지정된 날에 Application Insights 리소스에서 해당 구독의 일일 데이터 할당에 포함된 것보다 많은 데이터를 받으면 GB당 초과 데이터 요금이 적용됩니다. 
 * 일일 데이터 허용량은 각 노드에서 원격 분석을 보내는 날의 시간 수(UTC 사용 시간)를 24로 나눈 값에 200MB를 곱해 계산합니다. 따라서 하루 24시간 중 15시간 동안 원격 분석을 보내는 노드가 4개 있는 경우 해당일의 포함된 데이터는((4 x 15) / 24) x 200MB = 500MB가 됩니다. 데이터 초과분의 가격 조건이 GB당 2.30 달러일 때 노드에서 해당일 1GB의 데이터를 보내는 경우 요금은 1.15달러입니다.
 * 엔터프라이즈 요금제의 일일 허용량은 기본 옵션을 선택한 응용 프로그램과 공유되지 않으며, 사용되지 않은 허용량은 일일 기준으로 이월되지 않습니다. 

## <a name="here-are-some-examples-of-determining-distinct-node-count"></a>식별되는 노드 수를 결정하는 몇 가지 예는 다음과 같습니다.

| 시나리오                               | 일일 총 노드 수 |
|:---------------------------------------|:----------------:|
| 1개 응용 프로그램에서 3개 Azure App Service 인스턴스 및 1개 가상 서버를 사용합니다. | 4 |
| 2개 VM에서 실행되는 3개 응용 프로그램 및 이러한 응용 프로그램의 Application Insights 리소스는 동일한 구독 및 엔터프라이즈 계획에 있습니다. | 2 | 
| Applications Insights 리소스가 동일한 구독에 속한 4개 응용 프로그램이 있습니다. 각 응용 프로그램마다 16 비최대 사용 시간 동안 2개 인스턴스를 실행하고, 8 최대 사용 시간 동안 4개 인스턴스를 실행합니다. | 13.33 | 
| 1개 작업자 역할 및 1개 웹 역할이 부여된 클라우드 서비스에서 각 역할마다 2개 인스턴스를 실행합니다. | 4 | 
| 50개 마이크로 서비스를 실행하는 5개 노드 Service Fabric 클러스터에서 각 마이크로 서비스마다 3개 인스턴스를 실행합니다. | 5|

* 정확한 노드 계수 동작은 응용 프로그램에서 사용하는 Application Insights SDK에 따라 다릅니다. 
  * SDK 버전 2.2 이상에서 Application Insights [핵심 SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 또는 [웹 SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)는 모두 각 응용 프로그램 호스트를 노드(예: 물리적 서버와 VM 호스트의 컴퓨터 이름 또는 클라우드 서비스의 경우 인스턴스 이름)로 보고합니다.  유일한 예외는 [.NET Core](https://dotnet.github.io/) 및 Application Insights 핵심 SDK를 사용하는 응용 프로그램입니다.이 경우 호스트 이름을 사용할 수 없기 때문에 모든 호스트에 대해 하나의 노드만 보고됩니다. 
  * 이전 버전의 SDK에서는 [웹 SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)가 최신 SDK 버전과 마찬가지로 작동하지만, [핵심 SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)는 실제 응용 프로그램 호스트 수에 관계 없이 하나의 노드만 보고합니다. 
  * 응용 프로그램에서 SDK를 사용하여 roleInstance를 사용자 지정 값으로 설정하는 경우 기본적으로 동일한 값이 노드 수를 결정하는 데 사용됩니다. 
  * 클라이언트 컴퓨터 또는 모바일 장치에서 실행되는 앱과 함께 새 SDK 버전을 사용하는 경우 노드 수는 많은 수의 클라이언트 컴퓨터 또는 모바일 장치에서 매우 큰 숫자를 반환할 수 있습니다. 
