<properties
	pageTitle="Microsoft HPC 팩을 사용하여 하이브리드 HPC 클러스터 설정 | Microsoft Azure"
	description="Microsoft HPC Pack 및 Azure를 사용하여 소규모 하이브리드 HPC(고성능 컴퓨팅) 클러스터를 설정하는 방법에 대해 알아봅니다."
	services="cloud-services"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-service-management,hpc-pack"/>

<tags
	ms.service="cloud-services"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="danlep"/>


# Microsoft HPC 팩 및 주문형 Azure 인스턴스를 사용하여 하이브리드 계산 클러스터 설정
이 자습서에서는 Microsoft HPC Pack 2012 R2 및 Azure를 사용하여 작은 하이브리드 HPC(고성능 컴퓨팅) 클러스터를 설정하는 방법을 보여 줍니다. 클러스터는 온-프레미스 헤드 노드(Windows Server 운영 체제와 HPC 팩이 실행되는 컴퓨터) 및 Azure 클라우드 서비스에 작업자 역할 인스턴스로 배포되는 일부 계산 노드로 구성됩니다. 그런 다음 하이브리드 클러스터에서 계산 작업을 실행할 수 있습니다.

![하이브리드 HPC 클러스터][Overview]

이 자습서에서는 Azure의 확장 가능한 주문형 계산 리소스를 사용하여 계산이 많이 사용되는 응용 프로그램을 실행하는 방법을 보여 줍니다. 이 방법을 클러스터 "클라우드로 버스트"라고도 합니다.

