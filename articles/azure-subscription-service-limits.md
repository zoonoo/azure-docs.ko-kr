<properties linkid="azure-subscription-service-limits" urlDisplayName="Azure Subscription and Service Limits, Quotas, and Constraints" pageTitle="Microsoft Azure Subscription and Service Limits, Quotas, and Constraints" metaKeywords="Cloud Services, Virtual Machines, Web Sites, Virtual Network, SQL Database, Subscription, Storage" description="Provides a list of common Azure subscription and service limits along with maximum values." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth"></tags>

# Azure 구독 및 서비스 제한, 할당량 및 제약 조건

다음 문서는 가장 일반적인 Microsoft Azure 제한을 지정합니다. 현재 이 문서에서는 일부 Azure 서비스에 대해 다룹니다. 시간 경과에 따라 이 제한은 더 많은 플랫폼에 적용되도록 확장 및 업데이트됩니다.

-   [구독 제한][]
-   [웹 작업자 제한][]
-   [가상 컴퓨터 제한][]
-   [네트워킹 제한][]
-   [저장소 제한][]
-   [SQL 데이터베이스 제한][]

> [WACOM.NOTE] **기본 제한** 이상으로 제한을 높이려는 경우 [고객 지원][]에서 인시던트를 개설합니다. 다음 표에 나오는 **최대 제한** 값 이상으로 제한을 높일 수 없습니다. **최대 제한** 열이 없는 경우는 지정된 리소스에 조정 가능한 제한이 없습니다.

## <a name="subscription"></a>구독 제한

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">리소스</th>
<th align="left">기본 제한</th>
<th align="left">최대 제한</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/azure/hh531793.aspx">구독</a>당 코어<sup>1</sup></p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>10,000</p></td>
</tr>
<tr class="even">
<td align="left"><p>구독당 <a href="http://msdn.microsoft.com/en-us/library/azure/gg456328.aspx">공동 관리자</a></p></td>
<td align="left"><p>200</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p>구독당 <a href="http://azure.microsoft.com/ko-kr/documentation/articles/storage-whatis-account/">저장소 계정</a></p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p>구독당 <a href="http://azure.microsoft.com/ko-kr/documentation/articles/cloud-services-what-is/">클라우드 서비스</a></p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p>구독당 <a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">가상 네트워크</a><sup>2</sup></p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p>구독당 <a href="http://msdn.microsoft.com/en-us/library/jj157100.aspx">로컬 네트워크</a></p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="odd">
<td align="left"><p>구독당 DNS 서버</p></td>
<td align="left"><p>9</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p>구독당 예약된 IP</p></td>
<td align="left"><p>5</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="odd">
<td align="left"><p>구독당 호스티드 서비스 인증서</p></td>
<td align="left"><p>400</p></td>
<td align="left"><p>400</p></td>
</tr>
<tr class="even">
<td align="left"><p>구독당 <a href="http://msdn.microsoft.com/en-us/library/azure/jj156085.aspx">선호도 그룹</a></p></td>
<td align="left"><p>256</p></td>
<td align="left"><p>256</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>아주 작음 인스턴스는 부분 코어를 사용하더라도 코어 제한에 대해 하나의 코어로 계산됩니다.

<sup>2</sup>각 가상 네트워크는 단일 가상 네트워크 게이트웨이를 지원합니다.

## <a name="webworkerlimits"></a>웹/작업자 제한

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">리소스</th>
<th align="left">기본 제한</th>
<th align="left">최대 제한</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/ko-kr/documentation/articles/cloud-services-what-is/">배포당 웹/작업자 역할<sup>1</sup></a></p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p>배포당 <a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InstanceInputEndpoint">인스턴스 입력 끝점</a></p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="odd">
<td align="left"><p>배포당 <a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InputEndpoint">입력 끝점</a></p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p>배포당 <a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InternalEndpoint">내부 끝점</a></p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>웹/작업자 역할이 있는 각 클라우드 서비스에는 프로덕션용과 스테이징용으로 두 개의 배포가 있을 수 있습니다.

