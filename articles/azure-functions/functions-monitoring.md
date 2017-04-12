---
title: "Azure Functions 모니터링 | Microsoft 문서"
description: "Azure Functions를 모니터링하는 방법에 대해 알아봅니다."
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: "Azure 함수, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처"
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/03/2016
ms.author: wesmc
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 831b7cc21ee34f900f7e35885a36f6e0faf38211
ms.lasthandoff: 03/21/2017

---

# <a name="monitoring-azure-functions"></a>Azure Functions 모니터링

## <a name="overview"></a>개요 


각 함수에 대한 **모니터** 탭을 사용하면 함수의 각 실행을 검토할 수 있습니다.

![Azure Functions 모니터 탭](./media/functions-monitoring/monitor-tab.png) 

실행을 클릭하면 기간, 입력 데이터, 오류 및 관련 로그 파일을 검토할 수 있습니다. 이 탭은 함수를 디버그하고 성능을 조정하는 데 유용합니다.


> [!IMPORTANT]
> Azure Functions에 [소비 호스팅 계획](functions-overview.md#pricing)을 사용하는 경우 함수 앱 개요 블레이드의 **모니터링** 타일에서 데이터를 표시하지 않습니다. 이는 플랫폼에서 계산 인스턴스를 동적으로 확장하고 관리하여 이러한 메트릭이 소비 계획에서 의미가 없기 때문입니다. 함수 앱의 사용을 모니터링하려면 이 문서의 지침을 대신 사용해야 합니다.
> 
> 다음 스크린샷에서는 예제를 보여 줍니다.
> 
> ![기본 리소스 블레이드에서 모니터링](./media/functions-monitoring/app-service-overview-monitoring.png)



## <a name="real-time-monitoring"></a>실시간 모니터링

아래와 같이 **라이브 이벤트 스트림**을 클릭하면 실시간 모니터링을 사용할 수 있습니다. 

![모니터 탭의 라이브 이벤트 스트림 옵션](./media/functions-monitoring/monitor-tab-live-event-stream.png)

라이브 이벤트 스트림은 아래와 같이 새 브라우저 탭에서 그래프로 표시됩니다. 

![라이브 이벤트 스트림 예제](./media/functions-monitoring/live-event-stream.png)


> [!NOTE]
> 데이터를 채우지 못하게 하는 알려진 문제점이 있습니다. 이 문제가 발생하면 라이브 이벤트 스트림을 포함하고 있는 브라우저 탭을 닫은 다음 **라이브 이벤트 스트림**을 다시 클릭하여 이벤트 스트림 데이터를 제대로 채울 수 있도록 해야 합니다. 

라이브 이벤트 스트림은 함수에 대한 다음 통계를 그래프로 표시됩니다.

* 초당 시작된 실행
* 초당 완료된 실행
* 초당 실패한 실행
* 평균 실행 시간(밀리초)

이러한 통계는 실시간이지만 실행 데이터의 실제 그래프표시에는 약 10초의 대기 시간이 있을 수 있습니다.






## <a name="monitoring-log-files-from-a-command-line"></a>명령줄에서 로그 파일 모니터링


Azure CLI(Command Line Interface) 또는 PowerShell을 사용하여 로그 파일을 로컬 워크스테이션의 명령줄 세션으로 스트리밍할 수 있습니다.

### <a name="monitoring-function-app-log-files-with-the-azure-cli"></a>Azure CLI로 함수 앱 로그 파일 모니터링

시작하려면 [Azure CLI를 설치](../cli-install-nodejs.md)합니다.

다음 명령 또는 [Azure CLI에서 Azure에 로그인](../xplat-cli-connect.md)에 나오는 다른 옵션을 사용하여 Azure 계정에 로그인합니다.

    azure login

다음 명령을 사용하여 ASM(Azure CLI Service Management) 모드를 사용하도록 설정합니다.

    azure config mode asm

구독이 여러 개인 경우 다음 명령을 사용하여 구독을 나열하고 현재 구독을 함수 앱이 포함된 구독으로 설정합니다.

    azure account list
    azure account set <subscriptionNameOrId>

다음 명령은 함수 앱의 로그 파일을 명령줄로 스트리밍합니다.

    azure site log tail -v <function app name>

### <a name="monitoring-function-app-log-files-with-powershell"></a>PowerShell로 함수 앱 로그 파일 모니터링

시작하려면 [Azure PowerShell을 설치 및 구성](/powershell/azureps-cmdlets-docs)합니다.

다음 명령을 실행하여 Azure 계정을 추가합니다.

    PS C:\> Add-AzureAccount

구독이 여러 개인 경우 다음 명령으로 구독을 나열하여 `IsCurrent` 속성에 따라 올바른 구독이 현재 선택되어 있는지 확인할 수 있습니다.

    PS C:\> Get-AzureSubscription

활성 구독을 함수 앱을 포함하고 있는 구독으로 설정해야 하는 경우 다음 명령을 사용합니다.

    PS C:\> Get-AzureSubscription -SubscriptionName "MyFunctionAppSubscription" | Select-AzureSubscription

다음 명령을 사용하여 로그를 PowerShell 세션으로 스트리밍합니다.

    PS C:\> Get-AzureWebSiteLog -Name MyFunctionApp -Tail

자세한 내용은 [방법: 웹앱의 로그 스트리밍](../app-service-web/web-sites-enable-diagnostic-log.md#streamlogs)을 참조하세요. 

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 리소스를 참조하세요.

* [기능 테스트](functions-test-a-function.md)
* [기능 크기 조정](functions-scale.md)


