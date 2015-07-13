<properties
	pageTitle="프리미엄 저장소: Azure 가상 컴퓨터 작업용 고성능 저장소"
	description="디스크용 Azure 프리미엄 저장소 알아보기 프리미엄 저장소 계정 만드는 방법 알아보기"
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carolz"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/11/2015"
	ms.author="tamram;selcint"/>


# 프리미엄 저장소: Azure 가상 컴퓨터 작업용 고성능 저장소

## 개요

보다 빠른 가상 컴퓨터를 위한 **Azure 프리미엄 저장소 디스크** 사용을 환영합니다!

프리미엄 저장소가 도입되면서 이제 Microsoft Azure는 **프리미엄 저장소** 및 **표준 저장소**라는 두 가지 유형의 지속형 저장소를 제공합니다. 프리미엄 저장소는 최신 기술인 SSD(Solid State Drive)에 데이터를 저장하는 반면, 표준 저장소는 HDD(하드 디스크 드라이브)에 데이터를 저장합니다.

프리미엄 저장소는 Azure 가상 컴퓨터에서 실행되는 I/O 사용량이 많은 작업에 대해 대기 시간이 짧은 고성능 디스크 지원을 제공합니다. 여러 개의 프리미엄 저장소 디스크를 VM(가상 컴퓨터)에 연결할 수 있습니다. 프리미엄 저장소를 사용할 경우 응용프로그램이 VM당 최대 32TB의 저장소를 사용할 수 있으며, 읽기 작업의 대기 시간이 매우 짧은 상태로 VM당 64,000 IOPS(초당 입/출력 작업 수)를 얻을 수 있습니다.

