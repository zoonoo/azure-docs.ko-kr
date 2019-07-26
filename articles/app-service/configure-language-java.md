---
title: Windows Java 앱 구성-Azure App Service | Microsoft Docs
description: Azure App Service의 기본 Windows 인스턴스에서 실행 되도록 Java 앱을 구성 하는 방법에 대해 알아봅니다.
keywords: azure app service, 웹 앱, windows, oss, java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: c1ea306d8a6b5c1876ac6a9288820e1592dbfda6
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498522"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Azure App Service에 대 한 Windows Java 앱 구성

Azure App Service를 통해 Java 개발자는 완전히 관리 되는 Windows 기반 서비스에서 Tomcat 또는 Java Standard Edition (SE) 패키지 웹 응용 프로그램을 신속 하 게 빌드, 배포 및 확장할 수 있습니다. 명령줄에서 또는 IntelliJ, Eclipse, Visual Studio Code 같은 편집기에서 Maven 플러그 인을 사용하여 애플리케이션을 배포할 수 있습니다.

이 가이드에서는 App Service에서를 사용 하는 Java 개발자를 위한 주요 개념 및 지침을 제공 합니다. Azure App Service 사용한 적이 없는 경우 먼저 [Java 빠른](app-service-web-get-started-java.md) 시작을 참조 하세요. Java 개발과 관련 되지 않은 App Service 사용에 대 한 일반적인 질문에 대 한 답변은 [App Service WINDOWS FAQ](faq-configuration-and-management.md)에서 확인할 수 있습니다.

> [!NOTE]
> 원하는 항목을 찾을 수 없나요? Java 앱을 배포 하 고 보호 하는 방법에 대 한 자세한 내용은 [WINDOWS OSS FAQ](faq-configuration-and-management.md) 또는 [java Linux 구성 가이드](containers/configure-language-java.md) 를 참조 하세요.

## <a name="configuring-tomcat"></a>Tomcat 구성

Tomcat의 `server.xml` 또는 다른 구성 파일을 편집 하려면 먼저 포털에서 Tomcat 주 버전을 기록해 둡니다.

1. `env` 명령을 실행 하 여 버전에 대 한 Tomcat home 디렉터리를 찾습니다. 로 `AZURE_TOMCAT`시작 하 고 주 버전과 일치 하는 환경 변수를 검색 합니다. 예를 들어 `AZURE_TOMCAT85_HOME` 는 Tomcat 8.5에 대 한 Tomcat 디렉터리를 가리킵니다.
1. 버전에 대 한 Tomcat home 디렉터리를 확인 한 후에 구성 디렉터리를로 `D:\home`복사 합니다. 예를 들어의 `AZURE_TOMCAT85_HOME` `D:\Program Files (x86)\apache-tomcat-8.5.37`값이 인 경우 복사 된 디렉터리 `D:\home\apache-tomcat-8.5.37`의 새 경로는입니다.

마지막으로, App Service를 다시 시작합니다. 배포는 이전과 마찬가지로로 `D:\home\site\wwwroot\webapps` 이동 해야 합니다.

## <a name="java-runtime-statement-of-support"></a>Java 런타임 문 지원

### <a name="jdk-versions-and-maintenance"></a>JDK 버전 및 유지 관리

Azure에서 지원되는 JDK(Java Development Kit)는 [Azul Systems](https://www.azul.com/)를 통해 제공하는 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)입니다.

주 버전 업데이트는 Windows 용 Azure App Service의 새로운 런타임 옵션을 통해 제공 됩니다. 고객은 App Service 배포를 구성하여 최신 버전의 Java로 업데이트해야 하며, 주 업데이트를 테스트하고 요구 사항을 충족하도록 관리할 책임이 있습니다.

지원되는 JDK는 매년 분기마다 1월, 4월, 7월, 10월에 자동으로 패치됩니다.

### <a name="security-updates"></a>보안 업데이트

Azul Systems에서 주요 보안 취약점에 대한 패치 및 수정 사항을 출시하는 즉시 고객에게 제공됩니다. [NIST Common Vulnerability Scoring System 버전 2](https://nvd.nist.gov/cvss.cfm)에서 기본 점수 9.0 이상을 받으면 "주요" 취약점으로 정의됩니다.

### <a name="deprecation-and-retirement"></a>사용 중단 및 사용 중지

지원되는 Java 런타임이 폐기되는 경우 폐기 예정인 런타임을 사용하는 Azure 개발자에게는 적어도 런타임 폐기 6개월 전에 사용 중단 알림이 제공됩니다.

### <a name="local-development"></a>로컬 개발

개발자는 [Azul의 다운로드 사이트](https://www.azul.com/downloads/azure-only/zulu/)에서 로컬 개발용 Azul Zulu Enterprise JDK의 프로덕션 버전을 다운로드할 수 있습니다.

### <a name="development-support"></a>개발 지원

Azure [지원 Azul 줄루어 JDK](https://www.azul.com/downloads/azure-only/zulu/) 에 대 한 제품 지원은 azure 용으로 개발 하거나 [정규화 된 azure 지원 계획](https://azure.microsoft.com/support/plans/)을 사용 하 여 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 때 Microsoft를 통해 제공 됩니다.

### <a name="runtime-support"></a>런타임 지원

개발자는 [정규화된 지원 계획](https://azure.microsoft.com/support/plans/)이 있는 경우 Azure 지원을 통해 Azul Zulu JDK 관련 [문제를 제기](/azure/azure-supportability/how-to-create-azure-support-request)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 항목에서는 Windows의 Azure App Service에 대 한 Java 런타임 설명을 제공 합니다.

- Azure App Service에서 웹 응용 프로그램을 호스트 하는 방법에 대 한 자세한 내용은 [App Service 개요](overview.md)를 참조 하세요.
- Azure 개발의 Java에 대 한 자세한 내용은 [Azure Java 개발자 센터를](https://docs.microsoft.com/java/azure/?view=azure-java-stable)참조 하세요.
