---
title: Azure Resource Manager 템플릿 샘플
description: 몇 가지 일반적인 App Service 시나리오에 대한 Azure Resource Manager 템플릿 샘플을 찾습니다. App Service 배포 또는 관리 작업을 자동화하는 방법을 알아봅니다.
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 08/26/2020
ms.author: tomfitz
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 5129fccceb633991767cdd3b52bbb5b6af067270
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88891048"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>App Service에 대한 Azure Resource Manager 템플릿

다음 표는 Azure App Service를 위한 Azure Resource Manager 템플릿 링크를 포함합니다. 앱 템플릿을 만들 때 발생하는 일반적인 오류 방지에 대한 권장 사항은 [Azure Resource Manager 템플릿으로 앱을 배포하는 지침](deploy-resource-manager-template.md)을 참조하세요.

App Services 리소스의 JSON 구문 및 속성에 대해 알아보려면 [Microsoft.Web 리소스 종류](/azure/templates/microsoft.web/allversions)를 참조하세요.

| 앱 배포 | Description |
|-|-|
| [App Service 계획 및 기본 Linux 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Linux용으로 구성된 App Service 앱을 배포합니다. |
| [App Service 계획 및 기본 Windows 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Windows용으로 구성된 App Service 앱을 배포합니다. |
| [GitHub 리포지토리에 연결된 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| GitHub에서 코드를 끌어오는 App Service 앱을 배포합니다. |
| [사용자 지정 배포 슬롯이 있는 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| 사용자 지정 배포 슬롯/환경이 있는 App Service 앱을 배포합니다. |
| [프라이빗 엔드포인트가 포함된 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/101-private-endpoint-webapp)| 프라이빗 엔드포인트를 사용하여 App Service 앱을 배포합니다. |
|**앱 구성**| **설명** |
| [Key Vault의 앱 인증서](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Azure Key Vault 비밀의 App Service 앱 인증서를 배포하여 TLS/SSL 바인딩에 사용합니다. |
| [사용자 지정 도메인 및 SSL이 있는 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| 사용자 지정 호스트 이름이 있는 App Service 앱을 배포하고 TLS/SSL 바인딩을 위해 Key Vault에서 앱 인증서를 가져옵니다. |
| [GoLang 확장이 있는 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Golang 사이트 확장을 사용하여 App Service 앱을 배포합니다. 그런 다음, Azure의 Golang에서 개발된 웹 애플리케이션을 실행할 수 있습니다. |
| [Java 8 및 Tomcat 8이 있는 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Java 8 및 Tomcat 8을 사용하여 App Service 앱을 배포합니다. 그런 다음, Azure에서 Java 애플리케이션을 실행할 수 있습니다. |
| [지역 VNet 통합이 포함된 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-service-regional-vnet-integration)| 지역 VNet 통합이 활성화된 App Service 앱을 배포합니다. |
|**앱 보호**| **설명** |
| [Azure Application Gateway와 통합된 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2)| App Service 앱 및 Application Gateway를 배포하고 서비스 엔드포인트와 액세스 제한을 사용하여 트래픽을 격리합니다. |
|**연결된 리소스를 사용하는 Linux 앱**| **설명** |
| [MySQL을 사용하는 Linux의 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Azure Database for MySQL로 Linux에 App Service 앱을 배포합니다. |
| [PostgreSQL을 사용하는 Linux의 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Azure Database for PostgreSQL로 Linux에 App Service 앱을 배포합니다. |
|**연결된 리소스를 사용하는 앱**| **설명** |
| [MySQL을 사용하는 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Azure Database for MySQL로 Windows에 App Service 앱을 배포합니다. |
| [PostgreSQL을 사용하는 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Azure Database for PostgreSQL로 Windows에 App Service 앱을 배포합니다. |
| [Azure SQL Database의 데이터베이스가 포함된 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| 기본 서비스 수준에서 Azure SQL Database에 App Service 앱과 데이터베이스를 배포합니다. |
| [Blob 스토리지 연결을 사용하는 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Azure Blob 스토리지 연결 문자열을 사용하여 App Service 앱을 배포합니다. 그런 다음, 앱에서 Blob 스토리지를 사용할 수 있습니다. |
| [Azure Cache for Redis를 사용하는 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Azure Cache for Redis를 사용하여 App Service 앱을 배포합니다. |
| [백 엔드 웹앱에 연결된 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-privateendpoint-vnet-injection)| VNet 삽입 및 Private Endpoint와 함께 안전하게 연결된 두 개의 웹앱(프런트 엔드 및 백 엔드)을 배포합니다. |
|**App Service Environment**| **설명** |
| [App Service Environment v2 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | 가상 네트워크에 App Service Environment v2를 만듭니다. |
| [ILB 주소로 App Service Environment v2 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | 프라이빗 내부 부하 분산 장치 주소로 가상 네트워크에 App Service Environment v2를 만듭니다. |
| [ILB App Service Environment 또는 ILB App Service Environment v2에 대한 기본 SSL 인증서 구성](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | ILB App Service 환경 또는 ILB App Service 환경 v2에 대한 기본 TLS/SSL 인증서를 구성합니다. |
| | |
