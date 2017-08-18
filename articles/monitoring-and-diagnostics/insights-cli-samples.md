---
title: "Azure Monitor CLI 1.0 빠른 시작 샘플 | Microsoft Docs"
description: "Azure Monitor 기능에 대한 샘플 CLI 1.0 명령입니다. Azure Monitor는 Cloud Services, Virtual Machines 및 Web Apps의 크기를 자동으로 조정하고, 구성된 원격 분석 데이터 값을 기반으로 경고 알림을 보내거나 웹 URL을 호출할 수 있는 Microsoft Azure 서비스입니다."
author: kamathashwin
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1653aa81-0ee6-4622-9c65-d4801ed9006f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: ashwink
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: ec4512500dc3c77a40d2ebd1e6b460d5bb005811
ms.contentlocale: ko-kr
ms.lasthandoff: 08/10/2017

---
# <a name="azure-monitor--cross-platform-cli-10-quick-start-samples"></a>Azure Monitor 플랫폼 간 CLI 1.0 빠른 시작 샘플
이 문서에서는 Azure Monitor 기능에 액세스하는 데 유용한 샘플 CLI(명령줄 인터페이스) 명령을 보여 줍니다. Azure Monitor를 통해 Cloud Services, Virtual Machines 및 Web Apps의 크기를 자동으로 조정하고, 구성된 원격 분석 데이터의 값을 기반으로 경고 알림을 보내거나 웹 URL을 호출할 수 있습니다.

> [!NOTE]
> Azure Monitor는 2016년 9월 25일까지는 "Azure Insights"로 지칭했던 제품의 새로운 이름입니다. 하지만 네임스페이스와 아래 명령에서는 "insights"를 계속 포함합니다.
> 
> 

## <a name="prerequisites"></a>필수 조건
이미 Azure CLI를 설치하지 않은 경우 [Azure CLI 설치](../cli-install-nodejs.md)를 참조하세요. Azure CLI를 잘 모르는 경우 자세한 내용은 [Azure Resource Manager에서 Mac, Linux 및 Windows용 Azure CLI 사용](../xplat-cli-azure-resource-manager.md)을 참조하세요.

Windows의 경우 [Node.js 웹 사이트](https://nodejs.org/)에서 npm을 설치합니다. 설치를 완료한 후 관리자 권한으로 실행 권한으로 CMD.exe를 사용하여 npm이 설치된 폴더에서 다음을 실행합니다.

```console
npm install azure-cli --global
```

원하는 폴더/위치로 이동하여 명령줄에 다음을 입력합니다.

```console
azure help
```

## <a name="log-in-to-azure"></a>Azure에 로그인
첫 번째 단계에서는 Azure 계정에 로그인합니다.

```console
azure login
```

이 명령을 실행한 후 화면의 지시에 따라 로그인해야 합니다. 그러면 계정, 테넌트 ID 및 기본 구독 ID가 표시됩니다. 모든 명령은 기본 구독의 컨텍스트에서 작동합니다.

현재 구독에 대한 세부 정보를 나열하려면 다음 명령을 사용합니다.

```console
azure account show
```

작업 중인 컨텍스트를 다른 구독으로 변경하려면 다음 명령을 사용합니다.

```console
azure account set "subscription ID or subscription name"
```

Azure Resource Manager 및 Azure Monitor 명령을 사용하려면 Azure Resource Manager 모드에 있어야 합니다.

```console
azure config mode arm
```

지원되는 모든 Azure Monitor 명령 목록을 보려면 다음을 수행합니다.

```console
azure insights
```

## <a name="view-activity-log-for-a-subscription"></a>구독에 대한 활동 로그 보기
활동 로그 이벤트 목록을 보려면 다음을 수행합니다.

```console
azure insights logs list [options]
```

사용 가능한 모든 옵션을 보려면 다음을 수행합니다.

```console
azure insights logs list -help
```

다음은 resourceGroup별 로그를 나열하는 예제입니다.

```console
azure insights logs list --resourceGroup "myrg1"
```

호출자별 로그를 나열하는 예제

```console
azure insights logs list --caller "myname@company.com"
```

시작 날짜 및 종료 날짜 내 리소스 유형에 대한 호출자별 로그를 나열하는 예제

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>경고 작업
이 섹션의 정보를 사용하여 경고 작업을 수행할 수 있습니다.

### <a name="get-alert-rules-in-a-resource-group"></a>리소스 그룹의 경고 규칙 가져오기
```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>메트릭 경고 규칙 만들기
```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-webtest-alert-rule"></a>웹 테스트 경고 규칙 만들기
```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>경고 규칙 삭제
```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>로그 프로필
이 섹션의 정보를 사용하여 로그 프로필 작업을 수행할 수 있습니다.

### <a name="get-a-log-profile"></a>로그 프로필 가져오기
```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>보존하지 않는 로그 프로필 추가
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>로그 프로필 제거
```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>보존하는 로그 프로필 추가
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>보존하는 로그 프로필 및 이벤트 허브 추가
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>진단
이 섹션의 정보를 사용하여 진단 설정 작업을 수행할 수 있습니다.

### <a name="get-a-diagnostic-setting"></a>진단 설정 가져오기
```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>진단 설정 사용 안 함
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>보존하지 않는 진단 설정 사용
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>Autoscale
이 섹션의 정보를 사용하여 자동 크기 조정 설정 작업을 수행할 수 있습니다. 이러한 예제를 수정해야 합니다.

### <a name="get-autoscale-settings-for-a-resource-group"></a>리소스 그룹에 대한 자동 크기 조정 설정 가져오기
```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>리소스 그룹의 이름으로 자동 크기 조정 설정 가져오기
```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>자동 크기 조정 설정 지정
```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```

