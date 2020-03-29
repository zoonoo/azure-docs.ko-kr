---
title: Azure 스프링 클라우드용 CI/CD
description: Azure 스프링 클라우드용 CI/CD
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.openlocfilehash: f329fb5472c5a2eab6f22a2e81b19d90e7045330
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278521"
---
# <a name="cicd-for-azure-spring-cloud"></a>Azure 스프링 클라우드용 CI/CD

지속적인 통합과 지속적인 전달 도구를 통해 개발자는 최소한의 노력과 위험으로 기존 애플리케이션에 업데이트를 신속하게 배포할 수 있습니다. Azure DevOps를 사용하면 이러한 주요 작업을 구성하고 제어할 수 있습니다. 현재 Azure 스프링 클라우드는 특정 Azure DevOps 플러그인을 제공하지 않습니다.  그러나 [Azure CLI 작업을](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops)사용하여 스프링 클라우드 응용 프로그램을 DevOps와 통합할 수 있습니다. 이 문서에서는 Azure 스프링 클라우드와 함께 Azure CLI 작업을 사용하여 Azure DevOps와 통합하는 방법을 보여 주습니다.

## <a name="create-an-azure-resource-manager-service-connection"></a>Azure 리소스 관리자 서비스 연결 만들기

Azure DevOps 프로젝트에 Azure 리소스 관리자 서비스 연결을 만드는 방법을 알아보려면 [이 문서를](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) 읽어보십시오. Azure Spring Cloud 서비스 인스턴스에 대해 사용 중인 것과 동일한 구독을 선택해야 합니다.

## <a name="azure-cli-task-templates"></a>Azure CLI 작업 템플릿

### <a name="deploy-artifacts"></a>아티팩트 배포

일련의 `tasks`을 사용하여 프로젝트를 빌드하고 배포할 수 있습니다. 이 코드 조각은 먼저 응용 프로그램을 빌드하는 Maven 작업을 정의한 다음 Azure Spring Cloud Azure CLI 확장을 사용하여 JAR 파일을 배포하는 두 번째 작업을 정의합니다.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>원본에서 배포

별도의 빌드 단계 없이 Azure에 직접 배포할 수 있습니다.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
