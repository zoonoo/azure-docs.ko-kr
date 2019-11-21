---
title: Frequently asked questions about Azure Spring Cloud | Microsoft Docs
description: This article answers frequently asked questions about Azure Spring Cloud.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 6835132192040b4b943b156fb78ae1547522be0c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229245"
---
# <a name="azure-spring-cloud-faq"></a>Azure Spring Cloud FAQ

This article answers frequently asked questions about Azure Spring Cloud. 

## <a name="general"></a>일반

### <a name="why-azure-spring-cloud"></a>Azure Spring Cloud가 적합한 이유는?

Azure Spring Cloud provides a platform as a service (PaaS) for Spring Cloud developers. Azure Spring Cloud manages your application infrastructure so that you can focus on application code and business logic. Core features built into Azure Spring Cloud include Eureka, Config Server, Service Registry Server, Pivotal Build Service, Blue-green deployments, and more. This service also enables developers to bind their applications with other Azure services, such as Azure Cosmos DB, Azure Database for MySQL, and Azure Cache for Redis.

Azure Spring Cloud enhances the application diagnostics experience for developers and operators by integrating Azure Monitor, Application Insights, and Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Azure Spring Cloud에서 제공하는 서비스 계획은 어떻게 되나요?

Azure Spring Cloud는 미리 보기 기간 동안 하나의 서비스 계획을 제공합니다.  A Spring Cloud deployment contains 16 vCPU cores and 32 gigabytes (GB) of memory.  The upper bound for each microservice instance within a deployment is 4 vCPU cores with 8 GB of memory.

리소스 | 금액
------- | -------
Spring 애플리케이션당 앱 인스턴스 수 | 20
Azure Spring Cloud 서비스 인스턴스당 총 앱 인스턴스 수 | 500
구독당 지역별 Azure Spring Cloud 서비스 인스턴스 수 | 10
영구적 볼륨 | 10x50GB

\* _To raise the limit, open a [support ticket](https://azure.microsoft.com/support/faq/)._

For more information, see [Azure Support FAQ](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud는 얼마나 안전한가요?

Security and privacy are among the top priorities for Azure and Azure Spring Cloud customers. Azure helps ensure that only customers have access to application data, logs, or configurations by securely encrypting all of this data. Azure Spring Cloud의 모든 서비스 인스턴스는 서로 격리되어 있습니다.

Azure Spring Cloud는 완전한 SSL 및 인증서 관리를 제공합니다.

OpenJDK 및 Spring Cloud 런타임에 대한 중요 보안 패치는 가능한 한 빨리 Azure Spring Cloud에 적용됩니다.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>In which regions is Azure Spring Cloud available?

미국 동부, 미국 서부 2, 유럽 서부 및 동남 아시아 지역입니다.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>What are the known limitations of Azure Spring Cloud?

During preview release, Azure Spring Cloud has the following known limitations:

* `spring.application.name` will be overridden by the application name that's used to create each application.
* `server.port` is not allowed in the configuration file from the Git repo. Adding it to the configuration file will likely render your application unreachable from other applications or the internet.
* The Azure portal and Azure Resource Manager templates do not support uploading application packages. You can upload application packages only by deploying the application via the Azure CLI.
* To learn about quota limitations, see [What service plans does Azure Spring Cloud offer?](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>피드백 및 보고서 문제를 제출하려면 어떻게 해야 하나요?

If you encounter any issues with Azure Spring Cloud, create an [Azure Support Request](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). To submit a feature request or provide feedback, go to [Azure Feedback](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>개발

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>I am a Spring Cloud developer but new to Azure. What is the quickest way for me to learn how to develop an Azure Spring Cloud application?

For the quickest way to get started with Azure Spring Cloud, follow the instructions in [Quickstart: Launch an Azure Spring Cloud application by using the Azure portal](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud에서 지원하는 Java 런타임은 어떻게 되나요?

Azure Spring Cloud는 Java 8 및 11을 지원합니다.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Where can I view my Spring Cloud application logs and metrics?

메트릭은 [앱 개요] 탭 및 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) 탭에서 찾습니다.

Azure Spring Cloud supports exporting Spring Cloud application logs and metrics to Azure Storage, EventHub, and [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). The table name in Log Analytics is *AppPlatformLogsforSpring*. To learn how to enable it, see [Diagnostic services](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud는 분산 추적을 지원하나요?

예. For more information, see [Tutorial: Use Distributed Tracing with Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>서비스 바인딩에서 지원하는 리소스 종류는 무엇인가요?

Three services are currently supported: Azure Cosmos DB, Azure Database for MySQL, and Azure Cache for Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Can I view, add, or move persistent volumes from inside my applications?

예.

## <a name="deployment"></a>배포

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Does Azure Spring Cloud support blue-green deployment?
예. For more information, see [Set up a staging environment](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>내 애플리케이션 컨테이너를 조작하기 위해 Kubernetes에 액세스할 수 있나요?

아닙니다.  Azure Spring Cloud는 기본 아키텍처에서 개발자를 추상화하여 애플리케이션 코드와 비즈니스 논리에 집중할 수 있도록 합니다.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud는 원본에서 컨테이너를 빌드할 수 있도록 지원하나요?

예. For more information, see [Launch your Spring Cloud application from source code](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud에서 앱 인스턴스의 자동 크기 조정을 지원하나요?

아닙니다.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>What are the best practices for migrating existing Spring Cloud microservices to Azure Spring Cloud?

As you're migrating existing Spring Cloud microservices to Azure Spring Cloud, it's a good idea to observe the following best practices:
* 모든 애플리케이션 종속성을 확인해야 합니다.
* Prepare your configuration entries, environment variables, and JVM parameters so that you can compare them with the deployment in Azure Spring Cloud.
* If you want to use Service Binding, go through your Azure services and ensure that you've set the appropriate access permissions.
* We recommend that you remove or disable any embedded services that might conflict with services that are managed by Azure Spring Cloud, such as our Service Discovery service, Config Server, and so on.
* We recommend that you use official, stable Pivotal Spring libraries. Unofficial, beta, or forked versions of Pivotal Spring libraries have no service-level agreement (SLA) support.

After the migration, monitor your CPU/RAM metrics and network traffic to ensure that the application instances are scaled appropriately.

## <a name="next-steps"></a>다음 단계

If you have further questions, see the [Azure Spring Cloud troubleshooting guide](spring-cloud-troubleshoot.md).