## <a name="vmlimits"></a>가상 컴퓨터 제한

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">리소스</th>
<th align="left">기본 제한</th>
<th align="left">최대 제한</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>클라우드 서비스당 <a href="http://azure.microsoft.com/ko-kr/documentation/services/virtual-machines/">가상 컴퓨터</a><sup>1</sup></p></td>
<td align="left"><p>50</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p>클라우드 서비스당 입력 끝점<sup>2</sup></p></td>
<td align="left"><p>150</p></td>
<td align="left"><p>150</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>가상 컴퓨터를 만들면 컴퓨터를 포함할 클라우드 서비스가 자동으로 만들어집니다. 그러면 이 클라우드 서비스에 여러 개의 가상 컴퓨터를 추가할 수 있습니다.

<sup>2</sup>입력 끝점은 포함 클라우드 서비스 외부에 있는 가상 컴퓨터와 통신을 허용하는 데 사용됩니다. 이 클라우드 서비스 내의 가상 컴퓨터는 내부 통신을 위해 모든 UDP 포트와 TCP 포트 사이의 통신을 자동으로 허용합니다.

## <a name="networkinglimits"></a>네트워킹 제한

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">리소스</th>
<th align="left">기본 제한</th>
<th align="left">최대 제한</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">가상 네트워크</a><sup>2</sup>당 총 컴퓨터<sup>1</sup></p></td>
<td align="left"><p>2048</p></td>
<td align="left"><p>2048</p></td>
</tr>
<tr class="even">
<td align="left"><p>가상 컴퓨터 또는 역할 인스턴스용 동시 TCP 연결</p></td>
<td align="left"><p>500K</p></td>
<td align="left"><p>500K</p></td>
</tr>
<tr class="odd">
<td align="left"><p>끝점당 ACL(액세스 제어 목록)<sup>3</sup></p></td>
<td align="left"><p>50</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p>가상 네트워크당 로컬 네트워크 사이트</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>10</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>총 컴퓨터 수에는 가상 컴퓨터와 웹/작업자 역할 인스턴스가 포함됩니다.

<sup>2</sup>각 가상 네트워크는 하나의 [가상 네트워크 게이트웨이][]를 지원합니다.

<sup>3</sup>ACL은 가상 컴퓨터의 입력 끝점에서 지원됩니다. 웹/작업자 역할의 경우, 입력 끝점 및 인스턴스 입력 끝점에서 지원됩니다.

## <a name="storagelimits"></a>저장소 제한<sup>1</sup>

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">리소스</th>
<th align="left">기본 제한</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>저장소 계정당 TB<sup>2</sup></p></td>
<td align="left"><p>500</p></td>
</tr>
<tr class="even">
<td align="left"><p>영구적인 디스크의 최대 IOPS</p></td>
<td align="left"><p>500<sup>3</sup></p></td>
</tr>
<tr class="odd">
<td align="left"><p>저장소 계정당 최대 IOPS</p></td>
<td align="left"><p>20,000</p></td>
</tr>
<tr class="even">
<td align="left"><p>저장소 계정당 최대 수신(미국 지역)</p></td>
<td align="left"><p>GRS<sup>4</sup>를 사용하는 경우 20Gbps, 사용하지 않는 경우 30Gbps</p></td>
</tr>
<tr class="odd">
<td align="left"><p>저장소 계정당 최대 수신(유럽 및 아시아 지역)</p></td>
<td align="left"><p>GRS<sup>4</sup>를 사용하는 경우 5Gbps, 사용하지 않는 경우 10Gbps</p></td>
</tr>
<tr class="even">
<td align="left"><p>저장소 계정당 최대 송신(미국 지역)</p></td>
<td align="left"><p>GRS<sup>4</sup>를 사용하는 경우 20Gbps, 사용하지 않는 경우 30Gbps</p></td>
</tr>
<tr class="odd">
<td align="left"><p>저장소 계정당 최대 송신(유럽 및 아시아 지역)</p></td>
<td align="left"><p>GRS<sup>4</sup>를 사용하는 경우 10Gbps, 사용하지 않는 경우 15Gbps</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>이러한 제한에 대한 자세한 내용은 [Azure 저장소 확장성 및 성능 목표][]를 참조하세요.

