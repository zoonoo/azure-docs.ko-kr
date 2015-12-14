<properties
 pageTitle="Azure VM에서 HPC 팩 헤드 노드 만들기 | Microsoft Azure"
 description="Azure 클래식 포털과 클래식 배포 모델을 사용하여 Azure VM에서 Microsoft HPC Pack 헤드 노드를 만드는 방법에 대해 알아봅니다."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# 마켓플레이스 이미지를 사용하여 Azure VM에 HPC 팩 클러스터의 헤드 노드 만들기

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.


이 문서는 Azure 마켓플레이스의 [Microsoft HPC 팩 가상 머신 이미지](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)를 사용하여 Azure에서 클래식(서비스 관리) 배포 모델로 Windows HPC 클러스터의 헤드 노드를 만드는 방법을 보여줍니다. 헤드 노드는 Azure 가상 네트워크의 Active Directory 도메인에 연결해야 합니다. Azure에서 HPC 팩 개념 증명 배포로 이 헤드 노드를 사용하고 클러스터에 계산 리소스를 추가하여 HPC 워크로드를 실행할 수 있습니다.


![HPC 팩 헤드 노드][headnode]

>[AZURE.NOTE]현재, HPC 팩 VM 이미지는 HPC 팩 2012 R2 업데이트 2가 미리 설치된 Windows Server 2012 R2 Datacenter를 기준으로 합니다. Microsoft SQL Server 2014 Express도 미리 설치되어 있습니다.


Azure의 HPC 팩 클러스터 프로덕션 배포의 경우 [HPC 팩 IaaS 배포 스크립트](virtual-machines-hpcpack-cluster-powershell-script.md), Azure 리소스 관리자 [빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)과 같은 자동 배포 방법을 사용하는 것이 좋습니다.

## 고려 사항 계획

* **Active Directory 도메인** - HPC 서비스를 시작하기 전에 Azure에서 HPC 팩 헤드 노드를 Active Directory 도메인에 연결해야 합니다. 별도의 도메인 컨트롤러를 배포하고 VM을 연결할 수 있는 Azure에 포리스트를 배포하는 방법도 있습니다. 개념 증명 배포의 경우 HPC 서비스를 시작하기 전에 헤드 노드에 대해 도메인 컨트롤러로 만드는 VM을 승격할 수 있습니다.

* **Azure 가상 네트워크** - HPC 클러스터에 클러스터 계산 노드 VM을 추가하거나 클러스터를 위해 별도의 도메인 컨트롤러를 만드는 경우 Azure 가상 네트워크(VNet)에 헤드 노드를 배포해야 합니다. VNet이 없으면 클러스터에 Azure "버스트" 노드를 추가할 수 있습니다.

## 헤드 노드를 만드는 단계

다음은 HPC 팩 헤드 노드용 Azure VM을 만드는 개략적 단계입니다. 다양한 Azure 도구를 사용하여 Azure 클래식(서비스 관리) 배포 모델로 이러한 단계를 수행할 수 있습니다.


1. 헤드 노드 VM에 대한 VNet을 만들려면,[ Azure 포털을 사용하여 가상 네트워크(클래식) 만들기](../virtual-networks/virtual-networks-create-vnet-classic-pportal.md)를 참조하세요.

    **고려 사항**

    * 가상 네트워크 주소 공간 및 서브넷의 기본 구성을 수락할 수 있습니다.

    * HPC 팩 헤드 노드에 계산 집약적인 인스턴스 크기(A8 – A11)를 사용하거나 나중에 클러스터에 계산 리소스를 추가하는 경우 인스턴스를 사용할 수 있는 지역을 선택합니다. MPI 워크로드에 대해 A8 또는 A9 인스턴스를 사용하는 경우 가상 네트워크의 주소 공간이 Azure의 RDMA 네트워크에서 예약한 주소 공간(172.16.0.0/12)과 중복되지 않는지 확인하십시오. 자세한 내용은 [A8, A9, A10 및 A11 계산 집약적인 인스턴스 정보](virtual-machines-a8-a9-a10-a11-specs.md)를 참조하세요.

