<properties linkid="manage-services-integration-hybrid-connection" urlDisplayName="Hybrid Connections Overview - BizTalk Services" pageTitle="Hybrid Connections Overview | Azure" metaKeywords="BizTalk Services, BizTalk, websites, web sites, hybrid connections, Azure" description="Learn about hybrid connections, including Security." metaCanonical="" services="integration-services" documentationCenter="" title="Hybrid Connections Overview" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="mandia" />



# 하이브리드 연결 개요
이 항목에서는 하이브리드 연결을 소개하고, 지원되는 구성과 필요한 TCP 포트를 설명합니다. 구체적으로 살펴보면 다음과 같습니다.

- [하이브리드 연결 정의][하이브리드 연결 정의]
- [지원되는 구성][지원되는 구성]
- [보안][보안]

## <a name="HCOverview"></a> 하이브리드 연결 정의

하이브리드 연결은 Azure 웹 사이트 및 Azure 모바일 서비스를 온-프레미스 리소스에 연결하는 쉽고 편리한 방법을 제공합니다. 하이브리드 연결은 Azure BizTalk 서비스의 기능입니다.

![하이브리드 연결][하이브리드 연결]

하이브리드 연결은 다음과 같은 이점을 제공합니다.

- 웹 사이트 및 모바일 서비스는 기존 온-프레미스 데이터 및 서비스에 안전하게 액세스할 수 있습니다.
- 여러 웹 사이트 또는 모바일 서비스가 하나의 하이브리드 연결을 공유하여 온-프레미스 리소스에 액세스할 수 있습니다.
- 네트워크에 액세스하려면 최소의 TCP 포트가 필요합니다.
- 하이브리드 연결을 사용하는 응용 프로그램은 하이브리드 연결을 통해 게시되는 특정 온-프레미스 리소스에만 액세스합니다.
- SQL Server, MySQL, HTTP Web API 및 대부분의 사용자 지정 웹 서비스와 같이 정적 TCP 포트를 사용하는 모든 온-프레미스 리소스에 연결할 수 있습니다.

> [WACOM.NOTE] 동적 포트(예: FTP 수동 모드 또는 확장 수동 모드)를 사용하는 TCP 기반 서비스는 현재 지원되지 않습니다.

- Azure 웹 사이트에서 지원하는 모든 프레임워크(.NET, PHP, Java, Python, Node.js) 및 Azure 모바일 서비스에서 지원하는 모든 프레임워크(Node.js, .NET)에서 사용할 수 있습니다.
- 웹 사이트 및 모바일 서비스는 웹 사이트나 모바일 서비스가 로컬 네트워크에 위치하는 것처럼 정확히 동일한 방식으로 온-프레미스 리소스에 액세스할 수 있습니다. 예를 들어 온-프레미스에 사용되는 동일한 연결 문자열을 Azure에서도 사용할 수 있습니다.


또한 하이브리드 연결은 다음을 비롯하여 하이브리드 응용 프로그램에서 액세스하는 회사 리소스를 엔터프라이즈 관리자를 통해 제어하고 확인할 수 있도록 합니다.

- 관리자는 그룹 정책 설정을 사용하여 네트워크의 하이브리드 연결을 허용하고, 하이브리드 응용 프로그램을 통해 액세스할 수 있는 리소스를 지정할 수 있습니다.
- 회사 네트워크의 이벤트 및 감사 로그를 사용하여 하이브리드 연결을 통해 액세스하는 리소스를 확인할 수 있습니다.


## <a name="KnownIssues"></a> 지원되는 구성

하이브리드 연결은 다음 프레임워크 및 응용 프로그램 조합을 지원합니다.

- .NET Framework에서 SQL Server 액세스
- .NET Framework에서 WebClient를 사용하여 HTTP/HTTPS 서비스 액세스
- PHP에서 SQL Server, MySQL 액세스
- Java에서 SQL Server, MySQL 및 Oracle 액세스
- Java에서 HTTP/HTTPS 서비스 액세스

하이브리드 연결을 사용하여 온-프레미스 SQL Server에 액세스할 때는 다음을 고려하세요.

- SQL Express 명명된 인스턴스는 정적 포트를 사용하도록 구성해야 합니다. 기본적으로 SQL Express 명명된 인스턴스는 동적 포트를 사용합니다.
- SQL Express 기본 인스턴스는 정적 포트를 사용하지만 TCP를 사용하도록 설정해야 합니다. 기본적으로 TCP는 사용되도록 설정되어 있지 않습니다.
- 클러스터링 및 가용성 그룹을 사용할 때는 현재 MultiSubnetFailover=true 모드가 지원되지 않습니다.
- ApplicationIntent=ReadOnly는 현재 지원되지 않습니다.
- Azure 응용 프로그램 및 온-프레미스 SQL Server에서 지원하는 종단 간 권한 부여 방법으로 SQL 인증이 필요할 수 있습니다.


