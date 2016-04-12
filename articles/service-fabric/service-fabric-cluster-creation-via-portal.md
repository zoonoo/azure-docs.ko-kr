<properties
   pageTitle="Azure 포털에서 서비스 패브릭 클러스터 만들기 | Microsoft Azure"
   description="Azure 포털에서 서비스 패브릭 클러스터를 만듭니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/28/2016"
   ms.author="chackdan"/>


# Azure 포털에서 서비스 패브릭 클러스터 만들기

이 페이지에서 Azure 서비스 패브릭 클러스터를 설정할 수 있습니다. 사용자의 구독에 이 클러스터를 형성할 IaaS VM을 배포하기에 충분한 코어가 있어야 합니다.


## 서비스 패브릭 클러스터 리소스 검색

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

2. **+ 새로 만들기**를 클릭하여 새 리소스 템플릿을 추가합니다. **마켓플레이스**의 **모두** 아래에서 템플릿을 검색합니다. 이를 **서비스 패브릭 클러스터**라고 합니다.

    a. 최상위 수준에서 **마켓플레이스**를 클릭합니다.

    b. **모두** 아래에서 "패브릭"을 입력하고 Enter 키를 누릅니다. 가끔 자동 필터가 작동하지 않는 경우 Enter 키를 눌러야 합니다. ![Azure 포털에서 서비스 패브릭 클러스터 템플릿 검색 스크린샷][SearchforServiceFabricClusterTemplate]

3. 목록에서 **서비스 패브릭 클러스터**를 선택합니다.

4. **서비스 패브릭 클러스터** 블레이드로 이동하여 **만들기**를 클릭합니다.

5. 이제 4단계의 **서비스 패브릭 클러스터 만들기** 블레이드가 표시됩니다.

## 1단계 - 기본 사항

기본 사항 블레이드에서는 클러스터에 대한 기본 세부 정보를 제공해야 합니다.

1. 클러스터 이름을 입력합니다.

2. VM 원격 데스크톱의 **사용자 이름** 및 **암호**를 선택합니다.

3. 클러스터가 배포될 **구독**을 선택합니다. 특히 구독이 여러 개인 경우 반드시 선택해야 합니다.

4. **새 리소스 그룹**을 만듭니다. 클러스터 이름과 같은 이름을 사용하면 나중에 쉽게 찾을 수 있고, 특히 배포를 변경하거나 클러스터를 삭제하려고 할 때 매우 유용합니다.

    >[AZURE.NOTE] 기존 리소스 그룹을 사용해도 되지만 새 리소스 그룹을 만드는 것이 좋습니다. 이렇게 하면 필요하지 않은 클러스터를 쉽게 삭제할 수 있습니다.

 	![새 리소스 그룹 만들기 스크린샷][CreateRG]


5. 드롭다운 목록에서 **위치**를 선택합니다. 기본값은 **미국 서부**입니다. 확인을 누릅니다.

## 2단계 - 클러스터 구성

10. 먼저 **노드 유형**이 무엇인지 설명하겠습니다. 노드 유형은 클라우드 서비스의 역할과 똑같이 보일 수 있습니다. 노드 유형에서 VM 크기, VM 수 및 VM 속성을 정의합니다. 클러스터는 둘 이상의 노드 유형을 가질 수 있지만 주 노드 유형(포털에서 정의하는 첫 번째 노드)에는 최소한 5개의 VM이 있어야 하며, 서비스 패브릭 시스템 서비스가 배치된 노드 유형입니다. 다음을 고려하여 여러 노드 유형이 필요한지 결정합니다.

	* 배포하려는 응용 프로그램에 프런트 엔드 서비스 및 백 엔드 서비스가 포함되어 있습니다. 인터넷에 대 포트가 열려 있는 비교적 작은 VM(D2 같은 VM 크기)에 프런트 엔드 서비스를 배치하고, 인터넷에 연결되지 않은 비교적 큰 VM(D4, D6, D15 등과 같은 VM 크기)에 계산 집약적인 백 엔드 서비스를 배치하려고 합니다.

	* 하나의 노드 유형에 두 서비스를 배치할 수는 있지만 두 노드 유형을 사용하여 클러스터에 배치하는 것이 좋습니다. 각 노드 유형에는 인터넷 연결, VM 크기 및 독립적으로 확장될 수 있는 VM 수와 같은 고유한 속성이 있을 수 있습니다.

	* 먼저 5개 이상의 VM이 포함될 노드 유형을 정의합니다. 다른 노드 유형은 1대 이상의 VM을 가질 수 있습니다.

