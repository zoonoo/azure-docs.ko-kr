---
title: Azure 스프링 클라우드에 대해 자주 묻는 질문 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 스프링 클라우드에 대한 자주 묻는 질문에 대한 답변을 답변합니다.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 62623bcadb35f21117ddc2601195e34598c2dff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298768"
---
# <a name="azure-spring-cloud-faq"></a>Azure 스프링 클라우드 자주 묻는 질문

이 문서에서는 Azure 스프링 클라우드에 대한 자주 묻는 질문에 대한 답변을 답변합니다. 

## <a name="general"></a>일반

### <a name="why-azure-spring-cloud"></a>Azure Spring Cloud가 적합한 이유는?

Azure Spring Cloud는 스프링 클라우드 개발자를 위한 서비스(PaaS)로 플랫폼을 제공합니다. Azure Spring Cloud는 응용 프로그램 코드 및 비즈니스 논리에 집중할 수 있도록 응용 프로그램 인프라를 관리합니다. Azure Spring 클라우드에 기본제공된 핵심 기능에는 유레카, Config 서버, 서비스 레지스트리 서버, Pivotal 빌드 서비스, 블루 그린 배포 등이 있습니다. 또한 이 서비스를 통해 개발자는 Azure Cosmos DB, MySQL용 Azure 데이터베이스 및 Redis용 Azure 캐시와 같은 다른 Azure 서비스와 응용 프로그램을 바인딩할 수 있습니다.

Azure Spring Cloud는 Azure 모니터, 응용 프로그램 인사이트 및 로그 분석을 통합하여 개발자 및 운영자의 응용 프로그램 진단 환경을 향상시킵니다.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Azure Spring Cloud에서 제공하는 서비스 계획은 어떻게 되나요?

Azure Spring Cloud는 미리 보기 기간 동안 하나의 서비스 계획을 제공합니다.  스프링 클라우드 배포에는 16개의 vCPU 코어와 32기가바이트(GB)의 메모리가 포함되어 있습니다.  배포 내의 각 마이크로 서비스 인스턴스의 상한은 8GB메모리를 가진 4vCPU 코어입니다.

리소스 | Amount
------- | -------
Spring 애플리케이션당 앱 인스턴스 수 | 20
Azure Spring Cloud 서비스 인스턴스당 총 앱 인스턴스 수 | 500
구독당 지역별 Azure Spring Cloud 서비스 인스턴스 수 | 10
영구적 볼륨 | 10x50GB