2. 별도 VM에 새 Active Directory 포리스트를 만들어야 하는 경우 [Azure 가상 네트워크에 새 Active Directory 포리스트 설치](../active-directory/active-directory-new-forest-virtual-machine.md)를 참조하세요.

    **고려 사항**

    * 많은 테스트 배포에서 Azure에 단일 도메인 컨트롤러를 만들 수 있습니다. Active Directory 도메인에서 높은 가용성을 보장하려면 추가적인 백업 도메인 컨트롤러를 배포할 수 있습니다.

    * 간단한 개념 증명 배포의 경우 이 단계를 생략하고 나중에 헤드 노드 VM을 도메인 컨트롤러로 승격할 수 있습니다.

3. Azure 클래식 포털 또는 Azure 포털에서 Azure 마켓플레이스의 HPC Pack 2012 R2 이미지를 선택하여 클래식 VM을 만듭니다. ([여기](virtual-machines-windows-tutorial-classic-portal.md)에서 Azure 클래식 포털에 대한 단계를 참조하세요.)

    **고려 사항**

    * 최소 A4 이상인 VM 크기를 선택합니다.

    * VNet에 헤드 노드를 배포하려는 경우 VM 구성에 VNet을 지정해야 합니다.

    * VM에 대해 새 클라우드 서비스를 만드는 것이 좋습니다.

4. VM을 만든 다음 VM이 실행되면 VM을 기존 도메인 포리스트에 연결하거나 VM에 새 도메인 포리스트를 만듭니다.

    **고려 사항**

    * 기존 도메인 포리스트를 사용하여 Azure VNet에 VM을 만든 경우 VM에 연결합니다. 그런 다음 표준 서버 관리자 또는 Windows PowerShell 도구를 사용하여 도메인 포리스트에 연결합니다. 그런 다음 다시 시작합니다.

    * Azure VNet에 VM이 생성되지 않았거나 기존 도메인 포리스트 없이 VNet에 생성된 경우 도메인 컨트롤러로 승격합니다. 이렇게 하려면 VM에 연결한 다음 표준 서버 관리자 또는 Windows PowerShell 도구를 사용합니다. 자세한 단계를 보려면 [새 Windows Server 2012 Active Directory 포리스트 설치](https://technet.microsoft.com/library/jj574166.aspx)를 참조하세요.

5. VM이 실행되고 Active Directory 포리스트에 연결되면 헤드 노드에서 HPC 팩 서비스를 시작합니다. 다음을 수행합니다.

    a. 로컬 관리자 그룹의 구성원인 도메인 계정을 사용하여 VM에 연결합니다. 예를 들어 헤드 노드 VM을 만들 때 설정한 관리자 계정을 사용할 수 있습니다.

    b. 기본 헤드 노드 구성의 경우 관리자 권한으로 Windows PowerShell을 시작하고 다음을 입력합니다.

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    HPC 팩 서비스가 시작하는 데 몇 분이 소요될 수 있습니다.

    추가 헤드 노드 구성 옵션을 보려면 `get-help HPCHNPrepare.ps1`을 입력합니다.


## 다음 단계

* 이제 Windows HPC 클러스터의 헤드 노드를 사용할 수 있습니다. 예를 들어 HPC 클러스터 관리자를 시작하거나 HPC PowerShell cmdlet 사용을 시작할 수 있습니다.

* 클러스터에 [계산 노드 VM을 추가](virtual-machines-hpcpack-cluster-node-manage.md)하거나 클라우드 서비스에 [Azure 버스트 노드](virtual-machines-hpcpack-cluster-node-burst.md)를 추가합니다.

* 클러스터에서 테스트 워크로드를 실행합니다. 예를 보려면 HPC 팩 [시작하기 가이드](https://technet.microsoft.com/library/jj884144)를 참조하세요.

<!--Image references-->
[headnode]: ./media/virtual-machines-hpcpack-cluster-headnode/headnode.png

<!---HONumber=AcomDC_1203_2015-->