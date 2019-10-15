---
title: '자습서: Azure Spring Cloud의 애플리케이션 크기 조정 | Microsoft Docs'
description: 이 자습서에서는 Azure Portal에서 Azure Spring Cloud의 애플리케이션 크기를 조정하는 방법에 대해 알아봅니다.
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 10/06/2019
ms.openlocfilehash: bca88cac45e1ba8117eb4e10141e32d621434b86
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038635"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>자습서: Azure Spring Cloud의 애플리케이션 크기 조정

이 자습서에서는 Azure Portal에서 Azure Spring Cloud 대시보드를 사용하여 마이크로서비스 애플리케이션의 크기를 조정하는 방법을 보여줍니다. vCPU(가상 CPU) 수와 메모리 양을 수정하여 애플리케이션을 확장하고 축소합니다. 애플리케이션의 인스턴스 수를 수정하여 애플리케이션의 크기를 조정합니다. 작업이 완료되면 서비스에서 각 애플리케이션에 대해 빠르게 수동 조정을 수행하는 방법을 알게 됩니다. 크기 조정은 몇 초 안에 적용되며 코드를 변경하거나 다시 배포할 필요가 없습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.
* Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 
* 배포된 Azure Spring Cloud 서비스 인스턴스  [빠른 시작](spring-cloud-quickstart-launch-app-cli.md)을 따라 시작하세요.
* 해당 서비스 인스턴스에서 이미 하나 이상의 애플리케이션을 만들었습니다.


## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Azure Portal의 크기 조정 페이지로 이동합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Spring Cloud **개요** 페이지로 이동합니다.

1. 왼쪽 메뉴에서 **설정** 제목 아래의 **앱** 탭으로 이동합니다.

1. 크기를 조정하려는 애플리케이션을 선택합니다. 이 예제에서는 “account-service”라는 애플리케이션의 크기를 조정합니다. 그러면 애플리케이션의 **개요** 페이지로 이동합니다.

1. 왼쪽 메뉴에서 **설정** 제목 아래의 **크기 조정** 탭으로 이동합니다. 앞에서 언급한 각 크기 조정 특성에 대한 줄이 포함된 양식이 표시됩니다.

## <a name="scale-your-application"></a>애플리케이션 크기 조정

크기 조정 특성을 수정할 수 있습니다. 다음 사항을 고려하세요.

* **CPU**: 허용되는 최대 CPU 수는 애플리케이션 인스턴스당 4개입니다. 애플리케이션에 대한 총 CPU 수는 여기에 애플리케이션 인스턴스 수를 곱한 값으로 설정됩니다.

* **메모리/GB**: 허용되는 최대 메모리 양은 애플리케이션 인스턴스당 8GB입니다.  애플리케이션에 대한 총 메모리 양은 여기에 애플리케이션 인스턴스 수를 곱한 값으로 설정됩니다.

* **인스턴트 개수**: 표준 계층에서 최대 20개 인스턴스까지 확장할 수 있습니다. 이 값은 마이크로서비스 애플리케이션의 개별 실행 인스턴스 수를 변경합니다.

크기 조정 설정을 적용되도록 **저장** 단추를 클릭해야 합니다.

몇 초 후에 변경 내용이 **개요** 페이지에 표시되며, **애플리케이션 인스턴스** 탭에서 자세한 내용을 볼 수 있습니다. 크기를 조정하기 위해 코드를 변경하거나 다시 배포하지 않아도 됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Spring Cloud 애플리케이션의 크기를 수동으로 조정하는 방법에 대해 알아보았습니다.  애플리케이션을 모니터링하는 방법을 알려면 다음의 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [애플리케이션 모니터링 방법 알아보기](spring-cloud-tutorial-distributed-tracing.md)
