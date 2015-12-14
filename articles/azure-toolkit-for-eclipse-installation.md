<properties
    pageTitle="Eclipse용 Azure 도구 키트 설치"
    description="Eclipse용 Azure 도구 키트를 설치하는 방법을 알아봅니다."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/19/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/ko-KR/library/azure/hh690946.aspx -->

# Eclipse용 Azure 도구 키트 설치 #

Eclipse용 Azure 도구 키트는 Eclipse 개발 환경에서 Azure 응용 프로그램을 손쉽게 작성, 개발, 테스트 및 배포할 수 있는 템플릿과 기능을 제공합니다. 다음 URL에 있는 GitHub의 프로젝트 사이트를 통해 Apache 라이선스 2.0에 따라 소스 코드 사용이 허가된 오픈 소스 프로젝트입니다.

<https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava>.

다음 단계는 Eclipse용 Azure 도구 키트를 설치하는 방법을 보여줍니다.

## 필수 조건 ##

* Eclipse IDE for Java EE Developers, Indigo 이상. <http://www.eclipse.org/downloads/>에서 다운로드할 수 있습니다.
* JDK(Java Developer Kit) v1.7 이상 
* 운영 체제 - Eclipse용 Azure 도구 키트는 다음 운영 체제에서 테스트되었습니다.
    * Windows 10
    * Windows 8 및 Windows 8.1
    * Windows 7
    * Windows Server 2012
    * Windows Server 2008

> [AZURE.IMPORTANT]Eclipse용 Azure 도구 키트를 Windows에서 사용하는 경우 도구 키트를 위해 Azure SDK 2.7 이상을 설치해야 합니다. Azure SDK 설치하는 옵션은 두 가지입니다.
> 
> * [웹 플랫폼 설치 관리자(WebPI)][]를 사용하여 Azure SDK를 다운로드하고 설치할 수 있습니다.
> * 첫 번째 Azure 배포 프로젝트를 만들 때 Azure SDK가 설치되어 있지 않으면 Azure SDK 필수 버전을 자동으로 다운로드하고 설치한다는 메시지가 표시됩니다.
> 
> Azure SDK는 Windows에서만 필요합니다.

## Eclipse용 Azure 도구 키트 설치 ##

1. Eclipse를 시작합니다.
2. Eclipse의 메뉴에서 아래 다이어그램과 같이 <strong>Help</strong>를 클릭한 후 <strong>Install New Software</strong>를 클릭합니다. ![][ic590123]
3. <strong>Available Software</strong> 대화 상자의 <strong>Work with</strong> 텍스트 상자에 <strong>http://dl.msopentech.com/eclipse</strong>를 입력한 후 <strong>Enter</strong> 키를 누릅니다.
4. <strong>Name</strong> 창에서 <strong>Azure Toolkit for Eclipse</strong>를 선택하고 <strong>Contact all update sites during install to find required software</strong>를 선택하지 않습니다. 화면은 다음과 유사한 모양입니다. ![][ic719482]
5. <strong>Azure Toolkit for Eclipse</strong>를 확장하면 다음 항목이 표시됩니다.
    * **Azure Access Control Services Filter**: 이 구성 요소는 Azure ACS를 사용한 응용 프로그램 사용자 인증을 지원합니다.
    * **Azure Common Plugin**: 이 구성 요소는 다른 구성 요소가 사용하는 공유 기능을 포함합니다.
    * **Azure Toolkit for Eclipse**: 이 구성 요소는 프로젝트 구성 논리, publish-to-cloud 마법사, 사용자 인터페이스를 포함합니다.
    * **Microsoft JDBC Driver 4.0 for SQL Server**: 이 구성 요소는 SQL 데이터베이스를 사용하는 응용 프로그램 개발을 간소화합니다.
    * **Package for Apache Qpid Client Libraries for JMS**: 이 구성 요소는 응용 프로그램이 Azure 내에서 AMQP(Advanced Messaging Queuing Protocol) 기반 메시징을 사용할 수 있도록 Apache Qpid 프로젝트의 JMS 클라이언트 라이브러리를 제공합니다.
    * **Package for Azure Libraries for Java**: 이 구성 요소는 Azure의 확장 가능한 클라우드 컴퓨팅 리소스의 장점을 활용할 수 있도록 Java로 Azure 응용 프로그램을 빌드할 수 있도록 합니다.
    * **Application Insights Plugin for Java**: 이 구성 요소는 응용 프로그램 및 서버 인스턴스에 대해 Azure의 원격 분석 로깅 및 분석 서비스를 사용할 수 있도록 합니다.
6. **다음**을 클릭합니다. (도구 키트를 설치하는 동안 비정상적인 지연이 발생하는 경우에는 **Contact all update sites during install to find required software**가 선택되어 있지 않은지 확인합니다.)
7. **Install Details** 대화 상자에서 **Next**를 클릭합니다.
8. **Review Licenses** 대화 상자에서 사용권 계약 조건을 검토합니다. 사용권 계약 조건에 동의하면 **I accept the terms of the license agreements**를 클릭한 후 **Finish**를 클릭합니다. (나머지 단계에서는 사용권 계약 조건에 동의한다고 가정합니다. 사용권 계약 조건에 동의하지 않으면 설치 프로세스를 종료합니다.)
9. 설치를 완료하기 위해 Eclipse를 다시 시작한다는 메시지가 표시되면 **Restart Now**를 클릭합니다.

## 참고 항목 ##

[Eclipse용 Azure 도구 키트][]

[Eclipse에서 Azure용 Hello World 응용 프로그램 만들기][]

[Eclipse용 Azure 도구 키트의 새로운 기능][]

Java와 함께 Azure를 사용하는 방법에 대한 자세한 내용은 [Azure Java 개발자 센터][]를 참조하세요.

<!-- URL List -->

[Eclipse용 Azure 도구 키트]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure Java 개발자 센터]: http://go.microsoft.com/fwlink/?LinkID=699547
[Eclipse에서 Azure용 Hello World 응용 프로그램 만들기]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[웹 플랫폼 설치 관리자(WebPI)]: http://go.microsoft.com/fwlink/?LinkID=252838
[Eclipse용 Azure 도구 키트의 새로운 기능]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic590123]: ./media/azure-toolkit-for-eclipse-installation/ic590123.png
[ic719482]: ./media/azure-toolkit-for-eclipse-installation/ic719482.png

<!---HONumber=AcomDC_1203_2015-->