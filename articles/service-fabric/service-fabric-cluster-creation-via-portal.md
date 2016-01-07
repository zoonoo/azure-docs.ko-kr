<properties
   pageTitle="Azure 포털에서 서비스 패브릭 클러스터 설정 | Microsoft Azure"
   description="Azure 포털에서 서비스 패브릭 클러스터 설정."
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
   ms.date="11/19/2015"
   ms.author="chackdan"/>

# Azure 포털에서 서비스 패브릭 클러스터 설정

이 페이지는 서비스 패브릭 클러스터 설정을 안내합니다. 사용자의 구독에 이 클러스터를 형성할 IaaS VM을 배포하기에 충분한 코어가 있다고 가정합니다.


## 클러스터 만들기

1. Azure 포털 [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal)에 로그온합니다.

2. **+ 새로 만들기**를 클릭하여 새 리소스 템플릿을 추가합니다. 모든 항목 아래의 마켓플레이스에서 **서비스 패브릭 클러스터** 템플릿을 검색합니다 .최상위 범주 마켓플레이스를 클릭하여 **모든 항목**으로 이동한 다음 "모든 항목" 아래에서 "패브릭"을 검색하고 Enter 키를 누르면 됩니다. 오디오 필터가 작동하지 않을 때가 가끔 있으니 꼭 **Enter 키를 누르세요**. ![SearchforServiceFabricClusterTemplate][SearchforServiceFabricClusterTemplate]

3. 목록에서 "서비스 패브릭 클러스터"를 선택합니다.
4. 서비스 패브릭 클러스터 블레이드로 이동하여 **만들기**를 클릭하고 클러스터에 대한 세부 정보를 입력합니다.
5. **새 RG(리소스 그룹)**를 만듭니다. 클러스터 이름과 같은 이름을 사용하면 나중에 쉽게 찾을 수 있습니다. 특히 배포를 변경하거나 클러스터를 삭제하려고 할 때 매우 유용합니다.

  	참고 - 기존 리소스 그룹을 사용해도 되지만 새 리소스 그룹을 만드는 것이 좋습니다. 이렇게 하면 필요 없는 클러스터가 삭제되므로 매우 간편합니다.

 	 ![CreateRG][CreateRG]


6. 클러스터가 배포될 **구독**을 선택합니다. 특히 구독이 여러 개인 경우 반드시 선택해야 합니다.

7. 다른 위치에 만들려면 드롭다운 목록에서 **위치**를 선택합니다. 위치를 선택하지 않으면 기본 위치인 미국 서부가 사용됩니다.

8. **노드 유형**을 구성합니다. 노드 유형은 클라우드 서비스의 "역할"과 똑같이 보일 수 있으며 VM 크기, VM 수 및 VM 속성을 정의합니다. 클러스터의 노드 형식이 두 개 이상이어도 됩니다. VM 5대 이상이 포함된 NodeType(기본 노드 유형 또는 포털에서 처음으로 정의하는 노드 유형)이 적어도 하나 이상 있어야 한다는 것이 유일한 제약 조건입니다.
	1. VM 크기/가격 책정 계층을 선택합니다. 기본값은 D4 표준이며, 이 클러스터를 단순히 응용 프로그램 테스트에 사용할 생각이면 D2 또는 더 작은 VM 크기를 선택해도 됩니다.	
	2. VM 수를 선택합니다. 나중에 NodeType의 VM 수를 늘리거나 줄일 수 있지만 기본 또는 첫 번째 노드 유형의 VM이 적어도 5대 이상이어야 합니다.
	3. NodeType의 이름을 알파벳과 숫자로만 구성하여 1~12자 사이로 입력합니다.	
	4. VM 원격 데스크톱 사용자 이름 및 암호를 선택합니다.
	5. **노드 유형이 여러 개인 경우 노드 유형과 관련하여 고려할 사항**이 있습니다. 단일 노드 유형 클러스터를 배포할 계획이면 다음 단계로 건너뛰세요.

		- 이 개념을 설명하기 위해 예를 하나 들겠습니다. "프런트엔드" 서비스와 "백엔드" 서비스가 포함된 응용 프로그램을 배포하려는 경우 "프런트엔드" 서비스는 인터넷에 대해 포트가 열려 있는 작은 VM(A2, D2 등)에, 계산 작업이 많은 "백엔드" 서비스는 인터넷에 연결되지 않는 큰 VM(D4, D6, D12 등)에 배치하는 것이 좋습니다.
		- 두 서비스를 하나의 NodeType에 배치할 수는 있지만 노드 유형이 두 개인 클러스터에 배치하는 것이 좋습니다. 한 노드에는 인터넷 연결, 다른 노드에는 독립적으로 확장 가능한 VM 크기 및 VM 수처럼 서로 구분되는 속성을 부여할 수 있기 때문입니다.
		- VM이 적어도 5대 이상 포함될 노드 유형을 먼저 정의합니다. 다른 노드 유형은 VM이 1대만 있어도 됩니다.
					

  	![CreateNodeType][CreateNodeType]

