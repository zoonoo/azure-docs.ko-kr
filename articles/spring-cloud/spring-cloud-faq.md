---
title: Azure Spring Cloud에 대한 질문과 대답 | Microsoft Docs
description: Azure Spring Cloud에 대한 FAQ를 검토합니다.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: d4edc634ad41042642b1d73f28ec61cb6dc15ac4
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433313"
---
# <a name="frequently-asked-questions"></a>질문과 대답

이 문서에서는 Azure Spring Cloud에 대해 자주 묻는 질문과 대답을 검토합니다. 

## <a name="general"></a>일반

### <a name="why-azure-spring-cloud"></a>Azure Spring Cloud가 적합한 이유는?

Azure Spring Cloud는 Spring 개발자를 위한 PaaS(Platform as a Service)를 제공합니다. Azure Spring Cloud는 애플리케이션 인프라를 관리하므로 애플리케이션 코드 및 비즈니스 논리에 집중할 수 있습니다. Azure Spring Cloud에 기본 제공되는 핵심 기능에는 Eureka, Config 서버, 서비스 레지스트리 서버, Pivotal 빌드 서비스, Blue-Green 배포 등이 있습니다. 또한 개발자는 이 서비스를 사용하여 자신의 애플리케이션을 Azure 서비스(예: CosmosDB, MySQL 및 Azure Cache for Redis)에 바인딩할 수 있습니다.

Azure Spring Cloud는 Azure Monitor, Application Insights 및 Log Analytics를 통합하여 개발자와 운영자의 애플리케이션 진단 환경을 향상시킵니다.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Azure Spring Cloud에서 제공하는 서비스 계획은 어떻게 되나요?

Azure Spring Cloud는 미리 보기 기간 동안 하나의 서비스 계획을 제공합니다.

리소스 | Amount
------- | -------
vCPU | 4
메모리 | 8GB
Spring 애플리케이션당 앱 인스턴스 수 | 20
Azure Spring Cloud 서비스 인스턴스당 총 앱 인스턴스 수 | 50*
구독당 지역별 Azure Spring Cloud 서비스 인스턴스 수 | 2*
영구적 볼륨 | 10x50GB

