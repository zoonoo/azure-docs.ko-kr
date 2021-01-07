---
title: Azure 스프링 클라우드 마이크로 서비스 앱 용 Java 및 기본 OS
description: Azure 스프링 클라우드 마이크로 서비스 앱에 대 한 정상 Java 및 기본 운영 체제를 유지 관리 하기 위한 원칙
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/27/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0c90062f1968cc7be5a742a67363f57b9632fdfa
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090678"
---
# <a name="java-and-base-os-for-spring-microservice-apps"></a>스프링 마이크로서비스 앱용 Java 및 기본 OS

**이 문서는 다음에 적용됩니다.** ✔️ Java

다음은 스프링 마이크로 서비스 앱에 대 한 정상 Java 및 기본 운영 체제를 유지 관리 하기 위한 원칙입니다.
## <a name="principles-for-healthy-java-and-base-os"></a>정상 Java 및 기본 OS에 대 한 원칙
* 계층의 기본 운영 체제가 동일 해야 합니다.-기본 | 표준 | 유료.
    * 현재 Azure 스프링 클라우드의 앱은 Debian 10 및 Ubuntu 18.04을 혼합 하 여 사용 합니다.
    * VMware 빌드 서비스는 Ubuntu 18.04를 사용 합니다.
* 배포 시작 지점과 관계 없이 동일한 기본 운영 체제 여야 합니다.-원본 | 파일로
    * 현재 Azure 스프링 클라우드의 앱은 Debian 10 및 Ubuntu 18.04을 혼합 하 여 사용 합니다.
* 기본 운영 체제에는 보안 취약성이 없어야 합니다.
    * Debian 10 기본 운영 체제에는 147 open CVEs가 있습니다.
    * Ubuntu 18.04 기본 운영 체제에는 132 open CVEs가 있습니다.
* 는 JRE-헤드리스를 사용 해야 합니다.
    * 현재 Azure 스프링 클라우드의 앱은 JDK를 사용 합니다. JRE-헤드리스는 더 작은 이미지입니다.
* 는 Java의 최신 빌드를 사용 해야 합니다.
    * 현재 Azure 스프링 클라우드의 앱은 Java 8 빌드 242를 사용 합니다. 이는 오래 된 빌드입니다.
 
Azul 시스템은 기본 운영 체제에 대 한 변경 내용을 지속적으로 검색 하 고 마지막으로 빌드된 이미지를 최신 상태로 유지 합니다. Azure 스프링 클라우드는 이미지에 대 한 변경 내용을 검색 하 여 배포 간에 지속적으로 업데이트 합니다.
 
## <a name="faq-for-azure-spring-cloud"></a>Azure Spring Cloud에 대한 FAQ

* 어떤 버전의 Java가 지원 되나요? 주 버전 및 빌드 번호입니다.
    * LTS 버전-Java 8 및 11을 지원 합니다.
    * 최신 빌드를 사용 합니다. 예를 들어 지금 바로 Java 8 빌드 252 및 Java 11 빌드 7 빌드를 사용 합니다.
* 이러한 Java 런타임을 만든 사람은 누구 인가요?
    * Azul 시스템.
* 이미지에 대 한 기본 운영 체제는 무엇 인가요?
    * Ubuntu 20.04 LTS (초점면). 앱은 Ubuntu의 최신 LTS 버전을 계속 유지 합니다.
    * [Ubuntu 20.04 LTS (초점면)를](http://releases.ubuntu.com/focal/) 참조 하세요.
* 로컬 개발을 위해 지원 되는 Java 런타임을 다운로드 하려면 어떻게 해야 하나요? 
    * [Azure 및 Azure Stack에 대 한 JDK 설치를](/azure/developer/java/fundamentals/java-jdk-install) 참조 하세요.
* Java 런타임 수준에서 문제에 대 한 지원을 받으려면 어떻게 해야 하나요?
    * Azure 지원으로 지원 티켓을 엽니다.
 
## <a name="default-deployment-on-azure-spring-cloud"></a>Azure 스프링 클라우드의 기본 배포

> ![기본 배포](media/spring-cloud-principles/spring-cloud-default-deployment.png)
 
## <a name="next-steps"></a>다음 단계

* [빠른 시작: 첫 번째 Azure Spring Cloud 애플리케이션 배포](spring-cloud-quickstart.md)
* [Azure 및 Azure Stack에 대 한 Java 장기 지원](/azure/developer/java/fundamentals/java-jdk-long-term-support)