<sup>2</sup>페이지 Blob의 경우 사용 중인 페이지에서만 용량 사용이 발생합니다. 예를 들어 VHD가 127GB이지만 OS에서 30GB만 사용되는 가상 컴퓨터의 경우 전체 127GB가 아니라 VHD 내에서 사용되는 30GB 부분에 대해서만 청구됩니다.

<sup>3</sup>20,000 IOPS 제한을 피하려면 많이 사용되는 VHD를 40개를 초과하여 계정에 넣지 마세요.

<sup>4</sup>지역 중복 저장소 계정

## <a name="sqldblimits"></a>SQL 데이터베이스 제한

SQL 데이터베이스 제한에 대한 자세한 내용은 다음 항목을 참조하세요.

-   [Azure SQL 데이터베이스 서비스 계층(Edition)][]
-   [Azure SQL 데이터베이스 서비스 계층 및 성능 수준][]
-   [SQL 데이터베이스 리소스 제한][]

## <a name="seealso"></a>참고 항목

[Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기][]

[Azure 저장소 확장성 및 성능 목표][]

[Azure SQL 데이터베이스 서비스 계층(Edition)][]

[Azure SQL 데이터베이스 서비스 계층 및 성능 수준][]

[SQL 데이터베이스 리소스 제한][]

  [구독 제한]: #subscription
  [웹 작업자 제한]: #webworkerlimits
  [가상 컴퓨터 제한]: #vmlimits
  [네트워킹 제한]: #networkinglimits
  [저장소 제한]: #storagelimits
  [SQL 데이터베이스 제한]: #sqldblimits
  [고객 지원]: http://azure.microsoft.com/en-us/support/faq/
  [구독]: http://msdn.microsoft.com/en-us/library/azure/hh531793.aspx
  [공동 관리자]: http://msdn.microsoft.com/en-us/library/azure/gg456328.aspx
  [저장소 계정]: http://azure.microsoft.com/ko-kr/documentation/articles/storage-whatis-account/
  [클라우드 서비스]: http://azure.microsoft.com/ko-kr/documentation/articles/cloud-services-what-is/
  [가상 네트워크]: http://msdn.microsoft.com/library/azure/jj156007.aspx
  [로컬 네트워크]: http://msdn.microsoft.com/en-us/library/jj157100.aspx
  [선호도 그룹]: http://msdn.microsoft.com/en-us/library/azure/jj156085.aspx
  [인스턴스 입력 끝점]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InstanceInputEndpoint
  [입력 끝점]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InputEndpoint
  [내부 끝점]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InternalEndpoint
  [가상 컴퓨터]: http://azure.microsoft.com/ko-kr/documentation/services/virtual-machines/
  [가상 네트워크 게이트웨이]: http://msdn.microsoft.com/en-us/library/azure/jj156210.aspx
  [Azure 저장소 확장성 및 성능 목표]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [Azure SQL 데이터베이스 서비스 계층(Edition)]: http://msdn.microsoft.com/en-us/library/azure/dn741340.aspx
  [Azure SQL 데이터베이스 서비스 계층 및 성능 수준]: http://msdn.microsoft.com/en-us/library/azure/dn741336.aspx
  [SQL 데이터베이스 리소스 제한]: http://msdn.microsoft.com/en-us/library/azure/dn338081.aspx
  [Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기]: http://msdn.microsoft.com/en-us/library/azure/dn197896.aspx
