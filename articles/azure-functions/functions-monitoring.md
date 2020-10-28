---
title: Azure Functions 모니터링
description: Azure Application Insights를 Azure Functions와 함께 사용하여 함수 실행을 모니터링하는 방법을 알아봅니다.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 10/14/2020
ms.custom: devx-track-csharp, fasttrack-edit, contperfq2, devx-track-js
ms.openlocfilehash: 87c31df6ecb92acd5bedaee274f9886383e5c617
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92668738"
---
# <a name="monitor-azure-functions"></a>Azure Functions 모니터링

[Azure Functions](functions-overview.md)는 함수를 모니터링할 수 있도록 [Azure Application Insights](../azure-monitor/app/app-insights-overview.md)와 기본적으로 통합됩니다. 이 문서에서는 Azure Functions 모니터링을 위해 Azure에서 제공 하는 모니터링 기능에 대 한 개요를 제공 합니다.

Application Insights는 로그, 성능 및 오류 데이터를 수집 합니다. 성능 변칙을 자동으로 감지 하 고 강력한 분석 도구를 사용 하 여 더 쉽게 문제를 진단 하 고 함수 사용 방법을 더 잘 이해할 수 있습니다. 이러한 도구는 함수에 대 한 성능 및 사용 편의성을 지속적으로 개선 하는 데 도움이 되도록 설계 되었습니다. 로컬 함수 앱 프로젝트를 개발할 때도 Application Insights을 사용할 수 있습니다. 자세한 내용은 [Application Insights란?](../azure-monitor/app/app-insights-overview.md)을 참조하세요.

