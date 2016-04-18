<properties
   pageTitle="Power BI Embedded .NET API"
   description=""
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# Power BI Embedded .NET API

**Microsoft Power BI Embedded** 미리 보기는 주로 대부분의 기존 Power BI API 기능을 응용 프로그램을 개발하는 데 사용할 수 있는 Azure 서비스의 일부로 노출합니다. 또한 필요한 리소스 및 Power BI 콘텐츠를 프로그래밍 방식으로 프로비전, 개발 및 배포할 수 있습니다.

**Power BI API**를 사용하여 Power BI 콘텐츠 작업 영역을 만들고 관리할 수 있습니다. API를 사용하면

  - 키 기반 인증을 사용하여 Power BI Desktop 파일(PBIX)을 작업 영역으로 가져올 수 있습니다.
  - 데이터 집합의 연결 문자열을 수정할 수 있습니다.
  - 사용자 고유의 앱에 통합할 **보고서**를 가져올 수 있습니다.
  - 올바른 원본 데이터 소스에 설명할 수 있는 데이터 집합에 대한 자격 증명을 설정합니다.
  - **Power BI API**에 대한 자세한 내용은 [Microsoft Power BI Embedded 미리 보기 시작](power-bi-embedded-get-started.md)을 참조하세요.

[MSDN의 Power BI 참조](https://msdn.microsoft.com/library/mt669800.aspx)를 참조하세요.

다음은 **Power BI Embedded 샘플**에서 사용되는 클래스 및 메서드의 예입니다.

## Microsoft.PowerBI.Api.Beta 네임스페이스

### ReportsExtensions 클래스
|이름|설명
|---|---
|[GetReports(IReports, String, String)](https://msdn.microsoft.com/library/microsoft.powerbi.api.beta.reportsextensions.getreports.aspx)|지정된 작업 영역 내에서 사용할 수 있는 보고서 목록을 가져옵니다.
|[GetReportsAsync(IReports, String, String, CancellationToken)](https://msdn.microsoft.com/library/microsoft.powerbi.api.beta.reportsextensions.getreportsasync.aspx)|지정된 작업 영역 내에서 사용할 수 있는 보고서 목록을 가져옵니다.

## Microsoft.PowerBI.Security 네임스페이스

### PowerBIToken 메서드
|이름| 설명
|---|---
|[CreateDevToken(String, Guid)](https://msdn.microsoft.com/library/mt670215.aspx)|Power BI 플랫폼 서비스에 액세스하는 데 사용되는 개발자 토큰(기본 만료 날짜 사용)을 만듭니다.
|[CreateProvisionToken(String)](https://msdn.microsoft.com/library/mt670218.aspx)|작업 영역 컬렉션 내에서 작업 영역을 관리하는 데 사용되는 프로비전 토큰(기본 만료 날짜 사용)을 만듭니다.

## 참고 항목

- [Microsoft Power BI Embedded란](power-bi-embedded-what-is-power-bi-embedded.md)
- [Microsoft Power BI Embedded 미리 보기 시작](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0406_2016-->