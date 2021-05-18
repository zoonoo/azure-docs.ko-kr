---
title: Azure Spring Cloud에 대한 CI/CD
description: Azure Spring Cloud에 대한 CI/CD
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 331ef39facb9f7cf8f069f2a238be325f53de2d0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878544"
---
# <a name="cicd-for-azure-spring-cloud"></a>Azure Spring Cloud에 대한 CI/CD

연속 통합 및 지속적인 업데이트 도구를 사용하면 최소한의 노력과 위험으로 기존 애플리케이션에 업데이트를 신속하게 배포할 수 있습니다. Azure DevOps는 이러한 핵심 작업을 구성하고 제어하는 데 도움이 됩니다. 현재 Azure 스프링 클라우드는 특정 Azure DevOps 플러그 인을 제공하지 않습니다.  그러나 [Azure CLI 작업](/azure/devops/pipelines/tasks/deploy/azure-cli)을 사용하여 스프링 클라우드 애플리케이션을 DevOps와 통합할 수 있습니다.

이 문서에서는 Azure 스프링 클라우드에서 Azure CLI 작업을 사용하여 Azure DevOps와 통합하는 방법을 보여 줍니다.

## <a name="create-an-azure-resource-manager-service-connection"></a>Azure Resource Manager 서비스 연결 만들기

Azure DevOps 프로젝트에 대한 Azure Resource Manager 서비스 연결을 만드는 방법에 대해 알아보려면 [이 문서](/azure/devops/pipelines/library/connect-to-azure)를 참조하세요. Azure 스프링 클라우드 서비스 인스턴스에 사용하는 것과 동일한 구독을 선택해야 합니다.

## <a name="azure-cli-task-templates"></a>Azure CLI 작업 템플릿
::: zone pivot="programming-language-csharp"
### <a name="deploy-artifacts"></a>아티팩트 배포

일련의 `tasks`를 사용하여 프로젝트를 빌드하고 배포할 수 있습니다. 이 코드 조각은 변수, 애플리케이션을 빌드하는 .NET Core 작업 및 *.zip* 파일을 배포하는 Azure CLI 작업을 정의합니다.

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'
  resourceGroupName: '<your resource group name>'

steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Configure Azure CLI and install spring-cloud extension
- task: AzureCLI@1
  inputs:
    azureSubscription: '<your subscription>'
    scriptLocation: 'inlineScript'
    inlineScript: |
      az extension add --name spring-cloud --y
      az configure --defaults group=${{ variables.resourceGroupName }}
      az configure --defaults spring-cloud=${{ variables.serviceName }}
      az spring-cloud app deploy -n ${{ variables.planetAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.planetMainEntry }} --artifact-path ./${{ variables.planetAppName }}/publish-deploy-planet.zip
      az spring-cloud app deploy -n ${{ variables.solarAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.solarMainEntry }} --artifact-path ./${{ variables.solarAppName }}/publish-deploy-solar.zip
      az spring-cloud app update -n ${{ variables.solarAppName }} --assign-endpoint
      az spring-cloud app show -n ${{ variables.solarAppName }} -o table
    workingDirectory: '${{ variables.workingDirectory }}/src'
```

::: zone-end
::: zone pivot="programming-language-java"
### <a name="deploy-artifacts"></a>아티팩트 배포

일련의 `tasks`를 사용하여 프로젝트를 빌드하고 배포할 수 있습니다. 이 코드 조각은 먼저 애플리케이션을 빌드하는 Maven 작업을 정의하고, Azure 스프링 클라우드 Azure CLI 확장을 사용하여 JAR 파일을 배포하는 두 번째 작업을 정의합니다.

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

### <a name="deploy-from-source"></a>소스에서 배포

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
::: zone-end

## <a name="next-steps"></a>다음 단계

* [빠른 시작: 첫 번째 Azure Spring Cloud 애플리케이션 배포](spring-cloud-quickstart.md)