*_한도를 높이려면 [지원 티켓](https://azure.microsoft.com/support/faq/)을 엽니다._

자세한 내용은 [Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud는 얼마나 안전한가요?

보안 및 개인 정보 보호는 Azure 및 Azure Spring Cloud 고객에게 가장 높은 우선 순위 중 하나입니다. Azure는 이 모든 데이터를 안전하게 암호화하여 고객만 애플리케이션 데이터, 로그 또는 구성에 액세스할 수 있도록 합니다. Azure Spring Cloud의 모든 서비스 인스턴스는 서로 격리되어 있습니다.

Azure Spring Cloud는 완전한 SSL 및 인증서 관리를 제공합니다.

OpenJDK 및 Spring Cloud 런타임에 대한 중요 보안 패치는 가능한 한 빨리 Azure Spring Cloud에 적용됩니다.

### <a name="which-regions-azure-spring-cloud-are-available"></a>Azure Spring Cloud는 어떤 지역에서 사용할 수 있나요?

미국 동부, 미국 서부 2, 유럽 서부 및 동남 아시아 지역입니다.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure Spring Cloud의 알려진 제한 사항은 어떻게 되나요?

서비스가 미리 보기로 제공되는 동안 Azure Spring Cloud의 알려진 제한 사항은 다음과 같습니다.

* `spring.application.name`은 각 애플리케이션을 만드는 데 사용되는 애플리케이션 이름으로 재정의됩니다.
* `server.port`는 Git 리포지토리의 구성 파일에 허용되지 않습니다. 구성 파일에 추가되면 다른 애플리케이션 또는 인터넷에서 애플리케이션에 연결할 수 없습니다.
* Azure Portal 및 Resource Manager 템플릿에서 애플리케이션 패키지 업로드를 지원하지 않습니다. 이 작업은 Azure CLI의 애플리케이션 배포를 통해서만 수행할 수 있습니다.
* 할당량 제한 사항은 [Azure Spring Cloud에서 제공하는 서비스 계획은 어떻게 되나요?](#what-service-plans-does-azure-spring-cloud-offer)를 참조하세요.

### <a name="how-can-i-provide-feedback-and-report-issues"></a>피드백 및 보고서 문제를 제출하려면 어떻게 해야 하나요?

Azure Spring Cloud에서 Spring 서비스 인스턴스를 만든 경우 [Azure 지원 요청](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 제출할 수 있습니다. 아직 Azure Spring Cloud에 온보딩하지 않은 경우 [Azure Feedback](https://feedback.azure.com/)으로 이동하여 기능을 요청하거나 피드백을 제출할 수 있습니다.

## <a name="development"></a>개발

### <a name="i-am-a-spring-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-aazure-spring-cloud-application"></a>Azure를 처음 사용하는 Spring 개발자인 경우 Azure Spring Cloud 애플리케이션을 개발하는 방법을 알아볼 수 있는 가장 빠른 방법은 무엇인가요?

Azure Spring Cloud를 시작하는 가장 빠른 방법은 [이 빠른 시작](spring-cloud-quickstart-launch-app-portal.md)을 수행하는 것입니다.

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud에서 지원하는 Java 런타임은 어떻게 되나요?

Azure Spring Cloud는 Java 8 및 11을 지원합니다.

### <a name="where-can-i-see-my-spring-application-logs-and-metrics"></a>내 Spring 애플리케이션 로그 및 메트릭은 어디서 확인할 수 있나요?

메트릭은 [앱 개요] 탭 및 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) 탭에서 찾습니다.

Azure Spring Cloud는 Spring 애플리케이션 로그 및 메트릭을 Azure Storage, EventHub 및 [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries)로 내보내는 기능을 지원합니다. Log Analytics의 테이블 이름은 `AppPlatformLogsforSpring`입니다. 이 기능을 사용하도록 설정하려면 [진단 서비스](diagnostic-services.md)에 대한 이 문서를 검토하세요.

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud는 분산 추적을 지원하나요?

예, 자세한 내용은 [분산 추적](spring-cloud-tutorial-distributed-tracing.md)을 참조하세요.

### <a name="what-resource-types-does-service-binding-support"></a>서비스 바인딩에서 지원하는 리소스 종류는 무엇인가요?

현재 지원되는 세 가지 서비스는 Azure Cosmos DB, Azure Database for MySQL 및 Azure Cache for Redis입니다.

### <a name="can-i-viewaddmove-persistent-volumes-from-inside-my-applications"></a>애플리케이션 내부에서 영구 볼륨을 보거나 추가하거나 이동할 수 있나요?
예.

## <a name="deployment"></a>배포

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure Spring Cloud에서 Blue-Green 배포를 지원하나요?
예, 자세한 내용은 [스테이징 환경 가이드](spring-cloud-howto-staging-environment.md)를 참조하세요.

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>내 애플리케이션 컨테이너를 조작하기 위해 Kubernetes에 액세스할 수 있나요?

아니요.  Azure Spring Cloud는 기본 아키텍처에서 개발자를 추상화하여 애플리케이션 코드와 비즈니스 논리에 집중할 수 있도록 합니다.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud는 원본에서 컨테이너를 빌드할 수 있도록 지원하나요?

예, 자세한 내용은 [원본에서 배포](spring-cloud-launch-from-source.md)를 참조하세요.

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud에서 앱 인스턴스의 자동 크기 조정을 지원하나요?

아니요.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-microservices-to-azure-spring-cloud"></a>기존 Spring 마이크로서비스를 Azure Spring Cloud로 마이그레이션하기 위한 모범 사례는 무엇인가요?

기존 Spring 마이크로서비스를 Azure Spring Cloud로 마이그레이션하기 전에 다음을 수행합니다.
* 모든 애플리케이션 종속성을 확인해야 합니다.
* Azure Spring Cloud의 배포와 비교할 수 있도록 구성 항목, 환경 변수 및 JVM 매개 변수를 준비합니다.
* 서비스 바인딩을 사용하려면 Azure 서비스를 살펴보고 적절한 액세스 권한을 설정했는지 확인합니다.
* 서비스 검색 서비스, Config 서버 등과 같이 Azure Spring Cloud에서 관리하는 서비스와 충돌할 수 있는 포함된 서비스를 제거하거나 사용하지 않도록 설정하는 것이 좋습니다.
*-* 공식적이고 안정적인 Pivotal Spring 라이브러리를 사용하는 것이 좋습니다. Pivotal Spring 라이브러리의 비공식, 베타 또는 분기 버전은 SLA를 지원하지 않습니다.

마이그레이션 후에는 CPU/RAM 메트릭과 네트워크 트래픽을 모니터링하여 애플리케이션 인스턴스의 크기를 적절하게 조정해야 합니다.

## <a name="next-steps"></a>다음 단계

[추가 질문이 있는 경우 문제 해결 가이드를 확인합니다](spring-cloud-troubleshoot.md).