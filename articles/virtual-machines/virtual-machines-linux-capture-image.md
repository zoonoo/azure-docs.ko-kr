<properties
	pageTitle="Linux VM을 캡처하여 템플릿으로 사용 | Microsoft Azure"
	description="Azure 리소스 관리자 배포 모델을 사용하여 만든, Linux 기반 Azure VM(가상 컴퓨터)의 이미지를 캡처하는 방법을 알아봅니다."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/15/2016"
	ms.author="danlep"/>


# Linux 가상 컴퓨터를 캡처하여 리소스 관리자 템플릿으로 사용하는 방법

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-linux-classic-capture-image.md).


Azure CLI(명령줄 인터페이스)를 통해 Linux가 실행되는 Azure 가상 컴퓨터를 캡처하여 다른 가상 컴퓨터를 만드는 Azure Resource Manager 템플릿으로 사용하는 방법을 소개합니다. 이 템플릿에는 OS 디스크를 비롯해 가상 컴퓨터에 연결되는 데이터 디스크가 지정됩니다. Azure 리소스 관리자 VM을 만들 때 필요한 가상 네트워크 리소스는 포함되지 않으므로, 대부분의 경우 템플릿을 사용하는 다른 가상 컴퓨터를 만들기 전에 별도로 설정해야 합니다.

## 시작하기 전에

이 단계는 Azure 리소스 관리자 배포 모델에서 Azure 가상 컴퓨터를 이미 만들었고 응용 프로그램 설치와 같은 사용자 지정 및 데이터 디스크 연결을 비롯한 운영 체제 구성을 완료했다고 가정합니다. Azure CLI를 통한 방법을 포함하여 여러 가지 방법으로 이를 수행할 수 있습니다. 아직 완료되지 않았으면, Azure 리소스 관리자 모드에서 Azure CLI를 사용하는 관련 지침을 참고하세요.

- [Azure 리소스 관리자 템플릿 및 Azure CLI를 사용하여 가상 컴퓨터 배포 및 관리](virtual-machines-linux-cli-deploy-templates.md)

예를 들어 미국 중부 지역에 *MyResourceGroup*이라는 이름의 리소스 그룹을 만듭니다. 그 후 리소스 그룹에서 Ubuntu 14.04 LTS VM을 배포하기 위해 다음과 유사하게 **azure vm quick-create** 명령을 사용합니다.

 	azure vm quick-create -g MyResourceGroup -n <your-virtual-machine-name> "centralus" -y Linux -Q canonical:ubuntuserver:14.04.2-LTS:latest -u <your-user-name> -p <your-password>

VM이 프로비전되고 실행되면 데이터 디스크를 연결하고 탑재할 수 있습니다. [여기](virtual-machines-linux-add-disk.md)의 지침을 참조하세요.


## VM 캡처

1. VM을 캡처할 준비가 되면, SSH 클라이언트를 사용하여 VM에 연결합니다.

2. SSH 창에서 다음 명령을 입력합니다. **waagent**의 출력은 이 유틸리티의 버전에 따라 약간씩 다를 수 있습니다.

	`sudo waagent -deprovision+user`

	이 명령은 시스템을 정리하여 다시 프로비전하기에 적합하도록 만듭니다. 이 작업은 다음 작업을 수행합니다.

	- SSH 호스트 키를 제거합니다(구성 파일에서 Provisioning.RegenerateSshHostKeyPair가 'y'인 경우).
	- /etc/resolv.conf의 Nameserver 구성을 지웁니다.
	- /etc/shadow에서 `root` 사용자의 암호를 제거합니다(구성 파일에서 Provisioning.DeleteRootPassword가 'y'인 경우).
	- 캐시된 DHCP 클라이언트 임대 제거
	- 호스트 이름을 localhost.localdomain으로 다시 설정
	- 마지막으로 프로비전된 사용자 계정(/var/lib/waagent에서 얻은) 및 관련 데이터를 삭제합니다.

	>[AZURE.NOTE] 프로비저닝은 파일 및 데이터를 삭제하여 이미지를 "일반화"합니다. 이미지로 캡처하려는 VM에서만 이 명령을 실행합니다. 하지만 이미지에서 중요한 정보가 모두 지워지고 이미지가 제3자에게 다시 배포하기에 적합하다고 보증할 수는 없습니다.

3. 계속하려면 **y**를 입력합니다. 이 확인 단계를 피하려면 **-force** 매개 변수를 추가합니다.

