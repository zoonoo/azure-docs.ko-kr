<properties
   pageTitle="온-프레미스 또는 클라우드 Azure 서비스 패브릭 클러스터 만들기 | Microsoft Azure"
   description="Windows Server를 실행하는 컴퓨터(실제 또는 가상)에서 Azure 서비스 패브릭 클러스터를 만드는 방법 및 온-프레미스 또는 클라우드에 위치하는지 여부를 알아봅니다."
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
   ms.date="07/05/2016"
   ms.author="chackdan"/>


# Windows Server에서 실행되는 클러스터 만들기

Azure 서비스 패브릭을 사용하면 Windows Server를 실행 중인 가상 컴퓨터 또는 컴퓨터에서 서비스 패브릭 클러스터를 만들 수 있습니다. 즉, 온-프레미스 또는 클라우드 공급자에 서로 연결된 일련의 Windows Server 컴퓨터가 있는 환경에서 서비스 패브릭 응용 프로그램을 배포하고 실행할 수 있습니다. 서비스 패브릭은 독립 실행형 Windows Server 패키지라는 서비스 패브릭 클러스터를 만들 수 있는 설치 패키지를 제공합니다.

이 문서에서는 서비스 패브릭 온-프레미스에 독립 실행형 패키지를 사용하여 클러스터를 만드는 단계를 안내하지만 다른 클라우드 공급자와 같은 다른 환경에 쉽게 적용할 수 있습니다.

