<properties linkid="biztalk-backup-restore" urlDisplayName="BizTalk Services: Backup and Restore" pageTitle="BizTalk Services: Backup and Restore | Azure" metaKeywords="" description="BizTalk Services includes Backup and Restore capabilities. When creating a Backup, a snapshot of the BizTalk Services configuration is taken." metaCanonical="" services="" documentationCenter="" title="BizTalk Services: Backup and Restore" authors="" solutions="" writer="mandia" manager="paulettm" editor="cgronlun" />

BizTalk 서비스: 백업 및 복원
============================

Azure BizTalk 서비스에는 백업 및 복원 기능이 포함되어 있습니다. 백업을 만들 때 Azure BizTalk 서비스 구성에 대한 스냅샷이 만들어집니다.

다음과 같은 시나리오를 고려해 보십시오.

-   BizTalk 서비스 구성은 Azure 관리 포털에서 두 가지 방법으로 백업할 수 있습니다. 즉, 필요한 경우 백업(임시 백업)을 만들거나 예약된 백업을 실행할 수 있습니다.

-   백업 콘텐츠는 동일한 BizTalk 서비스 또는 새로운 BizTalk 서비스로 복원할 수 있습니다. 동일한 이름을 사용하여 BizTalk 서비스를 복원하려면 기존 BizTalk 서비스를 삭제해야 합니다. 그렇지 않으면 복원에 실패합니다.

-   BizTalk 서비스는 동일한 버전 이상으로 복원할 수 있습니다. BizTalk 서비스를 백업이 만들어진 버전보다 낮은 버전으로 복원할 수는 없습니다.

    예를 들어, Basic Edition을 사용한 백업을 Premium Edition으로 복원할 수 있습니다. 그러나 Premium Edition을 사용한 백업은 Standard Edition으로 복원할 수 없습니다.

-   EDI 제어 번호는 제어 번호의 연속성을 유지할 수 있도록 백업됩니다. 마지막 백업 후 메시지가 처리되는 경우 이 백업 콘텐츠를 복원하면 제어 번호가 중복될 수 있습니다.

-   BizTalk 서비스 Developer Edition에서는 백업 및 복원을 사용할 수 없습니다.

