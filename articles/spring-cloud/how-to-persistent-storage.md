---
title: Azure Spring Cloud에서 영구 스토리지를 사용하는 방법 | Microsoft Docs
description: Azure Spring Cloud에서 영구 스토리지를 사용하는 방법
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 77ccfa08d5e2076a83c5a11df3ce6caf597c2d2f
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108134864"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>Azure Spring Cloud에서 영구 스토리지 사용

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

Azure Spring Cloud는 애플리케이션에 영구 스토리지와 임시 스토리지라는 두 가지 유형의 스토리지를 제공합니다.

기본적으로 Azure Spring Cloud는 각 애플리케이션 인스턴스에 대해 임시 스토리지를 제공합니다. 임시 스토리지는 인스턴스별로 기본 탑재 경로가 /tmp인 5GB로 제한됩니다.

> [!WARNING]
> 애플리케이션 인스턴스를 다시 시작하면 연결된 임시 스토리지가 영구적으로 삭제됩니다.

영구 스토리지는 Azure에서 관리되며 애플리케이션마다 할당되는 파일 공유 컨테이너입니다. 영구적 스토리지에 저장된 데이터는 애플리케이션의 모든 인스턴스에서 공유됩니다. Azure Spring Cloud 인스턴스는 영구 스토리지를 사용하는 애플리케이션을 최대 10개까지 포함할 수 있습니다. 각 애플리케이션에는 50GB의 영구 스토리지가 할당됩니다. 영구 스토리지에 대한 기본 탑재경로는 /persistent입니다.

> [!WARNING]
> 애플리케이션의 영구 스토리지를 사용하지 않도록 설정하면 모든 스토리지의 할당이 취소되고 저장된 모든 데이터가 손실됩니다.

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>Azure Portal을 사용하여 영구 스토리지 활성화

1. Azure Portal **홈** 페이지에서 **모든 리소스** 를 선택합니다.

    >![모든 리소스 아이콘 찾기](media/portal-all-resources.jpg)

1. 영구 스토리지를 필요로 하는 Azure Spring Cloud 리소스를 선택합니다. 이 예제에서는 선택한 애플리케이션을 **upspring** 이라고 합니다.

    > ![애플리케이션을 선택합니다.](media/select-service.jpg)

1. **설정** 제목 아래에서 **앱** 을 선택합니다.

1. Azure Spring Cloud 서비스는 테이블에 표시됩니다.  영구 스토리지를 추가하려는 서비스를 선택합니다. 이 예제에서는 **게이트웨이** 서비스를 선택합니다.

    > ![서비스 선택](media/select-gateway.jpg)

1. 서비스의 구성 페이지에서 **구성** 을 선택합니다.

1. **영구 스토리지** 탭을 선택하고 **사용** 을 선택합니다.

    > ![영구 스토리지 사용하도록 설정](media/enable-persistent-storage.jpg)

영구 스토리지를 사용하도록 설정하면 크기와 경로가 구성 페이지에 표시됩니다.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Azure CLI를 사용하여 영구 스토리지 수정

필요한 경우 Azure CLI에 대한 Spring Cloud 확장을 설치합니다.

```azurecli
az extension add --name spring-cloud
```
기타 작업:

* 영구 스토리지를 사용하는 앱을 만들려면 다음과 같이 합니다.

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* 기존 앱에 영구 스토리지를 사용하도록 설정하려면 다음과 같이 합니다.

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* 기존 앱에서 영구 스토리지를 사용하지 않도록 설정하려면 다음과 같이 합니다.

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```

    > [!WARNING]
    > 애플리케이션의 영구 스토리지를 사용하지 않도록 설정하면 모든 스토리지의 할당이 취소되고 저장된 모든 데이터가 영구적으로 손실됩니다.

## <a name="next-steps"></a>다음 단계

* [애플리케이션 및 서비스 할당량](./quotas.md)에 대해 자세히 알아봅니다.
* [수동으로 애플리케이션 크기를 조정](./how-to-scale-manual.md)하는 방법을 알아봅니다.