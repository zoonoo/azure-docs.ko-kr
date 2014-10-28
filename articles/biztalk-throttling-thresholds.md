<properties linkid="manage-services-biztalk-services-throttling" urlDisplayName="Throttling" pageTitle="Throttling thresholds in BizTalk Services | Azure" metaKeywords="BizTalk Services, throttling, Azure" description="Learn about throttling thresholds and resulting runtime behaviors for BizTalk Services. Throttling is based on memory usage and number of simultaneous messages." metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Throttling" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia"></tags>

# BizTalk 서비스: 제한

Azure BizTalk 서비스는 메모리 사용량과 동시 메시지 처리 수의 두 가지 조건에 따라 서비스 제한을 구현합니다. 이 항목에서는 제한 임계값을 나열하고 제한 조건이 발생할 때의 런타임 동작을 설명합니다.

## 제한 임계값

다음 표에서는 제한 원본 및 임계값을 나열합니다.

<table border="1">
<tr bgcolor="FAF9F9">
<th>
</th>
<td>
<b>설명</b>

</td>
<td>
<b>낮은 임계값</b>

</td>
<td>
<b>높은 임계값</b>

</td>
</tr>
<tr>
<td>
메모리

</td>
<td>
사용 가능한 총 시스템/PageFileBytes 백분율
 사용 가능한 총 PageFileBytes는 시스템 RAM의 대략 2배입니다.

</td>
<td>
60%

</td>
<td>
70%

</td>
</tr>
<tr>
<td>
메시지 처리

</td>
<td>
동시 메시지 처리 수

</td>
<td>
40 * 코어 수

</td>
<td>
100 * 코어 수

</td>
</tr>
</table>
높은 임계값에 도달하면 Azure BizTalk 서비스에서 제한을 시작합니다. 낮은 임계값에 도달하면 제한이 중지됩니다. 예를 들어 서비스에서 65% 시스템 메모리를 사용하고 있다고 가정합니다. 이런 상황에서는 서비스가 제한하지 않습니다. 그러나 서비스가 70%의 시스템 메모리를 사용하기 시작하면 상황이 달라집니다. 서비스가 제한을 시작하여 60%(낮은 임계값)의 시스템 메모리를 사용할 때까지 계속 제한합니다.

Azure BizTalk 서비스는 제한 상태(일반 상태와 제한된 상태) 및 제한 기간을 추적합니다.

## 런타임 동작

Azure BizTalk 서비스가 제한 상태에 들어가면 다음과 같은 상황이 발생합니다.

-   제한은 역할 인스턴스별로 적용됩니다. 예를 들면 다음과 같습니다.
    RoleInstanceA는 제한하고 있고, RoleInstanceB는 제한하고 있지 않습니다. 이런 경우 RoleInstanceB의 메시지가 예상대로 처리됩니다. RoleInstanceA의 메시지는 취소되고 다음과 같은 오류를 표시하며 실패합니다.
    서버가 사용 중입니다. 나중에 다시 시도하십시오.
-   어떤 끌어오기 원본도 메시지를 폴링하거나 다운로드하지 않습니다. 예를 들면 다음과 같습니다.
    파이프라인이 외부 FTP 원본에서 메시지를 끌어옵니다. 끌어오기를 수행하는 역할 인스턴스가 제한 상태로 전환됩니다. 이런 경우 역할 인스턴스가 제한을 중지할 때까지 파이프라인에서 추가 메시지 다운로드를 중지합니다.
-   클라이언트에서 메시지를 다시 제출할 수 있도록 응답이 클라이언트로 전송됩니다.
-   제한이 해결될 때까지 기다려야 합니다. 특히 낮은 임계값에 도달할 때까지 기다려야 합니다.

## 중요

-   제한을 사용하지 않도록 설정할 수 없습니다.
-   제한 임계값은 수정할 수 없습니다.
-   제한은 시스템 전체에서 구현됩니다.
-   Azure SQL 데이터베이스 서버에는 기본 제공 제한도 있습니다.

## 추가 Azure BizTalk 서비스 항목

-   [Azure BizTalk 서비스 SDK 설치][Azure BizTalk 서비스 SDK 설치]
-   [자습서: Azure BizTalk 서비스][자습서: Azure BizTalk 서비스]
-   [Azure BizTalk 서비스 SDK로 시작하는 방법][Azure BizTalk 서비스 SDK로 시작하는 방법]
-   [Azure BizTalk 서비스][Azure BizTalk 서비스]

## 참고 항목

-   [BizTalk 서비스: Developer, Basic, Standard 및 Premium Editions 차트][BizTalk 서비스: Developer, Basic, Standard 및 Premium Editions 차트]
-   [BizTalk 서비스: Azure 관리 포털을 사용하여 프로비전][BizTalk 서비스: Azure 관리 포털을 사용하여 프로비전]
-   [BizTalk 서비스: 상태 차트 프로비전][BizTalk 서비스: 상태 차트 프로비전]
-   [BizTalk 서비스: 대시보드, 모니터 및 크기 조정 탭][BizTalk 서비스: 대시보드, 모니터 및 크기 조정 탭]
-   [BizTalk 서비스: 백업 및 복원][BizTalk 서비스: 백업 및 복원]
-   [BizTalk 서비스: 발급자 이름 및 발급자 키][BizTalk 서비스: 발급자 이름 및 발급자 키]

  [Azure BizTalk 서비스 SDK 설치]: http://go.microsoft.com/fwlink/p/?LinkID=241589
  [자습서: Azure BizTalk 서비스]: http://go.microsoft.com/fwlink/p/?LinkID=236944
  [Azure BizTalk 서비스 SDK로 시작하는 방법]: http://go.microsoft.com/fwlink/p/?LinkID=302335
  [Azure BizTalk 서비스]: http://go.microsoft.com/fwlink/p/?LinkID=303664
  [BizTalk 서비스: Developer, Basic, Standard 및 Premium Editions 차트]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [BizTalk 서비스: Azure 관리 포털을 사용하여 프로비전]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [BizTalk 서비스: 상태 차트 프로비전]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [BizTalk 서비스: 대시보드, 모니터 및 크기 조정 탭]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [BizTalk 서비스: 백업 및 복원]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [BizTalk 서비스: 발급자 이름 및 발급자 키]: http://go.microsoft.com/fwlink/p/?LinkID=303941
