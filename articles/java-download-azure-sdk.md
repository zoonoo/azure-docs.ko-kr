---
title: "Java용 Azure SDK 다운로드 | Microsoft Docs"
description: "Maven 프로젝트에 제공된 샘플 코드와 함께 Java용 Azure SDK를 다운로드하는 방법에 대해 알아 봅니다."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 4b8f8fe6-1b26-4bb4-9be9-6ae757a59e66
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm;asirveda
translationtype: Human Translation
ms.sourcegitcommit: 0933eabf99ef94f2c4b425f1435530edfde65e3f
ms.openlocfilehash: 9e55eb52797f1363cbb65050574e549c575ca5d8
ms.lasthandoff: 01/14/2017


---
# <a name="download-the-azure-sdk-for-java"></a>Java용 Azure SDK 다운로드
이 문서는 Java용 Azure 관리 라이브러리를 다운로드하고 설치하기 위한 지침이 포함되어 있습니다.

> [!NOTE]
> Java용 Azure 관리 라이브러리는 [Apache 라이선스, 버전 2.0][license]으로 분산됩니다.
>

## <a name="azure-libraries-for-java---manual-download"></a>Java용 Azure 라이브러리 - 수동 다운로드
Java용 Azure 관리 라이브러리를 수동으로 설치하려면 <http://go.microsoft.com/fwlink/?LinkId=690320>을 클릭하여 모든 라이브러리와 종속성이 포함된 ZIP 파일을 다운로드합니다.

ZIP 파일을 컴퓨터에 다운로드했으면 콘텐츠를 추출하고 다음 옵션 중 하나를 사용하여 프로젝트에 JAR 파일을 추가합니다.

* Eclipse 또는 IntelliJ의 Java 프로젝트에 JAR 파일을 가져옵니다.
* Eclipse 또는 IntelliJ의 Java 프로젝트에 대한 빌드 경로를 구성하여 JAR 파일에 경로를 포함시킵니다.

> [!NOTE]
> 라이선스 및 기타 정보는 ZIP에 포함된 license.txt 및 ThirdPartyNotices.txt 파일을 참조하세요.
>

## <a name="azure-management-libraries-for-java---building-with-maven"></a>Java용 Azure 관리 라이브러리 - Maven을 사용하여 빌드
### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>1단계 - 빌드에 Maven을 사용하도록 프로젝트 설정
Java용 Azure 관리 라이브러리를 사용하는 Eclipse에서 Maven 프로젝트를 만들려면 [Java용 Azure 관리 라이브러리 시작][maven-getting-started] 문서에 나와 있는 지침을 따르세요.

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>2단계 - Maven 설정에서 필수 종속성 구성
빌드에 Maven을 사용하도록 프로젝트를 구성했으면 다음 예제와 같은 구문을 사용하여 pom.xml 파일에 필수 종속성을 추가할 수 있습니다. 다음 예제에 나열되어 있는 모든 종속성을 추가할 필요는 없습니다. 프로젝트에 필요한 특정 종속성만 추가하면 됩니다.

> [!NOTE]
> 다음 샘플의 각 `<version>` 요소 내에서 이 예제의 "n.n.n" 자리 표시자를 유효한 버전 번호로 바꿉니다. 버전 번호는 [Maven의 Azure 라이브러리 리포지토리]에서 얻을 수 있습니다.
>
>

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

## <a name="see-also"></a>참고 항목
Java IDE용 Azure 도구 키트에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Eclipse용 Azure 도구 키트]
  * [Eclipse용 Azure 도구 키트 설치]
  * [Eclipse에서 Azure용 Hello World 웹앱 만들기]
  * [Eclipse용 Azure 도구 키트의 새로운 기능]
* [IntelliJ용 Azure 도구 키트]
  * [IntelliJ용 Azure 도구 키트 설치]
  * [IntelliJ에서 Azure용 Hello World 웹앱 만들기]
  * [IntelliJ용 Azure 도구 키트의 새로운 기능]

Java와 함께 Azure를 사용하는 방법에 대한 자세한 내용은 [Azure Java 개발자 센터]를 참조하세요.

> [!NOTE]
> Eclipse에서 빌드 경로 설정에 대한 자세한 내용은 Eclipse 웹 사이트에서 [Java 빌드 경로] 문서를 참조하세요.
>

<!-- URL List -->

[Eclipse용 Azure 도구 키트]: ./azure-toolkit-for-eclipse.md
[IntelliJ용 Azure 도구 키트]: ./azure-toolkit-for-intellij.md
[Eclipse에서 Azure용 Hello World 웹앱 만들기]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[IntelliJ에서 Azure용 Hello World 웹앱 만들기]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Eclipse용 Azure 도구 키트 설치]: ./azure-toolkit-for-eclipse-installation.md
[IntelliJ용 Azure 도구 키트 설치]: ./azure-toolkit-for-intellij-installation.md
[Eclipse용 Azure 도구 키트의 새로운 기능]: ./azure-toolkit-for-eclipse-whats-new.md
[IntelliJ용 Azure 도구 키트의 새로운 기능]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java 개발자 센터]: http://go.microsoft.com/fwlink/?LinkID=699547
[Maven의 Azure 라이브러리 리포지토리]: http://go.microsoft.com/fwlink/?LinkID=286274
[Java 빌드 경로]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998

