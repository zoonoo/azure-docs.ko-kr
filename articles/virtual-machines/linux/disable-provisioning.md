---
title: 프로 비전 에이전트를 사용 하지 않도록 설정 하거나 제거 합니다.
description: Linux Vm 및 이미지에서 프로 비전 에이전트를 사용 하지 않도록 설정 하거나 제거 하는 방법에 대해 알아봅니다.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 133de199c240cbc4ea7246a29e65347d53c50545
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045759"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>Vm 및 이미지에서 Linux 에이전트를 사용 하지 않도록 설정 하거나 제거 합니다.

Linux 에이전트를 제거 하기 전에 Linux 에이전트가 제거 된 후 VM에서 수행할 수 없는 작업을 이해 해야 합니다.

Azure VM (가상 컴퓨터) [확장](https://docs.microsoft.com/azure/virtual-machines/extensions/overview) 은 azure vm에 대 한 배포 후 구성 및 자동화 작업을 제공 하는 작은 응용 프로그램으로, azure 제어 평면에서 확장을 설치 하 고 관리 합니다. [Azure Linux 에이전트가](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) 플랫폼 확장 명령을 처리 하 고 VM 내에서 확장의 올바른 상태를 확인 하는 작업입니다.

Azure 플랫폼은 VM 구성, 모니터링, 보안 및 유틸리티 애플리케이션에 이르는 다양한 확장을 호스팅합니다. 첫 번째 및 타사 확장을 선택할 수 있으며 확장을 사용 하는 주요 시나리오의 예는 다음과 같습니다.
* Azure Backup, 모니터링, 디스크 암호화, 보안, 사이트 복제 등 자사 Azure 서비스를 지원 합니다.
* S h a/암호 재설정
* VM 구성-Chef, 퍼핏 에이전트 등을 설치 하는 사용자 지정 스크립트를 실행 합니다.
* AV 제품, VM 취약성 도구, VM 및 앱 모니터링 도구와 같은 타사 제품
* 확장은 새 VM 배포와 함께 번들로 묶을 수 있습니다. 예를 들어 대규모 배포의 일부이거나, VM 프로비전에서 애플리케이션을 구성하거나, 배포 후 지원되는 모든 확장 운영 체제에서 실행할 수 있습니다.

## <a name="disabling-extension-processing"></a>확장 처리 사용 안 함

필요에 따라 확장 처리를 사용 하지 않도록 설정 하는 몇 가지 방법이 있습니다. 계속 하기 전에 VM에 배포 된 확장을 모두 제거 **해야 합니다** . 예를 들어 AZ CLI를 사용 하 여 [나열](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-list) 하 고 [삭제할](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-delete)수 있습니다.

```bash
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```
> [!Note]
> 
> 위의 작업을 수행 하지 않으면 플랫폼은 확장 구성 및 40min 후의 제한 시간을 보내려고 시도 합니다.

### <a name="disable-at-the-control-plane"></a>컨트롤 평면에서 사용 안 함
향후 확장이 필요한 지 확실 하지 않은 경우 VM에 Linux 에이전트를 설치 된 상태로 두고 플랫폼에서 확장 처리 기능을 사용 하지 않도록 설정할 수 있습니다. 이 옵션은 `Microsoft.Compute` api 버전 이상에서 사용할 수 `2018-06-01` 있으며 설치 된 Linux 에이전트 버전에 대 한 종속성이 없습니다.

```bash
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
위의 명령을 사용 하 여 플랫폼에서이 확장 처리를 쉽게 다시 사용 하도록 설정할 수 있지만이를 ' t r u e '로 설정 합니다.

## <a name="remove-the-linux-agent-from-a-running-vm"></a>실행 중인 VM에서 Linux 에이전트 제거

위의 VM에서 기존 확장을 모두 **제거** 했는지 확인 합니다.

### <a name="step-1-remove-the-azure-linux-agent"></a>1 단계: Azure Linux 에이전트 제거

연결 된 구성 아티팩트가 아닌 Linux 에이전트를 제거 하는 경우 나중에 다시 설치할 수 있습니다. 다음 중 하나를 루트로 실행 하 여 Azure Linux 에이전트를 제거 합니다.

#### <a name="for-ubuntu-1804"></a>Ubuntu >= 18.04
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>Redhat >= 7.7
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>SUSE의 경우
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-2-optional-remove-the-azure-linux-agent-artifacts"></a>2 단계: (선택 사항) Azure Linux 에이전트 아티팩트 제거
> [!IMPORTANT] 
>
> Linux 에이전트의 연결 된 모든 아티팩트를 제거할 수 있지만 나중에 다시 설치할 수는 없습니다. 따라서 Linux 에이전트를 먼저 사용 하지 않도록 설정 하 고 위의 방법 으로만 Linux 에이전트를 제거 하는 것이 좋습니다. 

Linux 에이전트를 다시 설치 하지 않을 것으로 확인 되 면 다음을 실행할 수 있습니다.

#### <a name="for-ubuntu-1804"></a>Ubuntu >= 18.04
```bash
apt -y purge walinuxagent
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>Redhat >= 7.7
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>SUSE의 경우
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>Linux 에이전트를 사용 하지 않고 이미지 준비
이미 클라우드 init를 포함 하 고 있는 이미지가 있고, Linux 에이전트를 제거 하 고, 클라우드 init를 사용 하 여 계속 프로 비전 하려는 경우, Azure Linux 에이전트를 제거 하기 위해 2 단계 (그리고 필요에 따라 3 단계)의 단계를 실행 하 고, 다음은 클라우드 초기화 구성 및 캐시 된 데이터를 제거 하 고 VM을 준비 하 여 사용자 지정 이미지를 만듭니다.

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>이미지 프로 비전 해제 및 만들기
Linux 에이전트는 "waagent-프로 비전 + 사용자" 단계를 사용 하 여 기존 이미지 메타 데이터의 일부를 정리할 수 있지만, 제거 된 후에는 아래와 같은 작업을 수행 하 고 그 밖의 중요 한 데이터를 제거 해야 합니다.

- 기존 ssh 호스트 키를 모두 제거 합니다.

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- 관리자 계정 삭제

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- 루트 암호를 삭제 합니다.

   ```bash
   passwd -d root
   ```

위의 단계를 완료 하면 Azure CLI를 사용 하 여 사용자 지정 이미지를 만들 수 있습니다.


**일반 관리 이미지 만들기**
```bash
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**공유 이미지 갤러리에서 이미지 버전 만들기**

```bash
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>Linux 에이전트가 포함 되지 않은 이미지에서 VM 만들기
Linux 에이전트가 없는 이미지에서 VM을 만드는 경우 VM 배포 구성에서 확장이이 VM에서 지원 되지 않음을 확인 해야 합니다.

> [!NOTE] 
> 
> 위의 작업을 수행 하지 않으면 플랫폼은 확장 구성 및 40min 후의 제한 시간을 보내려고 시도 합니다.

확장이 사용 하지 않도록 설정 된 VM을 배포 하려면 [--enable-agent](https://docs.microsoft.com/cli/azure/vm#az-vm-create)와 함께 Azure CLI를 사용할 수 있습니다.

```bash
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

또는를 설정 하 여 ARM (Azure Resource Manager) 템플릿을 사용 하 여이 작업을 수행할 수 있습니다 `"provisionVMAgent": false,` .

```json
"osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "provisionVMAgent": false,
        "ssh": {
            "publicKeys": [
                {
                    "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
                    "keyData": "[parameters('adminPublicKey')]"
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Linux 프로 비전](provisioning.md)을 참조 하세요.
