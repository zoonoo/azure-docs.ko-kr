<properties 
    pageTitle="Azure 프리미엄 저장소로 마이그레이션 | Microsoft Azure" 
    description="Azure 가상 컴퓨터에서 실행되는 I/O 사용량이 많은 작업을 지원하는 짧은 대기 시간의 고성능 디스크를 위해 Azure 프리미엄 저장소로 마이그레이션합니다." 
    services="storage" 
    documentationCenter="na" 
    authors="tamram" 
    manager="adinah" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/14/2015" 
    ms.author="tamram"/>


# Azure 프리미엄 저장소로 마이그레이션

## 개요

프리미엄 저장소는 최신 기술 SSD(Solid State Drives)에 데이터를 저장하며, Azure 가상 컴퓨터에서 실행되는 I/O 사용량이 많은 작업을 지원하는 짧은 대기 시간의 고성능 디스크를 제공합니다. 프리미엄 저장소를 사용할 경우 응용 프로그램이 VM당 최대 32TB의 저장소를 사용할 수 있으며, 읽기 작업의 대기 시간이 매우 짧은 상태로 VM당 50,000 IOPS(초당 입/출력 작업 수)를 얻을 수 있습니다. 이 문서에서는 디스크, 가상 컴퓨터 (VM)를 온-프레미스 또는 표준 저장소 또는 다른 클라우드 플랫폼에서 Azure 프리미엄 저장소로 마이그레이션하는 방법에 대한 지침을 제공합니다. 프리미엄 저장소를 제공하는 Azure 프리미엄 저장소의 자세한 개요를 보려면 [Azure 가상 컴퓨터 작업에 대 한 고성능 저장소](storage-premium-storage-preview-portal.md)를 확인하세요.

## 시작하기 전에 

