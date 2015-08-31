<properties
   pageTitle="Microsoft Azure 보안 및 감사 로그 관리 | Microsoft Azure"
   description="문서는 Azure에서 호스팅되는 서비스에서 보안 로그 생성, 수집 및 분석 소개를 제공합니다. 조직의 보안 및 규정 준수 담당자를 포함하여, 매일 정보 자산 관리를 처리하는 IT 전문가 및 보안 분석가를 위한 것입니다."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="msStevenPo"
   editor=""/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/13/2015"
   ms.author="mnayak;tomsh;terrylan"/>

# Microsoft Azure 보안 및 감사 로그 관리

Azure을 통해 고객은 플랫폼에서 구독의 중앙 저장소에 대한 서비스(PaaS) 역할로, Azure 인프라에서 서비스(IaaS)로 보안 이벤트를 생성하고 수집할 수 있습니다. 고객은 [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/)를 사용하여 수집된 이벤트를 집계하고 분석할 수 있습니다. 또한 수집된 이 이벤트를 온-프레미스 보안 정보 및 지속적인 모니터링을 위한 이벤트 관리(SIEM) 시스템으로 내보낼 수 있습니다.

Azure 보안 로깅, 분석 및 수명 주기 모니터링은 다음을 포함합니다.

- **생성**: 이벤트를 발생시키는 응용 프로그램 및 인프라를 계측합니다.
- **컬렉션**: 저장소 계정에서 다양한 보안 로그를 수집하도록 Azure를 구성합니다.
- **분석**: HDInsight 및 온-프레미스 SIEM 시스템과 같은 Azure 도구를 사용하여 로그를 분석하고 보안 통찰력을 생성합니다.
- **모니터링 및 보고**: Azure는 연속 표시 여부 및 시기 적절한 경고를 제공하는 중앙 집중식 모니터링 및 분석 시스템을 제공합니다.

이 문서에서는 수명 주기의 생성 및 컬렉션 단계에 중점을 둡니다.

