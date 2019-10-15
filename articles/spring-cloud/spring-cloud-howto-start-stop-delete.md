---
title: Azure Spring Cloud 애플리케이션을 시작, 중지 및 삭제하는 방법 | Microsoft Docs
description: Azure Spring Cloud 애플리케이션을 시작, 중지 및 삭제하는 방법
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: f7f76644d13c20704d2c3bd908176ac452df2a20
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038442"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>Azure Spring Cloud 애플리케이션을 시작, 중지 및 삭제하는 방법

이 가이드에서는 Azure Portal 또는 CLI를 사용하여 Azure Spring Cloud에서 애플리케이션의 상태를 변경하는 방법을 설명합니다.

## <a name="using-the-azure-portal"></a>Azure Portal 사용

애플리케이션을 배포한 후에는 Azure Portal를 사용하여 **시작**, **중지** 및 **삭제**할 수 있습니다.

1. Azure Portal에서 Azure Spring Cloud 서비스 인스턴스로 이동합니다.
1. **애플리케이션 대시보드** 탭을 선택합니다.
1. 상태를 변경하려는 애플리케이션을 선택합니다.
2. 해당 애플리케이션의 **개요** 페이지에서 **시작/중지**, **다시 시작** 및 **삭제** 애플리케이션에 대한 단추를 찾습니다.

## <a name="using-the-azure-cli"></a>Azure CLI 사용

> [!NOTE]
> Azure CLI를 사용하여 선택적 매개 변수를 사용하고 기본값을 구성할 수 있습니다. 자세한 내용은 [참조 설명서](spring-cloud-cli-reference.md)를 참조하세요.

* 애플리케이션을 시작하려면 다음을 수행합니다.
    ```Azure CLI
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 애플리케이션을 중지하려면 다음을 수행합니다.
    ```Azure CLI
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 애플리케이션을 다시 시작하려면 다음을 수행합니다.
    ```Azure CLI
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 애플리케이션을 삭제하려면 다음을 수행합니다.
    ```Azure CLI
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
