<properties 
	pageTitle="프리미엄 저장소: Azure 가상 컴퓨터 작업을 고성능 저장소" 
	description="디스크용 Azure 프리미엄 저장소에 대해 알아봅니다. 프리미엄 저장소 계정을 만드는 방법에 대해 알아봅니다." 
	services="storage" 
	documentationCenter="" 
	authors="Selcin" 
	manager="adinah" 
	editor="tysonn"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2014" 
	ms.author="selcint"/>


# 프리미엄 저장소: Azure 가상 컴퓨터 작업을 고성능 저장소

**디스크용 Azure 프리미엄 저장소**의 공개 미리 보기입니다.

프리미엄 저장소가 도입되면서 이제 Microsoft Azure는 다음 두 가지 유형의 지속형 저장소를 제공합니다. **프리미엄 저장소** 및 **표준 저장소**. 프리미엄 저장소는 최신 기술인 SSD(Solid State Drive)에 데이터를 저장하는 반면, 표준 저장소는 HDD(하드 디스크 드라이브)에 데이터를 저장합니다. 

프리미엄 저장소는 Azure 가상 컴퓨터에서 실행되는 I/O 사용량이 많은 작업에 대해 대기 시간이 짧은 고성능 디스크 지원을 제공합니다. 여러 개의 프리미엄 저장소 디스크를 VM(가상 컴퓨터)에 연결할 수 있습니다. 프리미엄 저장소를 사용할 경우 응용 프로그램이 VM당 최대 32TB의 저장소를 사용할 수 있으며, 읽기 작업의 대기 시간이 매우 짧은 상태로 VM당 50,000 IOPS(초당 입/출력 작업 수)를 얻을 수 있습니다. 프리미엄 저장소는 Azure 가상 컴퓨터에서 사용되는 디스크에 데이터를 저장하는 데만 사용할 수 있습니다. 