4. SSH 클라이언트를 닫으려면 **exit**를 입력합니다.

	>[AZURE.NOTE] 다음 단계에서는 클라이언트 컴퓨터에 이미 [Azure CLI를 설치](../xplat-cli-install.md)했다고 가정합니다.

5. 클라이언트 컴퓨터에서 Azure CLI를 열고 Azure 구독에 로그인합니다. 자세한 내용은 [Azure CLI에서 Azure 구독에 연결](../xplat-cli-connect.md)을 참조하세요.

6. 리소스 관리자 모드에 있는지 확인합니다.

	`azure config mode arm`

7. 다음 명령을 사용하여 프로비전을 해제한 VM을 중지합니다.

	`azure vm deallocate -g <your-resource-group-name> -n <your-virtual-machine-name>`

8. 다음 명령을 사용하여 VM을 일반화합니다.

	`azure vm generalize –g <your-resource-group-name> -n <your-virtual-machine-name>`

9. 이제 다음 명령을 사용하여 이미지와 로컬 파일 템플릿을 캡처합니다.

	`azure vm capture <your-resource-group-name>  <your-virtual-machine-name> <your-vhd-name-prefix> -t <your-template-file-name.json>`

	이 명령은 VM 디스크에 지정한 VHD 이름 접두사를 사용하여 일반화된 OS 이미지를 만듭니다. 이미지 VHD 파일이 원본 VM이 사용된 동일한 저장소 계정에서 기본적으로 생성됩니다. **-t** 옵션은 이미지로부터 새로운 VM을 만들 때 사용할 수 있는 로컬 JSON 파일 템플릿을 만듭니다.

>[AZURE.TIP] 이미지의 위치를 찾으려면 JSON 파일 템플릿을 엽니다. **storageProfile**에서 **시스템** 컨테이너에 있는 **이미지**의 **uri**를 찾습니다. 예를 들어, OS 디스크 이미지의 uri는 `https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/<your-image-prefix>-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`와 유사합니다.

## 캡처한 이미지에서 새 VM 만들기
템플릿과 이미지를 사용하여 새 Linux VM을 만듭니다. 이 단계는 `azure vm capture` 명령을 사용하여 만든 Azure CLI 및 JSON 파일 템플릿을 사용하여 새 가상 네트워크에 VM을 만드는 방법을 보여줍니다.

### 네트워크 리소스 만들기

템플릿을 사용하려면 우선 새 VM에 대한 NIC와 가상 네트워크를 생성해야 합니다. 이 리소스에 대해 새로운 리소스 그룹을 만드는 것이 좋습니다. 다음과 유사한 명령을, 리소스 이름과 Azure 위치(이 명령의 경우 “centralus”)를 적절하게 대체한 후 실행합니다.

	azure group create <your-new-resource-group-name> -l "centralus"

	azure network vnet create <your-new-resource-group-name> <your-vnet-name> -l "centralus"

	azure network vnet subnet create <your-new-resource-group-name> <your-vnet-name> <your-subnet-name>

	azure network public-ip create <your-new-resource-group-name> <your-ip-name> -l "centralus"

	azure network nic create <your-new-resource-group-name> <your-nic-name> -k <your-subnetname> -m <your-vnet-name> -p <your-ip-name> -l "centralus"

캡처를 하는 동안 저장한 JSON을 사용하여 이미지의 VM을 배포하려면 NIC의 ID가 필요합니다. 다음 명령을 실행하여 ID를 확보합니다.

	azure network nic show <your-new-resource-group-name> <your-nic-name>

출력된 **Id**는 아래와 유사한 문자열입니다.

	/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<your-new-resource-group-name>/providers/Microsoft.Network/networkInterfaces/<your-nic-name>



### 새 배포 만들기
이제 다음 명령을 실행하여 캡처한 VM과 저장한 템플릿 JSON 파일로 VM을 만듭니다.

	azure group deployment create <your-new-resource-group-name> <your-new-deployment-name> -f <your-template-file-name.json>

새 VM 이름, 관리자 사용자 이름, 암호 및 이전에 만든 NIC의 ID를 제공하라는 프롬프트가 표시됩니다.

	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	vmName: mynewvm
	adminUserName: myadminuser
	adminPassword: ********
	networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic

