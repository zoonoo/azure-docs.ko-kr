---
title: 특정 시점의 키-값 쌍 검색
titleSuffix: Azure App Configuration
description: 키-값의 변경 기록을 유지 관리하는 Azure App Configuration에서 특정 시점 스냅샷을 사용하여 이전 키-값 쌍을 검색합니다.
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: fa2dbb11b3b8b9afd90c7f6fe3ffe77e2e57c4e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96929900"
---
# <a name="point-in-time-snapshot"></a>지정 시간 스냅샷

Azure App Configuration은 키-값의 변경 기록을 유지 관리합니다. 이 레코드는 키-값 변경의 타임라인을 제공합니다. 키-값 기록을 다시 생성하고 키 기록 기간(무료 계층 저장소의 경우 7일, 표준 계층 저장소의 경우 30일) 내에 어느 순간이든 과거 값을 제공할 수 있습니다. 이 기능을 사용하면 역방향으로 “시간 이동”하고 이전 키-값을 검색할 수 있습니다. 예를 들어, 애플리케이션을 이전 구성으로 롤백하기 위해 가장 최근 배포 이전에 사용된 구성 설정을 복구할 수 있습니다.

## <a name="key-value-retrieval"></a>키-값 검색

Azure Portal 또는 CLI를 사용하여 과거 키-값을 검색할 수 있습니다. Azure CLI에서 `az appconfig revision list`를 사용하고 적절한 매개 변수를 추가하여 필요한 값을 검색합니다.  저장소 이름(`--name <app-config-store-name>`)을 제공하거나 연결 문자열(`--connection-string <your-connection-string>`)을 사용하여 Azure App Configuration 인스턴스를 지정합니다. 특정 시점(`--datetime`)을 지정하고 반환할 최대 항목 수(`--top`)를 지정하여 출력을 제한합니다.

Azure CLI를 로컬로 설치하지 않은 경우 필요에 따라 [Azure Cloud Shell](../cloud-shell/overview.md)을 사용할 수 있습니다.

키-값의 기록된 모든 변경을 검색합니다.

```azurecli
az appconfig revision list --name <your-app-config-store-name>.
```

키 `environment`와 레이블 `test` 및 `prod`의 기록된 모든 변경을 검색합니다.

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

계층 구조 키 공간 `environment:prod`에서 기록된 모든 변경을 검색합니다.

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

특정 시점에 키 `color`의 기록된 모든 변경을 검색합니다.

```azurecli
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

키-값의 기록된 마지막 10개 변경을 검색하고 `key`, `label`, `last_modified` 타임스탬프의 값만 반환합니다.

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [ASP.NET Core 웹앱 만들기](./quickstart-aspnet-core-app.md)