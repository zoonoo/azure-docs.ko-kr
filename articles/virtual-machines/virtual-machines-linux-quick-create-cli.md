<properties
   pageTitle="CLI를 사용하여 Azure에서 Linux VM 만들기 | Microsoft Azure"
   description="CLI를 사용하여 Azure에서 Linux VM을 만듭니다."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/04/2016"
   ms.author="v-livech"/>


# CLI를 사용하여 Azure에서 Linux VM 만들기

이 문서에서는 Azure에서 Azure CLI의 `azure vm quick-create` 명령을 사용하여 신속하게 Linux VM을 만드는 방법을 보여주며 이는 프로토타입을 사용하거나 개념을 신속하게 테스트할 수 있는 기본 인프라로 VM을 만듭니다. 이 문서에는 리소스 관리자 모드(`azure config mode arm`)인 Azure 계정([무료 평가판 가져오기](https://azure.microsoft.com/pricing/free-trial/)] 및 [Azure CLI](../xplat-cli-install.md)가 있어야 합니다.

## 빠른 명령 요약

```
# One command to quickly the VM that prompts for arguments
chrisL@fedora$ azure vm quick-create
```

## 자세한 연습

### Linux VM 만들기

다음 명령에서 원하는 이미지를 사용할 수 없지만 이 예제는 `canonical:ubuntuserver:14.04.2-LTS:latest`를 사용하여 VM을 신속하게 만듭니다. (마켓플레이스에서 이미지를 찾으려면 [이미지 검색](virtual-machines-linux-cli-ps-findimage.md) 또는 [고유의 사용자 지정 이미지 업로드](virtual-machines-linux-create-upload-generic.md)를 사용할 수 있습니다.) 다음과 같이 표시됩니다.

다음 명령 예제에서 &lt;와 &gt; 사이의 값을 사용자 환경의 값으로 바꿉니다.

```bash
# Create the Linux VM using prompts
username@macbook$ azure vm quick-create
info:    Executing command vm quick-create
Resource group name: exampleResourceGroup
Virtual machine name: exampleVMname
Location name: westus
Operating system Type [Windows, Linux]: linux
ImageURN (in the format of "publisherName:offer:skus:version") or a VHD link to the user image: Canonical:UbuntuServer:14.04.4-LTS:latest
User name: ops
Password: *********
Confirm password: *********
+ Looking up the VM "exampleVMname"
info:    Using the VM Size "Standard_D1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli133501687
info:    Could not find the storage account "cli1301687", trying to create new one
+ Creating storage account "cli133501687" in "westus"
+ Looking up the storage account cli133501687
+ Looking up the NIC "quick-westu-1363648838-nic"
info:    An nic with given name "quick-westu-1363648838-nic" not found, creating a new one
+ Looking up the virtual network "quick-westu-1363648838-vnet"
info:    Preparing to create new virtual network and subnet
\ Creating a new virtual network "quick-westu-1363648838-vnet" [address prefix: "10.0.0.0/16"] with subnet "quick-westu-13636488+8-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "quick-westu-1363648838-vnet"
+ Looking up the subnet "quick-westu-1363648838-snet" under the virtual network "quick-westu-1363648838-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "quick-westu-1363648838-pip"
info:    PublicIP with given name "quick-westu-1363648838-pip" not found, creating a new one
+ Creating public ip "quick-westu-1363648838-pip"
+ Looking up the public ip "quick-westu-1363648838-pip"
+ Creating NIC "quick-westu-1363648838-nic"
+ Looking up the NIC "quick-westu-1363648838-nic"
+ Creating VM "quickcreate"
+ Looking up the VM "exampleVMname"
+ Looking up the NIC "quick-westu-1363648838-nic"
+ Looking up the public ip "quick-westu-1363648838-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMname
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMname
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_D1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :ubuntuserver
data:        Sku                         :14.04.2-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli350d386daac1f01c-os-1457063387485
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1361687.blob.core.windows.net/vhds/cli350d386daac1f01c-os-1457063387485.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMname
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-E9-66
data:          Provisioning State        :Succeeded
data:          Name                      :quick-westu-1363648838-nic
data:          Location                  :westus
data:            Public IP address       :137.135.33.58
data:            FQDN                    :quick-westu-1363648838-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://cli13601687.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

이제 기본 SSH 포트 22의 VM에 SSH할 수 있습니다.

`azure vm quick-create`은 로그인하여 진행할 수 있도록 신속하게 VM을 만듭니다. 복잡한 환경이 아니므로 환경을 사용자 지정하려는 경우 다음이 가능합니다. [Azure Resource Manager 템플릿을 사용하여 특정 배포를 신속하게 만들](virtual-machines-linux-cli-deploy-templates.md)거나 [Azure CLI 명령을 직접 사용하여 Linux VM에 대한 고유한 사용자 지정 환경을 만들](virtual-machines-linux-cli-deploy-templates.md) 수 있습니다.

위의 예제는 다음을 만듭니다.

- VM을 배포할 Azure 리소스 그룹
- VM 이미지인 .vhd 파일을 보유한 Azure 저장소 계정
- VM에 연결을 제공하는 Azure 가상 네트워크 및 서브넷
- 네트워크와 VM을 연결하는 가상 NIC(네트워크 인터페이스 카드)
- 외부 사용에 대한 인터넷 주소를 제공한 후 환경 내부에 Linux VM을 만드는 공용 IP 주소 및 하위 도메인 접두사

이 VM은 인터넷에 직접 노출되고 사용자 이름 및 암호에 의해서만 보호됩니다.

## 다음 단계

이제 Linux VM을 신속하게 만들었으므로 테스트 또는 데모를 위해 사용합니다. 다음과 같이 Azure에서 Linux VM으로 보안 실행 환경을 만들 수 있습니다.

- [Azure 템플릿을 사용하여 Azure에서 Linux VM 만들기](virtual-machines-linux-cli-deploy-templates.md)
- [Azure에서 Azure 템플릿을 사용하여 SSH 보안 Linux VM 만들기](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Azure CLI를 사용하고 인프라를 사용자 지정하여 Azure에서 Linux VM 만들기](virtual-machines-linux-create-cli-complete.md)

독점 및 오픈 소스 인프라 배포, 구성 및 오케스트레이션 도구도 있습니다.

<!---HONumber=AcomDC_0406_2016-->