이 자습서는 이전에 계산 클러스터나 HPC 팩을 사용한 경험이 없다고 가정합니다. 데모를 위해 하이브리드 계산 클러스터를 신속하게 배포하도록 도와주는 역할만 합니다. 하이브리드 HPC 팩 클러스터를 프로덕션 환경에 대규모로 배포하는 단계와 고려 사항은 [자세한 지침](http://go.microsoft.com/fwlink/p/?LinkID=200493)을 참조하십시오. Azure 가상 컴퓨터의 자동화된 클러스터 배포를 포함한 HPC 팩을 사용한 다른 시나리오의 경우 [Azure에서 Microsoft HPC 팩을 사용하는 HPC 클러스터 옵션](../virtual-machines/virtual-machines-hpcpack-cluster-options.md)을 참조하세요.

>[AZURE.NOTE]Azure는 각 작업에 적합한 [다양한 크기](../virtual-machines/virtual-machines-size-specs.md)를 계산 리소스에 제공합니다. 예를 들어 A8 및 A9 인스턴스는 특정 HPC 응용 프로그램에 필요한 낮은 대기 시간, 높은 처리량의 응용 프로그램 네트워크 액세스와 고성능을 결합합니다. [A8, A9, A10 및 A11 계산 집약적인 인스턴스 정보](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md)를 참조하세요.

## 필수 조건

* **Azure 구독** - 계정이 없는 경우에는 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Windows Server 2012 R2 또는 Windows Server 2012가 실행되는 온-프레미스 컴퓨터** - 이 컴퓨터는 HPC 클러스터의 헤드 노드입니다. Windows Server가 아직 실행되지 않는 경우 [평가판](http://technet.microsoft.com/evalcenter/dn205286.aspx)을 다운로드하고 설치할 수 있습니다.

	* Active Directory 도메인에 컴퓨터를 가입해야 합니다.

	* 추가 서버 역할이나 역할 서비스가 설치되지 않았는지 확인합니다.

	* HPC 팩을 지원하려면 운영 체제가 영어, 일본어 또는 중국어(간체) 언어 중 하나로 설치되어야 합니다.

	* 중요 업데이트가 설치되었는지 확인합니다.

* **HPC Pack 2012 R2** - 무료로 최신 버전의 설치 패키지를 [다운로드](http://go.microsoft.com/fwlink/p/?linkid=328024)하고 헤드 노드 컴퓨터나 네트워크 위치에 파일을 복사합니다. Windows Server 설치와 동일한 언어의 설치 파일을 선택합니다.

* **도메인 계정** - HPC 팩을 설치하려면 헤드 노드에서 로컬 관리자 권한으로 이 계정을 구성해야 합니다.

* **포트 443의 헤드 노드와 Azure 간 TCP 연결**

## 헤드 노드에 HPC 팩 설치

먼저 클러스터의 헤드 노드로 사용할 Windows Server를 실행하는 온-프레미스 컴퓨터에 Microsoft HPC 팩을 설치합니다.

1. 로컬 관리자 권한을 가진 도메인 계정을 사용하여 헤드 노드에 로그온합니다.

2. HPC 팩 설치 파일에서 Setup.exe를 실행하여 HPC Pack Installation Wizard를 시작합니다.

3. **HPC Pack 2012 R2 Setup** 화면에서 **New installation or add new features to an existing installation**을 클릭합니다.

	![HPC Pack 2012 설치][install_hpc1]

4. **Microsoft Software User Agreement page**에서 **Next**를 클릭합니다.

5. **Select Installation Type** 페이지에서 **Create a new HPC cluster by creating a head node**를 클릭한 후 **Next**를 클릭합니다.

	![설치 유형 선택][install_hpc2]

6. 마법사에서 여러 개의 사전 설치 테스트를 실행합니다. 테스트를 모두 통과한 경우 **Installation Rules** 페이지에서 **Next**를 클릭합니다. 그렇지 않으면 제공된 정보를 검토하고 필요에 따라 환경을 변경합니다. 다시 테스트를 실행하거나 필요한 경우 Installation Wizard를 다시 시작합니다.

	![설치 규칙][install_hpc3]

7. **HPC DB Configuration** 페이지에서 모든 HPC 데이터베이스에 대해 **Head Node**가 선택되었는지 확인하고 **Next**를 클릭합니다.

	![DB 구성][install_hpc4]

8. 마법사의 나머지 페이지에서 기본 선택을 적용합니다. **Install Required Components** 페이지에서 **Install**을 클릭합니다.

	![설치][install_hpc6]

9. 설치가 완료되면 **Start HPC Cluster Manager**를 선택 취소하고 **Finish**를 클릭합니다. (이후 단계에서 HPC 클러스터 관리자를 시작합니다.)

	![마침][install_hpc7]

## Azure 구독 준비
[Azure 클래식 포털](https://manage.windowsazure.com)을 사용하여 Azure 구독으로 다음 단계를 수행합니다. 이러한 단계는 나중에 온-프레미스 헤드 노드에서 Azure 노드를 배포하는 데 필요합니다.

- 헤드 노드와 Azure 서비스 간의 보안 연결에 필요한 관리 인증서 업로드

- Azure 노드(작업자 역할 인스턴스)가 실행되는 Azure 클라우드 서비스 만들기

- Azure 저장소 계정 만들기

	>[AZURE.NOTE]또한 나중에 필요한 Azure 구독 ID를 기록해 둡니다. Azure [계정 정보](https://account.windowsazure.com/Subscriptions)</a>에서 확인할 수 있습니다.

### 기본 관리 인증서 업로드
HPC 팩은 Azure 관리 인증서로 업로드할 수 있는 자체 서명된 인증서(기본 Microsoft HPC Azure 관리 인증서라고 함)를 헤드 노드에 설치합니다. 이 인증서는 테스트 목적 및 개념 증명 배포를 위해 제공됩니다.

1. 헤드 노드 컴퓨터에서 [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.

2. **설정**을 클릭한 후 **관리 인증서**를 클릭합니다.

3. 명령 모음에서 **업로드**를 클릭합니다.

	![인증서 설정][upload_cert1]

4. 헤드 노드에서 C:\\Program Files\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer 파일을 찾은 다음 **선택 단추**를 클릭합니다.

	![인증서 업로드][install_hpc10]

관리 인증서 목록에 **기본 HPC Azure 관리**가 표시됩니다.

### Azure 클라우드 서비스 만들기

>[AZURE.NOTE]최상의 성능을 얻으려면 동일한 지역에 클라우드 서비스와 저장소 계정을 만듭니다.

1. 클래식 포털의 명령 모음에서 **새로 만들기**를 클릭합니다.

2. **계산**, **클라우드 서비스**, **빠른 생성**을 차례로 클릭합니다.

3. 클라우드 서비스의 URL을 입력하고 **클라우드 서비스 만들기**를 클릭합니다.

	![서비스 만들기][createservice1]

### Azure 저장소 계정 만들기

1. 클래식 포털의 명령 모음에서 **새로 만들기**를 클릭합니다.

2. **데이터 서비스**, **저장소**, **빠른 생성**을 차례로 클릭합니다.

3. 계정의 URL을 입력하고 **저장소 계정 만들기**를 클릭합니다.

	![저장소 만들기][createstorage1]

## 헤드 노드 구성

HPC Cluster Manager를 사용하여 Azure 노드를 배포하고 작업을 제출하려면 먼저 필요한 클러스터 구성 단계를 수행합니다.

1. 헤드 노드에서 HPC Cluster Manager를 시작합니다. **Select Head Node** 대화 상자가 표시되면 **Local Computer**를 클릭합니다. **Deployment To-do List**가 나타납니다.

2. **Required deployment tasks**에서 **Configure your network**를 클릭합니다.

	![네트워크 구성][config_hpc2]

3. Network Configuration Wizard에서 **All nodes only on an enterprise network**(Topology 5)를 선택합니다.

	![토폴로지 5][config_hpc3]

	>[AZURE.NOTE]이 구성은 헤드 노드에 Active Directory 및 인터넷에 연결하기 위한 네트워크 어댑터 하나만 필요하기 때문에 가장 간단한 데모용 구성입니다. 이 자습서에서는 추가 네트워크가 필요한 클러스터 시나리오를 다루지 않습니다.

4. 마법사의 나머지 페이지에서 **Next**를 클릭하여 기본값을 적용합니다. **Review** 탭에서 **Configure**를 클릭하여 네트워크 구성을 완료합니다.

5. **Deployment To-do List**에서 **Provide installation credentials**를 클릭합니다.

6. **Installation Credentials** 대화 상자에서 HPC 팩을 설치하는 데 사용한 도메인 계정의 자격 증명을 입력합니다. 그런 후 **OK**를 클릭합니다.

	![설치 자격 증명][config_hpc6]

	>[AZURE.NOTE]HPC 팩 서비스는 설치 자격 증명만을 사용하여 도메인에 가입된 계산 노드를 배포합니다. 이 자습서에서 추가한 Azure 노드는 가입되지 않은 도메인입니다.

7. **Deployment To-do List**에서 **Configure the naming of new nodes**을 클릭합니다.

8. **Specify Node Naming Series** 대화 상자에서 기본 이름 지정 시리즈를 적용하고 **OK**를 클릭합니다.

	![노드 이름 지정][config_hpc8]

	>[AZURE.NOTE]이 이름 지정 시리즈는 도메인에 가입된 계산 노드의 이름만 생성합니다. Azure 작업자 노드의 이름은 자동으로 지정됩니다.

9. **Deployment To-do List**에서 **Create a node template**을 클릭합니다. 노드 템플릿을 사용하여 클러스터에 Azure 노드를 추가합니다.

10. Create Node Template Wizard에서 다음을 수행합니다.

	a. **노드 템플릿 유형 선택** 페이지에서 **Azure 노드 템플릿**을 클릭한 후 **다음**을 클릭합니다.

	![노드 템플릿][config_hpc10]

	b. **다음**을 클릭하여 기본 템플릿 이름을 적용합니다.

	c. **구독 정보 제공** 페이지에서 Azure 계정 정보에 제공된 Azure 구독 ID를 입력합니다. **Management certificate**에서 **Browse**를 클릭하고 **Default HPC Azure Management**를 선택합니다. 그런 후 **Next**를 클릭합니다.

	![노드 템플릿][config_hpc12]

	d. **서비스 정보 제공** 페이지에서 이전 단계를 통해 만든 클라우드 서비스와 저장소 계정을 선택합니다. 그런 후 **Next**를 클릭합니다.

	![노드 템플릿][config_hpc13]

	e. 마법사의 나머지 페이지에서 **Next**를 클릭하여 기본값을 적용합니다. **Review** 탭에서 **Create**를 클릭하여 노드 템플릿을 만듭니다.

	>[AZURE.NOTE]기본적으로 Azure 노드 템플릿에는 수동으로 노드를 시작(프로비전) 및 중지하기 위한 설정이 포함되어 있습니다. Azure 노드를 자동으로 시작 및 중지하도록 일정을 구성할 수도 있습니다.

## 클러스터에 Azure 노드 추가

이제 노드 템플릿을 사용하여 클러스터에 Azure 노드를 추가합니다. 클러스터에 노드를 추가하면 해당 구성 정보가 저장되므로 언제든지 클라우드 서비스의 역할 인스턴스로 노드를 시작(프로비전)할 수 있습니다. 클라우드 서비스에서 역할 인스턴스가 실행되는 경우 Azure 노드에 대해서만 구독에 요금이 부과됩니다.

이 자습서에서는 두 개의 작은 노드를 추가합니다.

1. HPC Cluster Manager의 **Node Management**, **Actions** 창에서 **Add Node**를 클릭합니다.

	![노드 추가][add_node1]

2. Add Node Wizard의 **Select Deployment Method** 페이지에서 **Add Azure nodes**를 클릭한 후 **Next**를 클릭합니다.

	![Azure 노드 추가][add_node1_1]

3. **Specify New Nodes** 페이지에서 이전에 만든 Azure 노드 템플릿(기본적으로 **Default AzureNode Template**)을 선택합니다. 크기가 **Small**인 노드 **2**개를 지정하고 **Next**를 클릭합니다.

	![노드 지정][add_node2]

	사용 가능한 크기에 대한 자세한 내용은 [클라우드 서비스에 적합한 크기](../cloud-services/cloud-services-sizes-specs.md)를 참조하세요.

4. **Completing the Add Node Wizard** 페이지에서 **Finish**를 클릭합니다.

	 이제 **AzureCN-0001** 및 **AzureCN-0002**라는 Azure 노드 2개가 HPC Cluster Manager에 표시됩니다. 둘 다 **Not-Deployed** 상태입니다.

	![추가된 노드][add_node3]

## Azure 노드 시작
Azure에서 클러스터 리소스를 사용하려면 HPC Cluster Manager를 통해 Azure 노드를 시작(프로비전)하고 온라인 상태로 전환합니다.

1.	HPC Cluster Manager의 **Node Management**(일부 버전의 HCP에서 **Resource Management**)에서 노드 중 하나 또는 둘 다를 클릭하고 **Actions** 창에서 **Start**를 클릭합니다.

	![노드 시작][add_node4]

2. **Start Azure Nodes** 대화 상자에서 **Start**를 클릭합니다.

	![노드 시작][add_node5]

	노드가 **Provisioning** 상태로 전환됩니다. 프로비전 로그를 보고 프로비전 진행 상태를 추적합니다.

	![노드 프로비전][add_node6]

3. 몇 분 후에 Azure 노드가 프로비전을 완료하고 **Offline** 상태로 전환됩니다. 이 상태에서는 역할 인스턴스가 실행되지만 클러스터 작업을 수락하지 않습니다.

4. 역할 인스턴스가 실행되고 있는지 확인하려면 [클래식 포털](https://manage.windowsazure.com)에서 **클라우드 서비스**를 클릭하고 클라우드 서비스의 이름을 클릭한 후 **인스턴스**를 클릭합니다.

	![실행 중인 인스턴스][view_instances1]

	서비스에서 두 개의 작업자 역할 인스턴스가 실행되고 있습니다. 또한 HPC 팩은 헤드 노드와 Azure 간의 통신을 처리하기 위해 **HpcProxy** 인스턴스(중간 크기) 두 개를 자동으로 배포합니다.

5. 클러스터 작업을 실행하기 위해 Azure 노드를 온라인 상태로 전환하려면 노드를 선택하고 마우스 오른쪽 단추를 클릭한 후 **Bring Online**을 클릭합니다.

	![오프라인 노드][add_node7]

	HPC Cluster Manager에서 노드가 **Online** 상태로 표시됩니다.

## 클러스터에서 명령 실행

설치를 확인하려면 HPC 팩 **clusrun** 명령을 사용하여 하나 이상의 클러스터 노드에서 명령이나 응용 프로그램을 실행합니다. 간단한 예로 **clusrun**을 사용하여 Azure 노드의 IP 구성을 가져옵니다.

1. 헤드 노드에서 명령 프롬프트를 엽니다.

2. 다음 명령을 입력합니다.

	`clusrun /nodes:azurecn* ipconfig`

3. 다음과 유사한 출력이 표시됩니다.

	![Clusrun][clusrun1]

## 테스트 작업 실행

이제 하이브리드 클러스터에서 실행되는 테스트 작업을 제출합니다. 이 예제는 **set /a** 명령을 사용하여 자체 정수를 추가하는 하위 작업을 실행하는 간단한 "매개 변수 스윕" 작업(본질적으로 병렬 계산 유형)입니다. 클러스터의 모든 노드는 1에서 100까지 정수의 하위 작업을 마치는 데 사용됩니다.

1. HPC Cluster Manager의 **Job Management**, **Actions** 창에서 **New Parametric Sweep Job**을 클릭합니다.

	![새 작업][test_job1]

2. **New Parametric Sweep Job** 대화 상자의 **Command line**에 `set /a *+*`을 입력합니다(표시되는 기본 명령줄을 덮어씀). 나머지 설정은 기본값을 그대로 두고 **Submit**를 클릭하여 작업을 제출합니다.

	![매개 변수 스윕][param_sweep1]

3. 작업이 완료되면 **My Sweep Task** 작업을 두 번 클릭합니다.

4. **View Tasks**를 클릭한 후 하위 작업을 클릭하여 해당 하위 작업의 계산된 출력을 표시합니다.

	![작업 결과][view_job361]

5. 하위 작업에 대한 계산을 수행한 노드를 보려면 **Allocated Nodes**를 클릭합니다. 클러스터에서 다른 노드 이름이 표시할 수도 있습니다.

	![작업 결과][view_job362]

## Azure 노드 중지

클러스터를 시도한 후 계정에 대한 불필요한 요금 부과를 피하기 위해 Azure 노드를 중지합니다. 클라우드 서비스가 중지되고 Azure 역할 인스턴스가 제거됩니다.

1. HPC Cluster Manager의 **Node Management**에서 Azure 노드를 둘 다 선택합니다. **Actions** 창에서 **Stop**을 클릭합니다.

	![노드 중지][stop_node1]

2. **Stop Azure Nodes** 대화 상자에서 **Stop**을 클릭합니다.

	![노드 중지][stop_node2]

3. 노드가 **Stopping** 상태로 전환됩니다. 몇 분 후에 HPC Cluster Manager에서 노드가 **Not-Deployed**로 표시됩니다.

	![배포되지 않은 노드][stop_node4]

4. 역할 인스턴스가 Azure에서 더 이상 실행되지 않는지 확인하려면 [포털](https://manage.windowsazure.com)에서 **클라우드 서비스**를 클릭하고 클라우드 서비스의 이름을 클릭한 후 **인스턴스**를 클릭합니다. 프로덕션 환경에는 인스턴스가 배포되지 않습니다.

	![인스턴스 없음][view_instances2]

	이제 자습서를 마쳤습니다.

## 관련 리소스

* [HPC Pack 2012 R2 및 HPC Pack 2012](http://go.microsoft.com/fwlink/p/?LinkID=263697

* [Microsoft HPC 팩을 사용한 Azure 작업자 역할 인스턴스에 버스트](http://go.microsoft.com/fwlink/p/?LinkID=200493)
* [Azure에서 Microsoft HPC 팩을 사용하는 HPC 클러스터 옵션](../virtual-machines/virtual-machines-hpcpack-cluster-options.md)
* [Azure에서의 큰 계산: 배치 및 HPC(고성능 컴퓨팅)에 대한 기술 리소스](../batch/big-compute-resources.md)


[Overview]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/hybrid_cluster_overview.png
[install_hpc1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc1.png
[install_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc2.png
[install_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc3.png
[install_hpc4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc4.png
[install_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc6.png
[install_hpc7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc7.png
[install_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc10.png
[upload_cert1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/upload_cert1.png
[createstorage1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/createstorage1.png
[createservice1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/createservice1.png
[config_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc2.png
[config_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc3.png
[config_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc6.png
[config_hpc8]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc8.png
[config_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc10.png
[config_hpc12]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc12.png
[config_hpc13]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc13.png
[add_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1.png
[add_node1_1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1_1.png
[add_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node2.png
[add_node3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node3.png
[add_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node4.png
[add_node5]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node5.png
[add_node6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node6.png
[add_node7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node7.png
[view_instances1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances1.png
[clusrun1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/clusrun1.png
[test_job1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/test_job1.png
[param_sweep1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/param_sweep1.png
[view_job361]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job361.png
[view_job362]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job362.png
[stop_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node1.png
[stop_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node2.png
[stop_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node4.png
[view_instances2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances2.png

<!---HONumber=AcomDC_0121_2016-->