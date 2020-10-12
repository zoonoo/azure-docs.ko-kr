---
title: Azure 스프링 클라우드 응용 프로그램 시작, 중지 및 삭제 | Microsoft Docs
description: Azure Spring Cloud 애플리케이션을 시작, 중지 및 삭제하는 방법
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 479daa9a94247ada30c54a9c5df3471035765087
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90908285"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Azure 스프링 클라우드 응용 프로그램 시작, 중지 및 삭제

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

이 가이드에서는 Azure Portal 또는 Azure CLI를 사용 하 여 Azure 스프링 클라우드에서 응용 프로그램의 상태를 변경 하는 방법을 설명 합니다.

## <a name="using-the-azure-portal"></a>Azure Portal 사용

응용 프로그램을 배포한 후 Azure Portal를 사용 하 여 응용 프로그램을 시작, 중지 및 삭제할 수 있습니다.

1. Azure Portal에서 Azure Spring Cloud 서비스 인스턴스로 이동합니다.
1. **애플리케이션 대시보드** 탭을 선택합니다.
1. 상태를 변경하려는 애플리케이션을 선택합니다.
1. 해당 응용 프로그램에 대 한 **개요** 페이지에서 **시작/중지**, **다시 시작**또는 **삭제**를 선택 합니다.

## <a name="using-the-azure-cli"></a>Azure CLI 사용

> [!NOTE]
> Azure CLI를 사용하여 선택적 매개 변수를 사용하고 기본값을 구성할 수 있습니다. [참조 설명서를 참조](/cli/azure/ext/spring-cloud/spring-cloud?view=azure-cli-latest&preserve-view=true)하 여 Azure CLI에 대해 자세히 알아보세요.  

먼저 다음과 같이 Azure CLI에 대 한 Azure 스프링 클라우드 확장을 설치 합니다.

```azurecli
az extension add --name spring-cloud
```

다음으로 Azure CLI 작업을 선택 합니다.

* 애플리케이션을 시작하려면 다음을 수행합니다.

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 애플리케이션을 중지하려면 다음을 수행합니다.

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 애플리케이션을 다시 시작하려면 다음을 수행합니다.

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 애플리케이션을 삭제하려면 다음을 수행합니다.

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
