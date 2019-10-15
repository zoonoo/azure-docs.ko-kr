---
title: Azure Spring Cloud에서 영구 스토리지를 사용하는 방법 | Microsoft Docs
description: Azure Spring Cloud에서 영구 스토리지를 사용하는 방법
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 8c57698471d1363438c10e5806f9ed6f1da5333f
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038452"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Azure Spring Cloud에서 영구 스토리지를 사용하는 방법

Azure Spring Cloud는 애플리케이션에 영구 스토리지와 임시 스토리지라는 두 가지 유형의 스토리지를 제공합니다.  Azure Spring Cloud는 각 애플리케이션 인스턴스에 대해 기본적으로 임시 스토리지를 사용하도록 설정합니다. 임시 스토리지는 5GB로 제한되며 해당 기본 탑재 경로는 `/tmp`입니다.

> [!WARNING]
> 애플리케이션 인스턴스를 다시 시작하면 연결된 해당 임시 스토리지가 영구적으로 삭제됩니다.

영구 스토리지는 애플리케이션 범위마다 할당되는 Azure에서 관리되는 파일 공유 컨테이너입니다. 영구 스토리지에 저장된 데이터는 모든 애플리케이션의 모든 인스턴스에서 공유됩니다. Azure Spring Cloud 서비스 인스턴스는 영구 디스크가 활성화된 최대 10개의 애플리케이션을 가질 수 있으며 각 애플리케이션에는 50GB의 영구 스토리지가 제공됩니다. 영구 스토리지에 대한 기본 탑재 경로는 `/persistent`입니다.

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Azure Portal을 사용하여 영구 스토리지 활성화

1. Azure Spring Cloud 서비스 페이지에서 **애플리케이션 대시보드**를 선택한 다음, 영구 스토리지가 필요한 애플리케이션을 선택합니다.
1. **개요** 탭에서 **영구 스토리지** 특성을 찾은 후 **사용 안 함**을 선택합니다.
1. **사용**을 클릭하여 영구 스토리지를 사용하도록 설정하고 **확인** 단추를 선택하여 변경 사항을 적용합니다.

영구 스토리지를 사용하는 경우 해당 크기 및 경로는 **개요** 페이지의 **영구 스토리지** 특성에 표시됩니다.

> [!WARNING]
> 영구 스토리지를 *사용하지 않도록 설정*하면 해당 애플리케이션에 대한 스토리지의 할당이 취소됩니다.  해당 스토리지 계정에 있는 모든 데이터가 손실됩니다. 

## <a name="enable-persistent-storage-using-the-azure-cli"></a>Azure CLI를 사용하여 영구 스토리지 활성화

영구 디스크가 사용하도록 설정된 앱 만들기
 
```azurecli
az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

기존 앱에서 영구 스토리지를 사용하도록 설정합니다.

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

기존 앱에서 영구 스토리지를 사용하지 않도록 설정합니다.

> [!WARNING]
> 영구 스토리지를 사용하지 않도록 설정하면 해당 애플리케이션에 대한 스토리지의 할당이 취소되므로 저장되어 있는 모든 데이터가 영구적으로 손실됩니다. 

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
```

## <a name="next-steps"></a>다음 단계

[애플리케이션 및 서비스 할당량](spring-cloud-quotas.md)에 대해 자세히 알아보거나 [수동으로 애플리케이션의 크기를 조정하는](spring-cloud-tutorial-scale-manual.md) 방법을 알아봅니다.