## 로그 생성
보안 이벤트는 가상 컴퓨터에서 **시스템**, **보안** 및 **응용 프로그램** 채널을 위한 Windows 이벤트 로그에서 발생합니다. 이벤트가 잠재적인 데이터 손실 없이 로그되도록 하려면, 이벤트 로그의 크기를 적절하게 구성하는 것이 중요합니다. 감사 정책 설정이 생성되고 이벤트 컬렉션 정책이 정의된 여러 이벤트의 이벤트 로그 크기를 기반으로 합니다. 자세한 내용은 [보안 감사 모니터링 및 관리 계획](http://technet.microsoft.com/library/ee513968.aspx#BKMK_4)을 참조하세요.

>[AZURE.NOTE]Windows 이벤트 전달(WEF) 또는 Azure 진단([로그 수집](#log-collection) 섹션에 설명된)을 사용하여 클라우드 서비스 또는 가상 컴퓨터에서 로그를 가져오는 경우, 시스템 중단의 잠재적인 영향을 고려해야 합니다. 예를 들어 WEF 환경이 일정 기간동안 중단되는 경우, 가능한 로그 데이터 손실을 준비하거나 더 긴 기간동안 계정에 대해 로그 크기가 충분한지 확인해야 합니다.

[Azure 가상 컴퓨터 마켓플레이스](http://azure.microsoft.com/marketplace/virtual-machines/#microsoft)에서 만든 Azure 및 가상 컴퓨터에서 배포된 클라우드 서비스 응용 프로그램의 경우, 운영 체제 보안 이벤트의 집합이 기본적으로 사용됩니다. 고객은 이벤트를 추가, 제거 또는 수정하여 운영 체제 감사 정책을 사용자 지정할 수 있습니다. 자세한 내용은 [보안 정책 설정 참조](http://technet.microsoft.com/library/jj852210.aspx)를 참조하세요.

다음 메서드를 사용하여 운영 체제(예: 감사 정책 변경) 및 Windows 구성 요소(예: IIS)에서 추가 로그를 생성할 수 있습니다.

- 도메인에 가입하는 Azure에서 가상 컴퓨터의 정책 설정을 롤아웃하는 그룹 정책
- 정책 설정을 푸시하고 관리하는 DSC(Desired State Configuration) 자세한 내용은 [Azure PowerShell DSC](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx)를 참조하세요.
- 클라우드 서비스(PaaS 시나리오)에 대한 설정을 롤아웃하는 서비스 배포 역할 시작 코드

Azure 역할 시작 작업 구성으로 역할을 시작하기 전에 실행하도록 코드를 사용합니다. 다음 예에 나타난 대로 서비스 정의 파일의 역할 정의에 **시작** 요소를 추가하여 역할의 시작 작업을 정의할 수 있습니다. 자세한 내용은 [Azure에서 시작 작업 실행](http://msdn.microsoft.com/library/azure/hh180155.aspx)을 참조하세요.

시작 작업으로 실행되어야 하는 작업 파일(다음 예에서 EnableLogOnAudit.cmd)은 빌드 패키지에 포함되어야 합니다. Visual Studio를 사용하는 경우, 클라우드 프로젝트에 파일을 추가하고, 파일 이름을 마우스 오른쪽 단추로 클릭하고, **속성**을 클릭한 다음 **출력 디렉터리에 복사**를 **항상 복사**로 설정합니다.

    <Startup>
        <Task commandLine="EnableLogOnAudit.cmd" executionContext="elevated" taskType="simple" />
    </Startup>

EnableLogOnAudit.cmd의 내용:

    @echo off
    auditpol.exe /set /category:"Logon/Logoff" /success:enable /failure:enable
    Exit /B 0

이전 예에서 사용된 [Auditpol.exe](https://technet.microsoft.com/library/cc731451.aspx)는 감사 정책 설정을 관리할 수 있도록 하는 Windows Server 운영 체제에 포함된 명령줄 도구입니다.

Windows 이벤트 로그 생성 외에, 다양한 Windows 운영 체제 구성 요소는 보안 분석 및 모니터링에 중요한 로그를 생성하도록 구성할 수 있습니다. 예를 들어, 인터넷 정보 서비스(IIS) 로그 및 http.err 로그는 웹 역할에 대해 자동으로 생성되며 컬렉션에 대해 구성할 수 있습니다. 이 로그는 무단으로 액세스 또는 웹 역할에 대한 공격을 식별하는데 사용할 수 있는 유용한 정보를 제공합니다. 자세한 내용은 [IIS에서 로깅 구성](http://technet.microsoft.com/library/hh831775.aspx) 및 [IIS의 고급 로깅 – 사용자 지정 로깅](http://www.iis.net/learn/extensions/advanced-logging-module/advanced-logging-for-iis-custom-logging)을 참조하세요.

웹 역할에서 IIS 로깅을 변경하려면 고객은 웹 역할 서비스 정의 파일에 시작 작업을 추가할 수 있습니다. 다음 예에서는 Contoso라는 웹 사이트에 대한 HTTP 로깅을 사용하며 IIS가 Contoso 웹 사이트에 대한 모든 요청을 기록하도록 지정합니다.

IIS 구성을 업데이트 하는 작업은 웹 역할의 서비스 정의 파일 내에 포함해야 합니다. ConfigureIISLogging.cmd이라는 스크립트를 실행하여 IIS 로깅을 구성하는 시작 작업을 실행하는 서비스 정의 파일을 다음과 같이 변경합니다.

    <Startup>
        <Task commandLine="ConfigureIISLogging.cmd" executionContext="elevated" taskType="simple" />
    </Startup>

ConfigureIISLogging:cmd의 내용

    @echo off
    appcmd.exe set config "Contoso" -section:system.webServer/httpLogging /dontLog:"True" /commit:apphost
    appcmd.exe set config "Contoso" -section:system.webServer/httpLogging /selectiveLogging:"LogAll" /commit
    Exit /B 0


## <a name="log-collection"></a>로그 수집
Azure에서 가상 컴퓨터 또는 클라우드 서비스에서 보안 이벤트 및 로그 수집은 두 기본 메서드를 통해 발생합니다.

- Azure 진단은 고객의 Azure 저장소 계정으로 이벤트를 수집합니다.
- Windows 이벤트 전달(WEF), Windows를 실행하는 컴퓨터의 기술

이 두 기술 간의 몇가지 주요 차이점은 아래 테이블에 포함됩니다. 요구 사항 및 이러한 주요 차이점에 따라, 로그 컬렉션 구현을 위해 적절한 방법을 선택해야 합니다.

| Azure 진단 | Windows 이벤트 전달 |
|-----|-----|
|Azure 가상 컴퓨터 및 Azure 클라우드 서비스 지원 | 도메인에 가입된 Azure 가상 컴퓨터만 지원 |
|Windows 이벤트 로그와 같은 다양한 로그 형식, [Windows용 이벤트 추적](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx)(ETW) 추적 및 IIS 로그 지원 자세한 내용은 [Azure 진단 지원 데이터 원본](#diagnostics)을 참조하세요. |Windows 이벤트 로그만 지원 |
|Azure 저장소에 수집된 데이터 푸시 |수집기 중앙 서버에 수집된 데이터 이동 |

##	Windows 이벤트 전달을 사용하여 보안 이벤트 데이터 수집
도메인에 가입된 Azure 가상 컴퓨터의 경우, 온-프레미스 도메인에 가입된 컴퓨터의 경우와 동일한 방식으로 그룹 정책 설정을 사용하여 WEF를 구성할 수 있습니다. 자세한 내용은 [하이브리드 클라우드](http://www.microsoft.com/server-cloud/solutions/hybrid-cloud.aspx)를 참조하세요.

이 방법을 사용하면 조직은 IaaS 구독을 구입하고, [Express 경로](http://azure.microsoft.com/services/expressroute/) 또는 사이트 간 VPN을 사용하여 회사 네트워크에 연결한 다음 회사 도메인의 Azure에 있는 가상 컴퓨터에 연결합니다. 그런 다음 도메인에 가입된 컴퓨터에서 WEF를 구성할 수 있습니다.

이벤트 전달은 원본 및 수집기의 두 부분으로 나뉩니다. 원본은 보안 로그가 생성되는 컴퓨터입니다. 수집기는 이벤트 로그를 수집하고 통합하는 중앙 서버입니다. IT 관리자는 원격 컴퓨터(이벤트 소스)에서 전달된 이벤트를 받고 저장할 수 있도록 이벤트를 구독할 수 있습니다. 자세한 내용은 [이벤트를 전달 및 수집하도록 컴퓨터 구성](http://technet.microsoft.com/library/cc748890.aspx)을 참조하세요.

수집된 Windows 이벤트는 추가 분석을 위해 SIEM 등의 온-프레미스 분석 도구로 보낼 수 있습니다.

## Azure 진단을 사용하여 보안 데이터 수집
Azure 진단을 사용하면 Azure에서 실행 중인 가상 컴퓨터, 클라우드 서비스 작업자 역할 또는 웹 역할에서 진단 데이터를 수집할 수 있습니다. 데이터 수집을 사용하도록 설정하고 구성해야 하는 미리 정의된 게스트 에이전트 확장 프로그램입니다. 고객의 구독에는 Azure 저장소에 대한 데이터 푸시가 포함될 수 있습니다.

데이터는 전송 중 암호화됩니다(HTTPS 사용). 이 문서에서 제공하는 예제는 Azure 진단 1.2를 사용 중입니다. 보안 데이터 컬렉션을 위해 1.2 이상 버전으로 업그레이드하는 것이 좋습니다. 자세한 내용은 [Azure 진단을 사용하여 로깅 데이터 수집](http://msdn.microsoft.com/library/gg433048.aspx)을 참조하세요.

다음 다이어그램에서는 Azure 진단 및 추가 분석과 모니터링을 사용하는 보안 데이터 컬렉션에 대한 높은 수준의 데이터 흐름을 보여 줍니다.

![][1]

Azure 진단은 고객 클라우드 서비스 응용 프로그램 및 [Azure 가상 컴퓨터](virtual-machines-about.md)에서 Azure 저장소로 로그를 이동합니다. 로그 형식에 따르면 일부 데이터는 Azure 테이블 및 blob의 일부에 저장됩니다. [Azure 저장소](storage-introduction.md)에서 수집된 데이터는 모니터링 및 분석을 위해 Azure 저장소 클라이언트 라이브러리를 사용하여 온-프레미스 SIEM 시스템으로 다운로드할 수 있습니다.

또한 HDInsight는 클라우드에서 데이터의 추가 분석을 위해 사용할 수 있습니다. 다음은 Azure 진단을 사용하는 보안 데이터 컬렉션의 일부 예입니다.

### Azure 진단을 사용하여 Azure 가상 컴퓨터에서 보안 데이터 컬렉션

다음 예에서는 Azure 진단 1.2 및 Azure PowerShell cmdlet을 사용하여 가상 컴퓨터에서 보안 데이터를 수집합니다. 데이터는 예약된 간격으로 가상 컴퓨터에서 수집되며 고객의 구독 내에서 Azure 저장소에 푸시됩니다. 이 섹션에서는 Azure 진단을 사용하여 두 로그 컬렉션 시나리오를 통해 작업합니다.

1. 가상 컴퓨터에서 보안 로그 컬렉션 파이프라인의 새 인스턴스를 설정합니다.
2. 가상 컴퓨터에서 새 구성을 사용하여 기존 보안 로그 컬렉션 파이프라인을 업데이트합니다.

#### 가상 컴퓨터에서 보안 로그 컬렉션 파이프라인의 새 인스턴스를 설정합니다.
이 예제에서는 Azure 진단을 사용하는 보안 로그 컬렉션 파이프라인의 새 인스턴스를 설정하고 가상 컴퓨터에서 로그온 실패 이벤트(이벤트 ID 4624 및 4625)를 감지합니다. 개발 환경에서 다음 단계를 구현하거나 클라우드에서 노드에 대해 원격 데스크톱 프로토콜(RDP)을 통해 원격 데스크톱 세션을 사용할 수 있습니다.

##### 1단계: Azure PowerShell SDK 설치
Azure PowerShell SDK는 Azure 가상 컴퓨터에 Azure 진단을 구성하는 cmdlet을 제공합니다. 필요한 cmdlet은 Azure PowerShell 버전 0.8.7 이상에서 사용할 수 있습니다. 자세한 내용은 [Azure PowerShell 설치 및 구성하는 방법](powershell-install-configure.md)을 참조하세요.

##### 2단계: 구성 파일 준비
수집하려는 이벤트에 따라 구성 파일을 준비합니다. 다음은 **보안** 채널에서 Windows 이벤트를 수집하는 Azure 진단 구성 파일의 예로, 로그온 성공 및 실패 이벤트만을 수집하도록 필터가 추가됩니다. 자세한 내용은 [Azure 진단 1.2 구성 스키마](http://msdn.microsoft.com/library/azure/dn782207.aspx)를 참조하세요.

저장소 계정은 구성 파일에 지정하거나 Azure 진단을 설정하도록 Azure PowerShell cmdlet을 실행할 때 매개 변수로 지정할 수 있습니다.

    <?xml version="1.0" encoding="utf-8" ?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
            <DiagnosticMonitorConfiguration overallQuotaInMB="10000">
                <WindowsEventLog scheduledTransferPeriod="PT1M">
                    <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
                </WindowsEventLog>
            </DiagnosticMonitorConfiguration>
        </WadCfg>
    </PublicConfig>

이전 내용을 XML 파일로 저장할 때 인코딩을 **UTF-8**로 설정합니다. 메모장을 사용하는 경우, '다른 이름으로 저장' 대화 상자에서 사용할 수 있는 인코딩 옵션이 표시됩니다. 아래 표에는 구성 파일에 기록할 일부 키 특성을 나열합니다.

| 특성 | 설명 |
|----- |-----|
| overallQuotaInMB | Azure 진단(값은 구성 가능)에서 사용할 수 있는 로컬 디스크 공간의 최대 크기입니다. |
| scheduledTransferPeriod | Azure 저장소에 예약된 전송 사이의 간격은 가장 가까운 시간(분)으로 반올림됩니다. |
| 이름 | WindowsEventLog에서 이 특성은 수집할 Windows 이벤트를 설명하는 XPath 쿼리입니다. 이벤트 ID, 공급자 이름 또는 채널과 같은 필터를 추가하여 데이터 컬렉션을 필터링할 수 있습니다. |

모든 Windows 이벤트 로그 데이터는 **WADWindowsEventLogsTable**이라는 테이블로 이동됩니다. 현재, Azure 진단은 테이블 이름 변경을 지원하지 않습니다.

##### <a name="step3"></a> 3단계: 구성 XML 파일의 유효성 검사
다음 절차에 따라 구성 XML 파일에서 오류가 없는지와 Azure 진단 스키마와 호환되는지 확인합니다.

1. 스키마 파일을 다운로드하려면 다음 명령을 실행한 다음 파일을 저장합니다.

    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfigSchema.xsd'

2. 스키마 파일을 다운로드한 후 스키마에 대해 구성 XML 파일을 확인할 수 있습니다. Visual Studio를 사용하여 파일의 유효성 검사:
  - Visual Studio에서 XML 파일 열기
  - F4를 눌러 **속성** 열기
  - **스키마**를 클릭하고, **추가**를 클릭하고, 다운로드한 스키마 파일(WadConfigSchema.XSD)을 선택한 다음 **확인**을 클릭합니다.

3.	**보기** 메뉴에서 **오류 목록**을 클릭하여 유효성 검사 오류가 있는지 확인합니다.

##### <a name="step4"></a> 4단계: Azure 진단 구성
 Azure 진단을 사용하도록 설정하고 데이터 수집을 시작하려면 다음 단계를 따르십시오.

 1.	Azure PowerShell을 열려면 **추가-AzureAccount**를 입력하고 ENTER를 누릅니다.
 2.	Azure 계정으로 로그인합니다.
 3.	다음 PowerShell 스크립트를 실행합니다. storage\_name, key, config\_path, service\_name 및 vm\_name을 업데이트해야 합니다.

 ```PowerShell
$storage_name ="<Storage Name>"
$key = "<Storage Key>"
$config_path="<Path Of WAD Config XML>"
$service_name="<Service Name. Usually it is same as VM Name>"
$vm_name="<VM Name>"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
$VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
$VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
$VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM
 ```

##### 5단계: 이벤트 생성
데모 목적으로 일부 로그온 이벤트를 만들고 Azure 저장소로 데이터가 흐르는지 확인합니다. 2단계에서 이전에 표시한 대로, XML 파일은 이벤트 ID 4624(로그온 성공) 및 이벤트 ID 4625(로그온 실패)를 **보안** 채널에서 수집하도록 구성됩니다.

 이 이벤트를 생성하려면 다음을 수행하십시오.

1.	가상 컴퓨터에 대한 RDP 세션을 엽니다.
2.	일부 실패한 로그온 이벤트(이벤트 ID 4625)를 생성하는 잘못된 자격 증명을 입력합니다.
3.	로그온 시도를 몇 번 실패한 후에 성공적인 로그온 이벤트(EventID 4624)를 생성하도록 올바른 자격 증명을 입력합니다.

##### 6단계: 데이터 보기
이전 단계를 완료한 다음 약 5분 후에, XML 파일의 구성에 따라 고객 저장소 계정으로 데이터 전송이 시작되어야 합니다. Azure 저장소에서 데이터를 볼 수 있는 많은 도구가 있습니다. 자세한 내용은 다음을 참조하세요.

- [서버 탐색기로 저장소 리소스 찾아보기(영문)](http://msdn.microsoft.com/library/azure/ff683677.aspx)
- [Azure 저장소 탐색기 6 미리 보기 3(2014년 8월)](http://azurestorageexplorer.codeplex.com/)

데이터를 보려면:

1.	Visual Studio(2013, 2012 및 2010 SP1)에서 **보기**를 선택한 다음 **서버 탐색기**를 클릭합니다.
2.	저장소 계정으로 이동
3.	**테이블**을 클릭한 다음 적절한 테이블을 두 번 클릭하여 가상 컴퓨터에서 수집된 보안 로그를 봅니다. ![][2]

4.	WADWindowsEventLogsTable이라는 테이블을 마우스 오른쪽 단추로 클릭한 다음 **데이터 보기**를 클릭하여 다음과 같이 표시된 테이블 보기를 엽니다.

![][3]

이전 저장소 테이블에서 **PartitionKey**, **RowKey** 및 **타임 스탬프**는 시스템 속성입니다.

- **PartitionKey**는 타임 스탬프(초)이며 테이블 내 파티션에 대한 고유 식별자입니다.
- **RowKey**는 파티션 내 엔터티의 고유 식별자입니다.

**PartitionKey** 및 **RowKey**는 테이블 내 모든 엔터티를 고유하게 식별합니다.

- 타임 스탬프는 엔터티를 마지막으로 수정한 시간을 추적할 서버에서 유지 관리되는 날짜/시간 값입니다.

>[AZURE.NOTE]Azure 저장소 테이블의 최대 행 크기는 1MB로 제한됩니다. 2012년 6월 이후 계정을 만든 경우 저장소 계정은 BLOB, 큐 및 테이블에서 최대 200TB의 데이터를 포함할 수 있습니다. 따라서 BLOB 및 큐 저장소가 공간을 사용하지 않는 경우 테이블 크기는 200TB까지 증가할 수 있습니다. 2012년 6월 이전에 만든 계정은 100TB의 제한이 있습니다.

또한 저장소 탐색기는 테이블 데이터를 편집하는 옵션을 제공합니다. 다음과 같이 엔터티 편집 창을 열려면 테이블 보기에서 특정 행을 두 번 클릭합니다.

![][4]

#### 가상 컴퓨터에서 새 구성을 사용하여 기존 보안 로그 컬렉션 파이프라인을 업데이트합니다.
이 섹션에서는 가상 컴퓨터에서 기존 Azure 진단 보안 로그 컬렉션 파이프라인을 업데이트하며 Windows 응용 프로그램 이벤트 로그 오류를 감지합니다.

##### 1단계: 원하는 이벤트를 포함하도록 구성 파일 업데이트
이전 예에서 만든 Azure 진단 파일은 Windows 응용 프로그램 이벤트 로그 오류 유형을 포함하도록 업데이트되어야 합니다.

>[AZURE.NOTE]새 구성 파일을 사용하여 모든 기존 Azure 진단 구성 설정을 병합해야 합니다. 새 파일에 정의된 설정은 기존 구성을 덮어쓰게 됩니다.

기존 구성 설정을 검색하려면 **Get-AzureVMDiagnosticsExtension** cmdlet을 사용할 수 있습니다. 다음은 기존 구성을 검색하는 샘플 Azure PowerShell 스크립트입니다.

    $service_name="<VM Name>"
    $VM1 = Get-AzureVM -ServiceName $service_name
    $config = Get-AzureVMDiagnosticsExtension -VM $VM1 | Select -Expand PublicConfiguration | % {$_.substring($_.IndexOf(':"')+2,$_.LastIndexOf('",')-$_.IndexOf(':"')-2)}
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($config))
다음과 같이 Windows 응용 프로그램 이벤트 로그 오류 및 중요한 이벤트를 수집하도록 Azure 진단 구성을 업데이트합니다.

    <?xml version="1.0" encoding="utf-8" ?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="10000">
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level =2)]]" />
                <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
            </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

[3단계: 구성 XML 파일의 유효성 검사](#step3)에서 이전에 설명한 것처럼 동일한 단계를 사용하여 구성 파일의 유효성을 검사합니다.

##### 2단계: 새 구성 파일을 사용하여 Azure 진단 업데이트
[4단계: Azure 진단 구성](#step4)에서 이전에 설명한 대로 **Set-AzureVMDiagnosticsExtension** 및 **Update-AzureVM** cmdlet을 사용하여 구성을 업데이트합니다.

##### 3단계: 구성 설정 확인
다음 명령을 실행하여 구성 설정이 업데이트되었음을 확인합니다.

    $service_name="<VM Name>"
    $VM1 = Get-AzureVM -ServiceName $service_name
    Get-AzureVMDiagnosticsExtension -VM $VM1

##### 4단계: 이벤트 생성
이 예제에서 다음 명령을 실행하여 유형 **오류**의 응용 프로그램 이벤트 로그를 생성합니다.

    eventcreate /t error /id 100 /l application /d "Create event in application log for Demo Purpose"

![][5]

이벤트 뷰어를 열어 해당 이벤트가 생성되었는지 확인합니다.

![][6]

##### 5단계: 데이터 보기
Visual Studio에서 서버 탐색기를 열어 로그 데이터를 봅니다. 다음과 같이 **ContosoDesktop**에서 만든 **EventID 100**이 표시되어야 합니다.

![][7]

## Azure 진단을 사용하여 Azure 클라우드 서비스에서 보안 데이터 수집

이제 Azure 진단을 사용하여 이전 가상 컴퓨터(IaaS) 섹션에서와 같이 Azure 클라우드 서비스에서 동일한 두 로그 컬렉션 시나리오를 탐색합니다.

1.	클라우드 서비스에서 보안 로그 파이프라인의 새 인스턴스를 설정합니다.
2.	클라우드 서비스의 새 구성으로 기존 로그 컬렉션 파이프라인을 업데이트합니다.

이 섹션의 단계별 연습에는 다음이 포함됩니다.

1.	클라우드 서비스를 빌드합니다.
2.	Azure 진단을 사용하여 보안 로그 컬렉션에 대한 클라우드 서비스를 구성합니다.
3.	클라우드 서비스에서 보안 이벤트의 생성 및 수집을 설명합니다.

    - 관리자를 권한 상승된 로컬 그룹에 추가
    - 새 프로세스 만들기
4.	클라우드 서비스에서 기존 로그 컬렉션 파이프라인 업데이트:

    - Auditpol을 사용하여 네트워크 보안 이벤트의 예로 호스트 방화벽 이벤트의 감사 사용
    - 고객 저장소 계정으로 수집된 이벤트를 수집하고 표시하도록 방화벽 감사 데이터 구성
5.	Windows 보안 이벤트 분산과 스파이크 검색을 표시합니다.
6.	IIS 로그의 컬렉션을 구성하고 데이터를 확인합니다.

모든 이벤트 및 로그가 Azure의 고객 저장소 계정으로 수집됩니다. 고객이 온-프레미스 SIEM 시스템으로 이벤트를 보고 내보낼 수 있습니다. 또한 HDInsight를 사용하여 집계하고 분석할 수도 있습니다.

### 클라우드 서비스에서 로그 컬렉션 파이프라인의 새 인스턴스를 설정합니다.
이 예제에서는 Azure 진단을 사용하는 보안 로그 컬렉션 파이프라인의 새 인스턴스를 설정하고, 클라우드 서비스 인스턴스에서 새 프로세스 생성 이벤트 및 로컬 그룹에 대한 사용자 추가를 감지합니다.

#### 1단계: 클라우드 서비스(웹 역할) 만들기 및 배포

1.	개발 컴퓨터에서 Visual Studio 2013을 실행합니다.
2.	새 클라우드 서비스 프로젝트를 만듭니다(이 예제는 ContosoWebRole 사용).
3.	**ASP.NET** 웹 역할을 선택합니다.
4.	**MVC** 프로젝트를 선택합니다.
5.	솔루션 탐색기에서 **역할**을 클릭한 다음, 웹 역할(WebRole1)을 두 번 클릭하여 **속성** 창을 엽니다.
6.	**구성** 탭에서 **진단 사용** 확인란을 선택 취소하여 Visual Studio 2013과 함께 제공되는 Azure 진단의 버전을 사용하지 않도록 설정합니다. ![][8]

7.	솔루션을 빌드하여 오류가 없는지 확인합니다.
8.	WebRole1/Controllers/HomeController.cs 파일을 엽니다.
9.	다음 메서드를 추가하고 샘플 응용 프로그램을 사용하도록 설정하여 HTTP 상태 코드 500을 샘플 IIS 로그 이벤트로 로그합니다(나중에 IIS 예제에서 사용함).

    ```
    public ActionResult StatusCode500()
        {
            throw new InvalidOperationException("Response.StatusCode is 500");
        }
    ```

10.	 클라우드 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

#### 2단계: 구성 파일 준비
이제 Azure 진단 구성 파일을 준비하여 다음 상황을 감지하는 데 도움이 되는 이벤트를 추가합니다.

- 로컬 그룹에 새 사용자 추가
- 새 프로세스 만들기

```
<?xml version="1.0" encoding="UTF-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
</PublicConfig>
```

#### 3단계: 구성 XML 파일의 유효성 검사
[3단계: 구성 XML 파일의 유효성 검사](#step3)에서 이전에 설명한 것처럼 동일한 단계를 사용하여 구성 파일의 유효성을 검사합니다.
#### 4단계: Azure 진단 구성
Azure 진단(storage\_name, 키, config\_path 및 service\_name을 업데이트할 수 있는지 확인)을 사용하도록 설정하려면 다음 Azure PowerShell 스크립트를 실행합니다.

    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

서비스에 최신 진단 구성이 있는지 확인하려면 다음 Azure PowerShell 명령을 실행합니다.

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### 5단계: 이벤트 생성
이벤트를 생성하려면 다음을 수행하십시오.

1.	클라우드 서비스 인스턴스에 대한 원격 데스크톱 세션을 시작하려면, Visual Studio에서 서버 탐색기를 열고 역할 인스턴스를 마우스 오른쪽 단추로 클릭하고 원격 데스크톱을 사용하여 연결을 클릭합니다.
2.	승격된 명령 프롬프트를 열고 다음 명령을 실행하여 가상 컴퓨터에서 로컬 관리자 계정을 만듭니다.


    net user contosoadmin <enterpassword> /add net localgroup administrators contosoadmin /add

3.	이벤트 뷰어를 열고 **보안** 채널을 열고, 다음과 같이 이벤트 4732가 만들어졌음을 알립니다.

![][9]

#### 6단계: 데이터 보기
Azure 진단 에이전트가 저장소 테이블에 이벤트를 푸시할 수 있도록 약 5분 기다립니다.

![][10]

프로세스 생성 이벤트의 유효성을 검사하려면 메모장을 엽니다. 여기에 표시된 것처럼 프로세스 생성 이벤트가 보안 채널에 기록됩니다.

![][11]

이제 다음과 같이 저장소 계정에서 동일한 이벤트를 볼 수 있습니다.

![][12]

### 새 구성으로 클라우드 서비스에서 기존 로그 컬렉션 파이프라인을 업데이트합니다.
이 섹션에서는 기존 Azure 진단 보안 로그 컬렉션 파이프라인을 업데이트하며 클라우드 서비스 인스턴스에서 Windows 방화벽 변경 이벤트를 감지합니다.

방화벽 변경 내용을 감지하려면 방화벽 변경 이벤트를 포함하도록 기존 구성을 업데이트합니다.

#### 1단계: 기존 구성 가져오기
>[AZURE.NOTE]새 구성 설정은 기존 구성을 덮어씁니다. 따라서, 새 구성 파일을 사용하여 모든 기존 Azure 진단 구성 설정을 병합하는 것이 중요합니다.

기존 구성 설정을 검색하려면 **Get-AzureServiceDiagnosticsExtension** cmdlet을 사용할 수 있습니다.

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### 2단계: 방화벽 이벤트를 포함하도록 구성 XML 업데이트

    <?xml version="1.0" encoding="UTF-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
        <WindowsEventLog scheduledTransferPeriod="PT1M">
            <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            <DataSource name="Security!*[System[(EventID &gt;= 4944 and EventID &lt;= 4958)]]" />
        </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

[3단계: 구성 XML 파일의 유효성 검사](#step3)에서 이전에 설명한 것처럼 동일한 유효성 검사 프로세스를 사용하여 XML 내용의 유효성을 검사합니다.

#### 3단계: 새 구성 파일을 사용하도록 Azure 진단 업데이트

새 구성을 사용하도록 다음 Azure PowerShell 스크립트를 실행하여 Azure 진단을 업데이트합니다(클라우드 서비스 정보를 사용하여 storage\_name, 키, config\_path, 및 service\_name가 업데이트되었는지 확인).

    Remove-AzureServiceDiagnosticsExtension -ServiceName <ServiceName> -Role <RoleName>
    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

서비스에 최신 진단 구성이 있는지 확인하려면 다음 Azure PowerShell 명령을 실행합니다.

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### 4단계: 방화벽 이벤트 활성화

1.	클라우드 서비스 인스턴스에 대한 원격 데스크톱 세션을 엽니다.
2.	승격된 명령 프롬프트를 열고 다음 명령을 실행합니다.

    ```
    auditpol.exe /set /category:"Policy Change" /subcategory:"MPSSVC rule-level Policy Change" /success:enable /failure:enable
    ```

#### 5단계: 이벤트 생성

1.	Windows 방화벽을 열고 **인바운드 규칙**을 클릭합니다.
2.	**새 규칙 추가**를 클릭한 후 **포트**를 클릭합니다.
3.	**로컬 포트** 필드에 **5000**을 입력한 다음 **다음**을 세 번 클릭합니다.
4.	**이름** 필드에 **Test5000**을 입력하고 **마침**을 클릭합니다.
5.	이벤트 뷰어를 열고 **보안** 채널을 열고, 다음과 같이 이벤트 ID 4946이 만들어졌음을 알립니다.

![][13]

#### 6단계: 데이터 보기
Azure 진단 에이전트가 저장소 테이블에 이벤트 데이터를 푸시할 수 있도록 약 5분 기다립니다.

![][14]

### 보안 이벤트 분산 및 스파이크 감지
이벤트가 고객의 저장소 계정에 있으면, 응용 프로그램은 저장소 클라이언트 라이브러리를 사용하여 액세스하고 이벤트 집계를 수행할 수 있습니다. 테이블 데이터에 액세스하는 샘플 코드는 [방법: 테이블 데이터 검색](storage-dotnet-how-to-use-tables.md)을 참조하세요.

다음은 이벤트 집계의 예제입니다. 이벤트 배포에서 모든 스파이크는 비정상적인 활동에 위해 추가로 조사될 수 있습니다.

![][15]

### HDInsight를 사용하여 IIS 로그 수집 및 처리
이 섹션에서는 웹 역할 인스턴스에서 IIS 로그를 수집하고 로그를 고객의 저장소 계정으로 Azure BLOB로 이동합니다.

#### 1단계: IIS 로그 컬렉션을 포함하도록 구성 파일 업데이트

    <?xml version="1.0" encoding="UTF-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
        <Directories scheduledTransferPeriod="PT5M">
        <IISLogs containerName="iislogs" />
        </Directories>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
            <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            <DataSource name="Security!*[System[(EventID &gt;= 4944 and EventID &lt;= 4958)]]" />
        </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

이전 Azure 진단 구성에서는 **containerName**은 고객의 저장소 계정 내에서 이동되는 로그에 대한 blob 컨테이너 이름입니다.

[3단계: 구성 XML 파일의 유효성 검사](#step3)에서 이전에 설명한 것처럼 동일한 단계를 사용하여 구성 파일의 유효성을 검사합니다.


#### 2단계: 새 구성을 사용하도록 Azure 진단 업데이트
새 구성을 사용하도록 다음 Azure PowerShell 스크립트를 실행하여 Azure 진단을 업데이트합니다(클라우드 서비스 정보를 사용하여 storage\_name, 키, config\_path, 및 service\_name가 업데이트되었는지 확인).

    Remove-AzureServiceDiagnosticsExtension -ServiceName <ServiceName> -Role <RoleName>
    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

서비스에 최신 진단 구성이 있는지 확인하려면 다음 Azure PowerShell 명령을 실행합니다.

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### 3단계: IIS 로그 생성

1.	웹 브라우저를 열고 클라우드 서비스 웹 역할(예: http://contosowebrole.cloudapp.net/))을 탐색합니다.
2.	**정보** 및 **연락처** 페이지를 탐색하여 일부 로그 이벤트를 만듭니다.
3.	상태 코드 500을 생성하는 페이지로 이동합니다(예: http://contosowebrole.cloudapp.net/Home/StatusCode500)). 다음과 같은 오류가 표시됩니다. 클라우드 ServiceName에서 로그 컬렉션 파이프라인의 새 인스턴스 설정이라는 1단계 섹션에서 **StatusCode500**의 코드를 추가했음을 기억하십시오. ![][16]
4.	클라우드 서비스 인스턴스에 대한 원격 데스크톱 세션을 엽니다.
5.	IIS 관리자를 엽니다.
6.	IIS 로그가 기본적으로 사용되며 W3C 형식으로 모든 필드를 포함하는 파일을 매시간 생성하도록 설정합니다. **찾아보기**를 클릭하면 여기에서와 같이 하나 이상의 로그 파일이 있습니다. ![][17]

7.	Azure 진단 에이전트에서 로그 파일을 blob 컨테이너에 푸시하도록 약 5 분 기다립니다. 데이터의 유효성을 검사하려면 **서버 탐색기** > **저장소** > **저장소 계정** > **BLOB**을 엽니다. 여기에 나와있는 대로 BLOB **iislogs**가 만들어집니다. ![][18]

8.	마우스 오른쪽 단추로 클릭하고 **BLOB 컨테이너 보기**를 선택하여 BLOB에 저장된 IIS 로그 파일을 표시합니다. ![][19]
9.	IIS 이벤트가 고객의 저장소 계정에 있는 경우, HDInsight 분석을 활용하는 응용 프로그램은 이벤트 집계를 수행하도록 사용할 수 있습니다. 다음 선형 차트는 HTTP 상태 코드 500을 표시하는 이벤트 집계 작업의 예입니다. ![][20]

## 보안 로그 컬렉션 권장 사항
보안 로그를 수집하는 경우 다음을 수행하는 것이 좋습니다.

- 사용자 고유의 서비스 응용 프로그램 관련 감사 로그 이벤트를 수집합니다.
- 분석 및 모니터링에 필요한 데이터만 구성합니다. 너무 많은 데이터를 캡처하면 문제를 해결하기가 힘들며 서비스 또는 저장소 비용에 영향을 줄 수 있습니다.
- 변경 내용과 기존 Azure 진단 구성 설정을 병합합니다. 새 구성 설정 파일은 기존 구성 설정을 덮어씁니다.
- **예약 전송 기간** 간격을 현명하게 선택합니다. 전송 시간이 짧을 수록 데이터 연관성이 늘어나지만 저장소 비용 및 처리 오버 헤드를 향상시킬 수 있습니다.

>[AZURE.NOTE]수집된 데이터의 양에 크게 영향을 주는 다른 변수는 로깅 수준입니다. 다음은 로깅 수준에 따라 로그를 필터링하는 방법의 예입니다.

    System!*[System[(Level =2)]]

로깅 수준은 누적입니다. 필터가 **경고**로 설정된 경우, **오류** 및 **위험** 이벤트도 수집됩니다.

- 더 이상 필요하지 않은 경우 Azure 저장소에서 진단 데이터를 주기적으로 지웁니다.

>[AZURE.NOTE]진단 데이터에 대한 자세한 내용을 보려면 [Azure 저장소에서 진단 데이터 저장 및 보기](https://msdn.microsoft.com/library/azure/hh411534.aspx)를 참조하세요. 진단 데이터를 저장하는 컨테이너 및 테이블은 다른 컨테이너 및 테이블과 동일하며, 다른 데이터에서와 마찬가지로 BLOB 및 엔터티를 삭제할 수 있습니다. 프로그래밍 방식으로 저장소 클라이언트 라이브러리의 중 하나에서 또는 시각적으로 [저장소 탐색기 클라이언트](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)에서 진단 데이터를 삭제할 수 있습니다.

- 별도의 저장소 계정으로 서비스 데이터 및 보안 로그 데이터를 저장하는 것이 좋습니다. 이 격리로 인해 보안 로그 데이터 저장은 프로덕션 서비스 데이터에 대한 저장소 성능에 영향을 주지 않습니다.
- 조직의 규정 준수 정책 및 데이터 분석 및 모니터링 요구 사항을 기반으로 로그 보존 기간을 선택합니다.

## 보안 로그를 다른 시스템으로 내보내기
Azure 저장소 클라이언트 라이브러리를 사용하여 BLOB 데이터를 다운로드한 후, 온-프레미스 시스템에 내보내 처리할 수 있습니다. BLOB 데이터를 관리하는 샘플 코드는 [.NET에서 BLOB 저장소를 사용하는 방법](storage-dotnet-how-to-use-blobs.md)을 참조하세요.

마찬가지로, Azure 저장소 클라이언트 라이브러리를 사용하여 Azure 테이블에 저장된 보안 데이터를 다운로드할 수 있습니다. 테이블에 저장된 데이터 액세스에 대해 자세히 알아보려면 [.NET에서 테이블 저장소를 사용하는 방법](storage-dotnet-how-to-use-tables.md)을 참조하세요.

## Azure Active Directory 보고서
Azure Active Directory(Azure AD)는 Azure AD 테넌트의 무결성 및 보안에 가시성을 제공하는 보안, 사용 및 감사 로그 보고서를 포함합니다. 예를 들어, Azure AD는 사용자 활동 및 표면 비정상적인 액세스를 자동으로 분석하고 고객에게 표시되는 보고서를 통해 사용할 수 있도록 하는 기능을 제공합니다.

이 보고서는 **Active Directory** > **디렉터리**의 [Azure 관리 포털](https://manage.windowsazure.com/)을 통해 사용 가능합니다. 이 보고서 중 일부는 무료이며 다른 일부는 Azure AD Premium Edition의 일부로 제공됩니다. Azure AD 보고서에 관한 자세한 내용은 [액세스 및 사용 보고서 보기](http://msdn.microsoft.com/library/azure/dn283934.aspx)를 참조하세요.

## Azure 작업 로그
Azure 구독 리소스와 관련된 작업 로그는 관리 포털의 **작업 로그** 기능을 통해 사용할 수도 있습니다.

**작업 로그**를 보려면, [Azure 관리 포털](https://manage.windowsazure.com/)을 열고 **관리 서비스**를 클릭한 다음 **작업 로그**를 클릭합니다.

## <a name="diagnostics"></a> Azure 진단 지원되는 데이터 원본

| 데이터 원본 | 설명 |
|----- | ----- |
| IIS 로그 | IIS 웹 사이트에 대한 정보 |
| Azure 진단 인프라 로그 | Azure 진단에 대한 정보 |
| IIS 실패한 요청 로그 | IIS 웹 사이트 또는 응용 프로그램에 대해 실패한 요청에 관한 정보 |
| Windows 이벤트 로그 | Windows 이벤트 로깅 시스템으로 전송된 정보 |
| 성능 카운터 | 운영 체제 및 사용자 지정 성능 카운터 |
| 크래시 덤프 | 응용 프로그램 크래시가 발생할 경우의 프로세스 상태에 대한 정보 |
| 사용자 지정 오류 로그 | 응용 프로그램 또는 서비스에서 생성한 로그 |
| .NET EventSource | .NET EventSource 클래스를 사용하여 코드에서 생성된 이벤트 |
| 매니페스트 기반 ETW | 모든 프로세스에서 생성된 Windows 이벤트용 이벤트 추적 |

## 추가 리소스
다음 리소스는 Microsoft Azure 및 관련 Microsoft 서비스에 대한 일반 정보를 제공합니다.

- [Microsoft Azure 보안 센터](http://azure.microsoft.com/support/trust-center/)

    보안 및 개인정보가 Azure의 개발에 포함 되는 방법 및 Azure가 광범위한 국제 및 특정 산업 규정 준수 표준을 충족하는 방법에 대한 정보

- [Microsoft Azure 홈페이지](http://www.microsoft.com/windowsazure/)

    Microsoft Azure에 대한 일반 정보 및 링크

- [Microsoft Azure 설명서 센터](http://msdn.microsoft.com/windowsazure/default.aspx)

    Azure 서비스 및 자동화 스크립트에 대한 지침

- [Microsoft 보안 응답 센터(MSRC)](http://www.microsoft.com/security/msrc/default.aspx)

    MSRC는 전세계 파트너 및 보안 연구원과 함께 작업하여 보안 문제를 방지하고 Microsoft 제품의 보안을 개선합니다.

- [Microsoft 보안 응답 센터 이메일](mailto:secure@microsoft.com)

    Microsoft Azure를 포함하여 Microsoft 보안 취약점의 보고서를 이메일로 보내기

<!--Image references-->
[1]: ./media/azure-security-audit-log-management/sec-security-data-collection-flow.png
[2]: ./media/azure-security-audit-log-management/sec-storage-table-security-log.PNG
[3]: ./media/azure-security-audit-log-management/sec-wad-windows-event-logs-table.png
[4]: ./media/azure-security-audit-log-management/sec-edit-entity.png
[5]: ./media/azure-security-audit-log-management/sec-app-event-log-cmd.png
[6]: ./media/azure-security-audit-log-management/sec-event-viewer.png
[7]: ./media/azure-security-audit-log-management/sec-event-id100.png
[8]: ./media/azure-security-audit-log-management/sec-diagnostics.png
[9]: ./media/azure-security-audit-log-management/sec-event4732.png
[10]: ./media/azure-security-audit-log-management/sec-step6.png
[11]: ./media/azure-security-audit-log-management/sec-process-creation-event.png
[12]: ./media/azure-security-audit-log-management/sec-process-creation-event-storage.png
[13]: ./media/azure-security-audit-log-management/sec-event4946.png
[14]: ./media/azure-security-audit-log-management/sec-event4946-storage.png
[15]: ./media/azure-security-audit-log-management/sec-event-aggregation.png
[16]: ./media/azure-security-audit-log-management/sec-status-code500.png
[17]: ./media/azure-security-audit-log-management/sec-w3c-format.png
[18]: ./media/azure-security-audit-log-management/sec-blob-iis-logs.png
[19]: ./media/azure-security-audit-log-management/sec-view-blob-container.png
[20]: ./media/azure-security-audit-log-management/sec-hdinsight-analysis.png

<!---HONumber=August15_HO8-->