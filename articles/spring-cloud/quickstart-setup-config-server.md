---
title: 빠른 시작 - Azure Spring Cloud 구성 서버 설정
description: 앱 배포를 위한 Azure Spring Cloud 구성 서버 설정에 대해 설명합니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java, fasttrack-edit
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 8c71e76213011beaa63deeaadfa3d6d2dc0d4ce2
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108286542"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>빠른 시작: Azure Spring Cloud 구성 서버 설정

Azure Spring Cloud 구성 서버는 분산 시스템을 위한 중앙 집중식 구성 서비스입니다. 현재 로컬 스토리지, Git 및 Subversion을 지원하는 플러그형 리포지토리 계층을 사용합니다. 이 빠른 시작에서는 Git 리포지토리에서 데이터를 가져오도록 구성 서버를 설정합니다.

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>사전 요구 사항

* 이 시리즈의 이전 빠른 시작을 완료합니다. [Azure Spring Cloud 서비스를 프로비저닝](./quickstart-provision-service-instance.md)합니다.

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure Spring Cloud 구성 서버 절차

다음 명령을 실행하여 프로젝트의 git 리포지토리 위치로 구성 서버를 설정합니다. `<service instance name>`을 앞에서 만든 서비스 이름으로 바꿉니다. 이전 빠른 시작에서 설정한 서비스 인스턴스 이름의 기본값은 이 명령에서 작동하지 않습니다.

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples --search-paths steeltoe-sample/config
```

이 명령은 샘플 앱 리포지토리의 [steeltoe-sample/config](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample/config) 폴더에서 구성 데이터를 찾도록 구성 서버에 지시합니다. 구성 데이터를 가져올 앱의 이름이 `planet-weather-provider`이므로 사용할 파일은 [planet-weather-provider.yml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/steeltoe-sample/config/planet-weather-provider.yml)입니다.

::: zone-end

::: zone pivot="programming-language-java"
Azure Spring Cloud 구성 서버는 분산 시스템을 위한 중앙 집중식 구성 서비스입니다. 현재 로컬 스토리지, Git 및 Subversion을 지원하는 플러그형 리포지토리 계층을 사용합니다.  마이크로서비스 앱을 Azure Spring Cloud에 배포하도록 구성 서버를 설정합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [JDK 8 설치](/java/azure/jdk/)
* [Azure 구독에 가입](https://azure.microsoft.com/free/)
* (선택 사항) [Azure CLI 버전 2.0.67 이상을 설치](/cli/azure/install-azure-cli)하고 `az extension add --name spring-cloud` 명령을 사용하여 Azure Spring Cloud 확장을 설치합니다.
* (선택 사항) [Azure Toolkit for IntelliJ를 설치](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/)하고 [로그인](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)합니다.

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure Spring Cloud 구성 서버 절차

#### <a name="portal"></a>[포털](#tab/Azure-portal)

다음 절차에서는 Azure Portal을 사용하여 [PetClinic 샘플](https://github.com/azure-samples/spring-petclinic-microservices)을 배포하는 구성 서버를 설정합니다.

1. 서비스 **개요** 페이지로 이동하여 **구성 서버** 를 선택합니다.

2. **기본 리포지토리** 섹션에서 **URI** 를 "https://github.com/azure-samples/spring-petclinic-microservices-config"로 설정합니다.

3. **유효성 검사** 를 클릭합니다.

    ![구성 서버로 이동](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

4. 유효성 검사가 완료되면 **적용** 을 클릭하여 변경 내용을 저장합니다.

    ![구성 서버 유효성 검사](media/spring-cloud-quickstart-launch-app-portal/validate-complete.png)

5. 구성을 업데이트하는 데 몇 분 정도 걸릴 수 있습니다.
 
    ![구성 서버 업데이트](media/spring-cloud-quickstart-launch-app-portal/updating-config.png) 

6. 구성이 완료되면 알림을 받게 됩니다.

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)


다음 절차에서는 Azure CLI를 사용하여 [Pet Clinic 샘플](https://github.com/azure-samples/spring-petclinic-microservices)을 배포하도록 구성 서버를 설정합니다.

다음 명령을 실행하여 기본 리포지토리를 설정합니다.

```azurecli

az spring-cloud config-server git set -n <service instance name> --uri https://github.com/azure-samples/spring-petclinic-microservices-config
::: zone-end

> [!TIP]
> If you are using a private repository for config server, please refer to our [tutorial on setting up authentication](./how-to-config-server.md).

## Troubleshooting of Azure Spring Cloud config server

The following procedure explains how to troubleshoot config server settings.

1. In the Azure portal, go to the service **Overview** page and select **Logs**. 
1. Select **Queries** and **Show the application logs that contain the "error" or "exception" terms"**. 
1. Click **Run**. 
1. If you find the error **java.lang.illegalStateException** in logs, this indicates that spring cloud service cannot locate properties from config server.

    [ ![ASC portal run query](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png)

1. Go to the service **Overview** page.
1. Select **Diagnose and solve problems**. 
1. Select **Config Server** detector.

    [ ![ASC portal diagnose problems](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png)

3. Click **Config Server Health Check**.

    [ ![ASC portal genie](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png)

4. Click **Config Server Status** to see more details from the detector.

    [ ![ASC portal health status](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png)

## Next steps

In this quickstart, you created Azure resources that will continue to accrue charges if they remain in your subscription. If you don't intend to continue on to the next quickstart, see [Clean up resources](./quickstart-logs-metrics-tracing.md#clean-up-resources). Otherwise, advance to the next quickstart:

> [!div class="nextstepaction"]
> [Build and deploy apps](./quickstart-deploy-apps.md)
