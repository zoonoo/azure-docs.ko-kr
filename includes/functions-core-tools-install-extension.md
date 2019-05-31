---
title: 포함 파일
description: 포함 파일
services: functions
author: craigshoemaker
ms.service: functions
ms.topic: include
ms.date: 09/25/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: fc5b43dcdee394fea023124171fb42c1a18224dc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131439"
---
확장 번들의 설정을 통해 사용할 수 있는 Azure Functions 팀에서 게시 하는 모든 바인딩을 확인 합니다 *host.json* 파일입니다. 로컬 개발에 대 한 최신 버전의 했는지를 확인 [Azure Functions 핵심 도구](../articles/azure-functions/functions-run-local.md#install-the-azure-functions-core-tools)합니다.

확장 번들을 사용 하려면 업데이트 된 *host.json* 파일에 대 한 다음 항목을 포함 하도록 `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

- `id` Microsoft Azure Functions 확장 번들에 대 한 네임 스페이스를 참조 하는 속성입니다.
- `version` 번들의 버전을 참조 합니다.

번들 변경에서 패키지로 번들 버전 증가 합니다. 주 버전 변경이 패키지 번들의 주 버전을 이동 하는 경우에 발생 합니다. 합니다 `version` 속성에서 사용 하는 [버전 범위를 지정 하기 위한 간격 표기법](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards)합니다. 함수 런타임에서 항상 버전 범위 또는 간격에 의해 정의 된 최대 허용 버전을 선택 합니다.

프로젝트에서 확장 번들을 참조 하 되 면 모든 기본 바인딩 함수가 제공 됩니다. 사용할 수 있는 바인딩 합니다 [확장 번들](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) 됩니다.

|패키지  |Version  |
|---------|---------|
|Microsoft.Azure.WebJobs.Extensions.CosmosDB|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.DurableTask|1.8.0|
|Microsoft.Azure.WebJobs.Extensions.EventGrid|2.0.0|
|Microsoft.Azure.WebJobs.Extensions.EventHubs|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SendGrid|3.0.0|
|Microsoft.Azure.WebJobs.Extensions.ServiceBus|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SignalRService|1.0.0|
|Microsoft.Azure.WebJobs.Extensions.Storage|3.0.4|
|Microsoft.Azure.WebJobs.Extensions.Twilio|3.0.0|