9. **응용 프로그램 포트**- 응용 프로그램을 클러스터에 즉시 배포하려면 응용 프로그램에 대해 열고 싶은 포트를 이 노드 유형(또는 이전에 만든 노드 유형)에 추가하세요. 나중에 이 노드 유형과 연결된 부하 분산 장치를 수정하여 노드 유형에 포트를 추가할 수도 있지만(프로브를 추가한 후 해당 프로브를 부하 분산 장치 규칙에 추가해야 함) 지금 추가하는 것이 좀 더 쉽습니다. 포털 자동화를 통해 필요한 프로브와 규칙이 LB에 추가되기 때문입니다.
	1. 응용 프로그램 패키지에 포함된 서비스 매니페스트에서 응용 프로그램 포트를 찾을 수 있습니다. 각 응용 프로그램으로 이동하여 서비스 매니페스트를 열고, 응용 프로그램이 외부 세계와 통신하는 데 필요한 모든 "입력" 끝점을 기록해 둡니다.
	2. "응용 프로그램 입력 끝점" 필드의 쉼표로 구분된 모든 포트를 추가합니다. TCP 클라이언트 연결 끝점은 기본적으로 19000이기 때문에 사용자가 지정할 필요가 없습니다. 예를 들어 응용 프로그램에서 포트 "83"을 열어야 한다고 가정하겠습니다. 이는 응용 프로그램 패키지의 servicemanifest.xml에서 찾을 수 있습니다(servicemanifest.xml이 여러 개일 수도 있음).

  대부분의 샘플 응용 프로그램에서 포트 80 및 8081을 사용하므로 이 클러스터에 샘플을 배포하려면 두 포트를 추가합니다.

  ![포트][Ports]



10. **선택 사항: 배치 속성**- 사용자는 여기서 구성을 추가할 필요가 없습니다. 시스템에서 기본 배치 속성 "NodeTypeName"을 자동으로 추가합니다. 응용 프로그램에서 구성을 더 추가하라고 요구할 경우에는 더 추가하면 됩니다. 

  
## 보안 구성

현재 서비스 패브릭에서는 x509 인증서를 통한 클러스터 보안만 지원됩니다. 이 프로세스를 시작하기 전에 KeyVault에 인증서를 업로드해야 합니다. 자세한 방법은 [서비스 패브릭 클러스터 보안](service-fabric-cluster-security.md)을 참조하세요.

클러스터 보안은 선택 사항이지만 강력하게 권장합니다. 클러스터 보안을 설정하지 않으려면 보안 모드를 "없음"으로 전환합니다.

보안 고려 사항 및 설정 방법에 대한 자세한 내용은 [서비스 패브릭 클러스터 보안](service-fabric-cluster-security.md)에 설명되어 있습니다.

![SecurityConfigs][SecurityConfigs]



## 선택 사항: 진단 구성

기본적으로 문제 해결을 돕기 위해 클러스터에서 진단이 활성화됩니다. 진단을 비활성화하는 방법은 다음과 같습니다.

1. 진단 구성 블레이드로 이동합니다.

2. 상태를 사용 안 함으로 변경합니다.

## 선택 사항: 패브릭 설정

이 옵션은 서비스 패브릭 클러스터에 대한 기본 설정을 변경할 수 있는 고급 옵션입니다. 응용 프로그램 및/또는 클러스터에 꼭 필요한 경우가 아니면 기본값을 변경하지 **않는** 것이 좋습니다.

