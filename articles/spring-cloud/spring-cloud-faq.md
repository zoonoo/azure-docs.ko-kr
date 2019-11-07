---
title: Azure 스프링 클라우드에 대 한 질문과 대답 | Microsoft Docs
description: Azure 스프링 클라우드의 FAQ 검토
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 1fb241ab1335e6b9c9be55acbbe2e21378831b69
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607238"
---
# <a name="frequently-asked-questions"></a>질문과 대답

이 문서에서는 Azure 스프링 클라우드에 대 한 자주 묻는 질문을 해결 합니다. 

## <a name="general"></a>일반

### <a name="why-azure-spring-cloud"></a>Azure 스프링 클라우드가 무엇 인가요?

Azure 스프링 클라우드는 스프링 개발자를 위한 PaaS (Platform as a Service)를 제공 합니다. Azure 스프링 클라우드는 응용 프로그램 인프라를 관리 하므로 응용 프로그램 코드 및 비즈니스 논리에 집중할 수 있습니다. Azure 스프링 클라우드에 기본 제공 되는 핵심 기능에는 Eureka, 구성 서버, 서비스 레지스트리 서버, Pivotal 빌드 서비스, Blue-녹색 배포 등이 포함 됩니다. 또한 개발자는이 서비스를 사용 하 여 응용 프로그램을 CosmosDB, MySQL 및 Redis 용 Azure 캐시와 같은 Azure 서비스에 바인딩할 수 있습니다.

Azure 스프링 클라우드는 Azure Monitor, Application Insights 및 Log Analytics를 통합 하 여 개발자 및 운영자를 위한 응용 프로그램 진단 환경을 향상 시킵니다.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Azure 스프링 클라우드에서 제공 하는 서비스 계획은 무엇 인가요?

Azure 스프링 클라우드는 미리 보기 기간 동안 하나의 서비스 계획을 제공 합니다.  스프링 클라우드 배포에는 16 개 vCPU 코어 및 32GB의 메모리가 포함 됩니다.  배포 내의 각 마이크로 서비스 인스턴스에 대 한 상한은 8GB의 메모리를 포함 하는 4 개의 vCPU 코어입니다.

리소스 | Amount
------- | -------
스프링 응용 프로그램당 앱 인스턴스 | 20
Azure 스프링 클라우드 서비스 인스턴스당 총 앱 인스턴스 수 | 50 *
구독 당 지역별 Azure 스프링 클라우드 서비스 인스턴스 수 | sr-2
영구적 볼륨 | 10 x 50 Gb

