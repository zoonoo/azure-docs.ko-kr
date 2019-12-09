---
title: '자습서: 경고 및 작업 그룹을 사용하여 Azure Spring Cloud 리소스 모니터링 | Microsoft Docs'
description: Spring Cloud 경고를 사용하는 방법을 알아봅니다.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/18/2019
ms.openlocfilehash: 2be21b20c394ae8505ad18f2c411db7aab06215f
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689568"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>자습서: 경고 및 작업 그룹을 사용하여 Spring Cloud 리소스 모니터링

Azure Spring Cloud 경고는 사용 가능한 스토리지, 요청 속도 또는 데이터 사용량과 같은 조건에 따라 모니터링 리소스를 지원합니다. 속도 또는 조건이 정의된 사양과 일치하면 경고에서 알림을 보냅니다.

경고 파이프라인을 설정하는 두 가지 단계가 있습니다. 
1. 이메일, SMS, Runbook 또는 웹후크와 같은 경고가 트리거될 때 수행할 작업을 사용하여 작업 그룹을 설정합니다. 작업 그룹은 서로 다른 경고 간에 다시 사용할 수 있습니다.
2. 경고 규칙을 설정합니다. 이러한 규칙은 대상 리소스, 메트릭, 조건, 시간 집계 등에 기반한 메트릭 패턴을 작업 그룹에 바인딩합니다.

## <a name="prerequisites"></a>필수 조건
이 자습서는 Azure Spring 요구 사항 외에도 다음과 같은 리소스에 따라 달라집니다.

* 배포된 Azure Spring Cloud 인스턴스.  [빠른 시작](spring-cloud-quickstart-launch-app-cli.md)을 따라 시작하세요.

* 모니터링할 Azure 리소스(예: [Azure Cosmos DB에서 Spring Data Apache Cassandra API를 사용하는 방법](https://docs.microsoft.com/azure/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db) 문서에서 구현된 데이터베이스)
 
다음 절차에서는 Spring Cloud 인스턴스의 왼쪽 탐색 창에 있는 **경고** 옵션에서 시작하여 **작업 그룹** 및 **경고**를 모두 초기화합니다. 이 절차는 Azure Portal의 **모니터 개요** 페이지에서 시작할 수도 있습니다. 

리소스 그룹에서 Spring Cloud 인스턴스로 이동합니다. 왼쪽 창에서 **경고**를 선택한 다음, **작업 관리**를 선택합니다.

![포털의 리소스 그룹 페이지에 대한 스크린샷](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>작업 그룹 설정

새 **작업 그룹**을 초기화하는 절차를 시작하려면 **+ 작업 그룹**을 선택합니다.

![포털의 작업 그룹 추가에 대한 스크린샷](media/alerts-action-groups/action-1.png)

**작업 그룹 추가** 페이지에서 다음을 수행합니다.

 1. **작업 그룹 이름** 및 **약식 이름**을 지정합니다.

 1. **구독** 및 **리소스 그룹**을 지정합니다.

 1. **작업 이름**을 지정합니다.

 1. **작업 유형**을 선택합니다.  그러면 오른쪽에 다른 창이 열려 활성화 시 수행할 작업을 정의합니다.

 1. 오른쪽 창의 옵션을 사용하여 작업을 정의합니다.  이 경우 이메일 알림을 사용합니다.

 1. 오른쪽 작업 창에서 **확인**을 클릭합니다.

 1. **작업 그룹 추가** 대화 상자에서 **확인**을 클릭합니다. 

  ![포털의 작업 정의에 대한 스크린샷](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>경고 설정 

이전 단계에서는 이메일을 사용하는 **작업 그룹**을 만들었습니다. 전화 알림, 웹후크, Azure 함수 등을 사용할 수도 있습니다.  

**경고**를 구성하려면 **경고** 페이지로 돌아가서 **경고 규칙 관리**를 클릭합니다.

  ![포털의 경고 정의에 대한 스크린샷](media/alerts-action-groups/alerts-2.png)

1. 경고에 대한 **리소스**를 선택합니다.

1. **+ 새 경고 규칙**을 클릭합니다.

  ![포털의 새 경고 규칙에 대한 스크린샷](media/alerts-action-groups/alerts-3.png)

1. **규칙 만들기** 페이지에서 **리소스**, **조건** 및 **작업**을 지정합니다.  **작업** 창에서 이전에 정의한 **작업 그룹**을 선택합니다.

1. **경고 세부 정보** 아래에서 경고 규칙의 이름을 지정합니다.

1. **경고 규칙 만들기**를 클릭합니다.

  ![포털의 새 경고 규칙에 대한 스크린샷](media/alerts-action-groups/alerts-4.png)

새 경고 규칙을 사용하도록 설정되었는지 확인합니다.

  ![포털의 새 경고 규칙에 대한 스크린샷](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>다음 단계
* [Azure Portal에서 작업 그룹 만들기 및 관리](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* [작업 그룹의 SMS 경고 동작](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
* [자습서: Azure Spring Cloud에서 분산 추적 사용](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
