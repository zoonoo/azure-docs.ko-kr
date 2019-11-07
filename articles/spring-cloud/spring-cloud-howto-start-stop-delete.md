---
title: Azure 스프링 클라우드 응용 프로그램을 시작, 중지 및 삭제 하는 방법 | Microsoft Docs
description: Azure 스프링 클라우드 응용 프로그램을 시작, 중지 및 삭제 하는 방법
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 9f537ab425428728137e04713e434d8dc09e065a
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607716"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>Azure 스프링 클라우드 응용 프로그램을 시작, 중지 및 삭제 하는 방법

이 가이드에서는 Azure Portal 또는 CLI를 사용 하 여 Azure 스프링 클라우드에서 응용 프로그램의 상태를 변경 하는 방법을 설명 합니다.

## <a name="using-the-azure-portal"></a>Azure 포털 사용

응용 프로그램을 배포한 후에는 Azure Portal를 사용 하 여 응용 프로그램을 **시작**, **중지**및 **삭제할** 수 있습니다.

1. Azure Portal에서 Azure 스프링 클라우드 서비스 인스턴스로 이동 합니다.
1. **응용 프로그램 대시보드** 탭을 선택 합니다.
1. 상태를 변경 하려는 응용 프로그램을 선택 합니다.
2. 응용 프로그램의 **개요** 페이지에서 응용 프로그램을 **시작/중지**, **다시 시작**및 **삭제** 하는 단추를 찾습니다.

## <a name="using-the-azure-cli"></a>Azure CLI 사용

> [!NOTE]
> 선택적 매개 변수를 사용 하 고 Azure CLI를 사용 하 여 기본값을 구성할 수 있습니다. [참조 설명서를 참조](spring-cloud-cli-reference.md)하 여에 대해 자세히 알아보세요.  

Azure CLI에 대 한 스프링 클라우드 확장을 설치 합니다.

```azurecli
az extension add --name spring-cloud
```

* 응용 프로그램을 시작 하려면:
    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 응용 프로그램을 중지 하려면:
    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 응용 프로그램을 다시 시작 하려면:
    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 응용 프로그램을 삭제 하려면:
    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
