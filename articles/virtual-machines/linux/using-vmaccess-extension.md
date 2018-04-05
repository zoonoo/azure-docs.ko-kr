---
title: Azure Linux VM에 대한 액세스 다시 설정 | Microsoft Docs
description: VMAccess 확장 및 Azure CLI 2.0을 사용하여 관리 사용자를 관리하고 Linux VM에 대한 액세스를 다시 설정하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 08/04/2017
ms.author: danlep
ms.openlocfilehash: a5467722b347e68693b335da6b3ac3c5d1a3a441
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20"></a>Azure CLI 2.0에서 VMAccess 확장을 사용하여 관리 사용자, SSH를 관리하고 Linux VM의 디스크를 검사 또는 복구
Linux VM의 디스크에 오류가 표시되어 있습니다. 사용자가 Linux VM의 루트 암호를 재설정했거나 SSH 개인 키를 실수로 삭제했습니다. 데이터 센터를 사용할 때는 이러한 경우 데이터 센터로 직접 가서 KVM을 열어 서버 콘솔에 액세스해야 했습니다. Azure VMAccess 확장을 콘솔에 액세스하여 Linux에 대한 액세스 권한을 재설정하거나 디스크 수준 유지 관리를 수행할 수 있는 이 KVM 스위치로 생각하세요.

이 문서는 Azure VMAccess 확장을 사용하여 디스크를 검사 또는 복구하거나, 사용자 액세스를 다시 설정하거나, 관리 사용자 계정을 관리하거나, Linux의 SSH 구성을 업데이트하는 방법을 설명합니다. [Azure CLI 1.0](using-vmaccess-extension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 이러한 단계를 수행할 수도 있습니다.


## <a name="ways-to-use-the-vmaccess-extension"></a>VMAccess 확장을 사용하는 방법
Linux VM에서 두 가지 방법으로 VMAccess 확장을 사용할 수 있습니다.

* Azure CLI 2.0 및 필수 매개 변수를 사용합니다.
* [VMAccess 확장을 처리하고 관련 작업을 수행하는 원시 JSON 파일을 사용](#use-json-files-and-the-vmaccess-extension)합니다.

다음 예에서는 [az vm user](/cli/azure/vm/user) 명령을 사용합니다. 이러한 단계를 수행하려면 최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/reference-index#az_login)을 사용하여 Azure 계정에 로그인해야 합니다.

## <a name="update-ssh-key"></a>SSH 키 업데이트
다음 예제에서는 VM `myVM`에서 사용자 `azureuser`에 대한 SSH 키를 업데이트합니다.

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **참고:** `az vm user update` 명령은 VM의 관리 사용자에 대한 `~/.ssh/authorized_keys` 파일에 새 공개 키 텍스트를 추가합니다. 기존 SSH 키를 대체하거나 제거하지 않습니다. 배포 시 설정된 이전 키 또는 VMAccess 확장을 통한 후속 업데이트를 제거하지 않습니다.

## <a name="reset-password"></a>암호 재설정
다음 예제에서는 VM `myVM`에서 사용자 `azureuser`에 대한 암호를 다시 설정합니다.

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>SSH 다시 시작
다음 예제에서는 SSH 디먼을 다시 시작하고 SSH 구성을 `myVM`이라는 VM에서 기본값으로 다시 설정합니다.

```azurecli
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>관리/sudo 사용자 만들기
다음 예제에서는 **sudo** 권한을 가진 `myNewUser`라는 사용자를 만듭니다. 계정은 `myVM`이라는 VM에서 인증을 위해 SSH 키를 사용합니다. 이 메서드는 현재 자격 증명을 분실하거나 잊어버린 상황에서 VM에 대한 액세스 권한을 다시 얻을 수 있도록 설계되었습니다. 모범 사례로, **sudo** 권한이 있는 계정은 제한되어야 합니다.

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```



## <a name="delete-a-user"></a>사용자 삭제
다음 예제에서는 VM `myVM`에서 사용자 `myNewUser`을 삭제합니다.

```azurecli
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```


## <a name="use-json-files-and-the-vmaccess-extension"></a>JSON 파일 및 VMAccess 확장 사용
다음 예제에서는 원시 JSON 파일을 사용합니다. [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set)을 사용하여 JSON 파일을 호출합니다. 이러한 JSON 파일은 Azure 템플릿에서도 호출할 수 있습니다. 

### <a name="reset-user-access"></a>사용자 액세스 다시 설정
Linux VM의 루트에 액세스할 수 없게 된 경우 VMAccess 스크립트를 시작하여 사용자의 SSH 키 또는 암호를 업데이트할 수 있습니다.

사용자의 SSH 공개 키를 업데이트하려면 파일 `update_ssh_key.json`을 만들고 다음 형식으로 설정을 추가합니다. `username` 및 `ssh_key` 매개 변수에 대해 고유한 값으로 대체합니다.

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

사용자 암호를 다시 설정하려면 파일 `reset_user_password.json`을 만들고 다음 형식으로 설정을 추가합니다. `username` 및 `password` 매개 변수에 대해 고유한 값으로 대체합니다.

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>SSH 다시 시작
SSH 디먼을 다시 시작하고 SSH 구성을 기본값으로 다시 설정하려면 파일 `reset_sshd.json`을 만듭니다. 다음 내용을 추가합니다.

```json
{
  "reset_ssh": true
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>관리 사용자 관리

인증을 위해 SSH 키를 사용하고 **sudo** 권한이 있는 사용자를 만들려면 `create_new_user.json`이라는 파일을 만들고 다음 형식으로 설정을 추가합니다. `username` 및 `ssh_key` 매개 변수에 대해 고유한 값으로 대체합니다. 이 메서드는 현재 자격 증명을 분실하거나 잊어버린 상황에서 VM에 대한 액세스 권한을 다시 얻을 수 있도록 설계되었습니다. 모범 사례로, **sudo** 권한이 있는 계정은 제한되어야 합니다.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

사용자를 삭제하려면 파일 `delete_user.json`을 만들고 다음 콘텐츠를 추가합니다. `remove_user` 매개 변수에 대해 고유한 값으로 대체합니다.

```json
{
  "remove_user":"myNewUser"
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>디스크 확인 또는 복구
VMAccess를 사용하여 Linux VM에 추가한 디스크를 확인하고 복구할 수도 있습니다.

디스크를 확인한 다음 복구하려면 파일 `disk_check_repair.json`을 만들고 다음 형식으로 설정을 추가합니다. `repair_disk`의 이름에 대해 고유한 값으로 대체합니다.

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```

## <a name="next-steps"></a>다음 단계
실행 중인 Linux VM에서 변경을 수행하는 한 가지 방법은 Azure VMAccess 확장을 사용하여 Linux를 업데이트하는 것입니다. cloud-init 및 Azure Resource Manager 템플릿 등의 도구를 사용하여 부팅 시 Linux VM을 수정할 수도 있습니다.

[Linux용 가상 머신 확장 및 기능](extensions-features.md)

[Linux VM 확장을 사용하여 Azure Resource Manager 템플릿 작성](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[cloud-init를 사용하여 생성 중인 Linux VM 사용자 지정](using-cloud-init.md)

