---
title: Azure의 WebJob, 백그라운드 작업
description: WebJobs에 대해 알아봅니다.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7714b090399b0b184e2e216ff6da7b10f2bf4386
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452274"
---
# <a name="webjobs-run-background-tasks-in-azure-app-service"></a>Azure App Service에서 백그라운드 작업 실행 WebJobs

이 문서에서는 실행 파일 또는 스크립트를 업로드하기 위해 [Azure Portal](https://portal.azure.com)을 사용하여 WebJobs를 배포하는 방법을 보여줍니다. Visual Studio를 사용하여 WebJobs를 개발하고 배포하는 방법에 대한 정보는 [Visual Studio를 사용하여 WebJobs 배포](webjobs-dotnet-deploy-vs.md)를 참조하세요.

## <a name="overview"></a>개요
WebJobs는 웹 앱, API 앱 또는 모바일 앱과 동일한 인스턴스에서 프로그램 또는 스크립트를 실행할 수 있도록 하는 [Azure App Service](index.yml) 기능입니다. 웹 작업을 사용하는 데 추가 비용은 없습니다.

> [!IMPORTANT]
> WebJobs는 Linux의 App Service에 대해서는 아직 지원되지 않습니다.

여러 프로그래밍 작업을 간소화하기 위해 Azure WebJobs SDK를 WebJobs로 사용할 수 있습니다. 자세한 내용은 [WebJobs SDK 정의](https://github.com/Azure/azure-webjobs-sdk/wiki)를 참조하세요.

Azure Functions는 프로그램 및 스크립트를 실행하는 다른 방법을 제공합니다. WebJobs와 Functions 간에 비교는 [Flow, Logic Apps, Functions 및 WebJobs 중에서 선택](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)을 참조하세요.

## <a name="webjob-types"></a>WebJob 형식

다음 표에서는 *연속* 및 *트리거* WebJobs 간의 차이점을 설명합니다.


|계속  |트리거  |
|---------|---------|
| WebJob이 만들어질 때 즉시 시작합니다. 작업을 종료하지 않으려면 프로그램 또는 스크립트는 일반적으로 무한 루프 내에서 작업을 수행합니다. 작업을 종료하는 경우 다시 시작할 수 있습니다. | 수동으로 또는 일정에서 트리거된 경우에만 시작합니다. |
| 웹앱이 실행되는 모든 인스턴스에서 실행됩니다. 필요에 따라 WebJob을 단일 인스턴스로 제한할 수 있습니다. |Azure에서 부하 분산을 위해 선택한 단일 인스턴스에서 실행합니다.|
| 원격 디버깅을 지원합니다. | 원격 디버깅을 지원하지 않습니다.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="add-webjob-to-source-control"></a>원본 제어에 WebJob 추가

응용 프로그램을 사용 하 여 구성 된 소스 제어를 사용 하는 경우 소스 제어 통합의 일부로 Webjobs를 배포 해야 합니다. 소스 제어가 응용 프로그램을 사용 하 여 구성 되 면 WebJob을 Azure Portal에서 추가할 수 없습니다.

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>스크립트 또는 프로그램에 지원되는 파일 형식

다음 파일 형식이 지원됩니다.

* .cmd, .bat, .exe(Windows cmd 사용)
* .ps1(PowerShell 사용)
* .sh(Bash 사용)
* .php(PHP 사용)
* .py(Python 사용)
* .js(Node.js 사용)
* .jar(Java 사용)

## <a name="next-steps"></a>다음 단계

* [WebJob을 만드는](./webjobs-create-ieux.md) 방법 알아보기
* [WebJobs](./webjobs-create-ieux-view-log.md) 의 로그 기록 보기