## <a name="HCSecurity"></a> 보안

하이브리드 연결은 SAS(공유 액세스 서명) 권한 부여를 사용하여 Azure 응용 프로그램 및 온-프레미스 하이브리드 연결 관리자에서 하이브리드 연결로의 연결에 대한 보안을 유지합니다. 응용 프로그램과 온-프레미스 하이브리드 연결 관리자에 대해 별도의 연결 키가 만들어집니다. 이러한 연결 키는 별도로 롤오버되고 해지됩니다.

하이브리드 연결은 응용 프로그램과 온-프레미스 하이브리드 연결 관리자로 원활하고 안전하게 키를 배포합니다.

[하이브리드 연결 만들기 및 관리][하이브리드 연결 만들기 및 관리](영문)를 참조하세요.

**응용 프로그램 권한 부여가 하이브리드 연결과는 별개임**. 적절한 어떤 권한 부여 방법도 사용될 수 있습니다. 권한 부여 방법은 Azure 클라우드와 온-프레미스 구성 요소에서 지원되는 종단 간 권한 부여 방법에 따라 좌우됩니다. 예를 들어 Azure 응용 프로그램은 온-프레미스 SQL Server에 액세스합니다. 이 시나리오에서 SQL 권한 부여는 종단 간에 지원되는 권한 부여 방법일 수 있습니다.

#### TCP 포트

<table border="1">
    <tr>
       <th><strong>포트</strong></th>
        <th>이유</th>
    </tr>
    <tr>
        <td>80</td>
        <td>HTTP 포트. 인증서 유효성 검사에 사용됩니다.</td>
    </tr>
    <tr>
        <td>443</td>
        <td>HTTPS 포트</td>
    </tr>
	<tr>
        <td>5671</td>
        <td>Azure에 연결하는 데 사용됩니다. TCP 포트 5671을 사용할 수 없으면 TCP 포트 443이 사용됩니다.</td>
	</tr>
	<tr>
        <td>9352</td>
        <td>데이터를 푸시하고 가져오는 데 사용됩니다. TCP 포트 9352을 사용할 수 없으면 TCP 포트 443이 사용됩니다.</td>
	</tr>
</table>


## 다음

- [하이브리드 연결 만들기 및 관리][하이브리드 연결 만들기 및 관리]
- [Azure 웹 사이트를 온-프레미스 리소스에 연결(영문)][Azure 웹 사이트를 온-프레미스 리소스에 연결(영문)]
- [하이브리드 연결 단계별 과정: Azure 웹 사이트에서 온-프레미스 SQL Server에 연결][하이브리드 연결 단계별 과정: Azure 웹 사이트에서 온-프레미스 SQL Server에 연결](영문)
- [Azure 모바일 서비스 및 하이브리드 연결(영문)][Azure 모바일 서비스 및 하이브리드 연결(영문)]


## 참고 항목

- [Microsoft Azure의 BizTalk 서비스를 관리하기 위한 REST API][Microsoft Azure의 BizTalk 서비스를 관리하기 위한 REST API]
- [BizTalk 서비스: Editions 차트][BizTalk 서비스: Editions 차트]
- [Azure 관리 포털을 사용하여 BizTalk 서비스 만들기][Azure 관리 포털을 사용하여 BizTalk 서비스 만들기]
- [BizTalk 서비스: 대시보드, 모니터 및 크기 조정 탭][BizTalk 서비스: 대시보드, 모니터 및 크기 조정 탭]


[하이브리드 연결 정의]: #HCOverview
[지원되는 구성]: #KnownIssues
[보안]: #HCSecurity
[하이브리드 연결]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[하이브리드 연결 만들기 및 관리]: http://azure.microsoft.com/ko--kr/documentation/articles/integration-hybrid-connection-create-manage
[Azure 웹 사이트를 온-프레미스 리소스에 연결(영문)]: http://go.microsoft.com/fwlink/p/?LinkId=397538
[하이브리드 연결 단계별 과정: Azure 웹 사이트에서 온-프레미스 SQL Server에 연결]: http://go.microsoft.com/fwlink/?LinkID=397979
[Azure 모바일 서비스 및 하이브리드 연결(영문)]: http://azure.microsoft.com/ko--kr/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started
[Microsoft Azure의 BizTalk 서비스를 관리하기 위한 REST API]: http://msdn.microsoft.com/library/azure/dn232347.aspx
[BizTalk 서비스: Editions 차트]: http://go.microsoft.com/fwlink/p/?LinkID=302279
[Azure 관리 포털을 사용하여 BizTalk 서비스 만들기]: http://go.microsoft.com/fwlink/p/?LinkID=302280
[BizTalk 서비스: 대시보드, 모니터 및 크기 조정 탭]: http://go.microsoft.com/fwlink/p/?LinkID=302281
