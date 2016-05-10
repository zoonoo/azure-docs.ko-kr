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
   ms.date="04/29/2016"
   ms.author="v-livech"/>


# CLI를 사용하여 Azure에서 Linux VM 만들기

이 문서에서는 Azure CLI의 `azure vm quick-create` 명령을 사용하여 Azure에서 Linux 가상 컴퓨터를 신속하게 배포하는 방법을 보여 줍니다. `quick-create` 명령은 개념을 신속하게 프로토타입하거나 테스트하는 데 사용할 수 있는 기본 인프라로 VM을 배포합니다. Linux bash 셸에 대한 가장 빠른 방법으로 생각합니다. 이 문서에는 Azure 계정([무료 평가판 가져오기](https://azure.microsoft.com/pricing/free-trial/))과, 리소스 관리자 모드(`azure config mode arm`)의 [Azure CLI](../xplat-cli-install.md) 로그인(`azure login`)이 필요합니다. [Azure 포털](virtual-machines-linux-quick-create-portal.md)을 사용하여 Linux VM을 신속히 배포할 수도 있습니다.

## 빠른 명령 요약

VM를 배포하고 SSH 키를 연결하는 하나의 명령

```
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
```

## Linux VM 배포

위와 동일한 명령을 사용하여 각 프롬프트와, 나타나는 출력을 살펴보겠습니다.

ImageURN의 경우 `canonical:ubuntuserver:14.04.2-LTS:latest`을 사용하여 Ubuntu 14.04 VM을 배포합니다. (마켓플레이스에서 이미지를 찾으려면 [이미지 검색](virtual-machines-linux-cli-ps-findimage.md) 또는 [고유의 사용자 지정 이미지 업로드](virtual-machines-linux-create-upload-generic.md)를 사용할 수 있습니다.)

다음 명령 연습에서 프롬프트를 사용자 환경의 값으로 바꿉니다. 여기서는 "example" 값을 사용합니다. 출력에는 다음과 같은 출력 블록이 있습니다.

프롬프트에 따라 고유한 이름을 입력합니다.

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
```

출력

```bash
info:    Executing command vm quick-create
Resource group name: exampleRGname
Virtual machine name: exampleVMname
Location name: westus
Operating system Type [Windows, Linux]: Linux
ImageURN (in the format of "publisherName:offer:skus:version") or a VHD link to the user image: Canonical:UbuntuServer:14.04.4-LTS:latest
User name: ahmet
Password: ************************************************
Confirm password: ************************************************
+ Looking up the VM "exampleVMname"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/azure_id_rsa.pub
info:    Using the VM Size "Standard_D1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli38948918364134011018
info:    Could not find the storage account "cli38948918364134011018", trying to create new one
+ Creating storage account "cli38948918364134011018" in "westus"
+ Looking up the storage account cli38948918364134011018
+ Looking up the NIC "examp-westu-3894891836-nic"
info:    An nic with given name "examp-westu-3894891836-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-3894891836-vnet"
info:    Preparing to create new virtual network and subnet
| Creating a new virtual network "examp-westu-3894891836-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-3894891836-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-3894891836-vnet"
+ Looking up the subnet "examp-westu-3894891836-snet" under the virtual network "examp-westu-3894891836-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-3894891836-pip"
info:    PublicIP with given name "examp-westu-3894891836-pip" not found, creating a new one
+ Creating public ip "examp-westu-3894891836-pip"
+ Looking up the public ip "examp-westu-3894891836-pip"
+ Creating NIC "examp-westu-3894891836-nic"
+ Looking up the NIC "examp-westu-3894891836-nic"
+ Creating VM "exampleVMname"
+ Looking up the VM "exampleVMname"
+ Looking up the NIC "examp-westu-3894891836-nic"
+ Looking up the public ip "examp-westu-3894891836-pip"
data:    Id                              :/subscriptions/<**subscriptionsID**>/resourceGroups/exampleRGname/providers/Microsoft.Compute/virtualMachines/exampleVMname
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
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clife36db80ae0539d2-os-1460152163612
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://<**subscriptionsID**>.blob.core.windows.net/vhds/clife36db80ae0539d2-os-1460152163612.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMname
data:      User Name                     :ahmet
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-4C-B2
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-3894891836-nic
data:          Location                  :westus
data:            Public IP address       :13.88.22.244
data:            FQDN                    :examp-westu-3894891836-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://<**subscriptionsID**>.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

이제 위의 출력에 나열된 기본 SSH 포트 22 및 공용 IP 주소(또는 정규화된 도메인 이름 -- FQDN) 에서 VM에 SSH할 수 있습니다.

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.88.22.244
```

## 다음 단계

`azure vm quick-create`은 bash 셸에 로그인하고 작업할 수 있도록 VM을 신속하게 배포하는 방법입니다. `vm quick-create` 사용 시 복잡한 환경에서 추가적인 혜택은 없습니다. 아래 문서 중 하나를 수행하여 인프라에 대해 사용자 지정된 Linux VM을 배포합니다.

- [Azure 리소스 관리자 템플릿을 사용하여 특정 배포 만들기](virtual-machines-linux-cli-deploy-templates.md)
- [Azure CLI 명령을 직접 사용하여 Linux VM에 대한 고유한 사용자 지정 환경 만들기](virtual-machines-linux-cli-deploy-templates.md)
- [템플릿을 사용하여 Azure에서 Linux VM 만들기](virtual-machines-linux-cli-deploy-templates.md)
- [템플릿을 사용하여 Azure에서 SSH 보안 Linux VM 만들기](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Azure CLI를 사용하여 Linux VM 만들기](virtual-machines-linux-create-cli-complete.md)

이러한 문서로 Azure 인프라뿐만 아니라 독점 및 오픈 소스 인프라 배포, 구성 및 오케스트레이션 도구 작성을 시작합니다.

<!---HONumber=AcomDC_0504_2016-->