Azure 프리미엄 저장소를 시작하려면 방문 [무료로 시작 하기](http://azure.microsoft.com/pricing/free-trial/) 페이지를 방문하세요.

이 문서에서는 Azure 프리미엄 저장소의 개요를 자세히 설명합니다.

## 프리미엄 저장소에 대해 알아야 할 중요 사항

다음은 프리미엄 저장소 사용 시 또는 이전에 고려해야 하는 중요 사항 목록입니다.

- 프리미엄 저장소를 사용하려면 프리미엄 저장소 계정이 있어야 합니다. 프리미엄 저장소 계정을 만드는 방법에 대한 자세한 내용은 [디스크용 프리미엄 저장소 계정 만들기 및 사용](#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)을 참조하세요.

- 프리미엄 저장소는 현재 [Microsoft Azure Preview 포털](https://portal.azure.com/)에서 사용할 수 있으며 SDK 라이브러리([저장소 REST API](http://msdn.microsoft.com//library/azure/dd179355.aspx) 버전 2014-02-14 이상, [서비스 관리 REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx) 버전 2014-10-01 이상 및 [Azure PowerShell](../install-configure-powershell.md) 버전 0.8.10 이상)를 통해 액세스할 수 있습니다.

- 프리미엄 저장소는 다음 지역에서 현재 사용할 수 있습니다. 미국 서부, 미국 동부 2, 서유럽, 중국 동부, 동남 아시아, 일본 서부 및 오스트레일리아 동부.

- 프리미엄 저장소는 Azure VM(가상 컴퓨터)용 영구적인 디스크를 포함하는 데 사용되는 Azure 페이지 Blob만 지원합니다. Azure 페이지 Blob에 대한 자세한 내용은 [블록 Blob 및 페이지 Blob 이해](http://msdn.microsoft.com/library/azure/ee691964.aspx)를 참조하세요. 프리미엄 저장소는 Azure 블록 Blob, Azure 파일, Azure 테이블 또는 Azure 큐를 지원하지 않습니다.

- 프리미엄 저장소 계정은 로컬에서 중복(LRS)되며 단일 지역 내에 데이터 복사본 3개를 유지합니다. 프리미엄 저장소 사용 시 지역에서 복제와 관련된 고려 사항은 이 문서의 [프리미엄 저장소 사용 시 Blob 스냅숏 생성 및 복사](#snapshots-and-copy-blob-whko-kring-premium-storage)를 참조하세요.

- VM 디스크에 프리미엄 저장소 계정을 사용하려는 경우 DS 시리즈의 VM을 사용해야 합니다. DS 시리즈 VM에는 표준 및 프리미엄 저장소 디스크를 모두 사용할 수 있습니다. 그러나 DS 시리즈가 아닌 VM의 경우 프리미엄 저장소 디스크를 사용할 수 없습니다. 사용 가능한 Azure VM 디스크 유형 및 크기에 대한 자세한 내용은 [Azure용 가상 컴퓨터 및 클라우드 서비스 크기](http://msdn.microsoft.com/library/azure/dn197896.aspx)를 참조하세요.

- VM용 프리미엄 저장소 디스크를 설정하는 프로세스는 표준 저장소 디스크와 유사합니다. Azure 디스크와 VM에 가장 적합한 프리미엄 저장소 옵션을 선택해야 합니다. 프리미엄 제품의 성능 특성에 따라 VM 크기가 작업에 적합해야 합니다. 자세한 내용은 [프리미엄 저장소 사용 시 확장성 및 성능 목표](#scalability-and-performance-targets-whko-kring-premium-storage)를 참조하세요.

- 프리미엄 저장소 계정은 사용자 지정 도메인 이름에 매핑할 수 없습니다.

- 프리미엄 저장소에 대한 저장소 분석은 현재 지원되지 않습니다. 프리미엄 저장소 계정에 디스크를 사용하여 VM의 성능 메트릭을 분석하려면 Windows VM용 [Windows 성능 모니터](https://technet.microsoft.com/library/cc749249.aspx) 및 Linux VM용 [IOSTAT](http://linux.die.net/man/1/iostat)와 같은 운영 체제 기반 도구를 사용합니다.

## 디스크용 프리미엄 저장소 사용
다음 두 가지 방법 중 하나로 디스크용 프리미엄 저장소를 사용할 수 있습니다.

- 먼저 새 프리미엄 저장소 계정을 만든 후 VM을 만들 때 사용합니다.
- 새 DS 시리즈 VM을 만듭니다. VM을 만드는 동안 이전에 만든 프리미엄 저장소 계정을 선택하거나, 새로 만들거나, Azure 포털에서 기본 프리미엄 계정을 만들도록 할 수 있습니다.

Azure는 저장소 계정을 운영 체제(OS) 및 데이터 디스크의 컨테이너로 사용합니다. 즉, Azure DS 시리즈 VM을 만들고 Azure 프리미엄 저장소 계정을 선택하는 경우 운영 체제와 데이터 디스크가 해당 저장소 계정에 저장됩니다.

프리미엄 저장소의 혜택을 활용하려면 먼저 *Premium_LRS* 계정 유형을 사용하여 프리미엄 저장소 계정을 만듭니다. 이렇게 하려면 [Microsoft Azure Preview 포털](https://portal.azure.com/), [Azure PowerShell](../install-configure-powershell.md) 또는 [서비스 관리 REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx)를 사용할 수 있습니다. 단계별 지침은 [디스크용 프리미엄 저장소 계정 만들기 및 사용](#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)을 참조하세요.

### 중요:

- 프리미엄 저장소 계정의 용량 및 대역폭 미리 보기 한도에 대한 자세한 내용은 [프리미엄 저장소 사용 시 확장성 및 성능 목표](#scalability-and-performance-targets-whko-kring-premium-storage) 섹션을 참조하세요. 응용 프로그램의 요구가 단일 저장소 계정의 확장성 목표를 초과하는 경우 여러 저장소 계정을 사용하도록 응용 프로그램을 빌드하고 데이터를 이러한 저장소 계정에 분할합니다. 예를 들어 많은 VM에 51TB(테라바이트) 디스크를 연결하려는 경우 35TB가 단일 프리미엄 저장소 계정의 한도이므로 두 개의 저장소 계정에 분산합니다. 단일 프리미엄 저장소 계정에 35TB 이상의 프로비전된 디스크가 없어야 합니다.
- 기본적으로 디스크 캐싱 정책은 VM에 연결된 프리미엄 운영 체제 디스크에 대한 "읽기 / 쓰기" 및 모든 프리미엄 데이터 디스크에 대한 "읽기 전용"입니다. 응용프로그램의 I/O에 대한 최적의 성능을 얻으려면 이 구성 설정이 좋습니다. 쓰기가 많거나 쓰기 전용인 디스크의 경우(예: SQL Server 로그 파일) 더 나은 응용 프로그램 성능을 얻기 위해 디스크 캐싱을 사용하지 않도록 설정합니다.
- VM에서 디스크 트래픽을 제어하기에 충분한 대역폭을 사용할 수 있는지 확인합니다. 예를 들어, STANDARD_DS1 VM에는 프리미엄 저장소 디스크 트래픽에 사용할 수 있는 초당 32MB의 전용 대역폭이 있습니다. 즉, 이 VM에 연결된 P10 프리미엄 저장소 디스크는 초당 최대 32MB로만 이동할 수 있으며 P10 디스크가 제공할 수 있는 초당 최대 100MB는 이동할 수 없습니다. 마찬가지로, STANDARD_DS13 VM은 모든 디스크에서 초당 최대 256MB로 이동할 수 있습니다. 현재, DS 계열에서 가장 큰 VM은 STANDARD_DS14 이며 모든 디스크에서 초당 최대 512MB를 제공할 수 있습니다.

	이러한 한도는 캐시 적중 수 및 네트워크 트래픽을 포함하지 않는 디스크 트래픽에만 단독으로 적용됩니다. VM 네트워크 트래픽에 사용할 수 있는 별도 대역폭이 있으며, 프리미엄 저장소 디스크 전용 대역폭과는 다릅니다. 다음 표에서 VM에 연결된 모든 디스크에서 DS 시리즈 VM당 현재 최대 IOPS 및 처리량(대역폭) 값이 나열됩니다.

	<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>VM 크기</strong></td>
	<td><strong>CPU 코어</strong></td>
	<td><strong>최대 IOPS</strong></td>
	<td><strong>최대 디스크 대역폭</strong></td>
</tr>
<tr>
	<td><strong>STANDARD_DS1</strong></td>
	<td>1</td>
	<td>3,200</td>
	<td>초당 32MB</td>
</tr>
<tr>
	<td><strong>STANDARD_DS2</strong></td>
	<td>2</td>
	<td>6,400</td>
	<td>초당 64MB</td>
</tr>
<tr>
	<td><strong>STANDARD_DS3</strong></td>
	<td>4</td>
	<td>12,800</td>
	<td>초당 128MB</td>
</tr>
<tr>
	<td><strong>STANDARD_DS4</strong></td>
	<td>8</td>
	<td>25,600</td>
	<td>초당 256MB</td>
</tr>
<tr>
	<td><strong>STANDARD_DS11</strong></td>
	<td>2</td>
	<td>6,400</td>
	<td>초당 64MB</td>
</tr>
<tr>
	<td><strong>STANDARD_DS12</strong></td>
	<td>4</td>
	<td>12,800</td>
	<td>초당 128MB</td>
</tr>
<tr>
	<td><strong>STANDARD_DS13</strong></td>
	<td>8</td>
	<td>25,600</td>
	<td>초당 256MB</td>
</tr>
<tr>
	<td><strong>STANDARD_DS14</strong></td>
	<td>16</td>
	<td>50,000</td>
	<td>초당 512MB</td>
</tr>
</tbody>
</table>

	최신 정보는 [Azure를 위한 가상 컴퓨터 및 클라우드서비스 크기](http://msdn.microsoft.com/library/azure/dn197896.aspx) 를 참조하세요. 프리미엄 저장소 디스크와 해당 IOPS 및 처리량 한도에 대한 자세한 내용은 이 문서에서 [프리미엄 저장소 사용 시 확장성 및 성능 목표](#scalability-and-performance-targets-whko-kring-premium-storage) 섹션의 테이블을 참조하세요.

> [AZURE.NOTE]캐시 적중 수는 디스크의 할당된 IOPS/처리량으로 제한되지 않습니다. 즉, DS 시리즈 VM에서 읽기 전용 캐시 설정된 데이터 디스크를 사용하는 경우 캐시에서 제공 하는 읽기는 프리미엄 저장소 디스크의 대상이 아닙니다. 따라서 워크로드가 대부분 읽기인 경우 디스크에서 상당히 높은 처리량을 얻을 수 있습니다. 캐시는 VM 크기에 기반한 VM 단계에서 별도의 IOPS/처리량으로 제한됩니다. DS 시리즈 VM은 대략 캐시 및 로컬 SSD IO에 대해 코어당 4000 IOPS 및 33MB/초여야 합니다.

- 동일한 DS 시리즈 VM에 프리미엄 및 표준 저장소 디스크를 모두 사용할 수 있습니다.
- 프리미엄 저장소를 사용하여 DS 시리즈 VM을 프로비전할 수 있으며 여러 영구 데이터 디스크를 VM에 연결할 수 있습니다. 필요한 경우, 볼륨의 성능과 용량을 늘리도록 디스크에 걸쳐 스트라이핑 할 수 있습니다. [저장소 공간](http://technet.microsoft.com/library/hh831739.aspx)을 사용하여 프리미엄 저장소 데이터 디스크를 스트라이프하는 경우, 사용되는 각 디스크에 대해 하나의 열로 구성해야 합니다. 그렇지 않은 경우, 디스크에서의 고르지 못한 트래픽 분배로 스트라이프 볼륨의 전반적인 성능이 예상보다 저하될 수 있습니다. 기본적으로 서버 관리자 사용자 인터페이스(UI)를 사용하면 최대 8개의 디스크를 열로 설정할 수 있습니다. 하지만 디스크가 8개 이상인 경우, 볼륨을 만들고 열 수를 수동으로 지정하려면 PowerShell을 사용해야 합니다. 그렇지 않은 경우 서버 관리자 UI는 더 많은 디스크가 있더라도 8개의 열을 사용하여 계속합니다. 예를 들어 단일 스트라이프 세트에 32개의 디스크가 있다면 32개의 열을 지정해야 합니다. [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) PowerShell cmdlet의 *NumberOfColumns* 매개 변수를 사용하여 가상 디스크에서 사용되는 열 수를 지정할 수 있습니다. 자세한 내용은 [저장소 공간 개요](http://technet.microsoft.com/library/jj822938.aspx) 및 [저장소 공간 질문과 대답](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)을 참조하세요.
- DS 시리즈 VM을 비 DS 시리즈 VM을 포함하는 기존 클라우드 서비스에 추가하지 마십시오. 가능한 해결 방법은 DS 시리즈 VM만 실행 중인 새 클라우드 서비스로 기존 VHD를 마이그레이션하는 것입니다. DS 시리즈 VM을 호스트하는 새 클라우드 서비스에 동일한 VIP(가상 IP 주소)를 유지하려는 경우, [예약된 IP 주소](https://msdn.microsoft.com/library/azure/dn690120.aspx) 기능을 사용합니다.
- 표준 저장소 계정 또는 프리미엄 저장소 계정에서 호스팅된 운영 체제(OS) 디스크를 사용하도록 Azure 가상 컴퓨터의 DS 시리즈를 구성할 수 있습니다. 부팅에 대해서만 OS 디스크를 사용하는 경우에 OS 디스크 기반 표준 저장소 사용을 고려할 수 있습니다. 이렇게 하면 부팅 후 프리미엄 저장소와 유사한 비용 혜택 및 유사한 성능 결과가 제공됩니다. 부팅이 아닌 OS 디스크에서 추가 작업을 수행하는 경우 더 나은 성능 결과를 제공하므로 프리미엄 저장소를 사용합니다. 예를 들어 응용프로그램이 OS 디스크를 읽고/쓰는 경우 OS 디스크 기반 프리미엄 저장소를 사용하면 VM에 더 나은 성능을 제공합니다.
- 프리미엄 저장소와 함께 [Azure CLI(Azure 명령줄 인터페이스)](../xplat-cli.md)를 사용할 수 있습니다. Azure CLI를 사용하여 디스크 중 하나에서 캐시 정책을 변경하려면 다음 명령을 실행합니다.

	`$ azure vm disk attach -h ReadOnly <VM-Name> <Disk-Name>`

	캐싱 정책 옵션은 읽기 전용, 없음 또는 읽기/쓰기입니다. 더 많은 옵션은 다음 명령을 실행하여 도움말을 참조하세요.

	`azure vm disk attach --help`


## 프리미엄 저장소 사용 시 확장성 및 성능 목표

프리미엄 저장소 계정에 대해 디스크를 프로비전할 때 사용할 수 있는 IOPS(초당 입/출력 작업 수) 및 처리량(대역폭)은 디스크 크기에 따라 달라집니다. 현재 프리미엄 저장소 디스크에는 P10, P20 및 P30의 세 가지 유형이 있습니다. 각 디스크에는 다음 표에 지정된 대로 특정 IOPS 및 처리량 한도가 있습니다.

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
	<td>128GiB</td>
	<td>512GiB</td>
	<td>1024GiB(1TB)</td>
</tr>
<tr>
	<td><strong>디스크당 IOPS</strong></td>
	<td>500</td>
	<td>2300</td>
	<td>5000</td>
</tr>
<tr>
	<td><strong>디스크당 처리량</strong></td>
	<td>초당 100MB*</td>
	<td>초당 150MB*</td>
	<td>초당 200MB*</td>
</tr>
</tbody>
</table>

> [AZURE.NOTE]앞서 이 문서의 [디스크용 프리미엄 저장소 사용](#using-premium-storage-for-disks) 섹션에서 설명한 대로 디스크 트래픽을 운용하기 위해 VM에서 사용할 수 있는 충분한 대역폭이 있는지 확인합니다. 충분하지 않은 경우, 디스크 처리량 및 IOPS가 이전 표에서 언급된 디스크 한도보다는 VM 한도에 기반한 값보다 더 낮게 제한됩니다.

Azure는 표에 지정된 대로 디스크 크기(반올림됨)를 가장 가까운 프리미엄 저장소 디스크 옵션에 매핑합니다. 예를 들어 크기가 100GiB인 디스크는 P10 옵션으로 분류되며 초당 최대 500개 IO 단위를 초당 최대 100MB 처리량으로 수행할 수 있습니다. 마찬가지로, 크기가 400GiB인 디스크는 P20 옵션으로 분류되며 초당 최대 2300개 IO 단위를 초당 최대 150MB 처리량으로 수행할 수 있습니다.

I/O(입/출력) 단위 크기는 256KB입니다. 전송되는 데이터가 256KB 미만일 경우 하나의 I/O 단위로 간주됩니다. 더 큰 I/O 크기는 256KB 크기의 여러 I/O로 계산됩니다. 예를 들어 1100KB I/O는 I/O 단위 5개로 계산됩니다.

처리량 한도에는 디스크에 쓰기뿐 아니라 캐시에서 제공되지 않은 디스크에서 읽기도 포함됩니다. 예를 들어, P10 디스크의 경우 캐시가 아닌 읽기 및 쓰기의 합계가 100MB보다 작거나 같아야 합니다. 마찬가지로, 하나의 P10 디스크는 초당 100MB의 캐시가 아닌 읽기 또는 초당 100MB 쓰기이거나 초당 60MB 읽기와 초당 40MB 쓰기 등일 수 있습니다.

Azure에서 디스크를 생성하는 경우 용량, 성능, 확장성 및 최대 부하 측면에서 응용 프로그램의 요구에 가장 적절한 프리미엄 저장소 디스크 제품을 선택합니다.

> [AZURE.NOTE]기존 디스크의 크기를 쉽게 확대할 수 있습니다. 예를 들어, 30GB 디스크의 크기 128GB 또는 1TB로 확대하려는 경우입니다. 또는 더 많은 용량 또는 더 많은 IOPS 및 처리량이 필요하기 때문에 P20 디스크에서 P30 디스크로 변환하려는 경우입니다. “-ResizedSizeInGB” 속성과 함께 “Update-AzureDisk” PowerShell commandlet를 사용하여 디스크를 확대할 수 있습니다. 이 작업 수행을 위해 디스크를 VM에서 분리해야 하거나 VM을 중단해야 합니다.

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
       <li type=round>디스크 용량: 35TB</li>
       <li type=round>스냅숏 용량: 10TB</li>
    </ul>
	</td>
	<td>인바운드+아웃바운드에 대해 초당 최대 50기가비트</td>
</tr>
</tbody>
</table>

- 인바운드는 저장소 계정으로 전송되는 모든 데이터(요청)를 가리킵니다.
- 아웃바운드는 저장소 계정에서 수신되는 모든 데이터(응답)를 가리킵니다.

자세한 내용은 [Azure 저장소 확장성 및 성능 목표](http://msdn.microsoft.com/library/azure/dn249410.aspx)를 참조하세요.

## 프리미엄 저장소 사용 시 제한
응용프로그램의 IOPS 또는 처리량이 프리미엄 저장소 디스크에 대한 할당된 한도를 초과하는 경우 또는 VM에서 모든 디스크에 전체 디스크 트래픽이 해당 VM에 사용할 수 있는 디스크 대역폭 한도를 초과하는 경우 제한이 보일 수 있습니다. 제한을 방지하려면 프로비전한 디스크의 확장성 및 성능 목표에 따라 및 VM에 사용할 수 있는 디스크 대역폭에 따라 디스크에 대해 보류 중인 I/O 요청 수를 제한하는 것이 좋습니다.

응용 프로그램이 제한을 방지하도록 설계된 경우 가장 낮은 대기 시간을 얻을 수 있습니다. 반면, 디스크에 대해 보류 중인 I/O 요청 수가 너무 작으면 응용 프로그램이 디스크에서 사용할 수 있는 최대 IOPS 및 처리량 수준을 활용할 수 없습니다.

다음 예제에서는 제한 수준을 계산하는 방법을 보여 줍니다.

### 예제 1:
응용 프로그램이 P10 디스크에서 초당 16KB 디스크 크기의 495개 I/O를 수행했습니다(495개 I/O 단위와 같음). 동일한 초에 2MB I/O를 시도하면 총 I/O 단위 수는 495+8이 됩니다. 이는 I/O 단위 크기가 256KB일 경우 2MB I/O에서 2048KB/256KB = 8개 I/O 단위가 나오기 때문입니다. 495+8의 합계가 디스크 한도인 500개 IOPS를 초과하므로 제한이 발생합니다.

### 참고:
모든 계산은 256KB의 I/O 단위 크기를 기반으로 합니다.

### 예 2:
응용 프로그램이 P10 디스크에서 256KB 디스크 크기의 400개 I/O를 수행했습니다. 소비되는 총 대역폭은 (400*256)/1024=100MB/초입니다. 초당 100MB는 P10 디스크의 처리량 한도입니다. 응용 프로그램이 해당 초에 추가 I/O를 수행하려고 하면 할당된 한도를 초과하므로 제한됩니다.

### 예 3:
연결된 두 P30 디스크와 DS4 VM이 있습니다. 각 P30 디스크는 처리량이 초당 200MB입니다. 하지만 DS4 VM은 초당 256MB의 총 디스크 대역폭 용량입니다. 따라서 동시에 이 DS4 VM에서 최대 처리량으로 연결된 디스크를 운용할 수 없습니다. 이를 해결하려면 한 디스크에서는 초당 200MB, 다른 디스크에서는 초당 56MB의 트래픽을 유지할 수 있습니다. 디스크 트래픽의 합계는 초당 256MB 이상으로 이동하며 해당 트래픽이 제한됩니다.

### 참고:
디스크 트래픽이 대체로 작은 I/O 크기로 구성된 경우 응용 프로그램이 처리량 한도 전에 IOPS 한도에 도달할 가능성이 큽니다. 반면, 디스크 트래픽이 대체로 큰 I/O 크기로 구성된 경우에는 응용 프로그램이 IOPS 한도 대신 처리량 한도에 도달할 가능성이 큽니다. 최적 I/O 크기를 사용하고 디스크에 대해 보류 중인 I/O 요청 수를 제한하여 응용 프로그램의 IOPS 및 처리량을 최대화할 수 있습니다.

## 프리미엄 저장소 사용 시 Blob 스냅숏 생성 및 복사
표준 저장소 사용 시 스냅숏을 만드는 것과 동일한 방식으로 프리미엄 저장소에 대한 스냅숏을 만들 수 있습니다. 프리미엄 저장소는 로컬에서 중복되므로 스냅숏을 만든 후 지역 중복 표준 저장소 계정에 이러한 스냅숏을 복사하는 것이 좋습니다. 자세한 내용은 [Azure 저장소 중복 옵션](http://msdn.microsoft.com/library/azure/dn727290.aspx)을 참조하세요.

디스크가 VM에 연결되어 있을 경우 디스크를 지원하는 페이지 Blob에 대해 특정 API 작업을 수행할 수 없습니다. 예를 들어 디스크가 VM에 연결되어 있는 한 해당 Blob에 대해 [Blob 복사](http://msdn.microsoft.com/library/azure/dd894037.aspx) 작업을 수행할 수 없습니다. 대신, 먼저 [Blob 스냅숏 생성](http://msdn.microsoft.com/library/azure/ee691971.aspx) REST API 메서드를 사용하여 해당 Blob의 스냅숏을 만든 후 스냅숏의 [Blob 복사](http://msdn.microsoft.com/library/azure/dd894037.aspx)를 수행하여 연결된 디스크를 복사합니다. 또는 디스크를 분리한 후 기본 Blob에 대해 필요한 작업을 수행할 수 있습니다.

### 중요:

- 프리미엄 저장소의 Blob 복사 작업이 대상의 기존 Blob을 덮어쓰는 경우 덮어쓸 Blob에 스냅숏이 없어야 합니다. 프리미엄 저장소 계정 간이나 내에서 복사하는 경우 복사를 시작할 때 대상 Blob에 스냅숏이 없어야 합니다.
- 단일 Blob의 스냅숏 수는 100개로 제한됩니다. 최대 10분마다 스냅숏을 생성할 수 있습니다.
- 10TB는 프리미엄 저장소 계정당 최대 스냅숏 용량입니다. 스냅숏 용량은 스냅숏에 있는 고유 데이터입니다. 즉, 스냅숏 범주에 기본 Blob 크기가 포함되지 않습니다.
- 프리미엄 저장소 계정에서 다른 계정에 스냅숏을 복사하려면 먼저 동일한 프리미엄 저장소 계정에서 새 Blob을 만들도록 스냅숏의 CopyBlob을 수행해야 합니다. 그런 다음 다른 저장소 계정에 새 Blob을 복사할 수 있습니다. 복사가 완료 된 후 중간 Blob을 삭제할 수 있습니다. 이 과정을 따라 AzCopy 또는 Blob 복사를 사용하여 프리미엄 저장소 계정에서 지역 중복 표준 저장소 계정으로 스냅숏을 복사할 수 있습니다. 자세한 내용은 [Microsoft Azure 저장소와 함께 AzCopy를 사용하는 방법](storage-use-azcopy.md) 및 [Blob 복사](http://msdn.microsoft.com/library/azure/dd894037.aspx)를 참조하세요.
- 프리미엄 저장소 계정에서 페이지 Blob에 대한 REST 작업을 수행하는 방법에 대한 자세한 내용은 MSDN 라이브러리에서 [Azure 프리미엄 저장소와 함께 Blob 서비스 작업 사용](http://go.microsoft.com/fwlink/?LinkId=521969)을 참조하세요.

## 프리미엄 저장소와 Linux VM 사용
프리미엄 저장소에서 Linux VM을 구성하기 위한 중요 지침은 아래를 참조하십시오.

- “ReadOnly(읽기 전용)” 또는 “None(없음)”으로 캐시 설정을 한 모든 프리미엄 저장소 디스크의 경우, 프리미엄 저장소에 대한 확장성 목표를 수행하기 위해 파일 시스템을 탑재하는 동안 “barrier(장벽)”을 사용하지 않도록 설정해야 합니다. 프리미엄 저장소 백업 디스크에 쓰기는 이러한 캐시 설정에 대해 내구성이 있기 때문에 이 시나리오에 대한 장벽이 필요하지 않습니다. 쓰기 요청이 성공적으로 완료되면 데이터는 영구 저장소에 작성됩니다. 파일 시스템에 따라 “barrier(장벽)”를 사용하지 않도록 설정하는 방법은 다음 메서드를 사용하십시오.
	- **reiserFS**를 사용하는 경우 탑재 옵션 “barrier=none”을 사용하여 장벽을 사용하지 않도록 설정(장벽 사용의 경우 “barrier=flush” 사용)
	- **ext3/ext4**를 사용하는 경우 탑재 옵션 “barrier=0”을 사용하여 장벽을 사용하지 않도록 설정(장벽 사용의 경우 “barrier=1” 사용)
	- **XFS**를 사용하는 경우 탑재 옵션 “nobarrier”를 사용하여 장벽을 사용하지 않도록 설정(장벽 사용의 경우 “barrier” 사용)

- “ReadWrite”으로 캐시가 설정된 프리미엄 저장소 디스크의 경우 쓰기 내구성을 위해 장벽이 설정되어야 합니다.

다음은 프리미엄 저장소로 유효성을 검사한 Linux 배포판입니다. 프리미엄 저장소 사용 시 더 나은 성능 및 확장성을 위해 이러한 버전 이상으로 VM을 업그레이드하는 것이 좋습니다. 또한 버전 중 일부는 최신 LIS(Microsoft Azure 용 Linux Integration Services v4.0)가 필요합니다. 다운로드 및 설치를 위해 아래 제공된 링크를 따르십시오. 추가 유효성 검사가 완료되면 목록에 대한 자세한 이미지 추가가 계속됩니다. 유효성 검사는 이러한 이미지에 따라 다른 성능을 보여주었으며 해당 이미지의 워크로드 특성 및 설정에 따라서도 달라집니다. 다른 종류의 워크로드에 대해 서로 다른 이미지가 조정됩니다. 
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;"> <tbody> <tr> <td><strong>배포</strong></td> <td><strong>버전</strong></td> <td><strong>지원 커널</strong></td> <td><strong>지원 이미지</strong></td> </tr> <tr> <td rowspan="4"><strong>Ubuntu</strong></td> <td>12.04</td> <td>3.2.0-75.110</td> <td>Ubuntu-12_04_5-LTS-amd64-server-20150119-ko-kr-30GB</td> </tr> <tr> <td>14.04</td> <td>3.13.0-44.73</td> <td>Ubuntu-14_04_1-LTS-amd64-server-20150123-ko-kr-30GB</td> </tr> <tr> <td>14.10</td> <td>3.16.0-29.39</td> <td>Ubuntu-14_10-amd64-server-20150202-ko-kr-30GB</td> </tr> <tr> <td>15.04</td> <td>3.19.0-15</td> <td>Ubuntu-15_04-amd64-server-20150422-ko-kr-30GB</td> </tr> <tr> <td><strong>SUSE</strong></td> <td>SLES 12</td> <td>3.12.36-38.1</td> <td>suse-sles-12-priority-v20150213<br>suse-sles-12-v20150213</td> </tr> <tr> <td><strong>CoreOS</strong></td> <td>584.0.0</td> <td>3.18.4</td> <td>CoreOS 584.0.0</td> </tr> <tr> <td rowspan="2"><strong>CentOS</strong></td> <td>6.5, 6.6, 7.0</td> <td></td> <td><a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 필수 </a></td> </tr> <tr> <td>7.1</td> <td>3.10.0-229.1.2.el7</td> <td><a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 권장 </a></td> </tr>

<tr>
	<td rowspan="2"><strong>Oracle</strong></td>
	<td>6.4</td>
	<td></td>
	<td><a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 필수 </a></td>
</tr>
<tr>
	<td>7.0</td>
	<td></td>
	<td>세부 정보는 지원에 문의</td>
</tr>
</tbody> </table>


## 프리미엄 저장소 사용 시 가격 책정 및 청구
프리미엄 저장소를 사용하는 경우 다음과 같은 청구 고려 사항이 적용됩니다.

- 프리미엄 저장소 디스크에 대한 청구는 프로비전된 디스크 크기에 따라 달라집니다. Azure는 [프리미엄 저장소 사용 시 확장성 및 성능 목표](#scalability-and-performance-targets-whko-kring-premium-storage) 섹션에 지정된 표에 따라 디스크 크기(반올림됨)를 가장 가까운 프리미엄 저장소 디스크 옵션에 매핑합니다 프로비전된 디스크에 대한 청구는 프리미엄 저장소 제품의 월별 가격을 사용하여 시간당 비례합니다. 예를 들어 P10 디스크를 프로비전하고 20시간 후 삭제한 경우 20시간에 비례하여 P10 제품에 대해 청구됩니다. 이는 디스크에 기록되는 실제 데이터 양이나 사용한 IOPS/처리량에 관계없이 적용됩니다.
- 프리미엄 저장소의 스냅숏은 스냅숏이 사용한 추가 용량에 대해 청구됩니다. 스냅숏에 대한 자세한 내용은 [Blob의 스냅숏 만들기](http://msdn.microsoft.com/library/azure/hh488361.aspx)를 참조하세요.
- [아웃바운드 데이터 전송](http://azure.microsoft.com/pricing/details/data-transfers/)(Azure 데이터 센터에서 데이터 전송) 시 대역폭 사용에 대해 청구가 발생합니다.

프리미엄 저장소와 DS 시리즈 VM에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 저장소 가격](http://azure.microsoft.com/pricing/details/storage/)
- [가상 컴퓨터 가격](http://azure.microsoft.com/pricing/details/virtual-machines/)

## 가상 컴퓨터 데이터 디스크에 대한 프리미엄 저장소 계정 만들기 및 사용

이 섹션에서는 Azure Preview 포털, Azure PowerShell 및 Azure CLI(Azure 명령줄 인터페이스)를 사용하여 프리미엄 저장소 계정을 만드는 방법을 보여줍니다. 또한 프리미엄 저장소 계정의 샘플 사용 사례를 보여 줍니다. 이 경우 가상 컴퓨터를 만든 후 프리미엄 저장소를 사용할 때 데이터 디스크를 가상 컴퓨터에 연결합니다.

### Azure Preview 포털을 통해 프리미엄 저장소를 사용하여 Azure 가상 컴퓨터 만들기

이 섹션에서이 섹션에서는 Azure Preview 포털을 사용하여 프리미엄 저장소 계정을 만드는 방법을 보여 줍니다.

1.	[Azure 미리 보기 포털](https://portal.azure.com/)에 로그인합니다. 아직 구독이 없으면 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/) 서비스를 확인하세요.


    > [AZURE.NOTE]Azure 관리 포털에 로그인한 경우 포털의 오른쪽 위에 있는 사용자 계정 이름을 클릭합니다. 그런 다음 **새 포털로 전환**을 클릭합니다.


2.	허브 메뉴에서 **새로 만들기**를 클릭합니다.

3.	**새로 만들기**에서 **모두**를 클릭합니다. **저장소, 캐시, +백업**을 선택합니다. 여기서 **저장소**를 클릭한 후 **만들기**를 클릭합니다.

4.	저장소 계정 블레이드에서 저장소 계정의 이름을 입력합니다. **가격 책정 계층**을 클릭합니다. **권장 가격 책정 계층** 블레이드에서 **모든 가격 책정 계층 찾아보기**를 클릭합니다. **가격 책정 계층 선택** 블레이드에서 **프리미엄 로컬 중복**을 선택합니다. **선택**을 클릭합니다. **저장소 계정** 블레이드에 기본적으로 **표준-GRS**가 **가격 책정 계층**으로 표시됩니다. **선택**을 클릭하면 **가격 책정 계층**이 **프리미엄-LRS**로 표시됩니다.

	![가격 책정 계층][Image1]


5.	**저장소 계정** 블레이드에서 **리소스 그룹**, **구독**, **위치** 및 **진단**의 기본값을 유지합니다. **만들기**를 클릭합니다.

Azure 환경 내의 전체 연습은 [Azure Preview 포털에서 Windows를 실행하는 가상 컴퓨터 만들기](../virtual-machines-windows-tutorial-azure-preview.md)를 참조하세요.

### Azure PowerShell을 통해 프리미엄 저장소를 사용하여 Azure 가상 컴퓨터 만들기
이 PowerShell 예제에서는 새 Premium 저장소 계정을 만들고 새 Azure 가상 컴퓨터에 해당 계정을 사용하는 데이터 디스크를 연결하는 방법을 보여줍니다.

1. [Azure PowerShell을 설치 및 구성하는 방법](../install-configure-powershell.md)에 제공된 단계에 따라 PowerShell 환경을 설정합니다.
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

### Azure 명령줄 인터페이스를 통해 프리미엄 저장소를 사용하여 Azure 가상 컴퓨터 만들기

[Azure CLI(Azure 명령줄 인터페이스)](../xplat-cli.md)는 Azure 플랫폼 작업을 위한 플랫폼 간 오픈 소스 명령 집합을 제공합니다. 다음 예제에서는 Azure CLI(버전 0.8.14 이상)를 사용하여 프리미엄 저장소 계정, 새 가상 컴퓨터를 만들고 프리미엄 저장소 계정에서 새 데이터 디스크를 연결하는 방법을 설명합니다.

#### 프리미엄 저장소 계정 만들기

````
azure storage account create "premiumtestaccount" -l "west us" --type PLRS
````

#### DS 시리즈 가상 컴퓨터 만들기

	azure vm create -z "Standard_DS2" -l "west us" -e 22 "premium-test-vm"
		"b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ko-kr-30GB" -u "myusername" -p "passwd@123"

#### 가상 컴퓨터에 대한 정보 표시

	azure vm show premium-test-vm

#### 새 데이터 디스크 연결

	azure vm disk attach-new premium-test-vm 20 https://premiumstorageaccount.blob.core.windows.net/vhd-store/data1.vhd

#### 새 데이터 디스크에 대한 정보 표시

	azure vm disk show premium-test-vm-premium-test-vm-0-201502210429470316

## 다음 단계

[Azure 프리미엄 저장소와 함께 Blob 서비스 작업 사용](http://go.microsoft.com/fwlink/?LinkId=521969)

[Windows를 실행하는 가상 컴퓨터 만들기](../virtual-machines-windows-tutorial-azure-preview.md)

[Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기](http://msdn.microsoft.com/library/azure/dn197896.aspx)

[저장소 설명서](http://azure.microsoft.com/documentation/services/storage/)

[MSDN 참조](http://msdn.microsoft.com/library/azure/gg433040.aspx)

[Image1]: ./media/storage-premium-storage-preview-portal/Azure_pricing_tier.png
 

<!---HONumber=58_postMigration-->