<properties 
	pageTitle="Linux 및 Mac에서 SSH 사용 | Microsoft Azure" 
	description="Azure의 리소스 관리자 및 클래식 배포 모델을 사용할 수 있도록 Linux 및 Mac에서 SSH 키를 생성하여 사용합니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/28/2015" 
	ms.author="rasquill"/>

#Azure에서 Linux 및 Mac과 함께 SSH를 사용하는 방법

> [AZURE.SELECTOR]
- [Windows](../articles/virtual-machines/virtual-machines-windows-use-ssh-key.md)
- [Linux/Mac](../articles/virtual-machines/virtual-machines-linux-use-ssh-key.md)

이 항목에서는 Linux와 Mac에서 **ssh-keygen** 및 **openssl**을 사용하여 **ssh-rsa** 형식 및 **.pem** 형식 파일을 만들어서 Linux 기반의 Azure VM과 안전하게 통신하는 방법을 설명합니다. 신규 배포에서 *ssh-rsa* 유형 공개 키 파일 또는 문자열(배포 클라이언트에 따라)을 취하는 경우에는 리소스 관리자 배포 모델을 사용하여 Linux 기반 Azure 가상 컴퓨터를 만드는 것이 좋습니다. 현재 [Preview 포털](https://portal.azure.com)에서는 클래식 또는 리소스 관리자 배포에 **ssh-rsa** 형식 문자열만 허용합니다.

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]Azure에서 Linux VM과 안전하게 통신할 수 있도록 이러한 파일 유형을 만들어서 Windows 컴퓨터에 사용하는 방법은 [Windows에서 SSH 키 사용](virtual-machines-windows-use-ssh-key.md)을 참조하세요.

## 어떤 파일이 필요합니까?

Azure에 대한 기본 SSH 설정은 2048비트(기본적으로 **ssh-keygen**은 기본값을 변경하지 않는 한 이러한 파일을 **~/.ssh/id\_rsa** 및 **~/.ssh/id-rsa.pub**으로 저장)의 **ssh-rsa** 공용 및 개인 키 쌍뿐만 아니라 클래식 포털의 클래식 배포 모델과 함께 사용하기 위한 **id\_rsa** 개인 키 파일에서 생성된 `.pem` 파일을 포함합니다.

다음은 배포 시나리오 및 각각에 사용되는 파일 형식입니다.