## 클러스터 생성 완료
**요약**을 클릭하여 입력한 구성을 살펴보거나 클러스터 배포에 사용될 ARM 템플릿을 다운로드할 수 있습니다. 필수 설정을 입력하면 만들기 단추가 활성화되고 클러스터 만들기 프로세스를 시작할 수 있습니다.
 

화면 오른쪽의 상태 표시줄 근처에 있는 "종" 모양 아이콘을 클릭하여 알림에서 진행 상황을 볼 수 있습니다. 클러스터를 만드는 동안 "시작 보드에 고정"을 클릭하면 "서비스 패브릭 클러스터 배포 중"이 시작 보드에 고정됩니다.

![알림][Notifications]

## 클러스터 상태 보기

배포가 완료되면 포털에서 클러스터를 검사할 수 있습니다.

1. **찾아보기**로 이동하여 리소스 - **서비스 패브릭 클러스터**를 클릭합니다.

2. 클러스터를 찾아 클릭합니다.

  ![BrowseCluster][BrowseCluster]

3. 이제 대시보드에서 클러스터의 공용 IP 주소를 포함한 클러스터 세부 정보를 볼 수 있습니다. **클러스터 공용 IP 주소** 위에 마우스 커서를 대면 클릭하여 IP 주소를 복사할 수 있는 클립보드가 나타납니다.

  ![ClusterDashboard][ClusterDashboard]

  클러스터 대시보드 블레이드의 노드 모니터 부분은 정상 상태인 VM 수와 비정상 상태인 VM 수를 표시합니다. 상태에 대한 자세한 내용은 본 문서의 [서비스 패브릭 상태 모델 소개](service-fabric-health-introduction.md) 섹션에서 확인할 수 있습니다.


## 클러스터에 연결 및 응용 프로그램 배포

클러스터가 설정되었으니, 이제 응용 프로그램을 연결하고 배포를 시작할 수 있습니다.

1. 서비스 패브릭 SDK가 설치된 컴퓨터에서 Windows PowerShell을 실행합니다.

2. 보안 또는 비보안 클러스터 중 어떤 것을 만들었는지에 따라 다음 PS 명령 집합 중 하나를 실행합니다.
 

- 옵션 1 **비보안 클러스터에 연결** 

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10 
```

다음 명령을 실행하여 응용 프로그램을 배포하고, 표시된 경로를 해당 컴퓨터의 적절한 경로로 바꿉니다.

 - 옵션 2 **보안 클러스터에 연결**

"Connect-serviceFabricCluster" PS cmd를 실행하는 데 사용할 컴퓨터에서 다음 명령을 실행하여 인증서를 설정합니다.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

다음 PS를 실행하여 보안 클러스터에 연결합니다. 인증서 세부 정보는 포털에서 입력한 것과 동일합니다.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
            -KeepAliveIntervalInSec 10 `
            -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
            -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
            -StoreLocation CurrentUser -StoreName My
```
예를 들어 위의 PS 명령은 다음과 비슷합니다.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
            -KeepAliveIntervalInSec 10 `
            -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
            -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
            -StoreLocation CurrentUser -StoreName My
```


다음 명령을 실행하여 응용 프로그램을 배포하고, 표시된 경로를 해당 컴퓨터의 적절한 경로로 바꿉니다. 아래는 단어 개수 샘플 응용 프로그램을 배포하는 예입니다.

이전 단계에서 연결한 클러스터에 패키지를 복사합니다.


```powershell
$applicationPath = "C:\VS2015\WordCount\WordCount\pkg\Debug"
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $applicationPath -ApplicationPackagePathInImageStore "WordCount" -ImageStoreConnectionString fabric:ImageStore
````
응용 프로그램 종류를 서비스 패브릭에 등록합니다.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "WordCount"
````

방금 등록한 응용 프로그램 유형에 새 인스턴스를 만듭니다.

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/WordCount -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0.0
````

선택한 브라우저를 열고 응용 프로그램이 수신하는 끝점에 연결합니다. 샘플 응용 프로그램 수의 경우 URL이 다음과 같습니다.

http://sfcluster4doc.westus.cloudapp.azure.com:31000



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
- [Visual Studio에서 서비스 패브릭 응용 프로그램 관리](service-fabric-manage-application-in-visual-studio.md).
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

<!-----HONumber=AcomDC_1203_2015-->