13.  노드 유형을 구성하려면

	a. 노드 유형에 대한 이름(문자와 숫자만을 포함하는 1~12자)을 선택합니다.

	b. 주 노드 유형에 대한 최소 VM 크기는 클러스터에 대해 선택한 내구성 계층에 따라 결정됩니다. 내구성 계층에 대한 기본값은 동입니다. [서비스 패브릭 클러스터 안정성 및 내구성 선택](service-fabric-cluster-capacity.md) 방법 문서를 참조하세요.

	b. VM 크기/가격 책정 계층을 선택합니다. 기본값은 D4 표준이지만 응용 프로그램을 테스트하는 데만 이 클러스터를 사용하는 경우 D2 또는 보다 작은 VM을 선택할 수 있습니다.

	c. 주 노드 유형에 대한 최소 VM 수는 선택한 안정성 계층에 따라 결정됩니다. 안정성 계층에 대한 기본값은 은입니다. [서비스 패브릭 클러스터 안정성 및 내구성 선택](service-fabric-cluster-reliability-and-durability.md) 방법 문서를 참조하세요.

	c. 노드 유형에 대한 VM 수를 선택합니다. 나중에 노드 유형의 VM 수를 늘리거나 줄일 수 있지만 주 노드 유형에서는 선택한 안정성 수준에 따라 최소 개수가 결정됩니다. 다른 노드 유형은 1대 이상의 VM을 가질 수 있습니다.


  	![노드 유형 만들기 스크린샷][CreateNodeType]

9. 응용 프로그램을 클러스터에 즉시 배포하려면 응용 프로그램에 대해 열고 싶은 포트를 **응용 프로그램 포트** 노드 유형(또는 이전에 만든 노드 유형)에 추가하세요. 이 노드 유형과 관련된 부하 분산 장치를 수정하여 나중에 노드 유형에 포트를 추가할 수 있습니다. (프로브를 추가한 다음 부하 분산 장치 규칙에 추가합니다.) 지금 추가하는 것이 좀 더 쉽습니다. 포털 자동화를 통해 필요한 프로브와 규칙이 부하 분산 장치에 추가되기 때문입니다.

	a. 응용 프로그램 패키지에 포함된 서비스 매니페스트에서 응용 프로그램 포트를 찾을 수 있습니다. 각 응용 프로그램으로 이동하여 서비스 매니페스트를 열고, 응용 프로그램이 외부 세계와 통신하는 데 필요한 모든 입력 끝점을 기록해 둡니다.

	b. **응용 프로그램 입력 끝점** 필드에 쉼표로 구분된 모든 포트를 추가합니다. TCP 클라이언트 연결 끝점은 기본적으로 19000이므로 지정할 필요가 없습니다. 예를 들어 WordCount 응용 프로그램 예제의 경우 포트 83을 열어야 합니다. 응용 프로그램 패키지의 servicemanifest.xml 파일에서 찾을 수 있습니다. (servicemanifest.xml 파일이 2개 이상 있을 수 있습니다.)

    c. 대부분의 응용 프로그램 예제에서 포트 80 및 8081을 사용하므로 이 클러스터에 샘플을 배포하려면 두 포트를 추가합니다. ![포트][Ports]

10. "NodeTypeName"의 기본 배치 속성은 시스템이 추가하므로 **배치 속성**을 구성할 필요가 없습니다. 응용 프로그램에 필요한 경우 더 추가할 수 있습니다.

