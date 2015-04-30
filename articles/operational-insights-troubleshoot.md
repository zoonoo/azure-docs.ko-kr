<properties 
   pageTitle="Operational Insights 문제 해결"
   description="Operational Insights 문제 해결에 대해 알아봅니다."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Operational Insights 문제 해결
다음 섹션의 정보를 사용하여 문제를 해결합니다. 발생한 문제가 이 문서에 없으면 [Operational Insights 팀 블로그](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx) 를 방문해봅니다.

## Operational Insights의 연결 진단 문제

Microsoft Azure Operational Insights는 클라우드로(부터) 이동한 데이터를 사용하기 때문에 연결 문제를 무력화할 수 있습니다. 다음 정보를 사용하여 연결 문제를 이해하고 해결합니다.



<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>오류 메시지</b></td>
		<td><b>가능한 원인</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>인터넷 연결을 검사했지만 Operational Insights 서비스에 대한 연결을 설정할 수 없습니다. 나중에 다시 시도하세요.|</td>
		<td>Operational Insights 서비스는 유지 관리됩니다. Operational Insights 유지 관리가 완료될 때까지 기다립니다.<p>네트워크가 Operational Insights를 차단했습니다. 네트워크 관리자에게 문의하고 Operational Insights에 대한 액세스를 요청하거나 다른 서버를 게이트웨이로 사용합니다.</td>
    </tr>
    <tr align="left" valign="top">
		<td>인터넷 연결을 설정할 수 없습니다. 프록시 설정을 확인합니다.</td>
		<td>이 서버는 인터넷에 연결되지 않았습니다. 인터넷 연결 상태를 확인하고 서버를 인터넷에 연결합니다.<p>프록시 설정이 올바르지 않습니다. 프록시 설정 또는 변경 방법에 대한 자세한 내용은 <A HREF="operational-insights-proxy-filewall.md">프록시 및 방화벽 설정 구성</A>을 참조하세요.<p>프록시 서버에 인증이 필요합니다. 프록시 서버를 사용하도록 Operations Manager를 구성하는 방법에 대한 자세한 내용은 <A HREF="operational-insights-proxy-filewall.md">프록시 및 방화벽 설정 구성</A>을 참조하세요.</td>
    </tr>
    </tbody>
    </table>

## SQL Server 검색 문제 해결

Microsoft SQL Server 2008 R2를 실행하고 Operations Manager 에이전트를 배포했음에도 불구하고 이 서버에 대한 경고가 나타나지 않으면 검색 문제가 있을 수 있습니다.

문제가 발생하는 원인이 검색 문제인지 확인하려면 다음 두 문제를 확인합니다.

- Operations Manager 이벤트 로그에서 이벤트 ID 4001을 볼 수 있습니다. 이 이벤트는 잘못된 클래스를 나타냅니다.

- SQL Server 구성 관리자에서 SQL Server 서비스를 볼 때 "원격 프로시저 호출에 실패했습니다. [0x0800706be]" 오류 메시지가 나타납니다.

두 가지 문제 모두에 해당할 경우 SQL Server 2008 R2 Service Pack 2를 설치해야 합니다. 이 서비스 팩을 다운로드하려면 Microsoft 다운로드 센터에서 [SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310) 을 참조하세요.

서비스 팩을 설치한 후 서버에 대한 Operational Insights 데이터가 24시간 내에 표시되어야 합니다.


<!--HONumber=52-->