1. **ssh-rsa** 키는 배포 모델과 관계없이 [Preview 포털](https://portal.azure.com)을 사용하는 모든 배포에 필요합니다.
2. [클래식 포털](https://manage.windowsazure.com)을 사용하는 VM을 만들려면 .pem 파일이 필요합니다. .pem 파일은 [Azure CLI](xplat-cli-install.md)를 사용하는 클래식 배포에서도 지원됩니다. 

## SSH와 함께 사용할 키 생성

시나리오에 따라 Azure에 2048비트의 **ssh-rsa** 형식 키 파일 또는 그에 해당하는 .pem 파일이 필요합니다. 이러한 파일이 이미 있는 경우 Azure VM을 만들 때 공개 키 파일을 전달하세요.

파일을 만들어야 하는 경우:

1. **ssh-keygen** 및 **openssl** 구현이 최신 상태인지 확인합니다. 이는 플랫폼에 따라 다릅니다. 

	- Mac의 경우 [Apple 제품 보안 웹 사이트](https://support.apple.com/HT201222)를 방문하여 필요에 따라 적절한 업데이트를 선택합니다.
	- Ubuntu, Debian, Mint 등과 같은 Debian 기반 Linux 배포의 경우:

			sudo apt-get update ssh-keygen
			sudo apt-get update openssl

	- CentOS, Oracle Linux 등과 같은 RPM 기반 Linux 배포의 경우:

			sudo yum update ssh-keygen
			sudo yum update openssl

	- SLES 및 OpenSUSE의 경우

			sudo zypper update ssh-keygen
			sudo zypper update openssl

2. **ssh-keygen**을 사용하여 2048비트 RSA 공개 및 개인 키 파일을 만들고, 파일의 특정 위치 또는 특정 이름이 없는 경우에는 `~/.ssh/id_rsa`의 기본 위치 및 이름을 수락합니다. 기본 명령은 다음과 같습니다.

		ssh-keygen -t rsa -b 2048 

	일반적으로 **ssh-keygen** 구현은 주석을 추가하는데, 컴퓨터의 사용자 이름 및 호스트 이름인 경우가 자주 있습니다. `-C` 옵션을 사용하여 특정 주석을 지정할 수 있습니다.

3. 클래식 포털을 작업할 수 있도록 `~/.ssh/id_rsa` 파일에서 .pem 파일을 만듭니다. 다음과 같이 **openssl**을 사용합니다.

		openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

	다른 개인 키 파일에서 .pem 파일을 만들려면 `-key` 인수를 수정하세요.

> [AZURE.NOTE]클래식 배포 모델로 배포되는 서비스를 관리하려는 경우 **.cer** 서식 파일을 만들어 포털에 업로드하려고 할 수 있습니다. 여기엔 이 문서의 주제인 Linux VM에 연결 또는 **ssh**는 포함되어 있지 않습니다. Linux 또는 Mac에서 이러한 파일을 만들려면 <br /> openssl.exe x509 -outform der -in myCert.pem -out myCert.cer을 입력합니다.

.pem 파일을 DER 인코딩된 X509 인증서 파일로 변환하려면

## 이미 갖고 있는 SSH 키 사용

모든 새 작업에 ssh-rsa(`.pub`) 키를 사용할 수 있으며, 특히 리소스 관리자 배포 모델 및 미리 보기 포털과 함께 사용할 수 있습니다. 클래식 포털을 사용해야 하는 경우 키에서 `.pem` 파일을 만들어야 할 수도 있습니다.

## 공개 키 파일을 사용하여 VM 만들기

필요한 파일을 만든 후에는 VM을 만들고 공개-개인 키 교환을 사용하여 안전하게 이 VM에 연결하는 다양한 방법이 있습니다. 대부분의 경우, 특히 ssh 키 파일 경로 또는 파일 내용을 문자열로 입력하라는 메시지가 표시되면 리소스 관리자 배포를 사용하여 .pub 파일을 전달하세요.

### 예: id\_rsa.pub 파일을 사용하여 VM 만들기

가장 일반적인 사용법은 명령을 통해 VM을 만들거나 템플릿을 업로드하여 VM을 만드는 경우입니다. 다음 코드 예제에서는 공개 파일 이름(이 예에서는 기본 `~/.ssh/id_rsa` 파일)을 `azure vm create` 명령에 전달하여 안전한 Linux VM을 새로 만드는 방법을 보여줍니다. (다른 인수는 이전에 만들었습니다.)

	azure vm create \
	--nic-name testnic \
	--public-ip-name testpip \
	--vnet-name testvnet \
	--vnet-subnet-name testsubnet \
	--storage-account-name computeteststore 
	--image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
	--username ops \
	-ssh-publickey-file ~/.ssh/id_rsa \
	testrg testvm westeurope linux

그 다음 예에서는 리소스 관리자 템플릿 및 Azure CLI와 함께 **ssh-rsa** 형식을 사용하여 Ubuntu VM을 만들고 사용자 이름 및 `~/.ssh/id_rsa.pub`의 내용을 문자열로 입력하여 보호하는 방법을 보여줍니다. (이 예에서는 쉽게 읽을 수 있도록 공개 키 문자열의 길이를 줄였습니다.)

	azure group deployment create \
	--resource-group test-sshtemplate \
	--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
	--name mysshdeployment
	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	testnewStorageAccountName: testsshvmtemplate3
	adminUserName: ops
	sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQA+/L+rHIjz+nXTzxApgnP+iKDZco9 user@macbookpro
	dnsNameForPublicIP: testsshvmtemplate
	location: West Europe
	vmName: sshvm
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "mysshdeployment"
	+ Waiting for deployment to complete
	data:    DeploymentName     : mysshdeployment
	data:    ResourceGroupName  : test-sshtemplate
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-10-08T00:12:12.2529678Z
	data:    Mode               : Incremental
	data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	data:    ContentVersion     : 1.0.0.0
	data:    Name                   Type    Value

	data:    newStorageAccountName  String  testtestsshvmtemplate3
	data:    adminUserName          String  ops
	data:    sshKeyData             String  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDAkek3P6V3EhmD+xP+iKDZco9 user@macbookpro
	data:    dnsNameForPublicIP     String  testsshvmtemplate
	data:    location               String  West Europe
	data:    vmSize                 String  Standard_A2
	data:    vmName                 String  sshvm
	data:    ubuntuOSVersion        String  14.04.2-LTS
	info:    group deployment create command OK


### 예: .pem 파일을 사용하여 VM 만들기

그런 후에는 다음 예와 같이 .pem 파일을 클래식 포털 또는 클래식 배포 모드 및 `azure vm create`와 함께 사용할 수 있습니다.

	azure vm create \
	-l "West US" -n testpemasm \
	-P -t myCert.pem -e 22 \
	testpemasm \
	b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-ko-KR-30GB \
	ops
	info:    Executing command vm create
	warn:    --vm-size has not been specified. Defaulting to "Small".
	+ Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-ko-KR-30GB
	+ Looking up cloud service
	info:    cloud service testpemasm not found.
	+ Creating cloud service
	+ Retrieving storage accounts
	+ Configuring certificate
	+ Creating VM
	info:    vm create command OK


## VM에 연결

**ssh** 명령은 로그온에 사용할 사용자 이름, 컴퓨터의 네트워크 주소 및 주소에 연결할 포트와 그 밖에 여러 특수한 변형을 가져옵니다. **ssh**에 대한 자세한 내용은 [보안 셸의 문서](https://en.wikipedia.org/wiki/Secure_Shell)로 시작할 수 있습니다.

하위 도메인과 배포 위치만 지정한 경우 일반적인 리소스 관리자 배포 사용 방법은 다음과 같습니다.

	ssh user@subdomain.westus.cloudapp.azure.com -p 22

또는 클래식 배포 클라우드 서비스에 연결하는 경우에 사용하는 주소는 다음과 같습니다.

	ssh user@subdomain.cloudapp.net -p 22

주소 양식이 변경될 가능성이 있기 때문에 항상 IP 주소를 사용할 수 있거나 사용자 지정 도메인 이름이 할당됩니다. 이 경우 Azure VM의 주소를 검색해야 합니다.

### 클래식 배포를 사용하여 Azure VM SSH 주소 검색

VM 이름과 함께 `azure vm show` 명령을 사용하여 VM 및 클래식 배포 모델에 사용할 주소를 검색할 수 있습니다.

	azure vm show testpemasm
	info:    Executing command vm show
	+ Getting virtual machines
	data:    DNSName "testpemasm.cloudapp.net"
	data:    Location "West US"
	data:    VMName "testpemasm"
	data:    IPAddress "100.116.160.154"
	data:    InstanceStatus "ReadyRole"
	data:    InstanceSize "Small"
	data:    Image "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-ko-KR-30GB"
	data:    OSDisk hostCaching "ReadWrite"
	data:    OSDisk name "testpemasm-testpemasm-0-201510102050230517"
	data:    OSDisk mediaLink "https://portalvhds4blttsxgjj1rf.blob.core.windows.net/vhd-store/testpemasm-2747c9c432b043ff.vhd"
	data:    OSDisk sourceImageName "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-ko-KR-30GB"
	data:    OSDisk operatingSystem "Linux"
	data:    OSDisk iOType "Standard"
	data:    ReservedIPName ""
	data:    VirtualIPAddresses 0 address "40.83.178.221"
	data:    VirtualIPAddresses 0 name "testpemasmContractContract"
	data:    VirtualIPAddresses 0 isDnsProgrammed true
	data:    Network Endpoints 0 localPort 22
	data:    Network Endpoints 0 name "ssh"
	data:    Network Endpoints 0 port 22
	data:    Network Endpoints 0 protocol "tcp"
	data:    Network Endpoints 0 virtualIPAddress "40.83.178.221"
	data:    Network Endpoints 0 enableDirectServerReturn false
	info:    vm show command OK

### 리소스 관리자 배포를 사용하여 Azure VM SSH 주소 검색

	azure vm show testrg testvm
	info:    Executing command vm show
	+ Looking up the VM "testvm"
	+ Looking up the NIC "testnic"
	+ Looking up the public ip "testpip"

네트워크 프로필 섹션을 검사합니다.

	data:    Network Profile:
	data:      Network Interfaces:
	data:        Network Interface #1:
	data:          Id                        :/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic
	data:          Primary                   :true
	data:          MAC Address               :00-0D-3A-21-8E-AE
	data:          Provisioning State        :Succeeded
	data:          Name                      :testnic
	data:          Location                  :westeurope
	data:            Private IP alloc-method :Static
	data:            Private IP address      :192.168.1.101
	data:            Public IP address       :40.115.48.189
	data:            FQDN                    :testsubdomain.westeurope.cloudapp.azure.com
	data:
	data:    Diagnostics Instance View:
	info:    vm show command OK

VM을 만들 때 기본 SSH 포트 22를 사용하지 않았으면 다음 예와 같이 `azure network nsg show` 명령을 사용하여 인바운드 규칙이 있는 포트를 검색할 수 있습니다.

	azure network nsg show testrg testnsg
	info:    Executing command network nsg show
	+ Looking up the network security group "testnsg"
	data:    Id                              : /subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testnsg
	data:    Name                            : testnsg
	data:    Type                            : Microsoft.Network/networkSecurityGroups
	data:    Location                        : westeurope
	data:    Provisioning state              : Succeeded
	data:    Security group rules:
	data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
	data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
	data:    testnsgrule                    *                  *            *               22                Tcp       Inbound    Allow   1000
	data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
	data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
	data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
	data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
	data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
	data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
	info:    network nsg show command OK

### 예: .pem 키 및 클래식 배포를 사용하는 SSH 세션의 출력

`~/.ssh/id_rsa` 파일에서 만든 .pem 파일을 사용하여 VM을 만든 경우 해당 VM으로 직접 ssh를 수행할 수 있습니다. 이렇게 하면 인증서 핸드셰이크가 `~/.ssh/id_rsa`에 개인 키를 사용하게 됩니다. 형식은 다음 예와 유사합니다.

	ssh ops@testpemasm.cloudapp.net -p 22
	The authenticity of host 'testpemasm.cloudapp.net (40.83.178.221)' can't be established.
	RSA key fingerprint is dc:bb:e4:cc:59:db:b9:49:dc:71:a3:c8:37:36:fd:62.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added 'testpemasm.cloudapp.net,40.83.178.221' (RSA) to the list of known hosts.
	Saving password to keychain failed
	Identity added: /Users/rasquill/.ssh/id_rsa (/Users/rasquill/.ssh/id_rsa)
	Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-28-generic x86_64)

	* Documentation:  https://help.ubuntu.com/

	System information as of Sat Oct 10 20:53:08 UTC 2015

	System load: 0.52              Memory usage: 5%   Processes:       80
	Usage of /:  45.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

	Graph this data and manage this system at:
		https://landscape.canonical.com/

	Get cloud support with Ubuntu Advantage Cloud Guest:
		http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.

	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

## 연결에 문제가 있는 경우

[SSH 연결 문제 해결](virtual-machines-troubleshoot-ssh-connections.md)의 제안을 읽고 문제 해결에 도움이 되는지 알아보세요.

## 다음 단계
 
VM에 연결했으니, 선택한 배포를 계속 사용할 수 있도록 업데이트해야 합니다.

<!---HONumber=AcomDC_1217_2015-->