배포에 성공하면 다음과 유사한 출력이 표시됩니다.

	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "dlnewdeploy"
	+ Waiting for deployment to complete
	data:    DeploymentName     : mynewdeploy
	data:    ResourceGroupName  : mynewrg
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-10-29T16:35:47.3419991Z
	data:    Mode               : Incremental
	data:    Name                Type          Value


	data:    ------------------  ------------  -------------------------------------

	data:    vmName              String        mynewvm


	data:    vmSize              String        Standard_D1


	data:    adminUserName       String        myadminuser


	data:    adminPassword       SecureString  undefined


	data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic
	info:    group deployment create command OK

### 배포 확인

생성한 가상 컴퓨터에 SSH를 실행하여 배포를 확인하고 새 VM 사용을 시작합니다. SSH를 통해 연결하려면 다음 명령을 실행하여 생성한 VM의 IP 주소를 찾습니다.

	azure network public-ip show <your-new-resource-group-name> <your-ip-name>

명령 출력에 공용 IP 주소가 나열됩니다. 기본적으로 SSH 포트 22를 사용하여 Linux VM에 연결합니다.

## 템플릿으로 추가 VM 만들기

앞의 섹션에 설명된 단계를 사용하여 캡처한 이미지와 템플릿을 사용하여 추가 VM을 배포합니다.

* VM 이미지가 VM의 VHD를 호스팅하는 계정과 동일한 저장소 계정에 있어야 합니다.
* 템플릿 JSON 파일을 복사하고 각 VM의 VHD의 **uri**에 고유한 값을 입력합니다.
* 새 NIC를 동일한 가상 네트워크 또는 다른 가상 네트워크에 만듭니다.
* 수정된 템플릿 JSON 파일을 사용하여, 가상 네트워크를 설정한 리소스 그룹에 배포를 만듭니다.

이미지에서 VM을 만들 때 네트워크에서 자동으로 설정되도록 하려면 GitHub의 [101-vm-from-user-image 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)을 사용합니다. 이 템플릿은 사용자 지정 이미지에서 VM을 만들고 필요한 가상 네트워크, 공용 IP 주소, NIC 리소스를 만듭니다. Azure 포털에서 템플릿 사용에 대한 안내는 [ARM 템플릿을 사용하여 사용자 지정 이미지에서 가상 컴퓨터 만드는 방법](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/)을 참조하십시오.

## azure vm create 명령 사용

리소스 관리자 템플릿을 사용하여 이미지에서 VM을 만드는 것이 일반적입니다. 하지만 **-Q**(**--image-urn**) 매개 변수와 함께 **azure vm create** 명령을 사용하면 _명령문_으로 VM을 만들 수 있습니다. 새 VM에 대한 OS .vhd 파일 위치를 지정하는 **-d**(**--os-dist-vhd**) 매개 변수도 전달합니다. 이는 이미지 VHD 파일이 저장된 저장소 계정의 VHD 컨테이너에 있어야 합니다. 이 명령은 새 VM에 대한 VHD를 자동으로 VHD 컨테이너에 복사합니다.

이미지에 **azure vm create**을 실행하기 전에 다음을 수행합니다.

1.	새 리소스 그룹을 만들거나 배포를 위한 기존 리소스 그룹을 확인합니다.

2.	새 VM에 대한 NIC 리소스와 공용 IP 주소 리소스를 만듭니다. CLI를 사용하여 가상 네트워크, 공용 IP 주소, NIC를 만드는 단계는 이 문서의 앞쪽을 참조하세요.(**azure vm create** 명령이 새 NIC를 만들 수 있지만 가상 네트워크와 서브넷을 위해 매개 변수를 추가로 전달해야 합니다.)


그런 다음 새 OS VHD 파일 및 기존 이미지에 URI를 전달하는 다음과 유사한 명령을 실행합니다.

	azure vm create <your-resource-group-name> <your-new-vm-name> eastus Linux -d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/<your-new-VM-prefix>.vhd" -Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/<your-image-prefix>-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd" -z Standard_A1 -u <your-admin-name> -p <your-admin-password> -f <your-nic-name>

추가적인 명령 옵션은 `azure help vm create`을 실행합니다.

## 다음 단계

CLI를 사용하여 VM을 관리하려면 [Azure 리소스 관리자 템플릿 및 Azure CLI를 사용하여 가상 컴퓨터 배포 및 관리](virtual-machines-linux-cli-deploy-templates.md)를 참조하세요.

<!---HONumber=AcomDC_0511_2016-->