이 항목에서는 Azure 관리 포털을 사용하여 BizTalk 서비스를 백업하고 복원하는 방법에 대해 설명합니다. 또한 REST API를 사용하여 BizTalk 서비스를 백업할 수도 있습니다. 이에 대한 자세한 내용은 [BizTalk 서비스 REST API](http://msdn.microsoft.com/library/windowsazure/dn232347.aspx)(영문)를 참조하십시오.

[백업 대상](#budata)

[백업 만들기](#createbu)

[복원](#restore)

백업 대상
---------

백업을 만들 때 다음 항목이 백업됩니다.

<table border="1"> 
<TR bgcolor="FAF9F9">
<th> </th>
<TH>백업되는 항목</TH> 
</TR> 
<TR>
<td colspan="2">
 <strong>Azure BizTalk 서비스 포털</strong></td>
</TR> 
<TR>
<TD>구성 및 런타임</TD> 
<TD><bl>
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
<li>AS2 메시지 MIC 값 </li>
</bl></TD>
</TR> 
 
<TR>
<td colspan="2">
 <strong>Azure BizTalk 서비스</td>
</TR> 
<TR>
<TD>SSL 인증서</TD> 
<TD>
<bl>
<li>SSL 인증서 데이터</li>
<li>SSL 인증서 암호</li>
</bl>
</TD>
</TR> 
<TR>
<TD>BizTalk 서비스 설정</TD> 
<TD>
<bl>
<li>배율 단위 수</li>
<li>버전</li>
<li>제품 버전</li>
<li>지역/데이터 센터</li>
<li>ACS(액세스 제어 서비스) 네임스페이스 및 키</li>
<li>추적 데이터베이스 연결 문자열</li>
<li>보관 저장소 계정 연결 문자열</li>
<li>모니터링 저장소 계정 연결 문자열</li>
</bl></TD>
</TR> 
<TR>
<td colspan="2">
 <strong>추가 항목</strong></td>
</TR> 
<TR>
<TD>추적 데이터베이스</TD> 
<TD>BizTalk 서비스를 프로비전할 때 Azure SQL 데이터베이스 서버 및 추적 데이터베이스 이름을 포함한 추적 데이터베이스 세부 정보가 입력됩니다. 추적 데이터베이스는 자동으로 백업되지 않습니다.<br/><br/>
<strong>중요</strong><br/>
추적 데이터베이스가 실수로 삭제되어 데이터베이스를 복구해야 하는 경우 이전 백업이 존재해야 합니다. 백업이 존재하지 않는 경우 추적 데이터베이스 및 데이터를 복구할 수 없습니다. 이런 경우에는 동일한 데이터베이스 이름으로 새로운 추적 데이터베이스를 만들어야 합니다. 지역에서 복제가 권장됩니다.</TD>
</TR> 
</table>

백업 만들기
-----------

언제든 백업을 만들어 완벽하게 제어할 수 있습니다. Azure 관리 포털 또는 BizTalk 서비스 REST API에서 백업을 만들 수 있습니다. BizTalk 서비스 REST API를 사용하여 백업을 만들려면 [BizTalk 서비스 백업](http://go.microsoft.com/fwlink/p/?LinkID=325584)(영문)을 참조하십시오.

이 섹션에서는 관리 포털을 사용하여 백업하는 방법에 대한 지침을 제공합니다. 관리 포털에서 임시 백업을 만들거나 필요한 간격으로 백업을 예약할 수 있습니다.

백업을 만들기 전에
------------------

백업을 만들기 전에 다음 고려 사항을 준수하는지 확인하십시오.

1.  임시 백업을 실행하기 전에 일괄 처리가 가능한 활성 메시지를 일괄 처리합니다. 그렇게 하면 이 백업을 복원할 *경우* 메시지 손실이 방지됩니다. 백업할 때 일괄 처리의 메시지는 저장되지 않습니다. 예약된 백업을 사용할 경우에는 백업이 시작될 때 일괄 처리의 메시지가 없는지 확인할 수 없습니다.

    **참고**

    일괄 처리의 활성 메시지가 있는 상태에서 백업하면 이러한 메시지는 백업되지 않으므로 손실됩니다.
2.  옵션: BizTalk 서비스 포털에서 모든 관리 작업을 중지합니다.
3.  REST API에서 사용 가능한 [BizTalk 서비스 백업](http://go.microsoft.com/fwlink/p/?LinkID=325584)(영문) 명령으로 저장소 계정에 대한 백업을 만듭니다.

[임시 백업](#backupnow)

[백업 예약](#backupschedule)

### <a name="backupnow"></a>임시 백업
1. Azure 관리 포털에서 BizTalk 서비스를 클릭한 후 백업할 BizTalk 서비스 이름을 클릭합니다.
2. BizTalk 서비스 **대시보드** 탭 페이지 아래쪽에서 **백업**을 클릭합니다.
3. **BizTalk 서비스 백업** 대화 상자에서 백업 이름을 지정합니다.
4. Blob 저장소 계정을 선택하고 확인 표시를 클릭하여 백업을 시작합니다. 


백업을 완료하면 지정된 백업 이름의 컨테이너가 저장소 계정에 만들어집니다. 이 컨테이너에는 BizTalk 서비스 백업 구성이 포함됩니다..

### 백업 예약

1.  Azure 관리 포털에서 BizTalk 서비스를 클릭하고, 자동 백업을 예약할 BizTalk 서비스 이름을 클릭한 후 **계속** 탭을 클릭합니다.
2.  **백업 상태**에서, 자동 백업을 예약하지 않으려는 경우 **없음**을 선택합니다. 자동 백업을 예약하려면 **자동**을 클릭합니다.
3.  **저장소 계정**에서, 백업을 만들 Azure 저장소 계정을 선택합니다.
4.  **주기**에서, 첫 번째 백업의 시작 날짜 및 시간과 백업을 만들 간격(일수)을 지정합니다.
5.  **보존 기간**에서, 백업을 보존할 기간(일수)을 지정합니다. 보존 기간은 백업 주기보다 길어야 합니다.
6.  **Always keep at least one backup** 확인란을 선택하여 보존 기간을 지난 경우에도 사용 가능한 백업이 하나 이상 있는지 확인합니다.
7.  **저장**을 클릭합니다.

예약된 백업 작업이 실행될 경우 지정된 저장소 계정으로 컨테이너(백업 데이터를 저장할 컨테이너)를 만듭니다. 컨테이너 이름의 형식은 *BizTalk 서비스 이름-날짜-시간*입니다.

백업이 실패할 경우 BizTalk 서비스 대시보드 페이지는 백업의 상태를 **실패**로 표시합니다.

![마지막으로 예약된 백업 상태](./media/biztalk-backup-restore/status-last-backup.png)

링크를 클릭하여 관리 서비스 작업 로그 페이지로 이동해서 오류에 대한 자세한 내용을 알아보십시오. BizTalk 서비스와 관련하여 작업 로그에 대한 자세한 내용은 [BizTalk 서비스: 작업 로그를 사용한 문제 해결](http://go.microsoft.com/fwlink/?LinkId=391211)(영문)을 참조하십시오.

복원
----

Azure 관리 포털 또는 BizTalk 서비스 REST API에서 백업을 복원할 수 있습니다. 이 섹션에서는 관리 포털을 사용하여 복원하는 방법에 대한 지침을 제공합니다. REST API를 사용하여 복원하려면 [백업에서 BizTalk 서비스 복원](http://go.microsoft.com/fwlink/p/?LinkID=325582)을 참조하십시오.

### 복원하기 전에

백업을 복원할 때 다음 사항을 고려하십시오.

-   BizTalk 서비스를 복원하는 동안 새로운 추적, 보관 및 모니터링 저장소를 지정할 수 있습니다.

-   동일한 이름을 사용하여 BizTalk 서비스를 복원하려면 복원을 시작하기 전에 기존 BizTalk 서비스를 삭제합니다. 그렇지 않으면 복원에 실패합니다.

-   동일한 EDI 런타임 데이터가 복원됩니다. EDI 런타임 백업은 제어 번호를 저장합니다. 복원된 제어 번호는 백업 시간부터 차례로 지정됩니다. 마지막 백업 후 메시지가 처리되는 경우 이 백업 콘텐츠를 복원하면 제어 번호가 중복될 수 있습니다.

#### 백업을 복원하려면

1.  Azure 관리 포털에서 **새로 만들기**를 클릭하고 **앱 서비스**와 **BizTalk 서비스**를 차례로 클릭한 후 **복원**을 클릭합니다.

    ![백업 복원](./media/biztalk-backup-restore/restore-ui.png)

2.  새 BizTalk 서비스 - 복원 마법사의 **BizTalk 서비스 복원** 페이지에 있는 **백업 URL** 텍스트 상자에서 폴더 아이콘을 클릭하여 **클라우드 저장소 찾아보기** 대화 상자를 엽니다. 대화 상자에서 Azure 저장소 계정을 나열합니다.

    백업을 만들거나 예약하는 동안 지정한 저장소 계정을 확장한 후 BizTalk 서비스 구성을 복원해야 하는 컨테이너 이름을 선택합니다.

    오른쪽 창에서 복원 중인 백업에 해당하는 .txt 파일을 선택한 후 **열기**를 클릭합니다.

3.  **BizTalk 서비스 복원** 페이지에서 다음 정보를 제공합니다.
- BizTalk 서비스 이름을 입력합니다. 기본적으로 백업한 BizTalk 서비스의 이름이 사용됩니다.
- 도메인 URL, 버전 및 복원된 BizTalk 서비스의 지역을 확인합니다.
- 추적 데이터베이스의 새로운 SQL 데이터베이스 인스턴스를 만들도록 선택한 후 오른쪽 화살표를 클릭합니다.

4.  **데이터베이스 설정 지정** 페이지에서 SQL 데이터베이스의 이름을 확인하고, SQL 데이터베이스를 만들 물리적 서버를 지정하고, 해당 서버의 사용자 이름/암호를 지정합니다.

    SQL 데이터베이스의 고급 설정을 구성하려는 경우 **고급 데이터베이스 설정 구성** 확인란을 선택한 후 오른쪽 화살표를 클릭합니다.

    고급 데이터베이스 설정을 구성하지 않으려는 경우 오른쪽 화살표를 클릭한 후 아래의 6단계로 건너뜁니다.

5.  **고급 데이터베이스 설정** 페이지에서, 사용할 데이터베이스 버전(**웹** 또는 **비즈니스**)을 선택하고, 최대 데이터베이스 크기 및 데이터 정렬을 지정합니다. 오른쪽 화살표를 클릭합니다.

6.  **모니터링/보관 설정 지정** 페이지에서 BizTalk 서비스 모니터링 정보를 저장할 저장소 계정을 새로 만들거나 기존 저장소 계정을 지정합니다.

7.  확인 표시를 클릭하여 복원 프로세스를 시작합니다.

8.  복원이 완료되면 Azure 관리 포털의 BizTalk 서비스 페이지에서 새로운 BizTalk 서비스가 일시 중단 상태로 나열됩니다.

### 백업을 복원한 후

BizTalk 서비스는 항상 **일시 중단** 상태로 복원됩니다. 따라서 필요한 경우 새로운 환경이 작동하기 전에 BizTalk 서비스 응용 프로그램에서 응용 프로그램의 모든 구성을 변경할 수 있습니다. 다음은 새로 복원한 BizTalk 서비스를 시작하기 전에 고려해야 하는 사항입니다.

-   Azure BizTalk 서비스 SDK를 사용하여 BizTalk 서비스 응용 프로그램을 만든 경우 복원된 환경에서 작동할 응용 프로그램에서 ACS 자격 증명을 업데이트해야 할 수도 있습니다.

-   이미 작동 중인 BizTalk 서비스 환경을 복제하도록 BizTalk 서비스를 복원할 수 있습니다. 이런 시나리오에서는, FTP 공유를 원본으로 사용하는 원래 BizTalk 서비스 포털에서 계약을 구성한 경우 새로 복원한 환경에서 다른 원본 또는 FTP 공유를 사용하도록 계약을 업데이트할 수 있습니다. 그렇게 하지 못하는 경우 두 가지 계약에서 동일한 메시지를 표시할 수 있습니다.

-   복원 작업을 사용하여 여러 BizTalk 서비스 환경을 생성한 경우 Visual Studio 응용 프로그램, PowerShell cmdlet, REST API 또는 거래 업체 관리 도구 OM API를 사용하여 올바른 환경을 대상으로 하는지 확인합니다.

-   또한 새로 복원한 BizTalk 서비스 환경에서 자동 백업을 구성하는 것도 좋습니다.

이러한 고려 사항이나 기타 고려 사항을 충족한 후에는 Azure 관리 포털의 BizTalk 서비스로 이동하고, 일시 중단 상태의 새로 복원한 BizTalk 서비스를 선택한 후 페이지 아래쪽에서 **다시 시작**을 클릭하십시오.

다음
----

Azure 관리 포털에서 Azure BizTalk 서비스를 프로비전하려면 [BizTalk 서비스: Azure 관리 포털을 사용하여 프로비전](http://go.microsoft.com/fwlink/p/?LinkID=302280)으로 이동하십시오. 응용 프로그램을 만들려면 [Azure BizTalk 서비스](http://go.microsoft.com/fwlink/p/?LinkID=235197)로 이동하십시오.

참고 항목
---------

-   [BizTalk 서비스 백업](http://go.microsoft.com/fwlink/p/?LinkID=325584)
-   [백업에서 BizTalk 서비스 복원](http://go.microsoft.com/fwlink/p/?LinkID=325582)
-   [BizTalk 서비스: Developer, Basic, Standard 및 Premium Editions 차트](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [BizTalk 서비스: Azure 관리 포털을 사용하여 프로비전](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [BizTalk 서비스: 상태 차트 프로비전](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [BizTalk 서비스: 대시보드, 모니터 및 크기 조정 탭](http://go.microsoft.com/fwlink/p/?LinkID=302281)
-   [BizTalk 서비스: 제한](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [BizTalk 서비스: 발급자 이름 및 발급자 키](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [Azure BizTalk 서비스 SDK로 시작하는 방법](http://go.microsoft.com/fwlink/p/?LinkID=302335)