>[AZURE.NOTE] 이 독립 실행형 Windows Server 패키지는 현재 미리 보기로 제공되며, 프로덕션 작업에 지원되지 않습니다. EULA의 복사본을 지금 다운로드하려는 경우 [여기를 클릭합니다](http://go.microsoft.com/fwlink/?LinkID=733084).

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
|EULA.txt|Microsoft Azure Service Fabric 독립 실행형 Windows Server 패키지를 사용하기 위한 사용 조건입니다. EULA의 복사본을 지금 다운로드하려는 경우 [여기를 클릭합니다](http://go.microsoft.com/fwlink/?LinkID=733084).|
|Readme.txt|릴리스 정보 및 기본 설치 지침에 연결합니다. 이 페이지에서 찾을 수 있는 지침의 하위 집합입니다.|
|CreateServiceFabricCluster.ps1|ClusterConfig.json 파일에서 설정을 사용하여 클러스터를 만드는 PowerShell 스크립트입니다.|
|RemoveServiceFabricCluster.ps1|ClusterConfig.json에서 설정을 사용하여 클러스터를 제거하는 PowerShell 스크립트입니다.|
|AddNode.ps1|기존 배포된 클러스터에 노드를 추가하는 PowerShell 스크립트입니다.|
|RemoveNode.ps1|기존 배포된 클러스터에서 노드를 제거하는 PowerShell 스크립트입니다.|


## 클러스터 배포를 위한 계획 및 준비
클러스터를 만들기 전에 다음 단계를 수행해야 합니다.

### 1단계: 클러스터 인프라 계획
클러스터가 견뎌낼 수 있는 실패의 종류를 결정하도록 소유한 컴퓨터에서 서비스 패브릭 클러스터를 만들려고 합니다. 예를 들어 이러한 컴퓨터를 제공하는 전원 줄 또는 인터넷 연결을 구분해야 하나요? 또한 이러한 컴퓨터의 물리적 보안을 고려해 야 합니다. 물리적 위치는 어디이며 액세스가 필요한 사용자는 누구인가요? 이러한 결정을 내리면 다양한 오류 도메인에 컴퓨터를 논리적으로 매핑합니다(자세한 내용은 아래 참조). 프로덕션 클러스터에 대한 인프라 계획은 테스트 클러스터보다 더 복잡합니다.

### 2단계: 컴퓨터를 준비하여 필수 구성 요소 충족
클러스터에 추가하려는 각 컴퓨터에 대한 필수 구성 요소:

- 최소 2GB 메모리를 사용하는 것이 좋습니다.
- 네트워크 연결. 컴퓨터가 안전한 네트워크 상에 있는지 확인합니다
- Windows Server 2012 R2 또는 Windows Server 2012(이를 위해 KB2858668를 설치해야 함).
- .NET Framework 4.5.1 이상, 전체 설치
- Windows PowerShell 3.0
- 클러스터를 배포하고 구성하는 클러스터 관리자는 각 컴퓨터에서 관리자 권한이 있어야 합니다.
- RemoteRegistry 서비스는 모든 컴퓨터에서 실행되어야 합니다.

### 3단계: 초기 클러스터 크기 결정
각 노드는 배포된 서비스 패브릭 런타임을 가지며 클러스터의 구성원입니다. 일반적인 프로덕션 배포에서는 OS 인스턴스당(실제 또는 가상) 하나의 노드가 있습니다. 클러스터 크기는 비즈니스 요구에 따라 결정됩니다. 그러나 최소 클러스터 크기인 세 개의 노드가 있어야 합니다(컴퓨터/VM). 개발 용도로 지정된 컴퓨터에 하나 이상의 노드를 보유할 수 있습니다. 프로덕션 환경에서 서비스 패브릭은 실제 또는 가상 컴퓨터당 하나의 노드만을 지원합니다.

### 4단계: 장애 도메인 및 업그레이드 도메인 수 확인
**FD(장애 도메인)**는 실패의 물리적 단위이며 데이터 센터의 물리적 인프라에 직접 연관됩니다. 장애 도메인은 실패한 단일 지점이 공유하는 하드웨어 구성 요소(컴퓨터, 스위치, 네트워크 등)로 구성됩니다. 쉽게 말해 장애 도메인과 랙 간에 1:1 매핑이 없지만 각 랙은 장애 도메인으로 간주될 수 있습니다. 클러스터에서 노드를 고려하는 경우 노드가 세 개 이상의 장애 도메인에 배포되는 것이 좋습니다.

FD를 *ClusterConfig.json*에 지정하는 경우 각 FD의 이름을 선택합니다. 서비스 패브릭은 내부에서 인프라 토폴로지를 반영할 수 있도록 계층적 FD를 지원합니다. 예를 들어 다음 FD가 유효합니다.

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


**UD(업그레이드 도메인)**는 노드의 논리적 단위입니다. 서비스 패브릭이 업그레이드(응용 프로그램 업그레이드 또는 클러스터 업그레이드)를 조정하는 동안 다른 UD의 노드를 요청을 처리하는 데 계속 사용할 수 있도록 업그레이드를 수행하기 위해 UD의 모든 노드를 사용합니다. 컴퓨터에 수행하는 펌웨어 업그레이드는 UD를 인식하지 못하므로 한 번에 하나의 컴퓨터를 수행해야 합니다.

이러한 개념에 대해 생각하는 가장 간단한 방법은 FD를 계획되지 않은 오류의 단위로, UD를 계획된 유지 관리의 단위로 인식하는 것입니다.

UD를 *ClusterConfig.json*에 지정하는 경우 각 UD의 이름을 선택합니다. 예를 들어 다음이 유효합니다.

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
|**NodeTypes**|노드 유형을 사용하면 클러스터 노드를 다양한 그룹으로 구분할 수 있습니다. 클러스터에는 하나 이상이 NodeType이 있어야 합니다. 그룹의 모든 노드는 다음과 같은 일반적인 특징이 있습니다. <br> **이름** - 노드 유형 이름입니다. <br>**끝점 포트** - 이 노드 유형과 연결된 다양한 이름의 끝점(포트)입니다. 이 매니페스트의 다른 요소와 충돌하지 않으며 컴퓨터/VM에서 실행 중인 다른 응용 프로그램에서 사용하지 않는 한 원하는 모든 포트 번호를 사용할 수 있습니다. <br> **PlacementProperties** - 이는 시스템 서비스 또는 서비스에 대한 배치 제약 조건으로 사용할 다음 노드 유형에 대한 속성을 설명합니다. 이런 속성은 주어진 노드에 대한 여분의 메타데이터를 제공하는 사용자 정의 키/값 쌍입니다. 노드 속성의 예로는 노드에 하드 드라이브 또는 그래픽 카드가 있는지 여부, 하드 드라이브, 코어에 포함된 스핀들 수, 기타 물리 속성이 있습니다. <br> **용량** - 노드 용량은 특정 노드가 사용할 수 있는 특정 리소스의 이름과 양을 정의합니다. 예를 들어 노드에는 "MemoryInMb"라는 메트릭에 대한 용량 및 기본적으로 사용할 수 있는 2048MB가 있음을 정의할 수 있습니다. 이러한 용량은 특정한 양의 리소스를 필요로 하는 서비스가 아직 사용 가능한 해당 리소스로 노드에 배치되도록 런타임 시 사용됩니다.<br>**IsPrimary** - 둘 이상의 정의된 NodeType이 있는 경우 하나만 *true* 값을 사용하여 기본으로 설정되며 여기에서 시스템 서비스가 실행됩니다. 다른 모든 노드 유형은 *false* 값으로 설정되어야 합니다.|
|**노드**|클러스터의 일부인 노드 각각에 대한 세부 정보입니다(노드 유형, 노드 이름, IP 주소, 노드의 장애 도메인 및 업그레이드 도메인). 클러스터를 만들려는 컴퓨터는 여기서 해당 IP 주소로 나열되어야 합니다. <br> 모든 노드에 동일한 IP 주소를 사용하는 경우 다음 one-box 클러스터가 만들어지며 테스트를 목적으로 사용할 수 있습니다. one-box 클러스터는 프로덕션 워크로드를 배포하는 데 사용하지 않아야 합니다.|

### 2단계: 클러스터 만들기 스크립트 실행
JSON 문서에서 클러스터 구성을 수정하고 여기에 모든 노드 정보를 추가하면 패키지 폴더에서 클러스터 만들기 *CreateServiceFabricCluster.ps1* PowerShell 스크립트를 실행하고 JSON 구성 파일에 대한 경로 및 패키지 CAB 파일의 위치에 전달합니다.

이 스크립트는 클러스터 구성 파일에서 노드로 나열된 모든 컴퓨터에 대한 관리자 액세스 권한이 있는 모든 컴퓨터에서 실행될 수 있습니다. 이 스크립트가 실행되는 컴퓨터는 클러스터의 일부일 수도 있고 일부가 아닐 수도 있습니다.

```
#Create an unsecure local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```
```
#Create an unsecure multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```

>[AZURE.NOTE] 배포 로그는 CreateServiceFabricCluster Powershell을 실행하는 VM/컴퓨터에서 로컬로 사용할 수 있습니다. 이러한 로그는 Powershell 명령을 실행한 폴더의 "DeploymentTraces"라는 하위 폴더에서 찾을 수 있습니다. 또한 컴퓨터에 서비스 패브릭이 올바르게 배포되었는지 여부를 확인하려면 C:\\ProgramData 디렉터리에서 설치된 파일을 찾을 수 있으며 작업 관리자에서 실행 중인 FabricHost.exe 및 Fabric.exe 프로세스를 볼 수 있습니다.

### 3단계: 클러스터에 연결
이제 http://localhost:19080/Explorer/index.html을(를) 사용하여 컴퓨터 중 하나에서 직접 또는 http://<*IPAddressofaMachine*>:19080/Explorer/index.html을 사용하여 원격으로 Service Fabric Explorer를 사용하여 클러스터에 연결할 수 있습니다.

## 클러스터에 노드 추가

1. 클러스터에 추가하려는 VM/컴퓨터를 준비합니다(계획의 2단계 참조 및 위의 클러스터 배포 섹션 준비).
2. 이 VM/컴퓨터를 추가하려는 장애 도메인 및 업그레이드 도메인으로 계획합니다.
3. [Windows Server용 서비스 패브릭에 대한 독립 실행형 패키지를 복사 또는 다운로드](http://go.microsoft.com/fwlink/?LinkId=730690)하고 클러스터에 추가하려는 VM/컴퓨터에 패키지 압축을 풉니다.
4. Powershell 관리자 프롬프트를 열고 압축을 푼 패키지의 위치로 이동합니다.
5. 추가할 새 노드를 설명하는 매개 변수를 사용하여 *AddNode.ps1* Powershell을 실행합니다. 아래 예제에서는 NodeType0 유형, IP 주소 182.17.34.52를 사용하여 VM5라는 새 노드를 UD1 및 FD1에 추가합니다. *ExistingClusterConnectionEndPoint*는 기존 클러스터에 이미 있는 노드에 대한 연결 끝점입니다. 클러스터에서 이에 대한 *모든* 노드 IP 주소를 선택할 수 있습니다.

```
.\AddNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClusterConnectionEndPoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1
```

## 클러스터에서 노드 제거

1. 클러스터에서 제거하려는 VM/컴퓨터로 RDP(원격 데스크톱).
2. [Windows Server용 서비스 패브릭에 대한 독립 실행형 패키지를 복사 또는 다운로드](http://go.microsoft.com/fwlink/?LinkId=730690)하고 클러스터에 추가하려는 VM/컴퓨터에 패키지 압축을 풉니다.
3. Powershell 관리자 프롬프트를 열고 압축을 푼 패키지의 위치로 이동합니다.
4. *RemoveNode.ps1* Powershell을 실행합니다. 아래 예제에서는 클러스터에서 현재 노드를 제거합니다. *ExistingClusterConnectionEndPoint*는 기존 클러스터에 이미 있는 노드에 대한 연결 끝점입니다. 클러스터에서 이에 대한 *모든* 노드 IP 주소를 선택할 수 있습니다.

```
.\RemoveNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -ExistingClusterConnectionEndPoint 182.17.34.50:19000
```

## 클러스터 제거
클러스터를 제거하려면 패키지 폴더에서 *RemoveServiceFabricCluster.ps1* Powershell 스크립트를 실행하고 JSON 구성 파일에 대한 경로 및 패키지 CAB 파일의 위치에 전달합니다.

이 스크립트는 클러스터 구성 파일에서 노드로 나열된 모든 컴퓨터에 대한 관리자 액세스 권한이 있는 모든 컴퓨터에서 실행될 수 있습니다. 이 스크립트가 실행되는 컴퓨터는 클러스터의 일부일 수도 있고 일부가 아닐 수도 있습니다.

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
```

## 방법: Azure IaaS VM으로 3개의 노드 클러스터 만들기
다음 단계는 독립 실행형 Windows Server 설치 관리자를 사용하여 Azure IaaS VM에 클러스터를 만드는 방법을 설명합니다. 이 IaaS 클러스터의 서비스 패브릭 런타임은 서비스 패브릭 리소스 공급자에 의해 업그레이드된 Azure 포털을 통해 만든 클러스터와 달리 사용자가 전적으로 관리합니다.

1. Azure 포털에 로그인하고 리소스 그룹에 새 Windows Server 2012 R2 Datacenter VM을 만듭니다.
2. 동일한 리소스 그룹에 두 개의 Windows Server 2012 R2 Datacenter VM을 더 추가합니다. 각 VM은 만들어질 때 동일한 관리 사용자 이름 및 암호를 갖습니다. 만들어지면 동일한 가상 네트워크에 3개의 모든 VM이 표시됩니다.
3. 각 VM에 연결하고 서버 관리자, 로컬 서버 대시보드를 사용하여 Windows 방화벽을 끕니다. 이렇게 하면 네트워크 트래픽은 컴퓨터 간에 통신할 수 있습니다. 각 컴퓨터에서 명령 프롬프트를 열고 *ipconfig*를 입력하여 IP 주소를 가져옵니다. 또는 Azure 포털에서 리소스 그룹에 대한 가상 네트워크 리소스를 선택하여 각 컴퓨터의 IP 주소를 볼 수 있습니다.
4. 컴퓨터 중 하나에 연결하고 다른 두 컴퓨터를 성공적으로 ping할 수 있는지 테스트합니다.
5. VM 중 하나에 연결하고 컴퓨터의 새 폴더에 독립 실행형 Windows Server 패키지를 다운로드하고 패키지의 압축을 풉니다.
6. 메모장에서 *ClusterConfig.Unsecure.MultiMachine.json* 파일을 열고 컴퓨터의 세 개의 IP 주소로 각 노드를 편집하고 위쪽에서 클러스터 이름을 변경하고 파일을 저장합니다. 클러스터 매니페스트의 부분적인 예는 각 컴퓨터의 IP 주소를 표시하는 다음과 같습니다.

    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```

7. Powershell ISE 창을 열고 독립 실행형 설치 관리자 패키지를 다운로드하고 압축을 풀고 위의 매니페스트 파일을 저장한 폴더로 이동합니다. 다음 Powershell 명령을 실행합니다.

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. Powershell 실행이 표시됩니다. 각 컴퓨터에 연결하고 클러스터를 만듭니다. 약 1분 후 컴퓨터 IP 주소 중 하나(예: http://10.7.0.5:19080/Explorer/index.html)에서 Service Fabric Explorer에 연결하여 클러스터가 작동되는지 테스트할 수 있습니다. 이는 IaaS VM의 독립 실행형 클러스터이기 때문에 안전하게 하려는 경우 VM에 인증서를 배포하거나 온-프레미스에 수행하는 것처럼 Windows 인증에 대한 Windows Server AD(Active Directory) 컨트롤러로 컴퓨터 중 하나를 설정해야 합니다. 보안 클러스터 설정은 아래의 다음 단계를 참조하세요.

## 다음 단계
- [Windows Server 또는 Linux에서 독립 실행형 서비스 패브릭 클러스터 만들기](service-fabric-deploy-anywhere.md)
- [독립 실행형 Windows 클러스터에 대한 구성 설정](service-fabric-cluster-manifest.md)

- [Windows 보안을 사용하여 독립 실행형 클러스터 보호](service-fabric-windows-cluster-windows-security.md)
- [X509 인증서를 사용하여 Windows에서 독립 실행형 클러스터 보호](service-fabric-windows-cluster-x509-security.md)

<!---HONumber=AcomDC_0713_2016-->