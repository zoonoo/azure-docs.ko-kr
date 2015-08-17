<properties 
	pageTitle="BizTalk 서비스에서 백업 만들기 및 복원 | Azure" 
	description="BizTalk 서비스에는 백업 및 복원이 포함되어 있습니다. 백업을 만들고 복원하며 백업 대상을 결정하는 방법에 대해 알아봅니다. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="mandia"/>


# BizTalk 서비스: 백업 및 복원

Azure BizTalk 서비스에는 백업 및 복원 기능이 포함되어 있습니다. 이 항목에서는 Azure 관리 포털을 사용하여 BizTalk 서비스를 백업하고 복원하는 방법에 대해 설명합니다.

[BizTalk 서비스 REST API](http://go.microsoft.com/fwlink/p/?LinkID=325584)를 사용하여 BizTalk 서비스를 백업할 수도 있습니다.

## 시작하기 전에

- 일부 버전에서는 백업 및 복원을 사용하지 못할 수도 있습니다. [BizTalk 서비스: 버전 차트](biztalk-editions-feature-chart.md)를 참조하세요.

	**참고** 하이브리드 연결은 버전에 상관없이 백업되지 않습니다.

- Azure 관리 포털을 사용하여 주문형 백업 또는 예약된 백업을 만들 수 있습니다.

- 백업 콘텐츠는 동일한 BizTalk 서비스 또는 새로운 BizTalk 서비스로 복원할 수 있습니다. 동일한 이름을 사용하여 BizTalk 서비스를 복원하려면 기존 BizTalk 서비스를 삭제해야 하며 이름을 사용할 수 있어야 합니다. BizTalk 서비스를 삭제한 후 동일한 이름의 서비스를 사용하려면 시간이 좀 더 오래 걸릴 수 있습니다. 동일한 이름의 서비스를 사용할 수 있을 때까지 기다릴 수 없으면 새 BizTalk 서비스로 복원합니다.

- BizTalk 서비스는 동일한 버전 이상으로 복원할 수 있습니다. BizTalk 서비스를 백업이 만들어진 버전보다 낮은 버전으로 복원할 수는 없습니다.

	예를 들어, Basic Edition을 사용한 백업을 Premium Edition으로 복원할 수 있습니다. 그러나 Premium Edition을 사용한 백업은 Standard Edition으로 복원할 수 없습니다.

- EDI 제어 번호는 제어 번호의 연속성을 유지할 수 있도록 백업됩니다. 마지막 백업 후 메시지가 처리되는 경우 이 백업 콘텐츠를 복원하면 제어 번호가 중복될 수 있습니다.

- 일괄 처리에 활성 메시지가 있는 경우 백업을 실행하기 **전에** 일괄 처리를 수행합니다. 필요에 따라 백업을 만들거나 예약된 백업을 만들 때 배치의 메시지는 저장되지 않습니다.

	**일괄 처리의 활성 메시지가 있는 상태에서 백업하면 이러한 메시지는 백업되지 않으므로 손실됩니다.**

- 선택 사항: BizTalk 서비스 포털에서 모든 관리 작업을 중지합니다.


## 백업 만들기

언제든 백업을 만들어 완벽하게 제어할 수 있습니다. 이 섹션에서는 다음을 비롯해 Azure 관리 포털을 사용하여 백업을 만드는 단계를 설명합니다.

[주문형 백업](#backupnow)

[백업 예약](#backupschedule)

#### <a name="backupnow"></a>주문형 백업
1. Azure 관리 포털에서 **BizTalk 서비스**를 선택한 후 백업할 BizTalk 서비스를 선택합니다.
2. **대시보드** 탭에서 페이지 맨 아래에 있는 **백업**을 선택합니다.
3. 백업 이름을 입력합니다. 예를 들어 *myBizTalkService*BU*Date*를 입력합니다.
4. Blob 저장소 계정을 선택한 후 확인 표시를 선택하여 백업을 시작합니다.

백업이 완료되면 입력한 백업 이름의 컨테이너가 저장소 계정에 만들어집니다. 이 컨테이너에는 BizTalk 서비스 백업 구성이 포함됩니다.

#### <a name="backupschedule"></a>백업 예약

1. Azure 관리 포털에서 **BizTalk 서비스**를 선택하고 백업을 예약할 BizTalk 서비스 이름을 선택한 후 **구성** 탭을 선택합니다.
2. **백업 상태**를 **자동**으로 설정합니다. 
3. 백업을 저장할 **저장소 계정**을 선택하고 백업을 만들 **빈도** 및 백업을 유지할 기간(**보존 기간(일)**)를 입력합니다.

	![][AutomaticBU]

	**참고** - **보존 기간(일)**에서 보존 기간은 백업 빈도보다 커야 합니다. - 보존 기간이 지난 경우에도 **항상 하나 이상의 백업 유지**를 선택합니다.
	

4. **저장**을 선택합니다.


예약된 백업 작업이 실행될 경우 입력한 저장소 계정으로 컨테이너(백업 데이터를 저장할 컨테이너)를 만듭니다. 컨테이너 이름은 *BizTalk 서비스 이름-날짜-시간*입니다.

BizTalk 서비스 대시보드에 **실패** 상태가 표시된 경우:

![마지막으로 예약된 백업 상태][BackupStatus]

이 링크를 클릭하면 문제 해결에 도움이 되는 관리 서비스 작업 로그가 열립니다. [BizTalk 서비스: 작업 로그를 사용하여 문제 해결](http://go.microsoft.com/fwlink/p/?LinkId=391211)을 참조하세요.

## 복원

Azure 관리 포털 또는 [BizTalk 서비스 REST API 복원](http://go.microsoft.com/fwlink/p/?LinkID=325582)에서 백업을 복원할 수 있습니다. 이 섹션에서는 관리 포털을 사용하여 복원하는 단계를 설명합니다.

#### 복원하기 전에

- BizTalk 서비스를 복원하는 동안 새로운 추적, 보관 및 모니터링 저장소를 입력할 수 있습니다.

- 동일한 EDI 런타임 데이터가 복원됩니다. EDI 런타임 백업은 제어 번호를 저장합니다. 복원된 제어 번호는 백업 시간부터 차례로 지정됩니다. 마지막 백업 후 메시지가 처리되는 경우 이 백업 콘텐츠를 복원하면 제어 번호가 중복될 수 있습니다.

#### 백업 복원

1. Azure 관리 포털에서 **새로 만들기** > **앱 서비스** > **BizTalk 서비스** > **복원**을 선택합니다.

	![백업 복원][Restore]

2. **URL 백업**에서 폴더 아이콘을 선택하고 BizTalk 서비스 구성 백업을 저장하는 Azure 저장소 계정을 확장합니다. 컨테이너를 확장하고 오른쪽 창에서 해당 백업 .txt 파일을 선택합니다. <br/><br/> **열기**를 선택합니다.

3. **BizTalk 서비스 복원** 페이지에서 **BizTalk 서비스 이름**을 입력하고 복원된 BizTalk 서비스의 **도메인 URL**, **버전** 및 **지역**을 입력합니다. 추적 데이터베이스에 대해 **새 SQL 데이터베이스 인스턴스 만들기**를 수행합니다.

	![][RestoreBizTalkService]

	다음 화살표를 선택합니다.

4. 	SQL 데이터베이스의 이름을 확인하고, SQL 데이터베이스를 만들 실제 서버와 해당 서버의 사용자 이름/암호를 입력합니다.


	SQL 데이터베이스 버전, 크기 및 기타 속성을 구성하려면 **고급 데이터베이스 설정 구성**을 선택합니다.

	다음 화살표를 선택합니다.

5. BizTalk 서비스에 대한 새 저장소 계정을 만들거나 기존 저장소 계정을 입력합니다.

7. 확인 표시를 선택하여 복원을 시작합니다.

복원이 완료되면 Azure 관리 포털의 BizTalk 서비스 페이지에서 새로운 BizTalk 서비스가 일시 중단 상태로 나열됩니다.



### <a name="postrestore"></a>백업을 복원한 후

BizTalk 서비스는 항상 **일시 중단** 상태로 복원됩니다. 이 상태에서는 새 환경을 작동하기 전에 다음을 비롯한 구성 변경을 적용할 수 있습니다.

- Azure BizTalk 서비스 SDK를 사용하여 BizTalk 서비스 응용 프로그램을 만든 경우 복원된 환경에서 작동할 응용 프로그램에서 ACS(액세스 제어) 자격 증명을 업데이트해야 할 수도 있습니다.

- 기존 BizTalk 서비스 환경을 복제하도록 BizTalk 서비스를 복원할 수 있습니다. 이 경우 원래 BizTalk 서비스 포털에 원본 FTP 폴더를 사용하는 계약이 구성되어 있으면 새로 복원한 환경에서 다른 원본 FTP 폴더를 사용하도록 계약을 업데이트해야 할 수도 있습니다. 그렇지 않으면 두 가지 계약에서 동일한 메시지를 가져오려고 할 수 있습니다.

- 여러 BizTalk 서비스 환경을 유지하도록 복원한 경우 Visual Studio 응용 프로그램, PowerShell cmdlet, REST API 또는 거래 업체 관리 도구 OM API에서 올바른 환경을 대상으로 하는지 확인합니다.

- 새로 복원한 BizTalk 서비스 환경에서 자동 백업을 구성하는 것이 좋습니다.

Azure 관리 포털에서 BizTalk 서비스를 시작하려면 복원된 BizTalk 서비스를 선택한 후 작업 표시줄에서 **계속**을 선택합니다.



## 백업 대상

백업을 만들 때 다음 항목이 백업됩니다.

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>백업되는 항목</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Azure BizTalk 서비스 포털</strong></td>
</tr> 
<tr>
<td>구성 및 런타임</td> 
<td>
<ul>
<li>파트너 및 프로필 세부 정보</li>
<li>파트너 계약</li>
<li>배포된 사용자 지정 어셈블리</li>
<li>배포된 브리지</li>
<li>인증서</li>
<li>배포된 변환</li>
<li>파이프라인</li>
<li>BizTalk 서비스 포털에서 만들어지고 저장된 템플릿</li>
<li>X12 ST01 및 GS01 매핑</li>
<li>제어 번호(EDI)</li>
<li>AS2 메시지 MIC 값</li>
</ul>
</td>
</tr> 
 
<tr>
<td colspan="2">
 <strong>Azure BizTalk 서비스</strong></td>
</tr> 
<tr>
<td>SSL 인증서</td> 
<td>
<ul>
<li>SSL 인증서 데이터</li>
<li>SSL 인증서 암호</li>
</ul>
</td>
</tr> 
<tr>
<td>BizTalk 서비스 설정</td> 
<td>
<ul>
<li>배율 단위 수</li>
<li>버전</li>
<li>제품 버전</li>
<li>지역/데이터 센터</li>
<li>ACS(액세스 제어 서비스) 네임스페이스 및 키</li>
<li>추적 데이터베이스 연결 문자열</li>
<li>보관 저장소 계정 연결 문자열</li>
<li>모니터링 저장소 계정 연결 문자열</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>추가 항목</strong></td>
</tr> 
<tr>
<td>추적 데이터베이스</td> 
<td>BizTalk 서비스를 만들 때 Azure SQL 데이터베이스 서버 및 추적 데이터베이스 이름을 포함한 추적 데이터베이스 세부 정보가 입력됩니다. 추적 데이터베이스는 자동으로 백업되지 않습니다.
<br/><br/>
<strong>중요</strong><br/>
추적 데이터베이스가 삭제되어 데이터베이스를 복구해야 하는 경우 이전 백업이 존재해야 합니다. 백업이 존재하지 않는 경우 추적 데이터베이스 및 데이터를 복구할 수 없습니다. 이런 경우에는 동일한 데이터베이스 이름으로 새로운 추적 데이터베이스를 만들어야 합니다. 지역에서 복제가 권장됩니다.</td>
</tr> 
</table>

## 다음

Azure 관리 포털에서 Azure BizTalk 서비스를 만들려면 [BizTalk 서비스: Azure 관리 포털을 사용하여 프로비전](http://go.microsoft.com/fwlink/p/?LinkID=302280)으로 이동하세요. 응용 프로그램을 만들려면 [Azure BizTalk 서비스](http://go.microsoft.com/fwlink/p/?LinkID=235197)로 이동하세요.

## 참고 항목
- [BizTalk 서비스 백업](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [백업에서 BizTalk 서비스 복원](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [BizTalk 서비스: Developer, Basic, Standard 및 Premium Editions 차트](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [BizTalk 서비스: Azure 관리 포털을 사용하여 프로비전](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [BizTalk 서비스: 프로비저닝 상태 차트](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [BizTalk 서비스: 대시보드, 모니터 및 크기 조정 탭](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [BizTalk 서비스: 제한](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [BizTalk 서비스: 발급자 이름 및 발급자 키](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Azure BizTalk 서비스 SDK로 시작하는 방법](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png
 

<!---HONumber=August15_HO6-->