\*_한도를 높이려면 [지원 티켓을](https://azure.microsoft.com/support/faq/)엽니다._

자세한 내용은 [Azure 지원 FAQ를](https://azure.microsoft.com/support/faq/)참조하십시오.

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud는 얼마나 안전한가요?

보안 및 개인 정보 보호는 Azure 및 Azure Spring 클라우드 고객의 최우선 순위 중 하나입니다. Azure는 이 모든 데이터를 안전하게 암호화하여 고객만 응용 프로그램 데이터, 로그 또는 구성에 액세스할 수 있도록 합니다. Azure Spring Cloud의 모든 서비스 인스턴스는 서로 격리되어 있습니다.

Azure 스프링 클라우드는 완전한 TLS/SSL 및 인증서 관리를 제공합니다.

OpenJDK 및 Spring Cloud 런타임에 대한 중요 보안 패치는 가능한 한 빨리 Azure Spring Cloud에 적용됩니다.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Azure 스프링 클라우드를 사용할 수 있는 지역은 어느 지역입니까?

미국 동부, 미국 서부 2, 서유럽 및 동남 아시아 지역입니다.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure 스프링 클라우드의 알려진 제한 사항은 무엇입니까?

미리 보기 릴리스 중에 Azure Spring 클라우드에는 다음과 같은 알려진 제한 사항이 있습니다.

* `spring.application.name`각 응용 프로그램을 만드는 데 사용되는 응용 프로그램 이름으로 재정의됩니다.
* `server.port`Git 리포지토리의 구성 파일에는 허용되지 않습니다. 구성 파일에 추가하면 다른 응용 프로그램이나 인터넷에서 응용 프로그램에 연결할 수 없게 될 수 있습니다.
* Azure 포털 및 Azure 리소스 관리자 템플릿은 응용 프로그램 패키지 업로드를 지원하지 않습니다. Azure CLI를 통해 응용 프로그램을 배포하는 경우에만 응용 프로그램 패키지를 업로드할 수 있습니다.
* 할당량 제한에 대해 알아보려면 [Azure Spring Cloud에서 제공하는 서비스 요금제는 무엇입니까?](#what-service-plans-does-azure-spring-cloud-offer)

### <a name="how-can-i-provide-feedback-and-report-issues"></a>피드백 및 보고서 문제를 제출하려면 어떻게 해야 하나요?

Azure 스프링 클라우드에 문제가 있는 경우 [Azure 지원 요청을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)만듭니다. 기능 요청을 제출하거나 피드백을 제공하려면 [Azure 피드백으로](https://feedback.azure.com/forums/34192--general-feedback)이동하십시오.

## <a name="development"></a>개발

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>나는 스프링 클라우드 개발자이지만 Azure에 새로운. Azure Spring 클라우드 응용 프로그램을 개발하는 방법을 배우는 가장 빠른 방법은 무엇입니까?

Azure Spring Cloud를 시작하는 가장 빠른 방법은 빠른 [시작: Azure 포털을 사용하여 Azure 스프링 클라우드 응용 프로그램 실행의](spring-cloud-quickstart-launch-app-portal.md)지침을 따르십시오.

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud에서 지원하는 Java 런타임은 어떻게 되나요?

Azure Spring Cloud는 Java 8 및 11을 지원합니다.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>스프링 클라우드 애플리케이션 로그 및 메트릭은 어디에서 볼 수 있습니까?

메트릭은 [앱 개요] 탭 및 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) 탭에서 찾습니다.

Azure Spring Cloud는 스프링 클라우드 응용 프로그램 로그 및 메트릭을 Azure 저장소, EventHub 및 [로그 분석으로](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries)내보낼 수 있도록 지원합니다. 로그 분석의 테이블 이름은 *AppPlatformLogsforSpring입니다.* 활성화 하는 방법에 대 한 자세한 내용은 [진단 서비스](diagnostic-services.md)참조

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud는 분산 추적을 지원하나요?

예. 자세한 내용은 [자습서: Azure 스프링 클라우드에서 분산 추적 을 사용합니다.](spring-cloud-tutorial-distributed-tracing.md)

### <a name="what-resource-types-does-service-binding-support"></a>서비스 바인딩에서 지원하는 리소스 종류는 무엇인가요?

현재 세 가지 서비스가 지원됩니다: Azure 코스모스 DB, MySQL용 Azure 데이터베이스 및 Redis용 Azure 캐시입니다.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>응용 프로그램 내에서 영구 볼륨을 보거나 추가하거나 이동할 수 있습니까?

예.

## <a name="deployment"></a>배포

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure Spring 클라우드가 청록색 배포를 지원합니까?
예. 자세한 내용은 [준비 환경 설정을](spring-cloud-howto-staging-environment.md)참조하십시오.

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>내 애플리케이션 컨테이너를 조작하기 위해 Kubernetes에 액세스할 수 있나요?

아니요.  Azure Spring Cloud는 기본 아키텍처에서 개발자를 추상화하여 애플리케이션 코드와 비즈니스 논리에 집중할 수 있도록 합니다.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud는 원본에서 컨테이너를 빌드할 수 있도록 지원하나요?

예. 자세한 내용은 [소스 코드에서 Spring Cloud 응용 프로그램 실행을](spring-cloud-launch-from-source.md)참조하십시오.

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud에서 앱 인스턴스의 자동 크기 조정을 지원하나요?

아니요.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>기존 스프링 클라우드 마이크로 서비스를 Azure 스프링 클라우드로 마이그레이션하는 모범 사례는 무엇입니까?

기존 Spring Cloud 마이크로 서비스를 Azure Spring Cloud로 마이그레이션하는 경우 다음 모범 사례를 준수하는 것이 좋습니다.
* 모든 애플리케이션 종속성을 확인해야 합니다.
* 구성 항목, 환경 변수 및 JVM 매개 변수를 Azure Spring Cloud의 배포와 비교할 수 있도록 준비합니다.
* 서비스 바인딩을 사용하려면 Azure 서비스를 살펴보고 적절한 액세스 권한을 설정했는지 확인합니다.
* 서비스 검색 서비스, Config Server 등Azure Spring Cloud에서 관리하는 서비스와 충돌할 수 있는 임베디드 서비스를 제거하거나 사용하지 않도록 설정하는 것이 좋습니다.
* 공식적인 안정적인 Pivotal 스프링 라이브러리를 사용하는 것이 좋습니다. Pivotal Spring 라이브러리의 비공식, 베타 또는 포크 버전에는 SLA(서비스 수준 계약)가 없습니다.

마이그레이션 후 CPU/RAM 메트릭 및 네트워크 트래픽을 모니터링하여 응용 프로그램 인스턴스의 크기조정이 적절하게 이루어지도록 합니다.

## <a name="next-steps"></a>다음 단계

추가 질문이 있는 경우 [Azure Spring 클라우드 문제 해결 가이드를](spring-cloud-troubleshoot.md)참조하십시오.