Application Insights 계측이 Azure Functions에 기본 제공 되므로 함수 앱을 Application Insights 리소스에 연결 하려면 유효한 계측 키가 필요 합니다. Azure에서 함수 앱 리소스를 만들 때 응용 프로그램 설정에 계측 키가 추가 됩니다. 함수 앱에 이 키가 아직 없는 경우 [수동으로 설정](configure-monitoring.md#enable-application-insights-integration)할 수 있습니다.  

## <a name="application-insights-pricing-and-limits"></a>Application Insights 가격 책정 및 제한

무료로 처리 되는 데이터의 양에 대 한 일일 한도를 제공 하는 Azure Functions와 Application Insights 통합을 사용해 볼 수 있습니다.

개발 하는 동안 Application Insights를 사용 하도록 설정 하면 테스트 중에이 제한에 도달할 수 있습니다. Azure는 일일 한도에 가까워지면 포털 및 이메일 알림을 제공합니다. 이러한 경고를 놓치고 제한에 도달하면 Application Insights 쿼리에 새 로그가 표시되지 않습니다. 불필요하게 문제 해결에 시간을 낭비하는 일이 없도록 이러한 제한을 잘 알고 있어야 합니다. 자세한 내용은 [Application Insights에서 가격 책정 및 데이터 볼륨 관리](../azure-monitor/app/pricing.md)를 참조하세요.

> [!IMPORTANT]
> Application Insights에는 최대 부하 시 실행이 완료될 때 원격 분석 데이터를 너무 많이 생성하지 않도록 방지하는 [샘플링](../azure-monitor/app/sampling.md) 기능이 포함되어 있습니다. 샘플링은 기본적으로 사용하도록 설정됩니다. 데이터가 누락된 것 같으면 샘플링 설정을 특정 모니터링 시나리오에 맞게 조정하면 됩니다. 자세한 내용은 [샘플링 구성](configure-monitoring.md#configure-sampling)을 참조하세요.

함수 앱에 사용할 수 있는 Application Insights 기능의 전체 목록은 [Azure Functions에 대한 Application Insights 지원 기능](../azure-monitor/app/azure-functions-supported-features.md)에 자세히 설명되어 있습니다.

## <a name="application-insights-integration"></a>Application Insights 통합

일반적으로 함수 앱을 만들 때 Application Insights 인스턴스를 만듭니다. 이 경우 통합에 필요한 계측 키는 *APPINSIGHTS_INSTRUMENTATIONKEY* 라는 응용 프로그램 설정으로 이미 설정 되어 있습니다. 일부 이유로 함수 앱에 계측 키가 설정 되지 않은 경우 [Application Insights 통합을 사용 하도록 설정](configure-monitoring.md#enable-application-insights-integration)해야 합니다.  

## <a name="collecting-telemetry-data"></a>원격 분석 데이터 수집

Application Insights 통합을 사용 하도록 설정 하면 원격 분석 데이터가 연결 된 Application Insights 인스턴스로 전송 됩니다. 이 데이터에는 함수 호스트에 의해 생성 된 로그, 함수 코드에서 작성 된 추적 및 성능 데이터가 포함 됩니다. 

>[!NOTE]
>함수 및 함수 호스트의 데이터 외에도 [함수 크기 조정 컨트롤러](#scale-controller-logs)에서 데이터를 수집할 수 있습니다.   

### <a name="log-levels-and-categories"></a>로그 수준 및 범주

응용 프로그램 코드에서 추적을 작성 하는 경우 추적에 로그 수준을 할당 해야 합니다. 로그 수준은 추적에서 수집 되는 데이터의 양을 제한 하는 방법을 제공 합니다.  

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

로그 수준에 대 한 자세한 내용은 [로그 수준 구성](configure-monitoring.md#configure-log-levels)을 참조 하세요.

기록 된 항목을 범주에 할당 하 여 함수 앱의 특정 원본에서 생성 된 원격 분석을 보다 효과적으로 제어할 수 있습니다. 범주를 통해 수집 된 데이터에 대 한 분석을 보다 쉽게 실행할 수 있습니다. 함수 코드에서 작성 된 추적은 함수 이름에 따라 개별 범주에 할당 됩니다. 범주에 대 한 자세한 내용은 [범주 구성](configure-monitoring.md#configure-categories)을 참조 하세요.

### <a name="custom-telemetry-data"></a>사용자 지정 원격 분석 데이터

[C #](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions) 및 [JAVASCRIPT](functions-reference-node.md#log-custom-telemetry)에서 Application Insights SDK를 사용 하 여 사용자 지정 원격 분석 데이터를 작성할 수 있습니다.

### <a name="dependencies"></a>종속성

버전 2.x의 함수부터 런타임은 특정 클라이언트 Sdk를 사용 하는 바인딩에 대 한 종속성에 대 한 데이터를 자동으로 수집 합니다. Application Insights는 다음 종속성에 대 한 데이터를 수집 합니다.

+ Azure Cosmos DB 
+ Azure Event Hubs
+ Azure Service Bus
+ Azure Storage 서비스 (Blob, 큐 및 테이블)

를 사용 하 여 HTTP 요청 및 데이터베이스 호출 `SqlClient` 도 캡처됩니다. Application Insights에서 지 원하는 종속성의 전체 목록은 [자동으로 추적](../azure-monitor/app/asp-net-dependencies.md#automatically-tracked-dependencies)되는 종속성을 참조 하세요.

Application Insights은 수집 된 종속성 데이터의 _응용 프로그램 맵을_ 생성 합니다. 다음은 큐 저장소 출력 바인딩을 사용 하는 HTTP 트리거 함수의 응용 프로그램 맵 예제입니다.  

![종속성이 있는 응용 프로그램 맵](./media/functions-monitoring/app-map.png)

종속성은 수준에서 작성 됩니다 `Information` . 이상에서 필터링 하 `Warning` 는 경우 종속성 데이터가 표시 되지 않습니다. 또한 자동 종속성 컬렉션은 사용자가 아닌 범위에서 발생 합니다. 종속성 데이터를 캡처하려면 수준이 `Information` 호스트의 사용자 범위 () 이상으로 설정 되어 있는지 확인 합니다 `Function.<YOUR_FUNCTION_NAME>.User` .

자동 종속성 데이터 수집 외에도 언어별 Application Insights Sdk 중 하나를 사용 하 여 로그에 사용자 지정 종속성 정보를 쓸 수 있습니다. 사용자 지정 종속성을 작성 하는 방법에 대 한 예제는 다음 언어 관련 예제 중 하나를 참조 하세요.

+ [C# 함수의 사용자 지정 원격 분석 로깅](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions)
+ [JavaScript 함수의 사용자 지정 원격 분석 로깅](functions-reference-node.md#log-custom-telemetry) 

## <a name="writing-to-logs"></a>로그에 쓰기 

로그에 쓰는 방법과 사용 하는 Api는 함수 앱 프로젝트의 언어에 따라 달라 집니다.   
함수에서 로그를 작성 하는 방법에 대 한 자세한 내용을 보려면 해당 언어에 대 한 개발자 가이드를 참조 하세요.

+ [C # (.NET 클래스 라이브러리)](functions-dotnet-class-library.md#logging)
+ [Java](functions-reference-java.md#logger)
+ [JavaScript](functions-reference-node.md#write-trace-output-to-logs) 
+ [PowerShell](functions-reference-powershell.md#logging)
+ [Python](functions-reference-python.md#logging)

## <a name="streaming-logs"></a>스트리밍 로그

응용 프로그램을 개발 하는 동안 Azure에서 실행할 때 거의 실시간으로 로그에 기록 되는 항목을 확인 하는 것이 좋습니다.

함수 실행에 의해 생성 되는 로그 데이터의 스트림을 확인 하는 방법에는 두 가지가 있습니다.

* **기본 제공 로그 스트리밍** : App Service 플랫폼을 사용하여 애플리케이션 로그 파일의 스트림을 볼 수 있습니다. 이 스트림은 [로컬 개발](functions-develop-local.md) 중 및 포털에서 **테스트** 탭을 사용할 때 표시 되는 출력에 해당 합니다. 모든 로그 기반 정보가 표시됩니다. 자세한 내용은 [로그 스트리밍](../app-service/troubleshoot-diagnostic-logs.md#stream-logs)을 참조하세요. 이 스트리밍 방법은 단일 인스턴스만 지원하며, 사용 계획을 사용하는 Linux에서 실행되는 앱에는 사용할 수 없습니다.

* **라이브 메트릭 스트림** : 함수 앱이 [Application Insights에 연결](configure-monitoring.md#enable-application-insights-integration)된 경우 [라이브 메트릭 스트림](../azure-monitor/app/live-stream.md)를 사용 하 여 Azure Portal에서 거의 실시간으로 로그 데이터 및 기타 메트릭을 볼 수 있습니다. 사용 계획을 사용하는 여러 인스턴스 또는 Linux에서 실행되는 함수를 모니터링할 때 이 방법을 사용합니다. 이 방법은 [샘플링된 데이터](configure-monitoring.md#configure-sampling)를 사용합니다.

로그 스트림은 포털과 대부분의 로컬 개발 환경에서 볼 수 있습니다. 로그 스트림을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [Azure Functions에서 스트리밍 실행 로그 사용](streaming-logs.md)을 참조 하세요.

## <a name="diagnostic-logs"></a>진단 로그

_이 기능은 미리 보기 상태입니다._ 

Application Insights를 사용 하 여 원격 분석 데이터를 장기 저장소 또는 다른 analysis services로 내보낼 수 있습니다.  

또한 함수는 Azure Monitor와 통합 되기 때문에 진단 설정을 사용 하 여 Azure Monitor 로그를 비롯 한 다양 한 대상에 원격 분석 데이터를 보낼 수도 있습니다. 자세한 내용은 [Azure Monitor 로그를 사용한 Azure Functions 모니터링](functions-monitor-log-analytics.md)을 참조하세요.

## <a name="scale-controller-logs"></a>컨트롤러 로그 크기 조정

_이 기능은 미리 보기 상태입니다._ 

[Azure Functions 크기 조정 컨트롤러](./functions-scale.md#runtime-scaling) 는 앱이 실행 되는 Azure Functions 호스트의 인스턴스를 모니터링 합니다. 이 컨트롤러는 현재 성능에 따라 인스턴스를 추가 하거나 제거할 시기에 대 한 결정을 내립니다. 크기 조정 컨트롤러는 함수 앱에 대 한 크기 조정 컨트롤러의 결정을 보다 잘 이해 하기 위해 Application Insights 하기 위해 로그를 내보낼 수 있습니다. 다른 서비스에서 분석을 위해 생성 된 로그를 Blob 저장소에 저장할 수도 있습니다. 

이 기능을 사용 하도록 설정 하려면 라는 응용 프로그램 설정을 `SCALE_CONTROLLER_LOGGING_ENABLED` 함수 앱 설정에 추가 합니다. 방법을 알아보려면 [크기 조정 컨트롤러 로그 구성](configure-monitoring.md#configure-scale-controller-logs)을 참조 하세요.

## <a name="report-issues"></a>문제 보고

Application Insights의 Functions 통합 문제를 보고하거나 제안 사항 또는 요청 사항을 보내려면 [GitHub에서 문제를 만듭니다](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core 로깅](/aspnet/core/fundamentals/logging/)