Azure 프리미엄 저장소 미리 보기를 등록하려면 [Azure 미리 보기](http://azure.microsoft.com/services/preview/) 페이지를 방문하세요.

이 문서에서는 Azure 프리미엄 저장소의 개요를 자세히 설명합니다.

**목차:**


* [프리미엄 저장소에 대해 알아야 할 중요 사항](#important)

* [디스크용 프리미엄 저장소 사용](#using)

* [프리미엄 저장소 사용 시 확장성 및 성능 목표](#scale)	

* [프리미엄 저장소 사용 시 제한](#throttling)	

* [프리미엄 저장소 사용 시 Blob 스냅숏 생성 및 복사](#restapi)	

* [프리미엄 저장소 사용 시 가격 책정 및 청구](#pricing)	

* [디스크용 프리미엄 저장소 계정 만들기 및 사용](#howto1)	

* [추가 리소스](#see)

###<a id="important">프리미엄 저장소에 대해 알아야 할 중요 사항</a>


다음은 프리미엄 저장소 사용 시 또는 이전에 고려해야 하는 중요 사항 목록입니다.

- 프리미엄 저장소를 사용하려면 프리미엄 저장소 계정이 있어야 합니다. 프리미엄 저장소 계정을 만드는 방법에 대한 자세한 내용은 [디스크용 프리미엄 저장소 계정 만들기 및 사용](#howto1)을 참조하세요.

- 프리미엄 저장소는 현재 [Microsoft Azure 미리 보기 포털](https://portal.azure.com/)에서 사용할 수 있으며, [저장소 REST API](http://msdn.microsoft.com//library/azure/dd179355.aspx) 버전 2014-02-14 이상, [서비스 관리 REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx) 버전 2014-10-01 이상 및 [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) 버전 0.8.10 이상의 SDK 라이브러리를 통해 액세스할 수 있습니다. 

- 프리미엄 저장소는 다음 지역에서 제한된 미리 보기에 사용할 수 있습니다. 미국 서부, 미국 동부 2 및 서유럽.

- 프리미엄 저장소는 Azure VM(가상 컴퓨터)용 영구적인 디스크를 포함하는 데 사용되는 Azure 페이지 Blob만 지원합니다. Azure 페이지 Blob에 대한 자세한 내용은 [블록 Blob 및 페이지 Blob 이해](http://msdn.microsoft.com/library/azure/ee691964.aspx)를 참조하세요. 프리미엄 저장소는 Azure 블록 Blob, Azure 파일, Azure 테이블 또는 Azure 큐를 지원하지 않습니다.

- 프리미엄 저장소 계정은 로컬에서 중복(LRS)되며 단일 지역 내에 데이터 복사본 3개를 유지합니다.  프리미엄 저장소 사용 시 지역에서 복제와 관련된 고려 사항은 이 문서의 [프리미엄 저장소 사용 시 Blob 스냅숏 생성 및 복사](#restapi) 섹션을 참조하세요.

- VM 디스크에 프리미엄 저장소 계정을 사용하려는 경우 DS 시리즈의 VM을 사용해야 합니다. DS 시리즈 VM에는 표준 및 프리미엄 저장소 디스크를 모두 사용할 수 있습니다. 그러나 DS 시리즈가 아닌 VM의 경우 프리미엄 저장소 디스크를 사용할 수 없습니다. 사용 가능한 Azure VM 디스크 유형 및 크기에 대한 자세한 내용은 [Azure용 가상 컴퓨터 및 클라우드 서비스 크기](http://msdn.microsoft.com/library/azure/dn197896.aspx)를 참조하세요. 

- VM용 프리미엄 저장소 디스크를 설정하는 프로세스는 표준 저장소 디스크와 유사합니다. Azure 디스크와 VM에 가장 적합한 프리미엄 저장소 옵션을 선택해야 합니다. 프리미엄 제품의 성능 특성에 따라 VM 크기가 작업에 적합해야 합니다. 자세한 내용은 [프리미엄 저장소 사용 시 확장성 및 성능 목표](#scale)를 참조하세요.
 
- 프리미엄 저장소 계정은 사용자 지정 도메인 이름에 매핑할 수 없습니다.

- 프리미엄 저장소에 대한 저장소 분석은 현재 지원되지 않습니다.

###<a id="using">디스크용 프리미엄 저장소 사용</a>
다음 두 가지 방법 중 하나로 디스크용 프리미엄 저장소를 사용할 수 있습니다.

- 먼저 새 프리미엄 저장소 계정을 만든 후 VM을 만들 때 사용합니다.
- 새 DS 시리즈 VM을 만듭니다. VM을 만드는 동안 이전에 만든 프리미엄 저장소 계정을 선택하거나, 새로 만들거나, Azure 포털에서 기본 프리미엄 계정을 만들도록 할 수 있습니다.

Azure는 저장소 계정을 운영 체제 및 데이터 디스크의 컨테이너로 사용합니다. 즉, Azure DS 시리즈 VM을 만들고 Azure 프리미엄 저장소 계정을 선택하는 경우 운영 체제와 데이터 디스크가 해당 저장소 계정에 저장됩니다.

프리미엄 저장소의 혜택을 활용하려면 먼저  *Premium_LRS* 계정 유형을 사용하여 프리미엄 저장소 계정을 만듭니다. 이렇게 하려면 [Microsoft Azure 미리 보기 포털](https://portal.azure.com/), [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) 또는 [서비스 관리 REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx)를 사용할 수 있습니다. 단계별 지침은 [디스크용 프리미엄 저장소 계정 만들기 및 사용](#howto1)을 참조하세요.

<h4>중요:</h4>

- 프리미엄 저장소 계정의 용량 및 대역폭 미리 보기 한도에 대한 자세한 내용은 [프리미엄 저장소 사용 시 확장성 및 성능 목표](#scale) 섹션을 참조하세요. 응용 프로그램의 요구가 단일 저장소 계정의 확장성 목표를 초과하는 경우 여러 저장소 계정을 사용하도록 응용 프로그램을 빌드하고 데이터를 이러한 저장소 계정에 분할합니다. 예를 들어 많은 VM에 51TB 디스크를 연결하려는 경우 32TB가 단일 프리미엄 저장소 계정의 한도이므로 두 개의 저장소 계정에 분산합니다.
- 기본적으로 디스크 캐싱 정책은 모든 프리미엄 데이터 디스크의 경우 "읽기 전용"이며, VM에 연결된 프리미엄 운영 체제 디스크의 경우 "읽기/쓰기"입니다. 응용 프로그램 I/O의 성능을 최적화하려면 이 구성 설정을 사용하는 것이 좋습니다. 쓰기가 많거나 쓰기 전용인 데이터 디스크의 경우(예: SQL Server 로그 파일) 더 나은 응용 프로그램 성능을 얻기 위해 디스크 캐싱을 사용하지 않도록 설정합니다.
- VM에서 디스크 트래픽을 제어하기에 충분한 대역폭을 사용할 수 있는지 확인합니다. 각 VM 크기에 사용 가능한 디스크 대역폭에 대한 자세한 내용은 [Azure용 가상 컴퓨터 및 클라우드 서비스 크기](http://msdn.microsoft.com/library/azure/dn197896.aspx)를 참조하세요.
- 동일한 DS 시리즈 VM에 프리미엄 및 표준 저장소 디스크를 모두 사용할 수 있습니다.
- 프리미엄 저장소를 사용하면 DS 시리즈 VM을 프로비전하고 여러 영구 데이터 디스크를 VM에 연결할 수 있습니다. 필요한 경우 디스크에서 스프라이프하여 볼륨의 용량 및 성능을 높일 수 있습니다. [저장소 공간](http://technet.microsoft.com/library/hh831739.aspx)을 사용하여 프리미엄 저장소 데이터 디스크를 스트라이프한 경우 사용되는 각 디스크에 대해 하나의 열로 구성해야 합니다. 그렇지 않으면 디스크 간의 균일하지 않은 트래픽 분포로 인해 스프라이프된 볼륨의 전체 성능이 저하될 수 있습니다. 기본적으로 서버 관리자 UI(사용자 인터페이스)에서는 최대 8개의 디스크에 대한 열을 설정할 수 있습니다. 그러나 디스크가 8개보다 많은 경우에는 PowerShell을 사용하여 볼륨을 만들고 열 수를 수동으로 지정해야 합니다. 그렇지 않으면 더 많은 디스크가 있는데도 서버 관리자 UI에서 계속 8개의 열을 사용합니다. 예를 들어 단일 스프라이프 집합에 32개의 디스크가 있는 경우 32개의 열을 지정해야 합니다. [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) PowerShell cmdlet의  *NumberOfColumns* 매개 변수를 사용하여 가상 디스크에서 사용되는 열 수를 지정할 수 있습니다. 자세한 내용은 [저장소 공간 개요](http://technet.microsoft.com/library/jj822938.aspx) 및 [저장소 공간 질문과 대답](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)을 참조하세요.


###<a id="scale">프리미엄 저장소 사용 시 확장성 및 성능 목표</a>
프리미엄 저장소 계정에 대해 디스크를 프로비전할 때 사용할 수 있는 IOPS(초당 입/출력 작업 수) 및 처리량(대역폭)은 디스크 크기에 따라 달라집니다. 현재 세 가지 유형(P10, P20 및 P30)의 프리미엄 저장소 디스크가 있습니다. 각 디스크에는 다음 표에 지정된 대로 특정 IOPS 및 처리량 한도가 있습니다.

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>프리미엄 저장소 디스크 유형</strong></td>
	<td><strong>P10</strong></td>
	<td><strong>P20</strong></td>
	<td><strong>P30</strong></td>
</tr>
<tr>
	<td><strong>디스크 크기</strong></td>
	<td>128GB</td>
	<td>512GB</td>
	<td>1024GB(1TB)</td>
</tr>
<tr>
	<td><strong>디스크당 IOPS</strong></td>
	<td>500</td>
	<td>2300</td>
	<td>5000</td>
</tr>
<tr>
	<td><strong>디스크당 처리량</strong></td>
	<td>초당 100MB</td>
	<td>초당 150 MB</td>
	<td>초당 200 MB</td>
</tr>
</tbody>
</table>

Azure는 표에 지정된 대로 디스크 크기(반올림됨)를 가장 가까운 프리미엄 저장소 디스크 옵션에 매핑합니다. 예를 들어 크기가 100GB인 디스크는 P10 옵션으로 분류되며 초당 최대 500개 IO 단위를 초당 최대 100MB 처리량으로 수행할 수 있습니다. 마찬가지로, 크기가 400GB인 디스크는 P20 옵션으로 분류되며 초당 최대 2300개 IO 단위를 초당 최대 150MB 처리량으로 수행할 수 있습니다.

I/O(입/출력) 단위 크기는 256KB입니다. 전송되는 데이터가 256KB 미만일 경우 하나의 I/O 단위로 간주됩니다. 더 큰 I/O 크기는 256KB 크기의 여러 I/O로 계산됩니다. 예를 들어 1100KB I/O는 I/O 단위 5개로 계산됩니다.

처리량 한도에는 디스크에 대한 읽기 및 쓰기가 모두 포함됩니다. 예를 들어 P10 디스크의 경우 읽기 및 쓰기의 합계가 100MB보다 작거나 같아야 합니다.  마찬가지로, 하나의 P10 디스크는 초당 100MB 읽기 또는 초당 100MB 쓰기이거나 초당 60MB 읽기와 초당 40MB 쓰기 등일 수 있습니다.
 
Azure에서 디스크를 생성하는 경우 용량, 성능, 확장성 및 최대 부하 측면에서 응용 프로그램의 요구에 가장 적절한 프리미엄 저장소 디스크 제품을 선택합니다.

다음 표에서는 프리미엄 저장소 계정의 확장성 목표에 대해 설명합니다.

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>총 계정 용량</strong></td>
	<td><strong>로컬 중복 저장소 계정의 총 대역폭</strong></td>
</tr>
<tr>
	<td>
	<ul>
       <li type=round>디스크 용량: 32TB</li>
       <li type=round>스냅숏 용량: 10 TB</li>
    </ul>
	</td>
	<td>인바운드+아웃바운드에 대해 초당 최대 50기가비트</td>
</tr>
</tbody>
</table>

- 인바운드는 저장소 계정으로 전송되는 모든 데이터(요청)를 가리킵니다.
- 아웃바운드는 저장소 계정에서 수신되는 모든 데이터(응답)를 가리킵니다.

자세한 내용은 [Azure 저장소 확장성 및 성능 목표](http://msdn.microsoft.com/library/dn249410.aspx)를 참조하세요.


###<a id="throttling">프리미엄 저장소 사용 시 제한</a>
응용 프로그램의 IOPS 또는 처리량이 프리미엄 저장소 디스크에 할당된 한도를 초과할 경우 제한이 표시될 수 있습니다. 제한을 방지하려면 프로비전한 디스크의 확장성 및 성능 목표에 따라 디스크에 대해 보류 중인 I/O 요청 수를 제한하는 것이 좋습니다.  

응용 프로그램이 제한을 방지하도록 설계된 경우 가장 낮은 대기 시간을 얻을 수 있습니다. 반면, 디스크에 대해 보류 중인 I/O 요청 수가 너무 작으면 응용 프로그램이 디스크에서 사용할 수 있는 최대 IOPS 및 처리량 수준을 활용할 수 없습니다.

다음 예제에서는 제한 수준을 계산하는 방법을 보여 줍니다.

<h4>예제 1:</h4>
응용 프로그램이 P10 디스크에서 초당 16KB 디스크 크기의 495개 I/O를 수행했습니다(495개 I/O 단위와 같음). 동일한 초에 2MB I/O를 시도하면 총 I/O 단위 수는 495+8이 됩니다. 이는 I/O 단위 크기가 256KB일 경우 2MB I/O에서 2048KB/256KB = 8개 I/O 단위가 나오기 때문입니다. 495+8의 합계가 디스크 한도인 500개 IOPS를 초과하므로 제한이 발생합니다. 

<h4>참고:</h4> 
모든 계산은 256KB의 I/O 단위 크기를 기반으로 합니다.

<h4>예제 2:</h4>
응용 프로그램이 P10 디스크에서 256KB 디스크 크기의 400개 I/O를 수행했습니다. 사용한 총 대역폭은 (400*256)/1024 = 100MB/초입니다. 초당 100MB가 P10 디스크의 처리량 한도입니다. 응용 프로그램이 해당 초에 추가 I/O를 수행하려고 하면 할당된 한도를 초과하므로 제한됩니다.

<h4>참고:</h4>
디스크 트래픽이 대체로 작은 I/O 크기로 구성된 경우 응용 프로그램이 처리량 한도 전에 IOPS 한도에 도달할 가능성이 큽니다. 반면, 디스크 트래픽이 대체로 큰 I/O 크기로 구성된 경우에는 응용 프로그램이 IOPS 한도 대신 처리량 한도에 도달할 가능성이 큽니다. 최적 I/O 크기를 사용하고 디스크에 대해 보류 중인 I/O 요청 수를 제한하여 응용 프로그램의 IOPS 및 처리량을 최대화할 수 있습니다.


###<a id="restapi">프리미엄 저장소 사용 시 Blob 스냅숏 생성 및 복사</a>
표준 저장소 사용 시 스냅숏을 만드는 것과 동일한 방식으로 프리미엄 저장소에 대한 스냅숏을 만들 수 있습니다. 프리미엄 저장소는 로컬에서 중복되므로 스냅숏을 만든 후 지역 중복 표준 저장소 계정에 이러한 스냅숏을 복사하는 것이 좋습니다. 자세한 내용은 [Azure 저장소 중복 옵션](http://msdn.microsoft.com/library/azure/dn727290.aspx)을 참조하세요.

디스크가 VM에 연결되어 있을 경우 디스크를 지원하는 페이지 Blob에 대해 특정 API 작업을 수행할 수 없습니다. 예를 들어 디스크가 VM에 연결되어 있는 한 해당 Blob에 대해 [Blob 복사](http://msdn.microsoft.com/library/azure/dd894037.aspx) 작업을 수행할 수 없습니다. 대신, 먼저 [Blob 스냅숏 생성](http://msdn.microsoft.com/library/azure/ee691971.aspx) REST API 메서드를 사용하여 해당 Blob의 스냅숏을 만든 후 스냅숏의 [Blob 복사](http://msdn.microsoft.com/library/azure/dd894037.aspx)를 수행하여 연결된 디스크를 복사합니다. 또는 디스크를 분리한 후 기본 Blob에 대해 필요한 작업을 수행할 수 있습니다.

<h4>중요:</h4>

- 프리미엄 저장소의 Blob 복사 작업이 대상의 기존 Blob을 덮어쓰는 경우 덮어쓸 Blob에 스냅숏이 없어야 합니다.  프리미엄 저장소 계정 간이나 내에서 복사하는 경우 복사를 시작할 때 대상 Blob에 스냅숏이 없어야 합니다.
- 단일 Blob의 스냅숏 수는 100개로 제한됩니다. 최대 10분마다 스냅숏을 생성할 수 있습니다.
- 10TB는 프리미엄 저장소 계정당 최대 스냅숏 용량입니다. 스냅숏 용량은 스냅숏에 있는 고유 데이터입니다. 즉, 스냅숏 범주에 기본 Blob 크기가 포함되지 않습니다.
- AzCopy 또는 Blob 복사를 사용하여 프리미엄 저장소 계정에서 지역 중복 표준 저장소 계정으로 스냅숏을 복사할 수 있습니다. 자세한 내용은 [Microsoft Azure 저장소와 함께 AzCopy를 사용하는 방법](http://azure.microsoft.com/documentation/articles/storage-use-azcopy/) 및 [Blob 복사](http://msdn.microsoft.com/library/azure/dd894037.aspx)를 참조하세요.
- 프리미엄 저장소 계정에서 페이지 Blob에 대한 REST 작업을 수행하는 방법에 대한 자세한 내용은 MSDN 라이브러리에서 [Azure 프리미엄 저장소와 함께 Blob 서비스 작업 사용](http://go.microsoft.com/fwlink/?LinkId=521969)을 참조하세요.


###<a id="pricing">프리미엄 저장소 사용 시 가격 책정 및 청구</a>
프리미엄 저장소를 사용하는 경우 다음과 같은 청구 고려 사항이 적용됩니다.

- 프리미엄 저장소 디스크에 대한 청구는 프로비전된 디스크 크기에 따라 달라집니다. Azure는 [프리미엄 저장소 사용 시 확장성 및 성능 목표](#scale) 섹션에 지정된 표에 따라 디스크 크기(반올림됨)를 가장 가까운 프리미엄 저장소 디스크 옵션에 매핑합니다 . 프로비전된 디스크에 대한 청구는 프리미엄 저장소 제품의 월별 가격을 사용하여 시간당 비례합니다. 예를 들어 P10 디스크를 프로비전하고 20시간 후 삭제한 경우 20시간에 비례하여 P10 제품에 대해 청구됩니다. 이는 디스크에 기록되는 실제 데이터 양이나 사용한 IOPS/처리량에 관계없이 적용됩니다.
- 프리미엄 저장소의 스냅숏은 스냅숏이 사용한 추가 용량에 대해 청구됩니다. 스냅숏에 대한 자세한 내용은 [Blob의 스냅숏 만들기](http://msdn.microsoft.com/library/azure/hh488361.aspx)를 참조하세요.
- [아웃바운드 데이터 전송](http://azure.microsoft.com/pricing/details/data-transfers/)(Azure 데이터 센터에서 데이터 전송) 시 대역폭 사용에 대해 청구가 발생합니다.

프리미엄 저장소와 DS 시리즈 VM에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 저장소 가격](http://azure.microsoft.com/pricing/details/storage/)
- [가상 컴퓨터 가격](http://azure.microsoft.com/pricing/details/virtual-machines/)

###<a id="howto1">디스크용 프리미엄 저장소 계정 만들기 및 사용</a>
이 섹션에서는 Azure 미리 보기 포털 및 Azure PowerShell을 사용하여 프리미엄 저장소 계정을 만드는 방법을 보여 줍니다. 또한 프리미엄 저장소 계정의 샘플 사용 사례를 보여 줍니다. 이 경우 가상 컴퓨터를 만든 후 프리미엄 저장소를 사용할 때 데이터 디스크를 가상 컴퓨터에 연결합니다.

섹션 내용:

* [Azure 미리 보기 포털: 프리미엄 저장소 계정 만들기](#howto3)	

* [Azure PowerShell: 프리미엄 저장소 계정 만들기 및 기본 VM 작업에 사용](#howto2)	
 
###<a id="howto3">Azure 미리 보기 포털: 프리미엄 저장소 계정 만들기</a>
이 섹션에서는 Azure 미리 보기 포털을 사용하여 프리미엄 저장소 계정을 만드는 방법을 보여 줍니다.

1.	[Azure 미리 보기 포털](https://portal.azure.com/)에 로그인합니다. 아직 구독이 없으면 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/) 서비스를 확인하세요. 


    >[AZURE.NOTE] Azure 관리 포털에 로그인한 경우 포털의 오른쪽 위에 있는 사용자 계정 이름을 클릭합니다. **새 포털로 전환**을 클릭합니다.
        

2.	허브 메뉴에서 **새로 만들기**를 클릭합니다.

3.	**새로 만들기**에서 **모두**를 클릭합니다. **저장소, 캐시, +백업**을 선택합니다. 여기서 **저장소**를 클릭한 후 **만들기**를 클릭합니다.

4.	저장소 계정 블레이드에서 저장소 계정의 이름을 입력합니다. **가격 계층**을 클릭합니다. **권장 가격 계층** 블레이드에서 **모든 가격 계층 찾아보기**를 클릭합니다. **가격 계층 선택** 블레이드에서 **Premium Locally Redundant(프리미엄 로컬 중복)**를 선택합니다. **선택**을 클릭합니다. **저장소 계정** 블레이드에 기본적으로 **표준-GRS**가 **가격 계층**으로 표시됩니다. **선택**을 클릭하면 **가격 계층**이 **프리미엄-LRS**로 표시됩니다.
	
	![Pricing Tier][Image1]

	
5.	**저장소 계정** 블레이드에서 **리소스 그룹**, **구독**, **위치** 및 **진단**의 기본값을 유지합니다. **만들기**를 클릭합니다.

Azure 환경 내의 전체 연습은 [Azure 미리 보기 포털에서 Windows를 실행하는 가상 컴퓨터 만들기](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial-azure-preview/)를 참조하세요.

###<a id="howto2">Azure PowerShell: 프리미엄 저장소 계정 만들기 및 기본 VM 작업에 사용</a>
이 섹션에서는 프리미엄 저장소 계정을 만드는 방법 및 가상 컴퓨터를 만들고 Azure PowerShell을 통해 데이터 디스크를 VM에 연결하는 동안 이 계정을 사용하는 방법을 보여 줍니다.

1. [Azure PowerShell을 설치 및 구성하는 방법](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)에 제공된 단계에 따라 PowerShell 환경을 설정합니다.
2. PowerShell 콘솔을 시작하고, 구독에 연결한 후 콘솔 창에서 다음 PowerShell cmdlet을 실행합니다. 이 PowerShell 문에 표시된 대로 프리미엄 저장소 계정을 만드는 경우 **Type** 매개 변수를 **Premium_LRS**로 지정해야 합니다.

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

3. 새 DS 시리즈 VM을 만든 후 콘솔 창에서 다음 PowerShell cmdlet을 실행하여 프리미엄 저장소를 만들도록 지정합니다.

    	$storageAccount = "yourpremiumaccount"
    	$adminName = "youradmin"
    	$adminPassword = "yourpassword"
    	$vmName ="yourVM"
    	$location = "West US"
    	$imageName = "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201409.01-en.us-127GB.vhd"
    	$vmSize ="Standard_DS2"
    	$OSDiskPath = "https://" + $storageAccount + ".blob.core.windows.net/vhds/" + $vmName + "_OS_PIO.vhd"
    	$vm = New-AzureVMConfig -Name $vmName -ImageName $imageName -InstanceSize $vmSize -MediaLocation $OSDiskPath
    	Add-AzureProvisioningConfig -Windows -VM $vm -AdminUsername $adminName -Password $adminPassword
    	New-AzureVM -ServiceName $vmName -VMs $VM -Location $location

4. VM에 추가 디스크 공간을 사용하려면 콘솔 창에서 다음 PowerShell cmdlet을 실행하여 생성 후 기존 DS 시리즈 VM에 새 데이터 디스크를 연결합니다.

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

###<a id="see">추가 리소스</a>
[Azure 프리미엄 저장소와 함께 Blob 서비스 작업 사용](http://go.microsoft.com/fwlink/?LinkId=521969)

[Windows를 실행하는 가상 컴퓨터 만들기](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial-azure-preview/)

[Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기](http://msdn.microsoft.com/library/azure/dn197896.aspx)

[저장소 설명서](http://azure.microsoft.com/documentation/services/storage/)

[MSDN 참조](http://msdn.microsoft.com/library/azure/gg433040.aspx)

[Image1]: ./media/storage-premium-storage-preview-portal/Azure_pricing_tier.png

<!--HONumber=42-->
