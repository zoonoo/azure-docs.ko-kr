<properties urlDisplayName="BizTalk Services: Service state chart" pageTitle="BizTalk 서비스: 서비스 상태 차트 | Azure" metaKeywords="" description="" metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Service state chart" authors="mandia" solutions="integration" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />



# BizTalk 서비스: 서비스 상태 차트
BizTalk 서비스의 현재 상태에 따라 BizTalk 서비스에서 수행할 수 있는 작업이나 수행할 수 없는 작업이 있습니다.

예를 들어 Azure 관리 포털에서 새로운 BizTalk 서비스를 프로비전합니다. 프로비전이 성공적으로 완료되면 BizTalk 서비스가 활성 상태입니다. 활성 상태에서 BizTalk 서비스를 중지할 수 있습니다. 중지가 완료되면 BizTalk 서비스가 중지 상태로 전환됩니다. 중지하지 못하면 BizTalk 서비스가 중지 실패 상태로 전환됩니다. 중지 실패 상태에서는 BizTalk 서비스를 다시 시작할 수 있습니다. BizTalk 서비스 계속과 같이 허용되지 않는 작업을 시도하는 경우 다음과 같은 오류가 발생합니다.

**작업 허용 안 됨**

BizTalk 서비스를 프로비전하려면 [BizTalk 서비스: Azure 관리 포털을 사용하여 프로비전](http://go.microsoft.com/fwlink/p/?LinkID=302280)을 참조하세요.

다음 표에서는 BizTalk 서비스가 특정 상태에 있을 때 수행할 수 있는 작업을 나열합니다. 확인 표시는 해당 상태에서 수행할 수 있는 작업을 의미합니다. 빈 항목은 해당 상태에서 수행할 수 없는 작업을 의미합니다.

#### 시작, 중지, 다시 시작, 일시 중단, 계속 및 삭제 작업
<table border="1">
<tr>
        <th colspan="15">작업</th>
</tr>

<tr>
        <th rowspan="18">BizTalk 서비스 상태</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>여기에서</th>
        <th>중지</th>
        <th>다시 시작</th>
        <th>일시 중단</th>
        <th>계속</th>
        <th>삭제</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>활성</b></td>
<td> </td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>사용 안 함</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>일시 중단</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>중지</b></td>
<td><center>x</center></td>
<td> </td>
<td><center>x</center></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>서비스 업데이트 실패</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>사용 안 함 실패</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>사용 실패</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>시작 실패<br/>
중지 실패<br/>
다시 시작 실패</b></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>일시 중단 실패<br/>
계속 실패</b></td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>생성 실패<br/>
복원 실패<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>구성 업데이트 실패</b></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>크기 조정 실패</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
</table>
<br/>

####크기 조정, 구성 업데이트 및 백업 작업
<table border="1">
<tr>
        <th colspan="15">작업</th>
</tr>

<tr>
        <th rowspan="18">BizTalk 서비스 상태</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>크기 조정</th>
        <th>구성 업데이트</th>
        <th>백업</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>활성</b></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>사용 안 함</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>일시 중단</b></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>중지</b></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>서비스 업데이트 실패</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>사용 안 함 실패</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>사용 실패</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>시작 실패<br/>
중지 실패<br/>
다시 시작 실패</b></td>
<td> </td>
<td><center>x</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>일시 중단 실패<br/>
계속 실패</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>생성 실패<br/>
복원 실패<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>구성 업데이트 실패</b></td>
<td> </td>
<td><center>x</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>크기 조정 실패</b></td>
<td><center>x</center></td>
<td> </td>
<td> </td>
</tr>
</table>

## 참고 항목
- [BizTalk 서비스: Azure 관리 포털을 사용하여 프로비전](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk 서비스: 대시보드, 모니터 및 크기 조정 탭](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk 서비스: Developer, Basic, Standard 및 Premium Editions 차트](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk 서비스: 백업 및 복원](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk 서비스: 제한](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [BizTalk 서비스: 발급자 이름 및 발급자 키](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Azure BizTalk 서비스 SDK로 시작하는 방법](http://go.microsoft.com/fwlink/p/?LinkID=302335)


