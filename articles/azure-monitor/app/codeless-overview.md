---
title: 코드 변경 없이 앱 모니터링-Azure Monitor Application Insights에 대 한 자동 계측 Microsoft Docs
description: Azure Monitor Application Insights의 자동 계측 개요-코드 없는 응용 프로그램 성능 관리
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 9ead123338a410daf53569ff577dfc8c728a8ddf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708495"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>자동 계측 또는 코드 없는 연결이란? - Azure Monitor Application Insights

자동 계측 또는 코드 없는 attach를 사용 하면 코드를 변경 하지 않고 Application Insights로 응용 프로그램 모니터링을 사용 하도록 설정할 수 있습니다.  

Application Insights 다양 한 리소스 공급자와 통합 되며 여러 환경에서 작동 합니다. 기본적으로 사용 하도록 설정 하 고, 일부 경우에는 원격 분석을 자동으로 수집 하는 에이전트를 구성 하면 됩니다. 언제 든 지 Application Insights 리소스에 메트릭, 데이터 및 종속성이 표시 되며,이를 통해 잠재적인 문제를 발생 하기 전에 원인을 파악 하 고 종단 간 트랜잭션 보기를 사용 하 여 근본 원인을 분석할 수 있습니다.

## <a name="supported-environments-languages-and-resource-providers"></a>지원 되는 환경, 언어 및 리소스 공급자

통합을 추가 하는 중에 자동 계측 기능 매트릭스가 복잡해 집니다. 아래 표에서는 다양 한 리소스 공급자, 언어 및 환경에 대 한 지원의 현재 상태를 보여 줍니다.

|환경/리소스 공급자          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Windows의 Azure App Service           | GA, OnBD *       | GA, 옵트인      | 진행 중     | 진행 중     | 지원되지 않음   |
|Linux의 Azure App Service             | 해당 없음             | 지원되지 않음   | 진행 중     | 공개 미리 보기  | 지원되지 않음   |
|AKS의 Azure App Service               | 해당 없음             | 디자인에서       | 디자인에서       | 디자인에서       | 지원되지 않음   |
|Azure Functions-기본                | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       |
|Azure Functions Windows-종속성 | 지원되지 않음   | 지원되지 않음   | 공개 미리 보기  | 지원되지 않음   | 지원되지 않음   |
|Azure Kubernetes Service               | 해당 없음             | 디자인에서       | 에이전트를 통해   | 디자인에서       | 지원되지 않음   |
|Azure Vm 창                      | 공개 미리 보기  | 지원되지 않음   | 지원되지 않음   | 지원되지 않음   | 지원되지 않음   |
|온-프레미스 Vm 창                | GA, 옵트인      | 지원되지 않음   | 에이전트를 통해   | 지원되지 않음   | 지원되지 않음   |
|독립 실행형 에이전트-모든 env.            | 지원되지 않음   | 지원되지 않음   | GA              | 지원되지 않음   | 지원되지 않음   |

* OnBD는 기본적으로 설정 되어 있습니다. 지원 되는 환경에서 앱을 배포 하면 Application Insights 자동으로 사용 하도록 설정 됩니다. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

#### <a name="net"></a>.NET
Windows의 Azure App Service에 대 한 응용 프로그램 모니터링은 [.net 응용 프로그램](./azure-web-apps.md?tabs=net) .net에 사용할 수 있으며 기본적으로 사용 하도록 설정 되어 있습니다.

#### <a name="netcore"></a>. Microsoft.netcore.portable.compatibility
모니터링 [. ](./azure-web-apps.md?tabs=netcore) 한 번의 클릭으로 NETCore 응용 프로그램을 사용 하도록 설정할 수 있습니다.

#### <a name="java"></a>Java
Windows에서 App Service의 Java 응용 프로그램 모니터링에 대 한 포털 통합을 현재 사용할 수 없습니다. 그러나 App Service에 앱을 배포 하기 전에 코드를 변경 하지 않고 응용 프로그램에 [java 3.0 독립 실행형 에이전트](./java-in-process-agent.md) Application Insights 추가할 수 있습니다. Application Insights Java 3.0 에이전트가 일반적으로 제공 됩니다.

#### <a name="nodejs"></a>Node.js
현재 포털에서 Windows의 Node.js 응용 프로그램에 대 한 모니터링을 사용 하도록 설정할 수 없습니다. Node.js 응용 프로그램을 모니터링 하려면 [SDK](./nodejs.md)를 사용 합니다.

### <a name="linux"></a>Linux

#### <a name="netcore"></a>. Microsoft.netcore.portable.compatibility
모니터링할입니다. Linux에서 실행 되는 NETCore 응용 프로그램은 [SDK](./asp-net-core.md)를 사용 합니다.

#### <a name="java"></a>Java 
포털에서 Linux의 App Service에 대 한 Java 응용 프로그램 모니터링을 사용 하도록 설정 하는 것은 불가능 하지만 앱을 App Service에 배포 하기 전에 [Application Insights java 3.0 에이전트](./java-in-process-agent.md) 를 앱에 추가할 수 있습니다. Application Insights Java 3.0 에이전트가 일반적으로 제공 됩니다.

#### <a name="nodejs"></a>Node.js
[Linux에서 App Service의 Node.js 응용 프로그램 모니터링](./azure-web-apps.md?tabs=nodejs) 은 공개 미리 보기 상태 이며 모든 지역에서 사용할 수 있는 Azure Portal에서 사용 하도록 설정할 수 있습니다. 

#### <a name="python"></a>Python
SDK를 사용 하 여 [Python 앱 모니터링](./opencensus-python.md) 

## <a name="azure-functions"></a>Azure Functions

기본적으로 Azure Functions에 대 한 기본 모니터링을 사용 하 여 로그, 성능, 오류 데이터 및 HTTP 요청을 수집 합니다. Java 응용 프로그램의 경우 분산 추적을 통해 다양 한 모니터링을 사용 하도록 설정 하 고 종단 간 트랜잭션 세부 정보를 얻을 수 있습니다. Java 용이 기능은 공개 미리 보기 상태 이며 [Azure Portal에서 사용 하도록 설정할](./monitor-functions.md)수 있습니다.

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Azure Kubernetes Service의 코드 없는 계측은 현재 [독립 실행형 에이전트](./java-in-process-agent.md)를 통해 Java 응용 프로그램에 사용할 수 있습니다. 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Azure Windows Vm 및 가상 머신 확장 집합

Azure Vm 및 가상 머신 확장 집합에 대 한 자동 계측은 [.net](./azure-vm-vmss-apps.md) 및 [Java](./java-in-process-agent.md)에서 사용할 수 있습니다.  

## <a name="on-premises-servers"></a>온-프레미스 서버
.NET 응용 프로그램 및 [Java 앱](./java-in-process-agent.md)에 대 한 [온-프레미스 Windows server](./status-monitor-v2-overview.md) 에 대 한 모니터링을 쉽게 사용 하도록 설정할 수 있습니다.

## <a name="other-environments"></a>기타 환경
다양 한 Java 독립 실행형 에이전트는 모든 환경에서 작동 하므로 코드를 계측할 필요가 없습니다. Application Insights를 사용 하도록 설정 하 고 Java 에이전트의 놀라운 기능을 보려면 [가이드를 따르세요](./java-in-process-agent.md) . 에이전트는 공개 미리 보기 상태 이며 모든 지역에서 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [Application Insights 개요](./app-insights-overview.md)
* [애플리케이션 맵](./app-map.md)
* [엔드투엔드 성능 모니터링](../app/tutorial-performance.md)