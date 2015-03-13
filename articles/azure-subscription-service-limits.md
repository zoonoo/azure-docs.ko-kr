<properties 
	pageTitle="Microsoft Azure 구독 및 서비스 제한, 할당량 및 제약 조건" 
	description="일반적인 Azure 구독 및 서비스 제한 및 최대값 목록을 제공합니다." 
	services="" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="jroth"/>

# Azure 구독 및 서비스 제한, 할당량 및 제약 조건

다음 문서는 가장 일반적인 Microsoft Azure 제한을 지정합니다. 현재 이 문서에서는 일부 Azure 서비스에 대해 다룹니다. 시간 경과에 따라 이 제한은 더 많은 플랫폼에 적용되도록 확장 및 업데이트됩니다.

- [구독 제한](#subscription)
- [클라우드 서비스 제한](#webworkerlimits)
- [가상 컴퓨터 제한](#vmlimits)
- [웹 사이트 제한](#websiteslimits)
- [네트워킹 제한](#networkinglimits)
- [저장소 제한](#storagelimits)
- [배치 미리 보기 제한](#batchlimits)
- [DocumentDB 미리 보기 제한](#documentdblimits)
- [SQL 데이터베이스 제한](#sqldblimits)
- [미디어 서비스 제한](#mediaserviceslimits)
- [서비스 버스 제한](#servicebuslimits)
- [Active Directory 제한](#adlimits)

> [WACOM.NOTE] **기본 제한** 이상으로 제한을 높이려는 경우 [무료로 온라인 고객 지원 요청을 개설][azurelimitsblogpost]할 수 있습니다. 다음 표에 나오는 **최대 제한** 값 이상으로 제한을 높일 수 없습니다. **최대 제한** 열이 없는 경우는 지정된 리소스에 조정 가능한 제한이 없습니다.

##<a name="subscription"></a>구독 제한

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">리소스</th>
   <th align="left" valign="middle">기본 제한</th>
   <th align="left" valign="middle">최대 제한</th>
</tr>
<tr>
   <td valign="middle"><p>코어 <a href="http://msdn.microsoft.com/library/azure/hh531793.aspx">(구독당)</a><sup>1</sup></p></td>
   <td valign="middle"><p>20</p></td>
   <td valign="middle"><p>10,000</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/gg456328.aspx">공동 관리자</a> (구독당)</p></td>
   <td valign="middle"><p>200</p></td>
   <td valign="middle"><p>200</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/storage-whatis-account/">저장소 계정</a> (구독당)</p></td>
   <td valign="middle"><p>100</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/cloud-services-what-is/">클라우드 서비스</a> (구독당)</p></td>
   <td valign="middle"><p>20</p></td>
   <td valign="middle"><p>200</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">가상 네트워크</a> (구독당)<sup>2</sup></p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/jj157100.aspx">로컬 네트워크</a> (구독당)</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>구독당 SQL 데이터베이스 서버</p></td>
   <td valign="middle"><p>6</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
<tr>
   <td valign="middle"><p>서버당 SQL 데이터베이스</p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>500</p></td>
</tr>
<tr>
   <td valign="middle"><p>구독당 DNS 서버</p></td>
   <td valign="middle"><p>9</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>구독당 예약된 IP</p></td>
   <td valign="middle"><p>5</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>구독당 호스티드 서비스 인증서</p></td>
   <td valign="middle"><p>400</p></td>
   <td valign="middle"><p>400</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156085.aspx">선호도 그룹</a> (구독당)</p></td>
   <td valign="middle"><p>256</p></td>
   <td valign="middle"><p>256</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups/">리소스 그룹</a> (구독당)</p></td>
   <td valign="middle"><p>300</p></td>
   <td valign="middle"><p>300</p></td>
</tr>

<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/services/batch/">배치 미리 보기</a> 구독당 지역별 계정</p></td>
   <td valign="middle"><p>1</p></td>
   <td valign="middle"><p>50</p></td>   
</tr>
</table>

<sup>1</sup>매우 작은 인스턴스는 부분 코어를 사용하더라도 코어 제한에 대해 하나의 코어로 계산됩니다.

<sup>2</sup>각 가상 네트워크는 단일 가상 네트워크 게이트웨이를 지원합니다.

##<a name="webworkerlimits"></a>클라우드 서비스 제한

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">리소스</th>
   <th align="left" valign="middle">기본 제한</th>
   <th align="left" valign="middle">최대 제한</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/cloud-services-what-is/">배포당 웹/작업자 역할<sup>1</sup></a></p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint">인스턴스 입력 끝점</a> (배포당)</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint">입력 끝점</a> (배포당)</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint">내부 끝점</a> (배포당)</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
</table>

<sup>1</sup>웹/작업자 역할이 있는 각 클라우드 서비스에는 프로덕션용과 스테이징용으로 두 개의 배포가 있을 수 있습니다. 또한 이 제한은 역할당 인스턴스 수(확장)가 아닌 고유 역할 수(구성)를 지칭합니다. 

##<a name="vmlimits"></a>가상 컴퓨터 제한

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">리소스</th>
   <th align="left" valign="middle">기본 제한</th>
   <th align="left" valign="middle">최대 제한</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/services/virtual-machines/">가상 컴퓨터</a> (클라우드 서비스당)<sup>1</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>클라우드 서비스당 입력 끝점<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>가상 컴퓨터를 만들면 컴퓨터를 포함할 클라우드 서비스가 자동으로 만들어집니다. 그러면 이 클라우드 서비스에 여러 개의 가상 컴퓨터를 추가할 수 있습니다.

<sup>2</sup>입력 끝점은 포함 클라우드 서비스 외부에 있는 가상 컴퓨터와 통신을 허용하는 데 사용됩니다. 이 클라우드 서비스 내의 가상 컴퓨터는 내부 통신을 위해 모든 UDP 포트와 TCP 포트 사이의 통신을 자동으로 허용합니다.

##<a name="websiteslimits"></a>웹 사이트 제한

[WACOM.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

##<a name="networkinglimits"></a>네트워킹 제한

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">리소스</th>
   <th align="left" valign="middle">기본 제한</th>
   <th align="left" valign="middle">최대 제한</th>
</tr>
<tr>
   <td valign="middle"><p>총 컴퓨터 수<sup>1</sup> 단위: <a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">가상 네트워크</a><sup>2</sup></p></td>
   <td valign="middle"><p>2048</p></td>
   <td valign="middle"><p>2048</p></td>
</tr>
<tr>
   <td valign="middle"><p>가상 컴퓨터 또는 역할 인스턴스용 동시 TCP 연결</p></td>
   <td valign="middle"><p>500K</p></td>
   <td valign="middle"><p>500K</p></td>
</tr>
<tr>
   <td valign="middle"><p>끝점당 ACL(액세스 제어 목록)<sup>3</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>가상 네트워크당 로컬 네트워크 사이트</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>10</p></td>
</tr>
</table>

<sup>1</sup>총 컴퓨터 수에는 가상 컴퓨터와 웹/작업자 역할 인스턴스가 포함됩니다.

<sup>2</sup>각 가상 네트워크는 단일 [가상 네트워크 게이트웨이][gateway]를 지원합니다.

<sup>3</sup>ACL은 가상 컴퓨터의 입력 끝점에서 지원됩니다. 웹/작업자 역할의 경우, 입력 끝점 및 인스턴스 입력 끝점에서 지원됩니다.

##<a name="storagelimits"></a>저장소 제한

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">리소스<sup>1</sup></th>
   <th align="left" valign="middle">기본 제한</th>
</tr>
<tr>
   <td valign="middle"><p>저장소 계정당 TB</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>단일 Blob 컨테이너, 테이블 또는 큐의 최대 크기</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>저장소 계정당 Blob 컨테이너, Blob, 파일 공유, 테이블, 큐, 엔터티 또는 메시지의 최대 수</p></td>
   <td valign="middle"><p>저장소 계정 용량 제한 500TB만 적용됨</p></td>
</tr>
<tr>
   <td valign="middle"><p>파일 공유의 최대 크기</p></td>
   <td valign="middle"><p>5 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>파일 공유 내 파일의 최대 수</p></td>
   <td valign="middle"><p>파일 공유의 총 용량 제한 5TB만 적용됨</p></td>
</tr>
<tr>
   <td valign="middle"><p>영구 디스크당 최대 8KB IOPS(기본 계층)</p></td>
   <td valign="middle"><p>300<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>영구 디스크당 최대 8KB IOPS(표준 계층)</p></td>
   <td valign="middle"><p>500<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>저장소 계정당 총 요청 빈도(개체 크기는 1KB로 가정함)</p></td>
   <td valign="middle"><p>초당 최대 20,000개 엔터티 또는 메시지</p></td>
</tr>
<tr>
   <td valign="middle"><p>단일 Blob의 목표 처리량</p></td>
   <td valign="middle"><p>초당 최대 60MB 또는 초당 최대 500개 요청</p></td>
</tr>
<tr>
   <td valign="middle"><p>단일 큐의 목표 처리량(1KB 메시지)</p></td>
   <td valign="middle"><p>초당 최대 2,000개의 메시지</p></td>
</tr>
<tr>
   <td valign="middle"><p>단일 테이블 파티션의 목표 처리량(1KB 엔터티)</p></td>
   <td valign="middle"><p>초당 최대 2,000개 엔터티</p></td>
</tr>
<tr>
   <td valign="middle"><p>저장소 계정당 최대 수신(미국 지역)</p></td>
   <td valign="middle"><p>10Gbps(GRS<sup>3</sup> 를 사용하는 경우), LRS의 경우 20Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>저장소 계정당 최대 송신(미국 지역)</p></td>
   <td valign="middle"><p>20Gbps(GRS<sup>3</sup> 를 사용하는 경우), LRS의 경우 30Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>저장소 계정당 최대 수신(유럽 및 아시아 지역)</p></td>
   <td valign="middle"><p>5Gbps(GRS<sup>3</sup> 를 사용하는 경우), LRS의 경우 10Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>저장소 계정당 최대 송신(유럽 및 아시아 지역)</p></td>
   <td valign="middle"><p>10Gbps(GRS<sup>3</sup> 를 사용하는 경우), LRS의 경우 15Gbps</p></td>
</tr>
</table>

<sup>1</sup>이러한 제한에 대한 자세한 내용은 [Azure 저장소 확장성 및 성능 목표][storagelimits]를 참조하세요. 

<sup>2</sup>기본 계층 가상 컴퓨터의 경우 총 요청 빈도 제한인 20,000개에 도달하지 않도록 하려면 많이 사용되는 VHD를 66개(20,000/300)보다 많이 저장소 계정에 넣지 마세요. 표준 계층 가상 컴퓨터의 경우 많이 사용되는 VHD를 40개(20,000/500)보다 많이 저장소 계정에 넣지 마세요. 자세한 내용은 [Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기][vmsizes]를 참조하세요. 

<sup>3</sup>GRS는 [지역 중복 저장소][georedundantstorage]의 약어입니다. LRS는 [로컬 중복 저장소][locallyredundantstorage]의 약어입니다. GRS도 로컬 중복 저장소입니다.

##<a name="batchlimits"></a>배치 미리 보기 제한

[WACOM.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

##<a name="documentdblimits"></a>DocumentDB 미리 보기 제한

[WACOM.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

##<a name="sqldblimits"></a>SQL 데이터베이스 제한

SQL 데이터베이스 제한에 대한 자세한 내용은 다음 항목을 참조하세요.

 - [Azure SQL 데이터베이스 서비스 계층(Edition)][sqltiers]
 - [Azure SQL 데이터베이스 서비스 계층 및 성능 수준][sqltiersperflevels]
 - [DTU(데이터베이스 처리량 단위) 할당량][sqlDTU]
 - [SQL 데이터베이스 리소스 제한][sqldblimits]

##<a name="mediaserviceslimits"></a>미디어 서비스 제한

[WACOM.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

##<a name="servicebuslimits"></a>서비스 버스 제한

[WACOM.INCLUDE [azure-servicebus-limits](../includes/azure-servicebus-limits.md)]

##<a name="adlimits"></a>Active Directory 제한

Azure AD(Active Directory)의 경우 다음 항목을 참조하세요.

 - [Azure Active Directory 서비스 제한 사항][adlimitsandrestrictions]

##<a name="seealso"></a>참고 항목

[Azure 제한 및 증가 이해][azurelimitsblogpost](영문)

[Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기][vmsizes]

  [customersupportfaq]: http://azure.microsoft.com/support/faq/
  [azurelimitsblogpost]: http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
  [gateway]: http://msdn.microsoft.com/library/azure/jj156210.aspx 
  [storagelimits]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [georedundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [sqldblimits]: http://msdn.microsoft.com/library/azure/dn338081.aspx
  [sqltiers]: http://msdn.microsoft.com/library/azure/dn741340.aspx
  [sqltiersperflevels]: http://msdn.microsoft.com/library/azure/dn741336.aspx
  [sqlDTU]: http://msdn.microsoft.com/library/azure/ee336245.aspx#DTUs
  [vmsizes]: http://msdn.microsoft.com/library/azure/dn197896.aspx
  [georedundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [locallyredundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx
  [adlimitsandrestrictions]: http://msdn.microsoft.com/library/azure/dn764971.aspx

<!--HONumber=35.2-->

<!--HONumber=46--> 
