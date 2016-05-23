<properties
   pageTitle="Visual Studio를 사용하여 서비스 패브릭 클러스터 설정 | Microsoft Azure"
   description="Visual Studio의 Azure 리소스 그룹 프로젝트에 의해 만들어진 ARM(Azure 리소스 관리자) 템플릿을 사용하여 서비스 패브릭 클러스터를 설정하는 방법을 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/04/2016"
   ms.author="karolz@microsoft.com"/>

# Visual Studio를 사용하여 서비스 패브릭 클러스터 설정
이 문서에서는 Visual Studio 및 ARM(Azure 리소스 관리자) 템플릿을 사용하여 Azure 서비스 패브릭 클러스터를 설정하는 방법을 설명합니다. Visual Studio Azure 리소스 그룹 프로젝트를 사용하여 템플릿을 만듭니다. 템플릿은 만들어진 후에 Visual Studio에서 Azure에 직접 배포될 수 있지만 스크립트 또는 CI(지속적인 통합) 기능의 일부로 사용될 수도 있습니다.

## Azure 리소스 그룹 프로젝트를 사용하여 서비스 패브릭 클러스터 템플릿 만들기
시작하려면 Visual Studio를 열고 Azure 리소스 그룹 프로젝트(**클라우드** 폴더에서 사용할 수 있음)를 만듭니다.

![선택한 Azure 리소스 그룹 프로젝트를 사용하는 새 프로젝트 대화 상자][1]

이 프로젝트에 대한 새 Visual Studio 솔루션을 만들거나 기존 솔루션에 이 프로젝트를 추가할 수 있습니다.

