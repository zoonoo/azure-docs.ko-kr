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
   ms.date="04/08/2016"
   ms.author="v-livech"/>


# CLI를 사용하여 Azure에서 Linux VM 만들기

이 문서에서는 Azure CLI의 `azure vm quick-create` 명령을 사용하여 Azure에서 Linux 가상 컴퓨터를 빠르게 만드는 방법을 보여 줍니다. `quick-create` 명령은 개념을 신속하게 프로토타입하거나 테스트하는 데 사용할 수 있는 기본 인프라로 VM을 만듭니다. Linux bash 셸에 대한 가장 빠른 방법으로 생각합니다. 이 문서에는 리소스 관리자 모드(`azure config mode arm`)인 Azure 계정([무료 평가판 가져오기](https://azure.microsoft.com/pricing/free-trial/)] 및 [the Azure CLI](../xplat-cli-install.md)가 있어야 합니다.

## 빠른 명령 요약

```
# One command to quickly the VM that prompts for arguments
ahmet@fedora$ azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
```

## 자세한 연습

## Linux VM 만들기

다음 명령에서 원하는 이미지를 사용할 수 없지만 이 예제는 `canonical:ubuntuserver:14.04.2-LTS:latest`를 사용하여 VM을 신속하게 만듭니다. (마켓플레이스에서 이미지를 찾으려면 [이미지 검색](virtual-machines-linux-cli-ps-findimage.md) 또는 [고유의 사용자 지정 이미지 업로드](virtual-machines-linux-create-upload-generic.md)를 사용할 수 있습니다.) 다음과 같이 표시됩니다.

다음 명령 연습에서 프롬프트를 사용자 환경의 값으로 바꿉니다. 이 문서에 “예제" 값을 사용하고 있습니다.

```bash
# Create the Linux VM using prompts
ahmet@fedora$ azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
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

이제 위의 출력에 나열된 기본 SSH 포트 22 및 공용 IP 주소에서 VM에 SSH할 수 있습니다.

```
ahmet@fedora$ ssh -i ~/.ssh/azure_id_rsa ubuntu@13.88.22.244
```

`azure vm quick-create`은(는) bash 셸에 로그인하고 작업할 수 있도록 VM을 신속하게 만드는 방법입니다. `vm quick-create` 사용은 복잡한 환경의 추가 혜택을 주지 않으므로 환경을 사용자 지정하려는 경우 다음이 가능합니다. [Azure Resource Manager 템플릿을 사용하여 특정 배포를 신속하게 만들](virtual-machines-linux-cli-deploy-templates.md)거나 [Azure CLI 명령을 직접 사용하여 Linux VM에 대한 고유한 사용자 지정 환경을 만들](virtual-machines-linux-cli-deploy-templates.md) 수 있습니다.

위의 예제는 다음을 만듭니다.

- VM을 배포할 Azure 리소스 그룹
- VM 이미지인 .vhd 파일을 보유한 Azure 저장소 계정
- VM에 연결을 제공하는 Azure 가상 네트워크 및 서브넷
- 네트워크와 VM을 연결하는 가상 NIC(네트워크 인터페이스 카드)
- 외부 사용에 대한 인터넷 주소를 제공한 후 환경 내부에 Linux VM을 만드는 공용 IP 주소 및 하위 도메인 접두사

## 다음 단계

이제 Linux VM을 신속하게 만들었으므로 테스트 또는 데모를 위해 사용합니다. 아래 문서 중 하나를 수행하여 인프라에 대해 사용자 지정된 Linux VM을 만들 수 있습니다.

- [템플릿을 사용하여 Azure에서 Linux VM 만들기](virtual-machines-linux-cli-deploy-templates.md)
- [템플릿을 사용하여 Azure에서 SSH 보안 Linux VM 만들기](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Azure CLI를 사용하여 Linux VM 만들기](virtual-machines-linux-create-cli-complete.md)

이러한 문서로 Azure 인프라뿐만 아니라 독점 및 오픈 소스 인프라 배포, 구성 및 오케스트레이션 도구 작성을 시작합니다.

<!---HONumber=AcomDC_0420_2016-->