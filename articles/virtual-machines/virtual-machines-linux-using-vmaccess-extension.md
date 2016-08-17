<properties
    pageTitle="VMAccess 확장을 사용하여 Azure Linux VM에 대한 액세스 권한 재설정 | Microsoft Azure"
    description="VMAccess 확장을 사용하여 Azure Linux VM에 대한 액세스 권한 재설정"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/29/2016"
    ms.author="v-livech"
/>

# VMAccess 확장을 사용하여 사용자, SSH 관리 및 Azure Linux VM의 디스크 검사 또는 복구

이 문서는 VMAccess VM 확장[(Github)](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)을 사용하여 디스크를 검사 또는 복구, 사용자 액세스 재설정, 사용자 계정 관리, Linux의 SSHD 구성 재설정을 사용하는 방법을 보여줍니다. 이 문서에서는 [Azure 계정](https://azure.microsoft.com/pricing/free-trial/), [SSH 키](virtual-machines-linux-mac-create-ssh-keys.md), Azure Linux 가상 컴퓨터가 필요하며 Azure CLI를 설치한 후 `azure config mode arm`을 사용하여 ARM 모드로 전환해야 합니다.

## 빠른 명령

Linux VM에서 VMAccess를 사용하는 방법에는 다음 두 가지가 있습니다.

- Azure CLI 및 필요한 매개 변수 사용
- VMAccess에서 처리한 후 관련 작업을 수행하는 원시 JSON 파일 사용

빠른 명령 섹션에서는 Azure CLI `azure vm reset-access` 메서드를 사용합니다. 다음 명령 예제에서 &lt;와 &gt; 사이의 값을 사용자 환경의 값으로 바꿉니다.

## 루트 암호 재설정

루트 암호 재설정 방법:

```bash
azure vm reset-access -g <resource group> -n <vm name> -u root -p <examplePassword>
```

## SSH 키 재설정

비-루트 사용자의 SSH 키 재설정 방법:

```bash
azure vm reset-access -g <resource group> -n <vm name> -u <exampleUser> -M <~/.ssh/azure_id_rsa.pub>
```

## 사용자 만들기

새 사용자를 만드는 방법:

```bash
azure vm reset-access -g <resource group> -n <vm name> -u <exampleNewUserName> -p <examplePassword>
```

## 사용자 제거

```bash
azure vm reset-access -g <resource group> -n <vm name> -R <exampleNewUserName>
```

## SSHD 재설정

SSHD 구성 재설정 방법:

```bash
azure vm reset-access -g <resource group> -n <vm name> -r
```


## 자세한 연습

### VMAccess 정의:

Linux VM의 디스크에 오류가 표시되어 있습니다. 사용자가 Linux VM의 루트 암호를 재설정했거나 SSH 개인 키를 실수로 삭제했습니다. 과거의 데이터 센터 시대에 이와 같은 상황이 발생했다면 데이터 센터로 운전해 이동한 다음 문을 잠금 해제하기 위해 지문을 제출하고 케이지로 들어가서 KVM을 크랙으로 열고 서버 콘솔로 들어갔을 것입니다. Azure VMAccess 확장을 콘솔에 액세스하여 Linux에 대한 액세스 권한을 재설정하거나 디스크 수준 유지 관리를 수행할 수 있는 이 KVM 스위치로 생각하세요.

자세한 연습에서는 원시 JSON 파일을 사용하는 긴 형식의 VMAccess를 사용합니다. 이러한 VMAccess JSON 파일은 Azure 템플릿에서도 호출할 수 있습니다.

### VMAccess를 사용하여 Linux VM의 디스크 검사 또는 복구

VMAccess를 사용하면 Linux VM에 있는 디스크에 fsck를 실행할 수 있습니다. 디스크 검사를 수행한 다음 오류가 발견될 경우 디스크 복구를 수행할 수 있습니다.

디스크를 검사한 다음 복구하려면 이 VMAccess 스크립트를 사용합니다.

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path disk_check_repair.json
```

### VMAccess를 사용하여 Linux에 대한 사용자 액세스 권한 재설정

Linux VM의 루트에 대한 액세스 권한을 잃어버린 경우 VMAccess 스크립트를 실행하여 루트 암호를 재설정할 수 있습니다.

루트 암호를 재설정하려면 이 VMAccess 스크립트를 사용합니다.

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"exampleNewPassword",   
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_root_password.json
```

비-루트 사용자의 SSH 키를 재설정하려면 이 VMAccess 스크립트를 사용합니다.

`reset_ssh_key.json`

```json
{
  "username":"exampleUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== exampleUser@exampleServer",   
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_ssh_key.json
```

### VMAccess를 사용하여 Linux에서 사용자 계정 관리

VMAccess는 로그인하고 sudo 또는 루트 계정을 사용하지 않고 Linux VM의 사용자를 관리하는 데 사용할 수 있는 Python 스크립트입니다.

새 사용자를 만들려면 이 VMAccess 스크립트를 사용합니다.

`create_new_user.json`

```json
{
"username":"exampleNewUserName",
"ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== exampleUser@exampleServer",
"password":"examplePassword",
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path create_new_user.json
```

새 사용자를 만들려면 이 VMAccess 스크립트를 사용합니다.

`remove_user.json`

```json
{
"remove_user":"exampleUser",
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path remove_user.json
```

### VMAccess를 사용하여 Linux에서 SSHD 구성 재설정

Linux VM SSHD 구성을 변경하고 변경 내용을 확인하기 전에 SSH 연결을 닫을 경우 SSH에 다시 로그인하지 못할 수 있습니다. VMAccess는 SSHD 구성을 문제 없는 것으로 알려진 구성으로 재설정하는 데 사용할 수 있습니다.

SSHD 구성을 재설정하려면 이 VMAccess 스크립트를 사용합니다.

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

다음을 사용하여 VMAccess 스크립트를 실행합니다.

```bash
azure vm extension set exampleResourceGroup exampleVM \
VMAccessForLinux Microsoft.OSTCExtensions * \
--private-config-path reset_sshd.json
```

<!---HONumber=AcomDC_0803_2016-->