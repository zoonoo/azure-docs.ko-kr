<properties
   pageTitle="독립 실행형 Azure 서비스 패브릭 클러스터 만들기 및 관리 | Microsoft Azure"
   description="Windows Server를 실행하는 컴퓨터(실제 또는 가상)에서 Azure 서비스 패브릭 클러스터를 만들고 관리하는 방법 및 온-프레미스 또는 클라우드에 위치하는지 여부를 알아봅니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="dkshir;chackdan"/>


# Windows Server에서 실행되는 클러스터 만들기 및 관리

Azure 서비스 패브릭을 사용하면 Windows Server를 실행 중인 가상 컴퓨터 또는 컴퓨터에서 서비스 패브릭 클러스터를 만들 수 있습니다. 즉, 온-프레미스 또는 클라우드 공급자에 서로 연결된 일련의 Windows Server 컴퓨터가 있는 환경에서 서비스 패브릭 응용 프로그램을 배포하고 실행할 수 있습니다. 서비스 패브릭은 독립 실행형 Windows Server 패키지라는 서비스 패브릭 클러스터를 만들 수 있는 설치 패키지를 제공합니다.

이 문서에서는 서비스 패브릭 온-프레미스에 독립 실행형 패키지를 사용하여 클러스터를 만드는 단계를 안내하지만 다른 클라우드 공급자와 같은 다른 환경에 쉽게 적용할 수 있습니다.

