<properties 
	pageTitle="BizTalk 서비스:" 
	description="작업 로그를 사용한 문제 해결 | Azure 작업 로그를 사용하여 BizTalk 서비스의 문제를 해결합니다. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="nitinme" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="mandia"/>


# BizTalk 서비스: 작업 로그를 사용한 문제 해결

작업 로그는 Azure 관리 포털에서 사용할 수 있는 관리 서비스 기능으로서 BizTalk 서비스를 포함한 Azure 서비스에서 수행된 작업의 기록 로그를 볼 수 있습니다. 이렇게 하면 BizTalk 서비스 구독에서 180일 이전의 관리 작업과 관련된 기록 데이터를 볼 수 있습니다.

<div class="dev-callout"><b>참고</b>
<p>이 기능은 서비스가 시작되었을 경우, 백업되었을 경우 등과 같은 BizTalk 서비스의 관리 작업에 대한 로그만 캡처합니다. 이러한 작업은 Azure 관리 포털에서 수행되었는지 또는 <a href="http://msdn.microsoft.com/library/windowsazure/dn232347.aspx">BizTalk 서비스 REST API</a>를 사용하여 수행되었는지 여부와 관계없이 추적됩니다. 관리 서비스를 사용하여 추적된 작업의 전체 목록을 보려면 <a href="#bizops">Azure 관리 서비스를 사용하여 추적된 작업</a>을 참조하세요.</p>
<p>BizTalk 서비스 런타임과 관련된 작업(예: 브리지에서 처리하는 메시지 등)에 대한 로그는 캡처하지 않습니다. 이러한 로그를 보려면 BizTalk 서비스 포털에서 추적 뷰를 사용해야 합니다. 자세한 내용은 <a HREF="http://msdn.microsoft.com/library/windowsazure/hh949805.aspx">메시지 추적</a>(영문)을 참조하세요.</p>
</div>

##<a name="viewlogs"></a>BizTalk 서비스 작업 로그 보기
1. Azure 관리 포털에서 관리 서비스를 클릭한 다음 작업 로그 탭을 클릭합니다.
2. 구독, 날짜 범위, 서비스 종류(예: BizTalk 서비스), 서비스 이름 또는 상태(작업 상태, 예: 성공, 실패)와 같은 다양한 매개 변수를 기준으로 로그를 필터링할 수 있습니다.
3. 확인 표시를 클릭하여 필터링된 목록을 봅니다. 다음 그림은 testbiztalkservice와 관련된 작업을 보여 줍니다.
	![View operation logs][ViewLogs] 
4. 특정 작업에 대한 자세한 내용을 보려면 페이지 맨 아래에서 <b>세부 정보</b>를 클릭합니다.


##<a name="bizops"></a>Azure 관리 서비스를 사용하여 추적된 작업
다음 표는 Azure 관리 서비스를 사용하여 추적된 작업의 목록을 보여 줍니다.

<table border="1" cellpadding="5">
<tr>
<td>CreateBizTalkService</td> 
<td align="left">새 BizTalk 서비스를 만드는 작업</td> 
</tr> 
<tr>
<td>DeleteBizTalkService</td> 
<td align="left">BizTalk 서비스를 삭제하는 작업</td>  
</tr> 
<tr>
<td>RestartBizTalkService</td> 
<td align="left">BizTalk 서비스를 다시 시작하는 작업</td> 
</tr>
<tr>
<td>StartBizTalkService</td> 
<td align="left">BizTalk 서비스를 시작하는 작업</td> 
</tr>
<tr>
<td>StopBizTalkService</td> 
<td align="left">BizTalk 서비스를 중지하는 작업</td> 
</tr>
<tr>
<td>DisableBizTalkService</td> 
<td align="left">BizTalk 서비스를 사용하지 않도록 설정하는 작업</td> 
</tr>
<tr>
<td>EnableBizTalkService</td> 
<td align="left">BizTalk 서비스를 사용하도록 설정하는 작업</td> 
</tr>
<tr>
<td>BackupBizTalkService</td> 
<td align="left">BizTalk 서비스를 백업하는 작업</td> 
</tr>
<tr>
<td>RestoreBizTalkService</td> 
<td align="left">BizTalk 서비스를 지정된 백업에서 복원하는 작업</td> 
</tr>
<tr>
<td>SuspendBizTalkService</td> 
<td align="left">BizTalk 서비스를 일시 중단하는 작업</td> 
</tr>
<tr>
<td>ResumeBizTalkService</td> 
<td align="left">BizTalk 서비스를 다시 시작하는 작업</td> 
</tr>
<tr>
<td>ScaleBizTalkService</td> 
<td align="left">BizTalk 서비스의 규모를 확장하거나 축소하는 작업</td> 
</tr>
<tr>
<td>ConfigUpdateBizTalkService</td> 
<td align="left">BizTalk 서비스의 구성을 업데이트하는 작업</td> 
</tr>
<tr>
<td>ServiceUpdateBizTalkService</td> 
<td align="left">BizTalk 서비스를 다른 버전으로 업그레이드하거나 다운그레이드하는 작업</td> 
</tr>
<tr>
<td>PurgeBackupBizTalkService</td> 
<td align="left">보존 기간이 지난 BizTalk 서비스의 백업을 삭제하는 작업</td> 
</tr>
</table>


## 참고 항목
- [BizTalk 서비스 백업](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [백업에서 BizTalk 서비스 복원](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [BizTalk 서비스: Developer, Basic, Standard 및 Premium Editions 차트](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [BizTalk 서비스: Azure 관리 포털을 사용하여 프로비전](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [BizTalk 서비스: 상태 차트 프로비전](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [BizTalk 서비스: 대시보드, 모니터 및 크기 조정 탭](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [BizTalk 서비스: 제한](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [BizTalk 서비스: 발급자 이름 및 발급자 키](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Azure BizTalk 서비스 SDK로 시작하는 방법](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png

<!--HONumber=46--> 
 