11. **용량 속성**은 반드시 구성해야 하는 것은 아니지만 응용 프로그램에서 이를 사용하여 서비스 패브릭 클러스터에서 시스템의 배치와 리소스 분산 결정에 영향을 미치는 시스템 부하를 보고할 수 있으므로 구성하는 것이 좋습니다. 서비스 패브릭 리소스 분산에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-architecture.md)를 참조하세요.

12. 모든 노드 유형에 대해 위의 단계를 계속합니다.

14. 클러스터 **진단**을 구성합니다. 기본적으로 문제 해결을 돕기 위해 클러스터에서 진단이 활성화됩니다. 진단을 사용하지 않으려면 **상태** 토글을 **사용 안 함**로 변경합니다. 진단을 끄는 것은 권장**하지 않습니다**.

15. 옵션: 서비스 **패브릭 클러스터 설정**을 지정합니다. 이는 고급 옵션이므로 서비스 패브릭 클러스터에 대한 기본 설정을 변경할 수 있습니다. 응용 프로그램 또는 클러스터에 확실히 필요하지 않으면 기본값을 변경하지 않는 것이 좋습니다.



## 3단계- 보안 구성

현재 서비스 패브릭에서는 x509 인증서를 통해서만 클러스터 보안을 지원합니다. 이 프로세스를 시작하기 전에 주요 자격 증명 모음에 인증서를 업로드해야 합니다. 이에 대한 자세한 방법은 [서비스 패브릭 클러스터 보안](service-fabric-cluster-security.md)을 참조하세요.

클러스터 보안은 선택 사항이지만 강력하게 권장합니다. 클러스터 보안을 설정하지 않으려면 **보안 모드**를 **안전하지 않음**으로 전환합니다. 보안되지 않은 클러스터는 나중에 보안되는 클러스터로 업데이트할 수 **없습니다**.

보안 고려 사항 및 지침은 [서비스 패브릭 클러스터 보안](service-fabric-cluster-security.md)에 설명되어 있습니다.

![Azure 포털의 보안 구성 스크린샷][SecurityConfigs]


## 4단계 - 클러스터 만들기 완료

클러스터 만들기를 완료하려면 **요약**을 클릭하여 제공된 구성을 참조하거나 클러스터를 배포하는 데 사용할 Azure Resource Manager 템플릿을 다운로드합니다. 필수 설정을 입력하면 **확인** 단추가 활성화되고 이를 클릭하여 클러스터 만들기 프로세스를 시작할 수 있습니다.

알림에서 만들기 진행률을 볼 수 있습니다. (화면 오른쪽 위의 상태 표시줄 근처에 있는 "종" 모양 아이콘을 클릭합니다.) 클러스터를 만드는 동안 **시작 보드에 고정**을 클릭하면 **서비스 패브릭 클러스터 배포 중**이 **시작** 보드에 고정됩니다.

![“배포 서비스 패브릭 클러스터”를 표시하는 시작 보드 스크린샷][Notifications]

## 클러스터 상태 보기

클러스터를 만들면 포털에서 클러스터를 검사할 수 있습니다.

1. **찾아보기**로 이동하고 **서비스 패브릭 클러스터**를 클릭합니다.

2. 클러스터를 찾아 클릭합니다. ![포털에서 클러스터를 찾는 스크린샷][BrowseCluster]

3. 이제 대시보드에서 클러스터의 공용 IP 주소를 포함한 클러스터 세부 정보를 볼 수 있습니다. **클러스터 공용 IP 주소** 위에 마우스 커서를 대면 클립보드가 나타나며 이를 클릭하여 주소를 복사할 수 있습니다. ![대시보드의 클러스터 세부 정보 스크린샷][ClusterDashboard]

  클러스터 대시보드 블레이드의 **노드 모니터** 섹션에 정상 및 비정상 상태인 VM 수가 표시됩니다. 클러스터 상태에 대한 자세한 내용은 [서비스 패브릭 상태 모델 소개](service-fabric-health-introduction.md)에서 볼 수 있습니다.

