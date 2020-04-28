---
title: Azure 스프링 클라우드에 대 한 질문과 대답 | Microsoft Docs
description: 이 문서에서는 Azure 스프링 클라우드에 대해 자주 묻는 질문에 답변 합니다.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 95260d9a15fdc32c9fddccbcf63ae9fa564fd36a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176773"
---
# <a name="azure-spring-cloud-faq"></a>Azure 스프링 클라우드 FAQ

이 문서에서는 Azure 스프링 클라우드에 대해 자주 묻는 질문에 답변 합니다.

## <a name="general"></a>일반

### <a name="why-azure-spring-cloud"></a>Azure Spring Cloud가 적합한 이유는?

Azure 스프링 클라우드는 스프링 클라우드 개발자를 위한 PaaS (platform as a service)를 제공 합니다. Azure 스프링 클라우드는 응용 프로그램 코드 및 비즈니스 논리에 집중할 수 있도록 응용 프로그램 인프라를 관리 합니다. Azure 스프링 클라우드에 기본 제공 되는 핵심 기능에는 Eureka, 구성 서버, 서비스 레지스트리 서버, Pivotal 빌드 서비스, Blue-녹색 배포 등이 포함 됩니다. 또한 개발자는이 서비스를 통해 Azure Cosmos DB, Azure Database for MySQL, Azure Cache for Redis 등의 다른 Azure 서비스와 응용 프로그램을 바인딩할 수 있습니다.

Azure 스프링 클라우드는 Azure Monitor, Application Insights 및 Log Analytics를 통합 하 여 개발자 및 운영자를 위한 응용 프로그램 진단 환경을 향상 시킵니다.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Azure Spring Cloud에서 제공하는 서비스 계획은 어떻게 되나요?

Azure Spring Cloud는 미리 보기 기간 동안 하나의 서비스 계획을 제공합니다.  스프링 클라우드 배포에는 16 개 vCPU 코어 및 32 기가바이트 (GB)의 메모리가 포함 되어 있습니다.  배포 내의 각 마이크로 서비스 인스턴스에 대 한 상한은 8gb의 메모리를 포함 하는 4 개의 vCPU 코어입니다.

리소스 | Amount
------- | -------
Spring 애플리케이션당 앱 인스턴스 수 | 20
Azure Spring Cloud 서비스 인스턴스당 총 앱 인스턴스 수 | 500
구독당 지역별 Azure Spring Cloud 서비스 인스턴스 수 | 10
영구적 볼륨 | 10x50GB

