---
title: 시점에서 키-값 쌍 검색
titleSuffix: Azure App Configuration
description: Azure 앱 구성에서 시간 스냅숏을 사용하여 이전 키-값 쌍을 검색합니다.
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523662"
---
# <a name="point-in-time-snapshot"></a>지정 시간 스냅샷

Azure 앱 구성은 키-값 쌍에 대한 변경 내용 레코드를 유지 관리합니다. 이 레코드는 키-값 변경 의 타임라인을 제공합니다. 키 값의 기록을 재구성하고 이전 7일 이내에 언제든지 과거 값을 제공할 수 있습니다. 이 기능을 사용하면 뒤로 "시간 이동"을 하고 이전 키 값을 검색할 수 있습니다. 예를 들어 응용 프로그램을 이전 구성으로 롤백하기 위해 가장 최근 배포 전에 사용된 구성 설정을 복구할 수 있습니다.

## <a name="key-value-retrieval"></a>키-값 검색

Azure PowerShell을 사용하여 과거 키 값을 검색할 수 있습니다.  을 `az appconfig revision list`사용하여 적절한 매개 변수를 추가하여 필요한 값을 검색합니다.  저장소 이름 ()`--name {app-config-store-name}`또는 연결 문자열 ()을`--connection-string {your-connection-string}`사용 하 여 Azure 앱 구성 인스턴스를 지정 합니다. 특정 시점()`--datetime`및 반환할 항목의 최대`--top`수()를 지정하여 출력을 제한합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

키-값에 기록된 모든 변경 내용을 검색합니다.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

키 `environment` 및 레이블 및 `test` `prod`에 대해 기록된 모든 변경 내용을 검색합니다.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

계층 적 키 공간에서 `environment:prod`기록된 모든 변경 내용을 검색합니다.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

특정 시점에 키에 `color` 대해 기록된 모든 변경 내용을 검색합니다.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

키-값에 기록된 마지막 10개의 변경 내용을 `key`검색하고 에 `label`대한 값만 반환합니다. `last-modified`

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [ASP.NET Core 웹앱 만들기](./quickstart-aspnet-core-app.md)  