_[지원 티켓](https://azure.microsoft.com/support/faq/) 을 열어 제한을 높일 *._

자세한 내용은 [Azure 지원 faq](https://azure.microsoft.com/support/faq/)를 참조 하세요.

### <a name="how-secure-is-azure-spring-cloud"></a>Azure 스프링 클라우드는 얼마나 안전 한가요?

보안 및 개인 정보 보호는 Azure 및 Azure 스프링 클라우드 고객에 게 가장 높은 우선 순위 중 하나입니다. Azure는이 모든 데이터를 안전 하 게 암호화 하 여 고객이 응용 프로그램 데이터, 로그 또는 구성에 액세스할 수 있도록 합니다. Azure 스프링 클라우드의 모든 서비스 인스턴스는 서로 격리 되어 있습니다.

Azure 스프링 클라우드는 완전 한 SSL 및 인증서 관리를 제공 합니다.

OpenJDK 및 스프링 클라우드 런타임에 대 한 중요 보안 패치는 가능한 한 빨리 Azure 스프링 클라우드에 적용 됩니다.

### <a name="which-regions-azure-spring-cloud-are-available"></a>어떤 지역에서 Azure 스프링 클라우드를 사용할 수 있나요?

미국 동부, 미국 서 부 2, 유럽 서부 및 동남 아시아.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure 스프링 클라우드의 알려진 제한 사항

서비스가 미리 보기로 제공 되는 동안 Azure 스프링 클라우드의 알려진 제한 사항은 다음과 같습니다.

* `spring.application.name`는 각 응용 프로그램을 만드는 데 사용 된 응용 프로그램 이름으로 재정의 됩니다.
* Git 리포지토리의 구성 파일에 `server.port`를 사용할 수 없습니다. 구성 파일에 추가 하면 다른 응용 프로그램 또는 인터넷에서 응용 프로그램에 연결할 수 없게 될 수 있습니다.
* Azure Portal 및 리소스 관리자 템플릿은 응용 프로그램 패키지 업로드를 지원 하지 않습니다. Azure CLI는 응용 프로그램 배포를 통해서만이 작업을 수행할 수 있습니다.
* 할당량 제한 사항은 [Azure 스프링 클라우드 제품에서 제공 하는 서비스 계획](#what-service-plans-does-azure-spring-cloud-offer)을 참조 하세요.

### <a name="how-can-i-provide-feedback-and-report-issues"></a>사용자 의견을 제공 하 고 문제를 보고 하려면 어떻게 해야 하나요?

Azure 스프링 클라우드에 문제가 발생 한 경우 [Azure 지원 요청](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 만드세요. 기능 요청에 대해 [Azure 피드백](https://feedback.azure.com/forums/34192--general-feedback) 으로 이동 하 여 기능을 요청 하거나 피드백을 제공 해 주세요.

## <a name="development"></a>개발

### <a name="i-am-a-spring-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-aazure-spring-cloud-application"></a>Azure에 대 한 새로운 기능을 제공 하는 스프링 개발자 인 경우, Azure 스프링 클라우드 응용 프로그램을 개발 하는 방법을 배우는 가장 빠른 방법은 무엇 인가요?

Azure 스프링 클라우드를 시작 하는 가장 빠른 방법은 [이 빠른](spring-cloud-quickstart-launch-app-portal.md)시작을 따르는 것입니다.

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure 스프링 클라우드에서 지원 되는 Java runtime은 무엇 인가요?

Azure 스프링 클라우드는 Java 8 및 11을 지원 합니다.

### <a name="where-can-i-see-my-spring-application-logs-and-metrics"></a>내 스프링 응용 프로그램 로그 및 메트릭은 어디에서 볼 수 있나요?

앱 개요 탭 및 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) 탭에서 메트릭을 찾습니다.

Azure 스프링 클라우드는 스프링 응용 프로그램 로그 및 메트릭을 Azure Storage, EventHub 및 [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries)으로 내보내는 것을 지원 합니다. Log Analytics의 테이블 이름이 `AppPlatformLogsforSpring`입니다. 사용 하도록 설정 하려면이 문서에서 [진단 서비스](diagnostic-services.md)를 검토 합니다.

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure 스프링 클라우드가 분산 추적을 지원 하나요?

예, 자세한 내용은 [분산 추적](spring-cloud-tutorial-distributed-tracing.md) 을 참조 하세요.

### <a name="what-resource-types-does-service-binding-support"></a>서비스 바인딩에서 지 원하는 리소스 종류는 무엇 인가요?

Azure Cosmos DB, Azure Database for MySQL 및 Azure Cache for Redis의 세 가지 서비스가 현재 지원 됩니다.

### <a name="can-i-viewaddmove-persistent-volumes-from-inside-my-applications"></a>내 응용 프로그램 내에서 영구적 볼륨을 보거나 추가/이동할 수 있나요?
예.

## <a name="deployment"></a>배포

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure 스프링 클라우드는 Blue-녹색 배포를 지원 하나요?
예, 자세한 내용은 [스테이징 환경 가이드](spring-cloud-howto-staging-environment.md) 를 참조 하세요.

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>응용 프로그램 컨테이너를 조작 하기 위해 Kubernetes에 액세스할 수 있나요?

아니요.  Azure 스프링 클라우드는 기본 아키텍처에서 개발자를 추상화 하 여 응용 프로그램 코드 및 비즈니스 논리에 집중할 수 있도록 합니다.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure 스프링 클라우드는 원본에서 컨테이너 빌드를 지원 하나요?

예, 자세한 내용은 [원본에서 배포](spring-cloud-launch-from-source.md) 를 참조 하세요.

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure 스프링 클라우드가 앱 인스턴스의 자동 크기 조정을 지원 하나요?

아니요.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-microservices-to-azure-spring-cloud"></a>기존 스프링 마이크로 서비스를 Azure 스프링 클라우드로 마이그레이션하기 위한 모범 사례는 무엇 인가요?

기존 스프링 마이크로 서비스를 Azure 스프링 클라우드로 마이그레이션하기 전에 다음을 수행 합니다.
* 모든 응용 프로그램 종속성을 확인 해야 합니다.
* 구성 항목, 환경 변수 및 JVM 매개 변수를 준비 하 여 Azure 스프링 클라우드의 배포와 비교할 수 있습니다.
* 서비스 바인딩을 사용 하려면 Azure 서비스를 살펴보고 적절 한 액세스 권한을 설정 했는지 확인 합니다.
* 서비스 검색 서비스, 구성 서버 등 Azure 스프링 클라우드에서 관리 되는 서비스와 충돌할 수 있는 포함 된 서비스를 제거 하거나 사용 하지 않도록 설정 하는 것이 좋습니다.
*-* 공식 및 안정적인 Pivotal 스프링 라이브러리를 사용 하는 것이 좋습니다. Pivotal 스프링 라이브러리의 비공식적, 베타 또는 분기 버전은 SLA를 지원 하지 않습니다.

마이그레이션 후에는 CPU/RAM 메트릭과 네트워크 트래픽을 모니터링 하 여 응용 프로그램 인스턴스의 크기를 적절히 조정 해야 합니다.

## <a name="next-steps"></a>다음 단계

[추가 질문이 있는 경우 문제 해결 가이드를 확인](spring-cloud-troubleshoot.md)하세요.