### 필수 조건
- Azure 구독이 필요합니다. 구독할 수 없다면, 한 달의 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/)을 구독하거나 [Azure 가격 책정](http://azure.microsoft.com/pricing/)을 방문하여 추가 옵션을 참고합니다. 
- PowerShell cmdlet을 실행하려면 Microsoft Azure PowerShell 모듈이 필요합니다. 모듈을 다운로드하려면 [Microsoft Azure 다운로드](http://azure.microsoft.com/downloads/)(영문)를 참조하세요.
- 프리미엄 저장소에서 실행되는 Azure VM을 사용하려는 경우 DS 시리즈 VM을 사용해야 합니다. DS 시리즈 VM에는 표준 및 프리미엄 저장소 디스크를 모두 사용할 수 있습니다. 프리미엄 저장소 디스크를 나중에 더 많은 VM 형식으로 사용할 수 있습니다. 사용 가능한 Azure VM 디스크 유형 및 크기에 대한 자세한 내용은 [Azure용 가상 컴퓨터 및 클라우드 서비스 크기](http://msdn.microsoft.com/library/azure/dn197896.aspx)를 참조하세요. 

### 고려 사항 

#### VM 크기 
DS 시리즈 VM 크기 및 특징은 아래에 요약합니다. 이러한 제공 프리미엄 저장소의 성능 특징을 검토하고 작업에 가장 적합한 Azure 디스크 및 VM에 대해 가장 적절한 옵션을 선택합니다. VM에서 디스크 트래픽을 제어하기에 충분한 대역폭을 사용할 수 있는지 확인합니다.

|VM 크기|CPU 코어|최대 IOPS|최대 디스크 대역폭|
|:---:|:---:|:---:|:---:|
|**STANDARD_DS1**|1|3,200|초당 32MB|
|**STANDARD_DS2**|2|6,400|초당 64MB|
|**STANDARD_DS3**|4|12,800|초당 128MB|
|**STANDARD_DS4**|8|25,600|초당 256MB|
|**STANDARD_DS11**|2|6,400|초당 64MB|
|**STANDARD_DS12**|4|12,800|초당 128MB|
|**STANDARD_DS13**|8|25,600|초당 256MB|
|**STANDARD_DS14**|16|50,000|초당 512MB|

#### 디스크 크기 
VM에서 사용할 수 있는 디스크에는 세 종류가 있으며 각 종류에는 특정 IOP가 있으며 제한됩니다. 용량, 성능, 확장성 및 최대 로드 측면에서 응용 프로그램의 필요에 따라 VM에 대한 디스크 유형을 선택할 때 이 제한을 고려해야 합니다.

|프리미엄 저장소 디스크 유형|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|디스크 크기|128GB|512GB|1024GB(1TB)|
|디스크당 IOPS|500|2300|5000|
|디스크당 처리량|초당 100MB|초당 150MB|초당 200MB|

#### 저장소 계정의 확장성 목표

프리미엄 저장소 계정에는 [Azure 저장소 확장성 및 성능 목표](http://msdn.microsoft.com/library/azure/dn249410.aspx) 이외에 다음 확장성 목표가 있습니다.  응용 프로그램의 요구가 단일 저장소 계정의 확장성 목표를 초과하는 경우, 여러 저장소 계정을 사용하도록 응용 프로그램을 빌드하고 데이터를 이러한 저장소 계정에 분할합니다.

|총 계정 용량|로컬 중복 저장소 계정의 총 대역폭|
|:--|:---|
|디스크 용량: 35TB<br />스냅숏 용량: 10TB|인바운드+아웃바운드에 대해 초당 최대 50기가비트|

프리미엄 저장소 사양에 대한 자세한 내용은 [프리미엄 저장소를 사용하는 경우 확장성 및 성능 목표 ](storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whko-kring-premium-storage)를 참조하세요.

#### 추가 데이터 디스크 
사용자 작업에 따라 추가 데이터 디스크가 VM에 필요한 경우를 결정합니다. VM에 여러 영구 데이터 디스크를 연결할 수 있습니다. 필요한 경우, 볼륨의 성능과 용량을 늘리도록 디스크에 걸쳐 스트라이핑 할 수 있습니다. [저장소 공간](http://technet.microsoft.com/library/hh831739.aspx)을 사용하여 프리미엄 저장소 데이터 디스크를 스트라이프하는 경우, 사용되는 각 디스크에 대해 하나의 열로 구성해야 합니다. 그렇지 않은 경우, 디스크에서의 고르지 못한 트래픽 분배로 스트라이프 볼륨의 전반적인 성능이 예상보다 저하될 수 있습니다. Linux VM의 경우, mdadm 유틸리티를 사용하여 동일한 작업을 수행할 수 있습니다. 자세한 내용은 [Linux에서 소프트웨어 RAID 구성](../virtual-machines-linux-configure-raid.md) 문서를 참조하세요.

#### 디스크 캐싱 정책 
기본적으로 디스크 캐싱 정책은 VM에 연결된 프리미엄 운영 체제 디스크에 대한 "읽기 / 쓰기" 및 모든 프리미엄 데이터 디스크에 대한 "읽기 전용"입니다. 응용 프로그램의 IO에 대한 최적의 성능을 얻으려면 이 구성 설정이 좋습니다. 쓰기가 많거나 쓰기 전용인 디스크의 경우(예: SQL Server 로그 파일) 더 나은 응용 프로그램 성능을 얻기 위해 디스크 캐싱을 사용하지 않도록 설정합니다. Azure 포털 또는 *Set-AzureDataDisk* cmdlet의 *-HostCaching* 매개 변수를 사용하여 기존 데이터 디스크에 대한 캐시 설정을 업데이트할 수 있습니다. 

#### 위치 
Azure 프리미엄 저장소를 사용할 수 있는 위치를 선택합니다. 사용 가능한 위치에 대한 최신 정보는 [프리미엄 저장소에 대해 알아야할 중요한 사항](storage-premium-storage-preview-portal.md#important-things-to-know-about-premium-storage)을 참조하세요. VM에 대한 디스크를 저장하는 저장소 계정과 동일한 지역에 있는 VM은 별도 영역에 있는 경우보다 뛰어난 성능을 제공합니다.

#### 기타 Azure VM 구성 설정 

Azure VM을 만들 때 특정 VM 설정을 구성해야 합니다. 나중에 다른 설정을 수정하거나 추가할 수 있지만 VM의 수명 동안 수정된 몇가지 설정이 있습니다. 이러한 Azure VM 구성 설정을 검토 하고 작업 부하 요구 사항과 일치하도록 적절하게 구성되었는지 확인해야 합니다. 자세한 내용은 [VM 구성 설정](https://msdn.microsoft.com/library/azure/dn763935.aspx)을 참조하세요.

## 마이그레이션을 위해 VHD를 준비합니다. 

다음 섹션에서 마이그레이션할 준비가 되도록 VM에서 VHD를 준비하는 지침을 제공합니다. VHD는 다음 중 하나일 수 있습니다.
 
- 여러 Azure VM을 만드는 데 사용될 수 있는 일반화된 운영 체제 이미지.  
- 단일 Azure 가상 컴퓨터 인스턴스를 사용할 수 있는 운영 체제 디스크.  
- 영구 저장을 위해 Azure VM에 연결할 수 있는 데이터 디스크.

### 필수 조건

- Azure 구독, 저장소 계정 및 저장소 계정이 VHD를 복사하려는 컨테이너입니다. 요구 사항에 따라 대상 저장소 계정은 표준 또는 프리미엄 저장소 계정일 수 있습니다. 
- 여러 VM 인스턴스를 만들 계획인 경우 VHD를 일반화하는 도구입니다. 예를 들어 Ubuntu용 virt-sysprep 또는 Windows용 sysprep입니다. VHD 파일을 저장소 계정에 업로드하는 도구입니다. 예를들어, [AzCopy](storage-use-azcopy.md) 또는 [Azure 저장소 탐색기](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)입니다. 이 가이드는 AzCopy 도구를 사용하 여 단계를 설명합니다. 대역폭이 제한된 많은 양의 데이터의 경우, [Microsoft Azure 가져오기/내보내기 서비스](storage-import-export-service.md)를 사용하여 하드 디스크 드라이브를 Azure 데이터 센터에 전달하고 사용자 데이터를 전송하는 방법을 고려할 수 있습니다. 가져오기/내보내기 서비스를 사용하여 표준 저장소 계정에만 데이터를 복사할 수 있습니다. 데이터가 표준 저장소 계정에 있는 경우, [복사 blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) 또는 AzCopy를 사용하여 프리미엄 저장소 계정에 데이터를 전송합니다.
- VHD를 복사하는 도구를 VHD와 동일한 지역의 Azure VM에서 실행할 수 있습니다.
- Microsoft Azure는 고정된 크기의 VHD 파일만을 지원합니다. 동적 VHD 또는 VHDX 파일은 지원되지 않습니다. 동적 VHD를 설정한 경우 [CONVERT-VHD](http://technet.microsoft.com/library/hh848454.aspx) cmdlet을 사용하여 고정된 크기를 변환할 수 있습니다.

### VHD 준비

|시나리오|단계|
|:---|:---|
|여러 VM 인스턴스를 만드는 일반화된 운영 체제 VHD|**여러 일반 Azure VM 인스턴스를 만드는데 사용할 VHD**를 <p>업로드하는 경우 , sysprep 유틸리티를 사용하여 VHD를 먼저 일반화해야 합니다. 온-프레미스 또는 클라우드에 있는 VHD에 적용됩니다. Sysprep는 VHD에서 모든 컴퓨터의 특정 정보를 제거합니다.</p><p>**중요:** 일반화하기 전에 VM의 스냅숏을 만들거나 백업합니다. Sysprep를 실행하면 VM 인스턴스를 삭제합니다.</p> Windows OS VHD를 sysprep하는 다음 단계를 수행합니다. <br />Sysprep 명령을 실행하려면 가상 컴퓨터를 종료해야 합니다. Sysprep에 대한 자세한 내용은 [Sysprep 개요](http://technet.microsoft.com/library/hh825209.aspx) 또는 [Sysprep 기술 참조](http://technet.microsoft.com/library/cc766049(v=ws.10).aspx)의 내용을 참조하세요. <ul><li>관리자로 명령 프롬프트 창을 엽니다.</li><li>Sysprep를 열려면 다음 명령을 입력합니다.<br />**%windir%\\system32\\sysprep\\sysprep.exe**</li><li>시스템 준비 도구에서 시스템 OOBE(첫 실행)를 선택하고, 일반화 확인란을 선택하고, **종료**를 선택한 다음 **확인**을 클릭합니다.</li><li>그러면 운영 체제를 일반화하고 시스템을 종료합니다.![][1]</li></ul>Ubuntu VM은 virt-sysprep를 사용하여 동일한 작업을 수행합니다. 자세한 내용은 [virt-sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html)를 참조하세요. 다른 Linux 운영 체제는 공개 소스 [Linux 서버 프로비전 소프트웨어](http://www.cyberciti.biz/tips/server-provisioning-software.html)의 일부도 참조하세요.|
|단일 VM 인스턴스를 만드는 고유 운영 체제 VHD|컴퓨터 특정 데이터를 필요로 하는 VM에서 실행 중인 응용 프로그램이 있는 경우 VHD를 일반화하지 않습니다. **일반화되지 않은 VHD는 고유한 Azure VM 인스턴스를 만드는 데 사용될 수 있습니다.** 예를들어 VHD에 도메인 컨트롤러가 있는 경우, sysprep를 실행하면 도메인 컨트롤러가 비효율적이게 됩니다. VHD를 일반화하기 전에 sysprep에 미치는 영향을 확인하고 VM에서 실행 중인 응용 프로그램을 검토합니다.|
|VM 인스턴스에 연결될 데이터 디스크 VHD|데이터 디스크가 마이그레이션할 클라우드 저장소에 있는 경우, 이 데이터 디스크를 사용하는 VM이 종료되었는지 확인해야 합니다. 온-프레미스에 있는 데이터 디스크의 경우, 일관된 VHD를 만듭니다.|
이제 VHD 준비되었으므로 다음 섹션에 설명된 단계에 따라 Azure 저장소에 VHD를 업로드하고 운영 체제 이미지, 프로비전된 운영 체제 디스크 또는 프로비전된 데이터 디스크로 등록합니다.

## Azure 저장소에 VHD 복사

### 소스

|시나리오|단계|
|:---|:---|
|Azure 저장소에서 VHD 복사|표준 Azure 저장소 계정에서 프리미엄 Azure 저장소 계정으로 VHD를 마이그레이션하는 경우, VHD 컨테이너의 원본 경로, VHD 파일 이름 및 원본 저장소 계정의 저장소 계정 키를 복사해야 합니다.<ul><li>Azure 포털 > 가상 컴퓨터 > 디스크로 이동하고</li><li>위치 열에서 VHD의 컨테이너 URL을 저장합니다.https://*AccountName*.blob.core.windows.net/*ContainerName*/</li></ul>|
|비-Azure 클라우드에서 VHD 복사|비-Azure 클라우드 저장소에서 Azure로 VHD를 마이그레이션하는 경우, 먼저 VHD를 로컬 디렉터리로 내보내야 합니다. VHD가 저장된 로컬 디렉터리의 전체 소스 경로를 복사합니다. <ul><li>AWS를 사용하는 경우, EC2 인스턴스를 Amazon S3 버킷의 VHD로 내보냅니다. [Amazon EC2 인스턴스 내보내기](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExportingEC2Instances.html)에 설명된 단계에 따라 Amazon EC2 명령줄 인터페이스(CLI) 도구를 설치하고 명령을 실행하여 VHD 파일에 EC2 인스턴스를 내보냅니다. 명령 실행 시 DISK_IMAGE_FORMAT 변수로 **VHD**를 사용해야 합니다. 내보낸된 VHD 파일은 해당 프로세스 중 지정된 Amazon S3 버킷에 저장됩니다.</li></ul>![][2]<ul><li>S3 버킷에서 VHD 파일을 다운로드합니다. VHD 파일 > **작업** > **다운로드**를 선택합니다.</li></ul>![][3]|
|온-프레미스에서 VHD 복사|온-프레미스 환경에서 VHD를 마이그레이션하는 경우, VHD가 저장된 전체 소스 경로가 필요합니다. 이 경로는 서버 위치 또는 파일 공유일 수 있습니다.

### 대상 

VHD를 유지 관리하기 위한 저장소 계정을 만듭니다. VHD를 저장할 위치를 계획할 때 다음 사항을 고려합니다.

- 응용 프로그램 요구 사항에 따라 대상 저장소 계정은 표준 또는 프리미엄 저장소가 될 수 있습니다. 
- 저장소 계정 위치는 최종 단계에서 만들 DS 시리즈 Azure VM과 동일해야 합니다. 새 저장소 계정으로 복사하거나 필요에 따라 동일한 저장소 계정을 사용할 수 있습니다.
- 다음 단계는 대상 저장소 계정의 저장소 계정 키를 복사하고 저장합니다.
- 데이터 디스크의 경우, 표준 저장소 계정(예: 냉각 저장소가 있는 디스크) 및 프리미엄 저장소 계정에 IOP가 높은 일부 저장소 계정으로 일부 데이터 디스크를 유지하도록 선택할 수 있습니다.

### AzCopy를 사용하여 VHD 복사

AzCopy를 사용하여 인터넷을 통해 VHD를 쉽게 업로드할 수 있습니다. 소요되는 시간은 VHD의 크기에 따라 다를 수 있습니다. 이 옵션을 사용하는 경우 저장소 계정 송/수신 제한을 확인해야 합니다. [여기](azure-subscription-service-limits#storage-limits)에서 Azure 저장소 제한을 확인할 수 있습니다.

1. [최신 버전의 AzCopy](http://aka.ms/downloadazcopy)에서 AzCopy를 다운로드하고 설치합니다.   
2. Azure PowerShell을 열고 AzCopy를 설치한 폴더로 이동합니다.  
3. "원본"에서 "대상"으로 VHD 파일을 복사하려면 다음 명령을 사용합니다. **AzCopy Source:** *&lt;Source&gt;* **SourceKey:** *&lt;Source-Storage-Key&gt;* **Destination:** *&lt;Destination&gt;* **DestKey:** *&lt;Dest-Storage-Key&gt;* **BlobType:page Pattern:** *&lt;File-Name&gt;*  
  - *&lt;Source&gt;:* VHD를 포함하는 폴더 또는 저장소 컨테이너 URL의 위치입니다.    
 - *&lt;Source-Storage-Key&gt;:* 원본 저장소 계정의 저장소 계정 키입니다.  
 - *&lt;Destination&gt;:* VHD를 복사할 저장소 컨테이너 URL입니다.
 - *&lt;Source-Storage-Key&gt;:* 대상 저장소 계정의 저장소 계정 키입니다.
 - BlobType:page:는 대상이 블록 Blob임을 지정합니다.
 - *&lt;File-Name&gt;*: 복사 하려는 VHD 파일의 이름입니다.
 - Pattern:*&lt;File-Name&gt;:*는 복사 하는 VHD의 파일 이름을 지정합니다.
   
이 명령은 *&lt;Source&gt;*의 모든 파일을 *&lt;Destination&gt;* 컨테이너에 복사합니다. AzCopy 사용에 대한 자세한 내용은 [AzCopy 명령줄 유틸리티](storage-use-azcopy.md)로 시작하기를 참조하세요.
### VHD를 업로드하기 위한 기타 옵션 

- [Azure 저장소 Blob 복사 API](https://msdn.microsoft.com/library/azure/dd894037.aspx) 
- [Azure 가져오기/내보내기 서비스](https://msdn.microsoft.com/library/dn529096.aspx) 

>[AZURE.NOTE]가져오기/내보내기를 사용하여 표준 저장소 계정만 복사할 수 있습니다. AzCopy와 같은 도구를 사용하여 표준 저장소에서 프리미엄 저장소 계정으로 복사해야 합니다.

## 프리미엄 저장소를 사용하여 Azure VM 만들기

VHD을 원하는 저장소 계정에 업로드 한 후, 이 섹션의 설명에 따라 VHD를 OS 이미지로 등록하거나 사용자 시나리오에 따라 OS 디스크를 등록한 다음, 해당 이미지나 디스크에서 VM 인스턴스를 만듭니다. 생성되면 데이터 디스크 VHD를 VM에 연결할 수 있습니다.

### VHD 등록
  
OS VHD에서 VM을 만들거나 새 VM에 데이터 VHD를 연결하려면 먼저 등록해야 합니다. 시나리오에 따라 아래 단계를 따르십시오.

<table>
<tr>
<th>시나리오</th>
<th>VHD를 등록하는 단계</th>
</tr>
<tr>
<td>여러 Azure VM 인스턴스를 만드는 일반화된 운영 체제 VHD</td>
<td><p>일반화 된 OS 이미지가 저장소 계정에 업로드된 후, <b>Azure VM 이미지</b>로 등록되면 하나 이상의 VM 인스턴스를 이미지에서 만들 수 있습니다. </p><p>Azure VM OS 이미지로 VHD를 등록 하려면 다음 PowerShell cmdlet을 사용합니다. VHD가 복사된 완전한 컨테이너 URL을 제공합니다.</p><p><code>Add-AzureVMImage -ImageName "OSImageName" -MediaLocation “https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd” -OS Windows</code></p>새 Azure VM 이미지의 이름을 복사하고 저장합니다. 위의 예에서 "OSImageName"입니다.</td>
</tr>
</tr>
<td>단일 Azure VM 인스턴스를 만드는 고유 운영 체제 VHD</td>
<td><p>고유 OS VHD를 저장소 계정에 업로드 한 후, <b>Azure OS 디스크</b>로 등록되면 VM 인스턴스를 이 디스크에서 만들 수 있습니다.</p><p>이 PowerShell cmdlet을 사용하여 Azure OS 이미지로 VHD를 등록합니다. VHD가 복사된 완전한 컨테이너 URL을 제공합니다.</p><code>"https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "OS Disk" -OS "Windows"</code><p>새 Azure OS 이미지의 이름을 복사하고 저장합니다. 위의 예에서 "OSDisk"입니다.</p></td>
</tr>
</tr>
<td>Azure VM 인스턴스에 연결될 데이터 디스크 VHD</td>
<td><p>데이터 디스크 VHD를 저장소 계정에 업로드 한 후 Azure 데이터 디스크로 등록되면 새 DS 시리즈 Azure VM 인스턴스에 연결할 수 있습니다.</p><p>이 PowerShell cmdlet을 사용하여 Azure 데이터 디스크로 VHD를 등록합니다. VHD가 복사된 완전한 컨테이너 URL을 제공합니다.</p><code>Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "Data Disk"</code><p>새 Azure 데이터 디스크의 이름을 복사하고 저장합니다. 위의 예에서 "DataDisk"입니다.</p></td>
</tr>
</table>

### DS 시리즈 Azure VM 만들기  

OS 이미지나 OS 디스크가 등록되면, 새 DS 시리즈 Azure VM 인스턴스를 만들 수 있습니다. 등록된 운영 체제 이미지 또는 운영 체제 디스크 이름을 사용합니다. 프리미엄 저장소 계층에서 VM 종류를 선택합니다. 아래 예제에서는 "Standard_DS2" VM 크기를 사용 중입니다.

>[AZURE.NOTE]디스크 크기를 업데이트하여 용량, 성능 요구 사항 및 사용 가능한 Azure 디스크 크기가 일치하는지 확인합니다.

아래 PowerShell cmdlet을 단계별로 수행하여 새 VM을 만듭니다.

먼저, 공통 매개 변수를 설정합니다.

	$vmSize ="Standard_DS2"
	$adminName = "youradmin"
	$adminPassword = "yourpassword"
	$vmName ="yourVM"
	$location = "East US 2"  

두 번째로, 시나리오에 따라 등록된 OS 이미지 또는 OS 디스크에서 Azure VM 인스턴스를 만듭니다.

<table>
<tr>
<th>시나리오</th>
<th>단계</th>
</tr>
<tr>
<td>여러 Azure VM 인스턴스를 만드는 일반화된 운영 체제 VHD</td>
<td>등록된 <b>Azure OS 이미지</b>를 사용하여 하나 이상의 새 DS 시리즈 Azure VM 인스턴스를 만듭니다. 아래와 같이 새 VM을 만들 때 VM 구성에서 이 OS 이미지 이름을 지정합니다.</br></br>
<code>$OSImage = Get-AzureVMImage –ImageName “OSImageName” $vm = New-AzureVMConfig -Name "NewVM" -InstanceSize $vmSize -ImageName $OSImage.ImageName</code>
</br></br>
<code>Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm New-AzureVM -ServiceName "NewVM"</code></td></tr>  
<tr>
<td>단일 Azure VM 인스턴스를 만드는 고유 운영 체제 VHD</td>
<td>등록된 <b>Azure OS 디스크</b>를 사용하여 새 DS 시리즈 Azure VM 인스턴스를 만듭니다. 아래와 같이 새 VM을 만들 때 VM 구성에서 이 OS 디스크 이름을 지정합니다.</br></br>
<code>$OSDisk = Get-AzureDisk –DiskName “OSDisk” $vm = New-AzureVMConfig -Name “NewVM” -InstanceSize $vmSize -DiskName $OSDisk.DiskName</code>
</br></br>  
<code>Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm New-AzureVM -ServiceName "NewVM"</code>
</td>
</tr>
</table>

클라우드 서비스, 지역, 저장소 계정, 가용성 집합 및 캐싱 정책 등의 기타 Azure VM 정보를 지정합니다. VM 인스턴스는 연관된 운영 체제 또는 데이터 디스크와 배치되므로, 선택된 클라우드 서비스, 지역 및 저장소 계정은 모두 해당 디스크의 기본 VHD와 동일한 위치에 있어야 합니다.

### 데이터 디스크 연결  

마지막으로, 등록된 데이터 디스크 VHD가 있는 경우 새 DS 시리즈 Azure VM에 연결합니다.

새 VM에 데이터 디스크를 연결하고 캐싱 정책을 지정하려면 다음 PowerShell cmdlet을 사용합니다. 캐싱 정책 아래 예제에서 읽기 전용으로 설정됩니다.

	$vmName ="yourVM"
	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
	Add-AzureDataDisk -ImportFrom -DiskName “DataDisk” -LUN 0 –HostCaching ReadOnly –VM $vm | Update-AzureVM  

>[AZURE.NOTE]이 가이드에서 다룰 수 없는 응용 프로그램을 지원하기 위해 구체적인 단계가 필요할 수 있습니다.


## 다음 단계  

가상 컴퓨터 마이그레이션에 대한 특정 시나리오에 대한 다음 리소스를 확인합니다.

- [저장소 계정 간에 Azure 가상 컴퓨터 마이그레이션](http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)  
- [Windows Server VHD를 만들고 Azure에 업로드합니다.](../virtual-machines-create-upload-vhd-windows-server.md)  
- [Linux 운영 체제를 포함하는 가상 하드 디스크 만들기 및 업로드](../virtual-machines-linux-create-upload-vhd.md)  
- [Amazon AWS에서 Microsoft Azure로 가상 컴퓨터 마이그레이션](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)  

Azure 저장소 및 Azure 가상 컴퓨터에 대한 자세한 내용을 보려면 다음 리소스도 확인합니다.

- [Azure 저장소](http://azure.microsoft.com/documentation/services/storage/)   
- [Azure 가상 컴퓨터](http://azure.microsoft.com/documentation/services/virtual-machines/)  
- [프리미엄 저장소: Azure 가상 컴퓨터 작업을 위한 고성능 저장소](storage-premium-storage-preview-portal.md)  



[1]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image1.png
[2]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image2.png
[3]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image3.png
<!--HONumber=52-->
 