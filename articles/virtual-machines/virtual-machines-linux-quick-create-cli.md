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
   ms.date="08/18/2016"
   ms.author="v-livech"/>


# CLI를 사용하여 Azure에서 Linux VM 만들기

> [AZURE.NOTE] 잠시 시간을 내어 사용 환경에 대한 [간단한 설문](https://aka.ms/linuxdocsurvey)에 응답하여 Azure Linux VM 설명서를 개선하는 데 도움을 주세요. 모든 답변은 작업 수행에 도움이 될 것입니다.

이 문서에서는 Azure CLI의 `azure vm quick-create` 명령을 사용하여 Azure에서 Linux 가상 컴퓨터를 신속하게 배포하는 방법을 보여 줍니다. `quick-create` 명령은 개념을 신속하게 프로토타입하거나 테스트하는 데 사용할 수 있는 기본 인프라로 VM을 배포합니다. 이 문서에는 Azure 계정([무료 평가판 가져오기](https://azure.microsoft.com/pricing/free-trial/))과, Resource Manager 모드(`azure config mode arm`)의 [Azure CLI](../xplat-cli-install.md) 로그인(`azure login`)이 필요합니다. [Azure 포털](virtual-machines-linux-quick-create-portal.md)을 사용하여 Linux VM을 신속히 배포할 수도 있습니다.

## 빠른 명령 요약

CoreOS VM를 배포하고 SSH 키를 연결하는 하나의 명령:

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

## Linux VM 배포

이제 명령하는 과정을 안내하고 RedHat Enterprise Linux 7.2를 사용하는 각 단계를 설명하겠습니다.

## ImageURN 별칭 사용

Azure CLI `quick-create` 명령에는 가장 일반적인 운영 체제 배포에 매핑된 별칭이 있습니다. 다음 테이블에는 배포 별칭이 나열되어 있습니다(Azure CLI 버전 0.10 현재). `quick-create`을(를) 사용한 모든 배포는 SSD 저장소 지원 VM으로 기본 설정되며, 고성능 환경을 제공합니다.

| Alias | 게시자 | 제안 | SKU | 버전 |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | Centos | 7\.2 | 최신 |
| CoreOS | CoreOS | CoreOS | Stable | 최신 |
| Debian | credativ | Debian | 8 | 최신 |
| openSUSE | SUSE | openSUSE | 13\.2 | 최신 |
| RHEL | Redhat | RHEL | 7\.2 | 최신 |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | 최신 |



**ImageURN** 옵션(`-Q`)의 경우 `RHEL`을 사용하여 RedHat Enterprise Linux 7.2 VM을 배포합니다. 이러한 `quick-create` 별칭은 Azure에서 사용할 수 있는 OS의 작은 부분을 나타냅니다. [이미지를 검색](virtual-machines-linux-cli-ps-findimage.md)하여 마켓플레이스에서 이미지를 찾거나 [고유의 사용자 지정 이미지 업로드](virtual-machines-linux-create-upload-generic.md)를 사용할 수 있습니다.

다음 명령 연습에서 프롬프트를 고유한 환경의 값으로 바꿉니다.

프롬프트에 따라 고유한 이름을 입력합니다.

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q RHEL
```

출력에는 다음과 같은 출력 블록이 있습니다.

```bash
info:    Executing command vm quick-create
Resource group name: rhel-quick
Virtual machine name: rhel
Location name: westus
Operating system Type [Windows, Linux]: linux
User name: ops
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "rhel"
info:    Verifying the public key SSH file: /Users/ops/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli1630678171193501687
info:    Could not find the storage account "cli1630678171193501687", trying to create new one
+ Creating storage account "cli1630678171193501687" in "westus"
+ Looking up the storage account cli1630678171193501687
+ Looking up the NIC "rhel-westu-1630678171-nic"
info:    An nic with given name "rhel-westu-1630678171-nic" not found, creating a new one
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
info:    Preparing to create new virtual network and subnet
+ Creating a new virtual network "rhel-westu-1630678171-vnet" [address prefix: "10.0.0.0/16"] with subnet "rhel-westu-1630678171-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
+ Looking up the subnet "rhel-westu-1630678171-snet" under the virtual network "rhel-westu-1630678171-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "rhel-westu-1630678171-pip"
info:    PublicIP with given name "rhel-westu-1630678171-pip" not found, creating a new one
+ Creating public ip "rhel-westu-1630678171-pip"
+ Looking up the public ip "rhel-westu-1630678171-pip"
+ Creating NIC "rhel-westu-1630678171-nic"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the storage account clisto909893658rhel
+ Creating VM "rhel"
+ Looking up the VM "rhel"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the public ip "rhel-westu-1630678171-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/rhel-quick/providers/Microsoft.Compute/virtualMachines/rhel
data:    ProvisioningState               :Succeeded
data:    Name                            :rhel
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :RedHat
data:        Offer                       :RHEL
data:        Sku                         :7.2
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic5abbc145c0242c1-os-1462425492101
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1630678171193501687.blob.core.windows.net/vhds/clic5abbc145c0242c1-os-1462425492101.vhd
data:
data:    OS Profile:
data:      Computer Name                 :rhel
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-0F-DD
data:          Provisioning State        :Succeeded
data:          Name                      :rhel-westu-1630678171-nic
data:          Location                  :westus
data:            Public IP address       :104.42.236.196
data:            FQDN                    :rhel-westu-1630678171-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto909893658rhel.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

출력에 나열된 포트 22 및 공용 IP 주소에 있는 VM에 SSH합니다. (나열된 FQDN을 사용할 수도 있습니다.)

```bash
ssh ops@rhel-westu-1630678171-pip.westus.cloudapp.azure.com
```
로그인 프로세스는 다음과 유사하게 표시됩니다.

```bash
The authenticity of host 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com (104.42.236.196)' can't be established.
RSA key fingerprint is 0e:81:c4:36:2d:eb:3c:5a:dc:7e:65:8a:3f:3e:b0:cb.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com,104.42.236.196' (RSA) to the list of known hosts.
[ops@rhel ~]$ ls -a
.  ..  .bash_logout  .bash_profile  .bashrc  .cache  .config  .ssh
```

## 다음 단계

`azure vm quick-create`은(는) Bash 셸에 로그인하고 작업할 수 있도록 VM을 신속하게 배포하는 방법입니다. `vm quick-create` 사용 시 복잡한 환경에서 추가적인 혜택은 없습니다. 이러한 문서 중 하나를 수행하여 인프라에 대해 사용자 지정된 Linux VM을 배포합니다.

- [Azure Resource Manager 템플릿을 사용하여 특정 배포 만들기](virtual-machines-linux-cli-deploy-templates.md)
- [Azure CLI 명령을 직접 사용하여 Linux VM에 대한 고유한 사용자 지정 환경 만들기](virtual-machines-linux-create-cli-complete.md)
- [템플릿을 사용하여 Azure에서 SSH 보안 Linux VM 만들기](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

<!---HONumber=AcomDC_0824_2016-->