>[AZURE.NOTE] 이 독립 실행형 Windows Server 패키지는 현재 미리 보기에 포함된 기능을 제공하며, 상용으로 지원되지 않습니다. 미리 보기에 있는 기능 목록을 보려면 이 문서의 맨 아래로 스크롤하세요. EULA의 복사본을 지금 다운로드하려는 경우 [여기를 클릭합니다](http://go.microsoft.com/fwlink/?LinkID=733084).


<a id="getsupport"></a>
## Service Fabric 독립 실행형 패키지에 대한 지원

- [Azure Service Fabric 포럼](https://social.msdn.microsoft.com/Forums/azure/ko-KR/home?forum=AzureServiceFabric?)에서 Windows Server용 Service Fabric 독립 실행형 패키지에 대해 커뮤니티에 문의합니다.

- [Service Fabric에 대한 전문 지원](http://support.microsoft.com/oas/default.aspx?prid=16146)에 대한 티켓을 엽니다. [Microsoft의 전문 지원](https://support.microsoft.com/ko-KR/gp/offerprophone?wa=wsignin1.0)에 대해 자세히 알아보기

<a id="downloadpackage"></a>
## 서비스 패브릭 독립 실행형 패키지 다운로드


*Microsoft.Azure.ServiceFabric.WindowsServer.&lt;버전&gt;.zip*이라는 [Windows Server 2012 R2 이상용 서비스 패브릭에 독립 실행형 패키지를 다운로드합니다](http://go.microsoft.com/fwlink/?LinkId=730690).


다운로드 패키지에서 다음 파일을 찾을 수 있습니다.

|**파일 이름**|**간단한 설명**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|클러스터의 각 컴퓨터에 배포되는 이진 파일을 포함하는 CAB 파일입니다.|
|ClusterConfig.Unsecure.DevCluster.json|클러스터에 있는 각 노드에 대한 정보를 포함하여 3개 노드의 비보안 단일 VM/컴퓨터 개발 클러스터에 대한 설정이 포함된 클러스터 구성 샘플 파일입니다. |
|ClusterConfig.Unsecure.MultiMachine.json|클러스터에 있는 각 컴퓨터에 대한 정보를 포함하여 비보안 다중 VM/컴퓨터 클러스터에 대한 설정이 포함된 클러스터 구성 샘플 파일입니다.|
|ClusterConfig.Windows.DevCluster.json|클러스터에 있는 각 노드에 대한 정보를 포함하여 3개 노드의 보안 단일 VM/컴퓨터 개발 클러스터에 대한 모든 설정이 포함된 클러스터 구성 샘플 파일입니다. 클러스터는 [Windows ID](https://msdn.microsoft.com/library/ff649396.aspx)를 사용하여 보안이 유지됩니다.|
|ClusterConfig.Windows.MultiMachine.json|보안 클러스터에 있는 각 컴퓨터에 대한 정보를 포함하여 보안 다중 VM/컴퓨터 클러스터에 대한 모든 설정이 포함된 클러스터 구성 샘플 파일입니다. 클러스터는 [Windows ID](https://msdn.microsoft.com/library/ff649396.aspx)를 사용하여 보안이 유지됩니다.|
|ClusterConfig.x509.DevCluster.json|클러스터의 각 노드에 대한 정보를 포함하여 3개 노드의 보안 단일 VM/컴퓨터 개발 클러스터에 대한 모든 설정이 포함된 클러스터 구성 샘플 파일입니다. 클러스터는 x509 인증서를 사용하여 보안이 유지됩니다.|
|ClusterConfig.x509.MultiMachine.json|보안 클러스터의 각 노드에 대한 정보를 포함하여 보안 다중 VM/컴퓨터 클러스터에 대한 모든 설정이 포함된 클러스터 구성 샘플 파일입니다. 클러스터는 x509 인증서를 사용하여 보안이 유지됩니다.|
|EULA\_ENU.txt|Microsoft Azure Service Fabric 독립 실행형 Windows Server 패키지를 사용하기 위한 사용 조건입니다. EULA의 복사본을 지금 다운로드하려는 경우 [여기를 클릭합니다](http://go.microsoft.com/fwlink/?LinkID=733084).|
|Readme.txt|릴리스 정보 및 기본 설치 지침에 연결합니다. 이 페이지에 포함된 지침의 일부입니다.|
|CreateServiceFabricCluster.ps1|ClusterConfig.json 파일에서 설정을 사용하여 클러스터를 만드는 PowerShell 스크립트입니다.|
|RemoveServiceFabricCluster.ps1|ClusterConfig.json에서 설정을 사용하여 클러스터를 제거하는 PowerShell 스크립트입니다.|
|ThirdPartyNotice.rtf |패키지에 있는 타사 소프트웨어에 대한 고지입니다.|
|AddNode.ps1|기존 배포된 클러스터에 노드를 추가하는 PowerShell 스크립트입니다.|
|RemoveNode.ps1|기존 배포된 클러스터에서 노드를 제거하는 PowerShell 스크립트입니다.|
|CleanFabric.ps1|현재 컴퓨터에서 독립 실행형 패브릭 설치를 정리하기 위한 PowerShell 스크립트입니다. 이전 MSI 설치는 자체 연결된 제거 프로그램을 사용하여 제거됩니다.|
|TestConfiguration.ps1|cluster.JSON에 지정된 대로 인프라를 분석하는 PowerShell 스크립트입니다.|


## 클러스터 배포를 위한 계획 및 준비
클러스터를 만들기 전에 다음 단계를 수행합니다.

### 1단계: 클러스터 인프라 계획
클러스터가 견뎌낼 수 있는 실패의 종류를 결정할 수 있도록 소유한 컴퓨터에서 서비스 패브릭 클러스터를 만들려고 합니다. 예를 들어 이러한 컴퓨터를 제공하는 전원 줄 또는 인터넷 연결을 구분해야 하나요? 또한 이러한 컴퓨터의 물리적 보안을 고려합니다. 물리적 위치는 어디이며 액세스가 필요한 사용자는 누구인가요? 이러한 결정을 내리면 다양한 오류 도메인에 컴퓨터를 논리적으로 매핑할 수 있습니다(자세한 내용을 보려면 아래로 스크롤). 프로덕션 클러스터에 대한 인프라 계획은 테스트 클러스터보다 더 복잡합니다.

<a id="preparemachines"></a>
### 2단계: 컴퓨터를 준비하여 필수 구성 요소 충족
클러스터에 추가하려는 각 컴퓨터에 대한 필수 구성 요소:

- 최소 16GB의 RAM을 사용하는 것이 좋습니다.
- 최소 40GB의 사용 가능한 디스크 공간이 있는 것이 좋습니다.
- 최소 4 코어 CPU가 있는 것이 좋습니다.
- 네트워크 연결: 컴퓨터가 안전한 네트워크 상에 있는지 확인합니다.
- Windows Server 2012 R2 또는 Windows Server 2012([KB2858668](https://support.microsoft.com/kb/2858668)을 설치해야 함).
- [.NET Framework 4.5.1 이상](https://www.microsoft.com/download/details.aspx?id=40773), 전체 설치.
- [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell).
- 클러스터를 배포하고 구성하는 클러스터 관리자는 각 컴퓨터에서 [관리자 권한](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx)이 있어야 합니다.
- [RemoteRegistry 서비스](https://technet.microsoft.com/library/cc754820)는 모든 컴퓨터에서 실행되어야 합니다.
- 도메인 컨트롤러에 서비스 패브릭을 설치할 수 없습니다.

### 3단계: 초기 클러스터 크기 결정
독립 실행형 서비스 패브릭 클러스터에 있는 각 노드는 배포된 서비스 패브릭 런타임을 가지며 클러스터의 구성원입니다. 일반적인 프로덕션 배포에서는 OS 인스턴스당(실제 또는 가상) 하나의 노드가 있습니다. 클러스터 크기는 비즈니스 요구에 따라 결정됩니다. 그러나 최소 클러스터 크기인 세 개의 노드가 있어야 합니다(컴퓨터/VM). 개발 용도로 지정된 컴퓨터에 하나 이상의 노드를 보유할 수 있습니다. 프로덕션 환경에서 서비스 패브릭은 실제 또는 가상 컴퓨터당 하나의 노드만을 지원합니다.

### 4단계: 장애 도메인 및 업그레이드 도메인 수 확인
**FD(장애 도메인)**는 실패의 물리적 단위이며 데이터 센터의 물리적 인프라에 직접 연관됩니다. 장애 도메인은 실패한 단일 지점이 공유하는 하드웨어 구성 요소(컴퓨터, 스위치, 네트워크 등)로 구성됩니다. 쉽게 말해 장애 도메인과 랙 간에 1:1 매핑이 없지만 각 랙은 장애 도메인으로 간주될 수 있습니다. 클러스터에서 노드를 고려하는 경우 노드가 세 개 이상의 장애 도메인에 배포되는 것이 좋습니다.

FD를 *ClusterConfig.json*에 지정하는 경우 각 FD의 이름을 선택할 수 있습니다. 서비스 패브릭은 내부에서 인프라 토폴로지를 반영할 수 있도록 계층적 FD를 지원합니다. 예를 들어 다음 FD가 유효합니다.

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


**UD(업그레이드 도메인)**는 노드의 논리적 단위입니다. 서비스 패브릭이 업그레이드(응용 프로그램 업그레이드 또는 클러스터 업그레이드)를 조정하는 동안 다른 UD의 노드를 요청을 처리하는 데 계속 사용할 수 있도록 업그레이드를 수행하기 위해 UD의 모든 노드를 사용합니다. 컴퓨터에 수행하는 펌웨어 업그레이드는 UD를 인식하지 못하므로 한 번에 하나의 컴퓨터를 수행해야 합니다.

이러한 개념에 대해 생각하는 가장 간단한 방법은 FD를 계획되지 않은 오류의 단위로, UD를 계획된 유지 관리의 단위로 인식하는 것입니다.

UD를 *ClusterConfig.json*에 지정하는 경우 각 UD의 이름을 선택할 수 있습니다. 예를 들어 다음이 유효합니다.

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

업그레이드 도메인 및 장애 도메인에 대한 자세한 내용은 [서비스 패브릭 클러스터 설명](service-fabric-cluster-resource-manager-cluster-description.md) 문서를 읽어보세요.

### 5단계: Windows Server용 서비스 패브릭 독립 실행형 패키지 다운로드
[Windows Server용 서비스 패브릭 독립 실행형 패키지를 다운로드](http://go.microsoft.com/fwlink/?LinkId=730690)하고 클러스터의 일부가 아닌 배포 컴퓨터 또는 클러스터의 일부인 컴퓨터 중 하나에 패키지의 압축을 풉니다.

<a id="createcluster"></a>
## 클러스터 만들기

위에서 계획 및 준비 섹션에 설명된 단계를 완료한 후에 클러스터를 만들 준비가 되었습니다.

### 1단계: 클러스터 구성 수정
클러스터는 *ClusterConfig.json* 파일에 설명되어 있습니다. 이 파일의 섹션에 대한 자세한 내용은 [독립 실행형 Windows 클러스터에 대한 구성 설정](service-fabric-cluster-manifest.md) 문서를 읽어보세요. 다운로드한 패키지에서 *ClusterConfig.json* 파일 중 하나를 열고 다음 설정을 수정합니다.

|**구성 설정**|**설명**|
|-----------------------|--------------------------|
|**NodeTypes**|노드 유형을 사용하면 클러스터 노드를 다양한 그룹으로 구분할 수 있습니다. 클러스터에는 하나 이상이 NodeType이 있어야 합니다. 그룹의 모든 노드는 다음과 같은 일반적인 특징이 있습니다. <br> **이름** - 노드 유형 이름입니다. <br>**끝점 포트** - 이 노드 유형과 연결된 다양한 이름의 끝점(포트)입니다. 이 매니페스트의 다른 요소와 충돌하지 않으며 컴퓨터/VM에서 실행 중인 다른 응용 프로그램에서 사용하지 않는 한 원하는 모든 포트 번호를 사용할 수 있습니다. <br> **Placement Properties** - 이는 시스템 서비스 또는 서비스에 대한 배치 제약 조건으로 사용할 다음 노드 유형에 대한 속성을 설명합니다. 이런 속성은 주어진 노드에 대한 여분의 메타데이터를 제공하는 사용자 정의 키/값 쌍입니다. 노드 속성의 예로는 노드에 하드 드라이브 또는 그래픽 카드가 있는지 여부, 하드 드라이브, 코어에 포함된 스핀들 수, 기타 물리 속성이 있습니다. <br> **용량** - 노드 용량은 특정 노드가 사용할 수 있는 특정 리소스의 이름과 양을 정의합니다. 예를 들어 노드에는 "MemoryInMb"라는 메트릭에 대한 용량 및 기본적으로 사용할 수 있는 2048MB가 있음을 정의할 수 있습니다. 이러한 용량은 특정한 양의 리소스를 필요로 하는 서비스가 필요한 양을 사용 가능한 해당 리소스로 노드에 배치되도록 런타임 시 사용됩니다.<br>**IsPrimary** - 둘 이상의 정의된 NodeType이 있는 경우 하나만 *true* 값을 사용하여 기본으로 설정되며 여기에서 시스템 서비스가 실행됩니다. 다른 모든 노드 유형은 *false* 값으로 설정되어야 합니다.|
|**노드**|클러스터의 일부인 노드 각각에 대한 세부 정보입니다(노드 유형, 노드 이름, IP 주소, 노드의 장애 도메인 및 업그레이드 도메인). 클러스터를 만들려는 컴퓨터는 여기서 해당 IP 주소로 나열되어야 합니다. <br> 모든 노드에 동일한 IP 주소를 사용하는 경우 다음 one-box 클러스터가 만들어지며 테스트를 목적으로 사용할 수 있습니다. one-box 클러스터는 프로덕션 워크로드를 배포하는 데 사용하지 마세요.|

### 5단계: TestConfiguration 스크립트 실행

이 스크립트는 cluster.JSON에 정의된 대로 인프라를 테스트하여 필요한 권한이 있는지, 컴퓨터가 서로 연결되어 있는지 등을 확인하여 배포가 성공할 수 있도록 합니다. 기본적으로 작은 크기의 모범 사례 분석기입니다. 시간이 지남에 따라 이 도구에 더욱 많은 유효성 검사 기능을 계속 추가하여 더욱 견고하게 만들 예정입니다.

이 스크립트는 클러스터 구성 파일에서 노드로 나열된 모든 컴퓨터에 대한 관리자 액세스 권한이 있는 모든 컴퓨터에서 실행될 수 있습니다. 이 스크립트가 실행되는 컴퓨터는 클러스터의 일부일 수도 있고 일부가 아닐 수도 있습니다.

```powershell

PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer.5.3.202.9494> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer.5.3.202.9494\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True 


```

### 3단계: 클러스터 만들기 스크립트 실행
JSON 문서에서 클러스터 구성을 수정하고 여기에 모든 노드 정보를 추가하면 패키지 폴더에서 클러스터 만들기 *CreateServiceFabricCluster.ps1* PowerShell 스크립트를 실행하고 JSON 구성 파일에 대한 경로를 전달하고 EULA에 동의합니다.

이 스크립트는 클러스터 구성 파일에서 노드로 나열된 모든 컴퓨터에 대한 관리자 액세스 권한이 있는 모든 컴퓨터에서 실행될 수 있습니다. 이 스크립트가 실행되는 컴퓨터는 클러스터의 일부일 수도 있고 일부가 아닐 수도 있습니다.

```
#Create an unsecured local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```
```
#Create an unsecured multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

>[AZURE.NOTE] 배포 로그는 CreateServiceFabricCluster Powershell을 실행하는 VM/컴퓨터에서 로컬로 사용할 수 있습니다. 이러한 로그는 Powershell 명령을 실행한 폴더의 "DeploymentTraces"라는 하위 폴더에 있습니다. 또한 컴퓨터에 서비스 패브릭이 올바르게 배포되었는지 여부를 확인하려면 C:\\ProgramData 디렉터리에서 설치된 파일을 찾을 수 있으며 작업 관리자에서 실행 중인 FabricHost.exe 및 Fabric.exe 프로세스를 볼 수 있습니다.

### 4단계: 클러스터에 연결

보안 클러스터에 연결하기 위한 지침은 [이 문서](service-fabric-connect-to-secure-cluster.md)를 참조하세요.

비보안 클러스터에 연결하려면 다음 PowerShell 명령을 실행합니다.

```powershell

Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>

Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000

```
### 5단계: Service Fabric Explorer 표시

이제 http://localhost:19080/Explorer/index.html을 사용하여 컴퓨터 중 하나에서 직접 또는 http://<*IPAddressofaMachine*>:19080/Explorer/index.html을 사용하여 원격으로 Service Fabric Explorer를 사용하여 클러스터에 연결할 수 있습니다.



## 클러스터에 노드 추가 및 제거

비즈니스 요구가 변경됨에 따라 독립 실행형 서비스 패브릭 클러스터에 노드를 추가 또는 제거할 수 있습니다. 자세한 단계는 [서비스 패브릭 독립 실행형 클러스터에 노드 추가 또는 제거](service-fabric-cluster-windows-server-add-remove-nodes.md)를 참조하세요.


## 클러스터 제거

클러스터를 제거하려면 패키지 폴더에서 *RemoveServiceFabricCluster.ps1* Powershell 스크립트를 실행하고 JSON 구성 파일에 대한 경로를 제공하고 경우에 따라 삭제 로그 위치를 지정합니다.

이 스크립트는 클러스터 구성 파일에서 노드로 나열된 모든 컴퓨터에 대한 관리자 액세스 권한이 있는 모든 컴퓨터에서 실행될 수 있습니다. 이 스크립트가 실행되는 컴퓨터는 클러스터의 일부일 수도 있고 일부가 아닐 수도 있습니다.

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   
```

<a id="telemetry"></a>
## 수집된 원격 분석 데이터 및 옵트아웃(opt out)

기본적으로 이 제품은 제품을 개선하기 위해 서비스 패브릭 사용 현황에 대한 원격 분석을 수집합니다. 설치의 일부로 실행되는 모범 사례 분석기는 [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1)에 대한 연결을 확인하고 연결할 수 없는 경우 원격 분석을 옵트아웃하지 않으면 설치가 실패합니다.

1) 원격 분석 파이프라인은 하루에 한 번 다음 데이터를 [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1)에 업로드하려고 시도합니다. 최상의 업로드이며 클러스터 기능에는 영향이 없습니다. 원격 분석은 장애 조치(Failover) 관리자 기본이 실행되는 노드에서만 전송됩니다. 다른 노드에서는 원격 분석이 전송되지 않습니다.

2) 원격 분석은 다음으로 구성됩니다.

1.            서비스 수
1.            ServiceTypes 수
1.            응용 프로그램 수
1.            ApplicationUpgrades 수
1.            FailoverUnits 수
1.            InBuildFailoverUnits 수
1.            UnhealthyFailoverUnits 수
1.            복제본 수
1.            InBuildReplicas 수
1.            StandByReplicas 수
1.            OfflineReplicas 수
1.            CommonQueueLength
1.            QueryQueueLength
1.            FailoverUnitQueueLength
1.            CommitQueueLength
1.            노드 수
1.            IsContextComplete: True/False
1.            ClusterId: 각 클러스터에 대해 임의로 생성된 GUID
1.            ServiceFabricVersion
1.             원격 분석을 업로드한 VM/컴퓨터의 IP 주소


원격 분석을 비활성화하려면 클러스터 config: "enableTelemetry": false의 "properties" 요소에 다음의 내용을 추가합니다.

<a id="previewfeatures"></a>
## 이 패키지에 포함된 미리 보기 기능

없음.

## 다음 단계
- [독립 실행형 Windows 클러스터에 대한 구성 설정](service-fabric-cluster-manifest.md)
- [독립 실행형 서비스 패브릭 클러스터에 노드 추가 또는 제거](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Windows를 실행하는 Azure VM에서 독립 실행형 서비스 패브릭 클러스터 만들기](service-fabric-cluster-creation-with-windows-azure-vms.md)
- [Windows 보안을 사용하여 독립 실행형 클러스터 보호](service-fabric-windows-cluster-windows-security.md)
- [X509 인증서를 사용하여 Windows에서 독립 실행형 클러스터 보호](service-fabric-windows-cluster-x509-security.md)


<!--Image references-->
[TrustedZone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png

<!---HONumber=AcomDC_0928_2016-->