\*한 _도를 높이려면 [지원 티켓](https://azure.microsoft.com/support/faq/)을 엽니다._

자세한 내용은 [Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조 하세요.

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud는 얼마나 안전한가요?

보안 및 개인 정보 보호는 Azure 및 Azure 스프링 클라우드 고객의 최우선 우선 순위에 있습니다. Azure는이 모든 데이터를 안전 하 게 암호화 하 여 고객이 응용 프로그램 데이터, 로그 또는 구성에 액세스할 수 있도록 합니다. Azure Spring Cloud의 모든 서비스 인스턴스는 서로 격리되어 있습니다.

Azure 스프링 클라우드는 전체 TLS/SSL 및 인증서 관리를 제공 합니다.

OpenJDK 및 Spring Cloud 런타임에 대한 중요 보안 패치는 가능한 한 빨리 Azure Spring Cloud에 적용됩니다.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Azure 스프링 클라우드를 사용할 수 있는 지역은 어디 인가요?

미국 동부, 미국 서부 2, 서유럽 및 동남 아시아 지역입니다.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure 스프링 클라우드의 알려진 제한은 무엇 인가요?

Preview 릴리스 중에 Azure 스프링 클라우드에는 다음과 같은 알려진 제한 사항이 있습니다.

* `spring.application.name`는 각 응용 프로그램을 만드는 데 사용 되는 응용 프로그램 이름으로 재정의 됩니다.
* `server.port`는 Git 리포지토리의 구성 파일에서 허용 되지 않습니다. 구성 파일에 추가 하면 다른 응용 프로그램 또는 인터넷에서 응용 프로그램에 연결할 수 없게 될 수 있습니다.
* Azure Portal 및 Azure Resource Manager 템플릿은 응용 프로그램 패키지 업로드를 지원 하지 않습니다. Azure CLI를 통해 응용 프로그램을 배포 해야만 응용 프로그램 패키지를 업로드할 수 있습니다.
* 할당량 제한 사항에 대 한 자세한 내용은 [Azure 스프링 클라우드에서 제공 하는 서비스 계획](#what-service-plans-does-azure-spring-cloud-offer)을 참조 하세요.

### <a name="how-can-i-provide-feedback-and-report-issues"></a>피드백 및 보고서 문제를 제출하려면 어떻게 해야 하나요?

Azure 스프링 클라우드에 문제가 발생 한 경우 [Azure 지원 요청](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 만듭니다. 기능 요청을 제출 하거나 피드백을 제공 하려면 [Azure 사용자 의견](https://feedback.azure.com/forums/34192--general-feedback)으로 이동 하세요.

## <a name="development"></a>개발

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>저는 Azure를 처음 접하는 스프링 클라우드 개발자입니다. Azure 스프링 클라우드 응용 프로그램을 개발 하는 방법을 배우는 가장 빠른 방법은 무엇 인가요?

Azure 스프링 클라우드를 시작 하는 가장 빠른 방법은 [빠른 시작: Azure Portal을 사용 하 여 Azure 스프링 클라우드 응용 프로그램 시작](spring-cloud-quickstart-launch-app-portal.md)의 지침을 따르세요.

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud에서 지원하는 Java 런타임은 어떻게 되나요?

Azure Spring Cloud는 Java 8 및 11을 지원합니다.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>내 스프링 클라우드 응용 프로그램 로그 및 메트릭은 어디에서 볼 수 있나요?

메트릭은 [앱 개요] 탭 및 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) 탭에서 찾습니다.

Azure 스프링 클라우드는 스프링 클라우드 응용 프로그램 로그 및 메트릭을 Azure Storage, EventHub 및 [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries)으로 내보내는 것을 지원 합니다. Log Analytics의 테이블 이름은 *AppPlatformLogsforSpring*입니다. 사용 하도록 설정 하는 방법을 알아보려면 [진단 서비스](diagnostic-services.md)를 참조 하세요.

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud는 분산 추적을 지원하나요?

예. 자세한 내용은 [자습서: Azure 스프링 클라우드에서 분산 추적 사용](spring-cloud-tutorial-distributed-tracing.md)을 참조 하세요.

### <a name="what-resource-types-does-service-binding-support"></a>서비스 바인딩에서 지원하는 리소스 종류는 무엇인가요?

Azure Cosmos DB, Azure Database for MySQL 및 Azure Cache for Redis의 세 가지 서비스가 현재 지원 됩니다.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>내 응용 프로그램 내에서 영구적 볼륨을 보거나 추가 하거나 이동할 수 있나요?

예.

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>Azure 스프링 클라우드 서비스 인스턴스를 삭제/이동할 때 확장 리소스도 삭제/이동 됩니까?

확장 리소스가 속한 리소스 공급자의 논리에 따라 다릅니다. `Microsoft.AppPlatform` 인스턴스의 확장 리소스는 동일한 네임 스페이스에 속하지 않으므로 다른 리소스 공급자에 따라 동작이 달라 집니다. 예를 들어, 삭제/이동 작업은 **진단 설정** 리소스에 종속 되지 않습니다. 새 Azure 스프링 클라우드 인스턴스가 삭제 된 것과 동일한 리소스 ID로 프로 비전 되거나 이전 Azure 스프링 클라우드 인스턴스가 다시 이동 되는 경우 이전 **진단 설정** 리소스에서 계속 확장 합니다.

## <a name="deployment"></a>배포

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure 스프링 클라우드는 blue-녹색 배포를 지원 하나요?
예. 자세한 내용은 [스테이징 환경 설정](spring-cloud-howto-staging-environment.md)을 참조 하세요.

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>내 애플리케이션 컨테이너를 조작하기 위해 Kubernetes에 액세스할 수 있나요?

아니요.  Azure Spring Cloud는 기본 아키텍처에서 개발자를 추상화하여 애플리케이션 코드와 비즈니스 논리에 집중할 수 있도록 합니다.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud는 원본에서 컨테이너를 빌드할 수 있도록 지원하나요?

예. 자세한 내용은 [소스 코드에서 스프링 클라우드 응용 프로그램 시작](spring-cloud-launch-from-source.md)을 참조 하세요.

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud에서 앱 인스턴스의 자동 크기 조정을 지원하나요?

아니요.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>기존 스프링 클라우드 마이크로 서비스를 Azure 스프링 클라우드로 마이그레이션하기 위한 모범 사례는 무엇 인가요?

기존 스프링 클라우드 마이크로 서비스를 Azure 스프링 클라우드로 마이그레이션할 때는 다음과 같은 모범 사례를 따르는 것이 좋습니다.
* 모든 애플리케이션 종속성을 확인해야 합니다.
* 구성 항목, 환경 변수 및 JVM 매개 변수를 준비 하 여 Azure 스프링 클라우드의 배포와 비교할 수 있습니다.
* 서비스 바인딩을 사용 하려면 Azure 서비스를 살펴보고 적절 한 액세스 권한을 설정 했는지 확인 합니다.
* 서비스 검색 서비스, 구성 서버 등과 같이 Azure 스프링 클라우드에서 관리 되는 서비스와 충돌할 수 있는 포함 된 서비스를 제거 하거나 사용 하지 않도록 설정 하는 것이 좋습니다.
* 공식적인 안정적인 Pivotal 스프링 라이브러리를 사용 하는 것이 좋습니다. Pivotal 스프링 라이브러리의 비공식적, 베타 또는 분기 버전에는 SLA (서비스 수준 계약)가 지원 되지 않습니다.

마이그레이션 후에는 CPU/RAM 메트릭과 네트워크 트래픽을 모니터링 하 여 응용 프로그램 인스턴스의 크기를 적절히 조정 해야 합니다.

## <a name="next-steps"></a>다음 단계

추가 질문이 있는 경우 [Azure 스프링 클라우드 문제 해결 가이드](spring-cloud-troubleshoot.md)를 참조 하세요.