>[AZURE.NOTE] 가용성을 유지하고 상태를 보존하기 위해 서비스 패브릭 클러스터에서 특정 수의 노드가 항상 작동 상태를 유지해야 하며, 이 숫자를 "유지 관리 쿼럼"이라고 합니다. 따라서 [상태 전체 백업](service-fabric-reliable-services-backup-restore.md)을 처음으로 수행하는 경우 외에는 일반적으로 클러스터의 모든 컴퓨터를 종료하는 것은 안전하지 않습니다.

## 클러스터에 연결하고 응용 프로그램 배포

이제 클러스터 설정이 완료되어 응용 프로그램을 연결하고 배포를 시작할 수 있습니다. 서비스 패브릭 SDK가 설치된 컴퓨터에서 Windows PowerShell을 시작합니다. 그런 다음 클러스터에 연결하려면 보안 또는 비보안 클러스터를 만들었는지 여부에 따라 다음 PowerShell 명령 집합 중 하나를 실행합니다.

### 비보안 클러스터에 연결

    ```powershell
    Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10
    ```

### 보안 클러스터에 연결

    1. Run the following to set up the certificate on the machine that you are going to use to run the "Connect-serviceFabricCluster" PowerShell command.

        ```powershell
        Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
                -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
                -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
        ```

    2. Run the following PowerShell command to connect to a secure cluster. The certificate details are the same ones that you gave on the portal.

        ```powershell
        Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
                  -KeepAliveIntervalInSec 10 `
                  -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
                  -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
                  -StoreLocation CurrentUser -StoreName My
        ```

        For example, the PowerShell command above should look similar to the following:

        ```powershell
        Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
                  -KeepAliveIntervalInSec 10 `
                  -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
                  -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
                  -StoreLocation CurrentUser -StoreName My
        ```

### 앱 배포
이제 연결되었으며 다음 명령을 실행하여 응용 프로그램을 배포하고 표시된 경로를 해당 컴퓨터의 적절한 경로로 바꿉니다. 아래 예제에서는 단어 개수 응용 프로그램 예제를 배포합니다.

1. 이전에 연결한 클러스터에 패키지를 복사합니다.

    ```powershell
    $applicationPath = "C:\VS2015\WordCount\WordCount\pkg\Debug"
    ```

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $applicationPath -ApplicationPackagePathInImageStore "WordCount" -ImageStoreConnectionString fabric:ImageStore
    ```
2. 응용 프로그램 종류를 서비스 패브릭에 등록합니다.

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore "WordCount"
    ```

3. 방금 등록한 응용 프로그램 종류에 새 인스턴스를 만듭니다.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/WordCount -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0.0
    ```

4. 이제 선택한 브라우저를 열고 응용 프로그램이 수신하는 끝점에 연결합니다. WordCount 응용 프로그램 예제의 경우 URL은 다음과 같습니다.

    http://sfcluster4doc.westus.cloudapp.azure.com:31000

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## VMSS(가상 컴퓨터 크기 집합) 인스턴스 또는 클러스터 노드에 원격 연결

클러스터에서 지정한 각 NodeType에 따라 VMSS가 설정됩니다. 자세한 내용은 [VMSS 인스턴스로 RDP 연결하는 방법](service-fabric-cluster-nodetypes.md)을 참조하세요.

## 다음 단계

클러스터를 만든 후 클러스터에 보안을 설정하고 앱을 배포하는 방법에 대해 알아봅니다.
- [Visual Studio에서 서비스 패브릭 응용 프로그램 관리](service-fabric-manage-application-in-visual-studio.md)
- [서비스 패브릭 클러스터 보안](service-fabric-cluster-security.md)
- [서비스 패브릭 상태 모델 소개](service-fabric-health-introduction.md)


<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[Ports]: ./media/service-fabric-cluster-creation-via-portal/ports.png
[SFConfigurations]: ./media/service-fabric-cluster-creation-via-portal/SFConfigurations.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[BrowseCluster]: ./media/service-fabric-cluster-creation-via-portal/browse.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[SecureConnection]: ./media/service-fabric-cluster-creation-via-portal/SecureConnection.png

<!---HONumber=AcomDC_0330_2016-->