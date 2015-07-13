<properties 
	pageTitle="클라우드로 저장된 데이터를 전송하기 위한 옵션 | Azure" 
	description="고급 분석을 위해 온-프레미스 기타 클라우드 원본에서 Microsoft Azure로 데이터를 전송하는 최상의 옵션을 선택하기 위한 지침." 
	services="data-factory, hdinsight, machine-learning, storage, sql-database" 
	documentationCenter="" 
	authors="cjgronlund" 
	manager="paulettm" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="01/07/2014" 
	ms.author="cgronlun"/>

# Azure 클라우드에 저장된 데이터를 전송하기 위한 옵션

이 문서에서는 고급 데이터 분석을 위해 온-프레미스 배포 또는 기타 클라우드 소스에서 Microsoft Azure로 데이터를 전송하는 적절한 옵션을 선택하기 위한 지침을 제공 합니다. 많은 양의 데이터를 전송하려면 시간이 오래 걸릴 수 있으며 적절한 보안을 필요로 합니다.

이 문서의 내용

* [Blob 저장소에 대한 Azure 가져오기/내보내기 서비스](#blob)
* [AZCopy 유틸리티](#azcopy-utility)
* [Azure PowerShell](#ps)
* [Azure 데이터 팩터리(미리 보기)](#data-factory)
* [Azure SQL 데이터베이스 마이그레이션 도구](#tools)
* [Azure SQL 데이터 동기화(미리 보기)](#data-sync)
* [Azure 이벤트 허브](#event-hubs)
* [데이터 전송에 대한 다른 옵션](#other)
* [적절한 데이터 전송 옵션 선택](#choose)


## Blob 저장소에 대한 Azure 가져오기/내보내기 서비스

네트워크를 통한 업로드나 다운로드가 엄청나게 많은 비용이 들거나 실행 가능하지 않은 경우, Azure 가져오기/내보내기 서비스를 사용하여 대량의 파일 데이터를 Azure Blob 저장소로(부터) 전송할 수 있습니다. 큰 데이터 집합을 네트워크를 통해 업로드하거나 다운로드하려면 시간이 매우 오래 걸릴 수 있습니다. 예를 들어 10TB는 T3 통해 1개월의 시간이 소요됩니다(44.7 Mbps). Microsoft Azure 가져오기/내보내기 서비스를 사용하여 고객은 하드 드라이브를 제공하여 데이터 업로드 또는 다운로드 시간을 단축할 수 있습니다. 배송을 포함하여 며칠 계획합니다.

대량의 파일 데이터 집합을 Blob 저장소로 전송하기 위해 해당 데이터가 포함된 하나 이상의 하드 드라이브를 Azure 데이터 센터로 보내서 데이터를 저장소 계정으로 업로드할 수 있습니다. 마찬가지로, Blob 저장소에서 데이터를 내보내기 위해 빈 하드 드라이브를 Azure 데이터 센터로 보내서 저장소 계정의 Blob 데이터를 하드 드라이브에 복사한 다음 하드 드라이브를 돌려받을 수 있습니다. 데이터가 포함된 드라이브를 보내기 전에 드라이브의 데이터를 암호화합니다. 가져오기/내보내기 서비스에서 사용자에게 보낼 데이터를 내보내면 해당 데이터도 발송 전에 암호화됩니다.

자세한 내용은 [Microsoft Azure 가져오기/내보내기 서비스를 사용하여 Blob 저장소에 데이터 전송][import-export]을 참조하세요.


## AZCopy 유틸리티

AzCopy는 Microsoft Azure Blob, 파일 및 테이블 저장소에 대해 고성능으로 데이터 업로드, 다운로드 및 복사를 수행하도록 디자인된 명령줄 유틸리티입니다. 네트워크를 통한 데이터 전송이 적절한 경우, 이 유틸리티는 온-프레미스 및 Azure 저장소 간에 일회성 데이터를 이동하기에 적합합니다. [AzCopy 명령줄 유틸리티 시작][azcopy]을 참조하십시오.

> [AZURE.NOTE]Linux 사용자: [Linux용 ACP, AzCopy 다운로드](http://www.paratools.com/acp)


## Azure PowerShell

Azure PowerShell은 Azure에서 작업의 배포와 관리를 제어 및 자동화하기 위해 사용할 수 있는 강력한 스크립팅 환경입니다. Azure PowerShell을 사용하여 데이터를 Blob 저장소에 업로드할 수 있으므로 고급 분석 또는 MapReduce 작업에서 데이터를 처리할 수 있습니다.

참고 항목:

* [Azure PowerShell을 사용하여 Blob 저장소에 데이터 업로드][upload]
* [Azure PowerShell을 설치 및 구성하는 방법][install]


## Azure 데이터 팩터리(미리 보기)

Azure 데이터 팩터리는 데이터 저장소, 처리 및 이동 서비스를 효율적이고 확장 가능하며 안정적인 데이터 프로덕션 파이프라인으로 구성하기 위해 완전히 관리되는 서비스입니다.

개발자는 해당 온-프레미스(데이터 관리 게이트웨이를 통해), 클라우드 기반 및 인터넷 서비스에서 제공된 반 구조화된 데이터, 구조화되지 않은 데이터와 구조화된 데이터를 조인, 집계 및 변환하고, 간단한 JSON 스크립팅을 통해 복잡한 데이터 처리를 설정하는 데이터 기반 워크플로를 구축할 수 있습니다. 고급 분석을 위해 Azure 저장소 또는 Azure SQL 데이터베이스에 결과 데이터를 저장할 수 있습니다.

구체적으로 개발자는 다양한 유형의 데이터 저장소, 열 매핑, 직렬화 형식 및 유형 처리에 대한 속성도 포함하고 있는 [의 "[지원되는 원본 및 싱크](data-factory-copy-activity.md#SupportedSourcesAndSinks)" 섹션](data-factory-copy-activity.md)에 표시된 다양한 원본과 대상 사이에서 일반적인 복사 작업을 오케스트레이션할 수 있습니다.

서비스는 자동 다시 시작으로 오류를 처리할 수 있으며 데이터를 다른 형식으로 이동할 때 형식 변환을 허용할 수 있습니다. 복사 작업을 정의하려면 [데이터 팩터리 시작][start]을 참조하세요. 데이터 저장소 등록 및 게이트웨이 설치 경험은 [온-프레미스 데이터로 작업하여 파이프라인을 사용하도록 설정][pipelines]에서 설명됩니다.

참고 항목:

* [데이터 팩터리 소개][intro]

## Azure SQL 데이터베이스 마이그레이션 도구

SQL Server 온-프레미스 및 비-SQL Server 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션할 수 있는 여러 도구가 있습니다. 시나리오에 가장 적합한 도구는 마이그레이션 중인 데이터베이스의 형식, 크기 및 복잡성에 따라 달라집니다.

* 데이터베이스를 내보내고, Azure Blob 저장소 계정에 내보내기 파일을 저장한 다음 새 Azure SQL 데이터베이스로 가져와서 스키마와 기존 Azure SQL 데이터베이스의 데이터를 모두 마이그레이션할 수 있습니다. 이 내보내기 시 만들어진 파일을 BACPAC 파일이라고 합니다. 자세한 내용은 [방법: Azure SQL 데이터베이스의 가져오기 및 내보내기 서비스 사용)][sql-import]을 참조하세요.
* 데이터베이스 복사 기능은 기존 Azure SQL 데이터베이스의 트랜잭션 일관성 있는 복사본인 Azure에서 새 데이터베이스를 만듭니다. 자세한 내용은 [Azure SQL 데이터베이스에서 데이터베이스 복사][sql-copy]를 참조하세요.
* 복잡한 데이터 변환이 필요한 경우 SQL Server Integration Services(SSIS)를 사용할 수 있습니다. SSIS는 Azure SQL 데이터베이스 내부 및 외부로 데이터를 이동하는 데 사용할 수 있습니다. [방법: Integration Services를 사용하여 Azure SQL 데이터베이스로 데이터베이스에 마이그레이션][integrate] 및 [Azure 및 하이브리드 데이터 이동을 위한 SSIS][SSIS]를 참조하세요.
* SQL Server 가져오기 및 내보내기 마법사는 데이터를 마이그레이션하기 위해 SSIS 패키지를 쉽게 만들 수 있습니다. 소스 및 대상을 구성한 후 기본 데이터 변환을 지정할 수 있습니다. 이러한 패키지를 저장, 수정, 실행 및 작업으로 예약할 수 있습니다. 자세한 내용은 [방법: 가져오기 및 내보내기 마법사를 사용하여 Azure SQL 데이터베이스로 데이터베이스 마이그레이션][wizard]을 참조하세요.
* SQL 데이터베이스 마이그레이션 마법사는 온-프레미스 SQL Server와 Azure SQL 데이터베이스 사이 및 Azure SQL 데이터베이스 서버 사이에 스키마와 데이터를 모두 마이그레이션하는 데 도움이 되는 도구입니다. 이 도구는 Azure SQL 데이터베이스와의 호환성 문제에 대한 추적 파일 및 스크립트도 분석합니다. 자세한 내용은 [방법: SQL 데이터베이스 마이그레이션 마법사 사용][use-wizard]을 참조하세요.
* bcp 유틸리티를 사용하여 SQL Server 테이블에 많은 수의 새 행을 가져오거나 테이블에서 데이터 파일로 데이터를 내보낼 수 있습니다. 자세한 내용은 [방법: bcp를 사용하여 Azure SQL 데이터베이스로 데이터베이스 마이그레이션][bcp]을 참조하세요.

참고 항목:

* [Azure SQL 데이터베이스로 데이터베이스 마이그레이션][migrate]
 

## Azure SQL 데이터 동기화(미리 보기)

SQL 데이터 동기화(미리 보기)는 Azure SQL 데이터베이스와 SQL Server 또는 Azure SQL 데이터베이스에서 호스팅되는 데이터베이스 간에 정기적인 동기화를 만들고 예약할 수 있습니다.

SQL 데이터 동기화는 개발자가 온-프레미스와 클라우드 Azure 데이터베이스 간의 델타 변경 내용을 동기화하는 데 적합합니다. [SQL 데이터 동기화][sync]를 참조하세요.

##	Azure 이벤트 허브

Microsoft Azure 이벤트 허브는 짧은 대기 시간 및 높은 안정성으로 이벤트 및 원격 분석을 엄청난 규모의 클라우드에 제공하는 이벤트 ingestor 서비스입니다. 다른 다운스트림 서비스와 함께 사용되는 이 서비스는 응용 프로그램 계측, 사용자 경험 또는 워크플로 처리 및 사물 인터넷 시나리오에서 특히 유용합니다.

개발자는 이벤트 허브로 데이터를 보내는 코드를 작성하고, 데이터를 처리하며 추가 프로세스를 위해 Azure Blob 또는 Azure SQL 데이터베이스에 저장합니다.

또는 개발자는 출력에 대한 스트리밍 데이터를 처리하는 확장 가능한 복잡한 이벤트를 제공하는 완전히 관리되는 서비스인 Azure 스트림 분석을 활용할 수 있습니다. 개발자는 Azure 이벤트 허브에서 스트림을 선택하고 들어오는 이벤트를 serialize하는데 사용되는 형식(예: JSON, CSV 또는 Avro 형식)을 지정한 다음, Azure Blob 또는 Azure SQL 데이터베이스를 포함하여 출력 위치를 추가할 수 있습니다.

다음을 참조하세요.

* [이벤트 허브 서비스 정보](/services/event-hubs/)
* [이벤트 허브 개요][overview]
* [Azure 스트림 분석 소개][stream]

## 데이터 전송에 대한 다른 옵션

하이브리드 연결은 Azure 웹 사이트 및 Azure 모바일 서비스를 온-프레미스 리소스에 연결하는 쉽고 편리한 방법을 제공합니다. 개발자는 온-프레미스에서 Azure에 데이터를 이동하는 웹 사이트를 구축할 수 있습니다. 자세한 내용은 [하이브리드 연결 개요][hybrid]를 참조하세요.

[가상 네트워크](/services/virtual-network/)를 통해 Azure 가상 컴퓨터에서 실행 중인 데이터 통합 도구를 사용하여 안전하게 현장 데이터 센터에서 온-프레미스 SQL Server 데이터베이스에 연결할 수 있습니다. 동일한 가상 네트워크 내에서 가상 컴퓨터와 서비스만이 식별되거나 서로 연결할 수 있습니다. 원하는 경우 네트워크 서비스 공급자 또는 Exchange 공급자를 통해 Azure에 대한 [Express 경로](/services/expressroute/) 직접 연결을 생성하고 공용 인터넷을 완전히 무시할 수도 있습니다.

[Azure Marketplace](?../source=datamarket.md)는 Storm 관리 파일 전송과 같이 Azure에 대한 데이터 이동을 활성화하는 파트너 솔루션을 제공합니다.

## 적절한 데이터 전송 옵션 선택

### 의사 결정 트리

![선택할 클라우드 데이터 전송 옵션의 결정을 지원하는 도움말][decision]

의사 결정 트리에 대한 참고 사항:

* SQL 데이터 동기화(미리 보기)는 변경된 데이터 동기화를 트리거합니다.
* 데이터 팩터리(미리 보기)는 Azure 저장소, Azure SQL 데이터베이스 및 온-프레미스 SQL Server 간의 데이터 복사를 지원합니다. 
* 데이터 팩터리를 사용 하는 것 외에도 예약된 방식으로 클라우드에 데이터를 이동하도록 기존 SSIS 패키지를 확장할 수 있습니다.

### 데이터(GB) 이동

<table border="1">
<tr>
<th>데이터 이동</th>
<th>한번</th>
<th>예약된</th>
<th>고려 사항</th>
</tr>

<tr>
<td><p>Azure 저장소에 파일</p>
</td>
<td><ul>
<li><a href="/documentation/articles/storage-use-azcopy/">AzCopy</a></li>
<li><a href="http://www.paratools.com/acp" target="_blank">acp</a></li>
<li><a href="/documentation/articles/install-configure-powershell/">Azure PowerShell</a></li>
<li><a href="/documentation/articles/storage-import-export-service/">Azure 가져오기/내보내기</a></li>
</ul>
</td>
<td><p>해당 없음</p>
</td>
<td><p>네트워크를 통해 데이터가 전송 불가능한 경우에 AzCopy, acp 및 Azure PowerShell 대신 Azure 가져오기/내보내기를 사용합니다. 서비스는 데이터 센터에 디스크를 전달하는 것 외에도 며칠의 시간이 소요될 수 있습니다.</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>Azure SQL 데이터베이스에 SQL Server</li>
<li>Azure SQL 데이터베이스를 SQL Server</li>
</ul>
</td>
<td><ul>
<li><a href="/documentation/articles/data-factory-introduction/">Azure 데이터 팩터리</a></li>
<li><a href="http://msdn.microsoft.com/library/azure/ee730904.aspx">SQL 데이터베이스 마이그레이션 도구</a></li>
</ul>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Azure 데이터 팩터리</a></p>
</td>
<td><p>예외적인 경우 형식 변환 및 자동 복구 작업을 제공하는 마이그레이션 도구 대신 Azure 데이터 팩터리(미리 보기)를 사용하도록 제안합니다. SQL Server가 온-프레미스인 경우 SQL Server에 연결할 데이터 관리 게이트웨이가 필요합니다.</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>Azure SQL 데이터베이스에 Azure 저장소</li>
<li>Azure 저장소에 Azure SQL 데이터베이스</li>
</ul>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Azure 데이터 팩터리</a></p>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Azure 데이터 팩터리</a></p>
</td>
<td><p>Azure 데이터 팩터리(미리 보기)는 예외적인 경우 형식 변환 및 자동 복구 작업을 제공합니다. SQL Server가 온-프레미스인 경우 SQL Server에 연결할 데이터 관리 게이트웨이가 필요합니다.</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>SQL 데이터 동기화에 SQL Server</li>
<li>Azure SQL 데이터베이스에 SQL 데이터 동기화</li>
</ul>
</td>
<td><p>해당 없음</p>
</td>
<td><p><a href="http://msdn.microsoft.com/library/azure/hh456371.aspx">SQL 데이터 동기화</a></p>
</td>
<td><p>SQL 데이터 동기화(미리 보기)는 동기화 일정 뿐만 아니라 동기화할 데이터베이스, 테이블 및 열을 정의하는 동기화 그룹으로 데이터를 동기화합니다.</p>
</td>
</tr>

</table>



### 데이터(TB) 이동

<table border="1">
<tr>
<th>데이터 이동</th>
<th>한번</th>
<th>예약된</th>
<th>고려 사항</th>
</tr>

<tr>
<td><p>Azure 저장소에 파일</p>
</td>
<td><p><a href="/documentation/articles/storage-import-export-service/">Azure 가져오기/내보내기</a></p>
</td>
<td><p>해당 없음</p>
</td>
<td><p>서비스는 디스크가 데이터 센터에 도달하기 위해 소요되는 시간 외에 며칠의 시간이 걸릴 수 있습니다.</p>
</td>
</tr>

</table>
<br>


<!--Anchors-->
[Azure Import/Export service for Blob storage]: #blob
[AZCopy utility]: #azcopy-utility
[Azure PowerShell]: #ps
[Azure Data Factory (preview)]: #data-factory
[Azure SQL Database migration tools]: #tools
[Azure SQL Data Sync (preview)]: #data-sync
[Azure Event Hubs]: #event-hubs
[Other options for data transfer]: #other
[Choose the right data transfer option]: #choose

<!--Image references-->
[decision]: ./media/data-management-options-for-transferring-data/data-transfer-decision-tree.png


<!--Link references-->
[import-export]: ../storage-import-export-service.md
[azcopy]: ../storage-use-azcopy.md
[upload]: ../hdinsight-upload-data.md#powershell
[install]: ../install-configure-powershell.md
[start]: data-factory-get-started.md
[pipelines]: data-factory-use-onpremises-datasources.md
[copy]: data-factory-copy-activity.md
[intro]: data-factory-introduction.md
[sql-import]: http://msdn.microsoft.com/library/azure/hh335292.aspx
[sql-copy]: http://msdn.microsoft.com/library/azure/ff951624.aspx
[integrate]: http://msdn.microsoft.com/library/azure/jj156150.aspx
[SSIS]: http://msdn.microsoft.com/library/jj901708.aspx
[wizard]: http://msdn.microsoft.com/library/azure/jj156152.aspx
[use-wizard]: http://msdn.microsoft.com/library/azure/jj156144.aspx
[bcp]: http://msdn.microsoft.com/library/azure/jj156153.aspx
[migrate]: http://msdn.microsoft.com/library/azure/ee730904.aspx
[overview]: http://msdn.microsoft.com/library/dn836025.aspx
[stream]: ../stream-analytics-introduction.md
[sync]: http://msdn.microsoft.com/library/azure/hh456371.aspx
[hybrid]: ../integration-hybrid-connection-overview.md
 

<!---HONumber=62-->