>[AZURE.NOTE] 클라우드 노드 아래에 Azure 리소스 그룹 프로젝트가 표시되지 않는 경우 Azure SDK가 설치되지 않은 것입니다. 웹 플랫폼 설치 관리자를 시작한 다음(아직 설치하지 않은 경우 [지금 설치](http://www.microsoft.com/web/downloads/platform.aspx)) ".NET용 Azure SDK"를 검색하여 사용자의 Visual Studio 버전과 호환되는 버전을 설치합니다.

확인 단추를 누르면 Visual Studio에서 만들 리소스 관리자 템플릿을 선택하라는 메시지를 표시합니다.

![선택한 서비스 패브릭 클러스터 템플릿을 사용하여 Azure 템플릿 대화 상자를 선택합니다.][2]

서비스 패브릭 클러스터 템플릿을 선택하고 확인 단추를 다시 누릅니다. 이제 프로젝트 및 리소스 관리자 템플릿이 만들어졌습니다.

## 배포용 템플릿 준비
클러스터를 만들기 위해 템플릿을 배포하기 전에 필요한 템플릿 매개 변수 값을 제공해야 합니다. 이러한 매개 변수 값은 리소스 그룹 프로젝트의 `Templates` 폴더에 있는 `ServiceFabricCluster.parameters.json` 파일에서 읽습니다. 파일을 열고 다음 값을 제공합니다.

|매개 변수 이름 |설명|
|-----------------------  |--------------------------|
|certificateThumbprint |클러스터를 보호하는 인증서의 지문입니다.|
|sourceVaultResourceId |저장된 클러스터를 보호하는 인증서가 있는 키 자격 증명 모음의 *리소스 ID*입니다.|
|certificateUrlValue |클러스터 보안 인증서의 URL입니다.|

Visual Studio 서비스 패브릭 리소스 관리자 템플릿은 인증서로 보호되는 보안 클러스터를 만듭니다. 이 인증서는 마지막 3개의 템플릿 매개 변수(`certificateThumbprint`, `sourceVaultValue` 및 `certificateUrlValue`)로 식별되며 **Azure 키 자격 증명 모음**에 있어야 합니다. 클러스터 보안 인증서를 만드는 방법에 대한 자세한 내용은 [인증서를 사용하여 서비스 패브릭 클러스터를 보호하는 방법](service-fabric-cluster-security.md#secure-a-service-fabric-cluster-by-using-certificates) 항목을 참조하세요.

## 선택 사항: 클러스터 이름 변경
모든 서비스 패브릭 클러스터에는 이름이 있습니다. Azure에서 패브릭 클러스터가 만들어지면 클러스터 이름은 클러스터에 대한 DNS(Domain Name System) 이름을 Azure 지역과 함께 결정합니다. 예를 들어 클러스터 이름을 `myBigCluster`로 지정하고 매개 변수를 `clusterLocation`로 설정한 경우 매개 변수는 클러스터의 DNS 이름이 `myBigCluster.eastus.cloudapp.azure.com`인 미국 동부로 설정됩니다.

기본적으로 클러스터 이름은 자동으로 생성되며 임의의 접미사를 "cluster" 접두사에 추가하여 고유하게 만들어집니다. 이렇게 하면 템플릿을 **Ci(연속 통합)** 시스템의 일부로 쉽게 사용할 수 있습니다. 클러스터에 대해 사용자에게 의미가 있는 특정 이름을 사용하려는 경우 리소스 관리자 템플릿 파일(`ServiceFabricCluster.json`)의 `clusterName` 변수 값을 선택한 이름으로 설정합니다. 해당 파일에 정의된 첫 번째 변수입니다.

## 옵션: 공용 응용 프로그램 포트 추가
배포하기 전에 클러스터에 대한 공용 응용 프로그램 포트를 변경할 수도 있습니다. 기본적으로 템플릿에서는 두 개의 공용 TCP 포트(80과 8081)만 열립니다. 응용 프로그램에 더 많은 포트가 필요한 경우 템플릿에서 Azure 부하 분산 장치 정의를 수정합니다. 정의는 기본 템플릿 파일(`ServiceFabricCluster.json`)에 저장됩니다. 해당 파일을 열고 `loadBalancedAppPort`를 검색합니다. 각 포트는 세 개의 아티팩트에 연결됩니다.

1. 포트에 대한 TCP 포트 값을 정의하는 템플릿 매개 변수:

	```json
	"loadBalancedAppPort1": "80"
	```

2. *프로브*는 Azure 부하 분산 장치에서 다른 장치에 장애 조치하기 전에 특정 서비스 패브릭 노드를 얼마나 자주, 얼마나 오래 사용하려 하는지를 정의합니다. 프로브는 부하 분산 장치 리소스의 일부입니다. 다음은 첫 기본 응용 프로그램 포트에 대한 프로브 정의입니다.

	```json
	{
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[variables('loadBalancedAppPort1')]",
            "protocol": "Tcp"
        }
    }
	```

3. 포트 및 프로브를 함께 연결하여 서비스 패브릭 클러스터 노드 집합에서 부하 분산을 유지할 수 있도록 하는 *부하 분산 규칙*:

    ```json
	{
	    "name": "AppPortLBRule1",
	    "properties": {
	        "backendAddressPool": {
	            "id": "[variables('lbPoolID0')]"
	        },
	        "backendPort": "[variables('loadBalancedAppPort1')]",
	        "enableFloatingIP": false,
	        "frontendIPConfiguration": {
	            "id": "[variables('lbIPConfig0')]"
	        },
	        "frontendPort": "[variables('loadBalancedAppPort1')]",
	        "idleTimeoutInMinutes": 5,
	        "probe": {
	            "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
	        },
	        "protocol": "Tcp"
	    }
	}
    ```
클러스터에 배포하려고 하는 응용 프로그램이 더 많은 포트를 필요로 하는 경우 추가 프로브 및 부하 분산 규칙 정의를 만들어 추가할 수 있습니다. 리소스 관리자 템플릿을 통해 Azure 부하 분산 장치를 사용하는 방법에 대한 자세한 내용은 [템플릿을 사용하여 내부 부하 분산 장치 만들기 시작](../load-balancer/load-balancer-get-started-ilb-arm-template.md)을 참조하세요.

## Visual Studio를 사용하여 템플릿 배포
`ServiceFabricCluster.param.dev.json` 파일에 모든 필수 매개 변수 값을 저장하면 템플릿을 배포하고 서비스 패브릭 클러스터를 만들 준비가 된 것입니다. Visual Studio 솔루션 탐색기에서 리소스 그룹 프로젝트를 마우스 오른쪽 단추로 클릭하고 **배포**를 선택합니다. Visual Studio에서 **리소스 그룹에 배포** 대화 상자를 표시하고 필요한 경우 Azure에 인증하라는 메시지가 표시됩니다.

![리소스 그룹 대화 상자에 배포][3]

대화 상자에서 클러스터에 대해 기존 리소스 관리자 리소스 그룹을 선택할 수 있고 새 리소스 그룹을 만들 수 있는 옵션을 제공합니다. 일반적으로 서비스 패브릭 클러스터에 대한 별도 리소스 그룹을 사용하는 것이 좋습니다.

배포 단추를 누르면 Visual Studio에서 템플릿 매개 변수 값을 확인하라는 메시지를 표시합니다. **저장** 단추를 누릅니다. 한 매개 변수에 지속형 값인 클러스터에 대한 관리자 계정 암호가 없습니다. Visual Studio에서 값을 제공하라는 메시지가 표시되면 암호 값을 제공해야 합니다.

>[AZURE.NOTE] 지금 사용하고 있는 컴퓨터에서 Azure를 관리하는 데 PowerShell을 사용한 적이 없는 경우 약간의 관리 유지 작업을 해야 합니다.
>1. [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx) 명령을 실행하여 PowerShell 스크립팅을 사용합니다. 개발 컴퓨터인 경우 "제한 없음" 정책이 일반적으로 허용됩니다.
>2. Azure PowerShell 명령에서 진단 데이터 수집을 허용할지 여부를 결정하고 필요에 따라 [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) 또는 [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx)을 실행합니다. 이렇게 하면 템플릿 배포 중 발생하는 불필요한 프롬프트를 줄일 수 있습니다.

Visual Studio 출력 창에서 배포 프로세스의 진행률을 모니터링할 수 있습니다. 템플릿 배포가 완료되면 새 클러스터를 사용할 준비가 된 것입니다!

오류가 있는 경우 [Azure 포털](https://portal.azure.com/)로 이동한 후 배포한 리소스 그룹을 엽니다. **모든 설정**을 클릭한 다음 설정 블레이드에서 **배포**를 클릭합니다. 리소스 그룹 배포에 오류가 발생한 경우 여기에 자세한 진단 정보를 남깁니다.

>[AZURE.NOTE] 가용성을 유지하고 상태를 보존하기 위해 서비스 패브릭 클러스터에서 특정 수의 노드가 항상 작동 상태를 유지해야 하며, 이 숫자를 "유지 관리 쿼럼"이라고 합니다. 따라서 [상태 전체 백업](service-fabric-reliable-services-backup-restore.md)을 처음으로 수행하는 경우 외에는 일반적으로 클러스터의 모든 컴퓨터를 종료하는 것은 안전하지 않습니다.

## 다음 단계
- [Azure 포털을 사용하여 서비스 패브릭 클러스터 설정에 대해 알아보기](service-fabric-cluster-creation-via-portal.md)
- [Visual Studio를 사용하여 서비스 패브릭 응용 프로그램을 관리 및 배포하는 방법 알아보기](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png

<!---HONumber=AcomDC_0511_2016-->