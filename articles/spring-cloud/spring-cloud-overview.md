---
title: Azure Spring Cloud 소개
description: Azure에서 Java Spring 애플리케이션을 배포 및 관리하는 Azure Spring Cloud의 기능 및 이점을 알아봅니다.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.openlocfilehash: 4426044b3608be0ded378f4f56cbec6bc1948d75
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273252"
---
# <a name="what-is-azure-spring-cloud"></a>Azure Spring Cloud란?

Azure Spring Cloud를 사용하면 코드 변경 없이 Spring Boot 기반 마이크로서비스 애플리케이션을 Azure에 쉽게 배포할 수 있습니다.  Azure Spring Cloud는 Spring Cloud 애플리케이션의 수명 주기를 관리하므로 개발자는 코드에 집중할 수 있습니다.  Spring Cloud는 포괄적인 모니터링 및 진단, 구성 관리, 서비스 검색, CI/CD 통합, blue-green 배포 등을 사용하여 수명 주기 관리를 제공합니다.

Azure 에코시스템의 일부인 Azure Spring Cloud를 사용하면 스토리지, 데이터베이스, 모니터링 등을 비롯한 다른 Azure 서비스에 쉽게 바인딩할 수 있습니다.

Azure Spring Cloud는 현재 퍼블릭 미리 보기로 제공됩니다. 퍼블릭 미리 보기 제품을 통해 고객은 공식 릴리스 전에 새로운 기능을 시험해 볼 수 있습니다.  퍼블릭 미리 보기 기능 및 서비스는 프로덕션 용도로 사용되지 않습니다.  미리 보기 동안 제공되는 지원에 대한 자세한 내용은 [FAQ](https://azure.microsoft.com/support/faq/)를 검토하거나 [지원 요청](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조하여 자세히 알아보세요.

시작하려면 [Azure CLI](spring-cloud-quickstart-launch-app-cli.md), [Azure Portal](spring-cloud-quickstart-launch-app-portal.md) 또는 [Maven](spring-cloud-quickstart-launch-app-maven.md)을 사용하여 Spring Cloud 빠른 시작을 완료합니다.

GitHub에서 더 많은 샘플을 사용할 수 있습니다. [Azure Spring Cloud 샘플](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

## <a name="application-configuration"></a>애플리케이션 구성

### <a name="spring-cloud-config-server"></a>Spring Cloud 구성 서버

Azure Spring Cloud 구성 서버는 분산 시스템에서 표면화된 구성에 대한 서버 및 클라이언트 쪽 지원을 모두 제공합니다.  구성 서버는 모든 환경의 애플리케이션 속성을 관리하는 중앙 위치를 제공합니다.  자세히 알아보려면 [Spring Cloud Config 서버 참조](https://spring.io/projects/spring-cloud-config.md)를 확인하고 자습서를 완료하세요.

### <a name="enable-bluegreen-deployments"></a>blue/green 배포 사용

Azure Spring Cloud는 코드를 프로덕션 환경으로 릴리스 및 업데이트하기 위한 blue/green 배포를 지원합니다.  개발자는 필요한 경우 이 변경 관리 패턴을 활용하여 안전한 즉시 대체 방식으로 기능 및 코드 변경 사항을 구현할 수 있습니다.  개발자는 Azure를 사용하여 여러 프로덕션 환경을 관리하고 애플리케이션을 중단하지 않으면서 코드 변경 내용을 쉽게 업데이트하거나 롤백하는 방법으로 코드 작성에 집중할 수 있습니다.  스테이징 환경 및 blue/green 배포에 대해 자세히 알아보려면 이 [방법 문서](spring-cloud-howto-staging-environment.md)를 참조하세요.

### <a name="automate-cicd-pipelines"></a>CI/CD 파이프라인 자동화

Azure Spring Cloud는 Azure CLI를 사용하여 Azure DevOps와의 통합을 제공합니다.  Azure DevOps를 사용하여 Spring 애플리케이션에 대한 코드 통합 및 배포를 자동화할 수 있습니다.  자세히 알아보려면 이 [문서](spring-cloud-howto-cicd.md)를 참조하세요.

### <a name="scale-your-application"></a>애플리케이션 크기 조정

Azure Spring Cloud를 사용하면 Azure Spring Cloud 대시보드에서 마이크로서비스를 쉽게 확장할 수 있습니다.  마이크로서비스에 사용할 수 있는 메모리 양과 vCPU 수를 요구 사항에 맞게 모두 확장하거나 축소할 수 있습니다.  크기 조정은 몇 초 안에 적용되며 코드를 변경하거나 다시 배포할 필요가 없습니다.  자세히 알아보려면 이 [자습서](spring-cloud-tutorial-scale-manual.md)를 완료하세요.

## <a name="application-monitoring"></a>애플리케이션 모니터링

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>분산 추적 및 Azure App Insights를 사용하여 애플리케이션 모니터링

개발자는 Spring Cloud의 분산 추적 도구를 사용하여 애플리케이션에서 마이크로서비스 간의 복잡한 상호 연결을 디버깅하고 모니터링할 수 있습니다.  [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth)를 Azure의 [Application Insights](../azure-monitor/insights/insights-overview.md)와 통합하면 Azure가 Azure Portal에서 바로 강력한 분산 추적 기능을 제공합니다.  자세히 알아보려면 이 [자습서](spring-cloud-tutorial-distributed-tracing.md)를 완료하세요.

## <a name="next-steps"></a>다음 단계

- [CLI에서 Spring Cloud 앱 시작](spring-cloud-quickstart-launch-app-cli.md)
