---
title: 특정 시점에서 키-값 쌍 검색
titleSuffix: Azure App Configuration
description: Azure 앱 구성에서 지정 시간 스냅숏을 사용 하 여 이전 키-값 쌍을 검색 합니다.
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523662"
---
# <a name="point-in-time-snapshot"></a>지정 시간 스냅샷

Azure 앱 구성은 키-값 쌍의 변경 내용에 대 한 기록을 유지 관리 합니다. 이 레코드는 키-값 변경의 타임 라인을 제공 합니다. 모든 키 값의 기록을 다시 생성 하 고 이전 7 일 이내에 언제 든 지 과거 값을 제공할 수 있습니다. 이 기능을 사용 하 여 뒤로 "시간 이동" 하 고 이전 키-값을 검색할 수 있습니다. 예를 들어 응용 프로그램을 이전 구성으로 롤백하려면 최신 배포 이전에 사용 되는 구성 설정을 복구할 수 있습니다.

## <a name="key-value-retrieval"></a>키-값 검색

Azure PowerShell를 사용 하 여 이전 키 값을 검색할 수 있습니다.  `az appconfig revision list`를 사용 하 여 필요한 값을 검색 하는 적절 한 매개 변수를 추가 합니다.  저장소 이름 (`--name {app-config-store-name}`) 또는 연결 문자열 (`--connection-string {your-connection-string}`)을 사용 하 여 Azure 앱 구성 인스턴스를 지정 합니다. 특정 시점 (`--datetime`)을 지정 하 고 반환할 최대 항목 수 (`--top`)를 지정 하 여 출력을 제한 합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

키 값에 대해 기록 된 모든 변경 내용을 검색 합니다.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

키 `environment` 및 레이블 `test` 및 `prod`에 대해 기록 된 모든 변경 내용을 검색 합니다.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

`environment:prod`계층 키 공간에서 기록 된 모든 변경 내용을 검색 합니다.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

특정 시점에서 키 `color`에 대해 기록 된 모든 변경 내용을 검색 합니다.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

키 값에 대해 기록 된 마지막 10 개 변경 내용을 검색 하 고 `key`, `label`및 `last-modified` 타임 스탬프에 대 한 값만 반환 합니다.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [ASP.NET Core 웹앱 만들기](./quickstart-aspnet-core-app.md)  
