---
title: "Azure VM에 대한 SSH 연결 문제 해결 | Microsoft Docs"
description: "Linux를 실행하는 Azure VM에 대해 &quot;SSH 연결 실패&quot; 또는 &quot;SSH 연결 거부&quot;와 같은 문제를 해결하는 방법입니다."
keywords: "ssh 연결 거부, ssh 오류, azure ssh, SSH 연결 실패"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 5d99c53ab5a6c71446b971fb19c6ca4ac4164e2b
ms.lasthandoff: 03/27/2017


---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>실패하거나 오류가 발생하거나 거부되는 Azure Linux VM에 대한 SSH 연결 문제 해결
Linux VM(가상 컴퓨터)에 연결하려고 할 때 다양한 이유로 인해 SSH(Secure Shell) 오류, SSH 연결 실패 또는 SSH 연결 거부 문제가 발생할 수 있습니다. 이 문서는 문제를 찾아 해결하는 데 도움이 됩니다. Azure Portal, Azure CLI 또는 Linux용 VM 액세스 확장을 사용하여 연결 문제를 해결할 수 있습니다.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](http://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](http://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](http://azure.microsoft.com/support/faq/)를 참조하세요.

## <a name="quick-troubleshooting-steps"></a>빠른 문제 해결 단계
각 문제 해결 단계 후 VM에 다시 연결을 시도합니다.

1. SSH 구성을 재설정합니다.
2. 사용자의 자격 증명을 다시 설정합니다.
3. [네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md) 규칙이 SSH 트래픽을 허용하는지 확인합니다.
   * 네트워크 보안 그룹 규칙이 SSH 트래픽을 허용하기 위해 존재하는지 확인합니다(기본적으로 TCP 포트 22).
   * Azure Load Balancer를 사용하지 않고 포트 리디렉션/매핑을 사용할 수 없습니다.
4. [VM 리소스 상태](../resource-health/resource-health-overview.md)를 확인합니다. 
   * VM이 정상으로 보고하는지 확인합니다.
   * 부팅 진단을 사용하도록 설정하는 경우 VM이 로그에서 부팅 오류를 보고하지 않는지 확인합니다.
5. VM을 다시 시작합니다.
6. VM을 다시 배포합니다.

자세한 문제 해결 단계와 설명이 필요한 경우 계속 읽어보세요.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>SSH 연결 문제 해결에 사용할 수 있는 방법
다음 방법 중 하나를 사용하여 자격 증명 또는 SSH 구성을 다시 설정할 수 있습니다.

* [Azure Portal](#use-the-azure-portal) - SSH 구성 또는 SSH 키를 신속하게 다시 설정해야 하는데 Azure 도구가 설치되지 않은 경우에 매우 유용합니다.
* [Azure CLI 2.0](#use-the-azure-cli-20) - 명령줄에 이미 도달한 경우 SSH 구성 또는 자격 증명을 신속하게 다시 설정합니다. [Azure CLI 1.0](#use-the-azure-cli-10)을 사용할 수도 있습니다.
* [Azure VMAccessForLinux 확장](#use-the-vmaccess-extension) - json 정의 파일을 만들고 다시 사용하여 SSH 구성 또는 사용자 자격 증명을 다시 설정합니다.

각 문제 해결 단계 후 VM에 다시 연결을 시도합니다. 그래도 연결할 수 없으면 다음 단계를 시도합니다.

## <a name="use-the-azure-portal"></a>Azure 포털 사용
Azure Portal은 로컬 컴퓨터에 도구를 설치하지 않고 SSH 구성 또는 사용자 자격 증명을 다시 설정하는 빠른 방법을 제공합니다.

Azure Portal에서 VM을 선택합니다. **지원 + 문제 해결** 섹션까지 아래로 스크롤하고 다음 예제와 같이 **암호 재설정**을 선택합니다.

![Azure Portal에서 SSH 구성 또는 자격 증명 다시 설정](./media/virtual-machines-linux-troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>SSH 구성 다시 설정
첫 번째 단계로 이전 화면처럼 **모드** 드롭 다운 메뉴에서 `Reset SSH configuration only`을 선택하고 **다시 설정** 단추를 클릭합니다. 이 작업이 완료되면 VM에 다시 액세스하려고 합니다.

### <a name="reset-ssh-credentials-for-a-user"></a>사용자에 대한 SSH 자격 증명 다시 설정
기존 사용자의 자격 증명을 다시 설정하려면 이전 스크린샷과 같이 **모드** 드롭다운 메뉴에서 `Reset SSH public key` 또는 `Reset password`을 선택합니다. 사용자 이름 및 SSH 키 또는 새 암호를 지정하고 **다시 설정** 단추를 클릭합니다.

이 메뉴에서 VM에 대해 sudo 권한이 있는 사용자를 만들 수도 있습니다. 새 사용자 이름 및 연결된 암호 또는 SSH 키를 입력한 다음 **다시 설정** 단추를 클릭합니다.

## <a name="use-the-azure-cli-20"></a>Azure CLI 2.0 사용
아직 설치하지 않은 경우 최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/#login)을 사용하여 Azure 계정에 로그인합니다.

사용자 지정 Linux 디스크 이미지를 만들고 업로드한 경우 [Microsoft Azure Linux 에이전트](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 버전 2.0.5 이상을 설치해야 합니다. 갤러리 이미지를 사용하여 만든 VM의 경우 이 액세스 확장이 이미 설치되어 자동으로 구성됩니다.

### <a name="reset-ssh-credentials-for-a-user"></a>사용자에 대한 SSH 자격 증명 다시 설정
다음 예제에서는 [az vm access set-linux-user](/cli/azure/vm/access#set-linux-user)를 사용하여 `myResourceGroup`의 `myVM`이라는 VM에서 `myUsername`의 자격 증명을 `myPassword`에 지정된 값으로 다시 설정합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
az vm access set-linux-user --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

SSH 키 인증을 사용하는 경우 지정된 사용자의 SSH 키를 다시 설정할 수 있습니다. 다음 예제에서는 **az vm access set-linux-user**를 사용하여 `myResourceGroup`의 `myVM`이라는 VM에서 `~/.ssh/id_rsa.pub`이라는 사용자의 `myUsername`에 저장된 SSH 키를 업데이트합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
az vm access set-linux-user --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>VMAccess 확장 사용
Linux용 VM 액세스 확장은 수행하는 작업을 정의하는 json 파일에서 읽습니다. 이러한 작업에는 SSHD 다시 설정, SSH 키를 다시 설정 또는 사용자 추가가 포함됩니다. Azure CLI를 사용하여 VMAccess 확장을 호출할 수 있지만 원하는 경우 여러 VM에 걸쳐 json 파일을 재사용할 수 있습니다. 이 방법을 통해 주어진 시나리오에 대해 호출될 수 있는 json 파일의 레포지토리를 만들 수 있습니다.

### <a name="reset-sshd"></a>SSHD 재설정
다음과 같은 내용으로 `PrivateConf.json`라는 파일을 만듭니다.

```json
{  
    "reset_ssh":"True"
}
```

Azure CLI를 사용하여 json 파일을 지정하여 SSHD 연결을 다시 설정하도록 `VMAccessForLinux` 확장을 호출합니다. 다음 예제에서는 `myResourceGroup`의 VM `myVM`에서 SSHD를 다시 설정합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>사용자에 대한 SSH 자격 증명 다시 설정
SSHD가 정상적으로 작동하는 것으로 나타나면 지정된 사용자의 자격 증명을 다시 설정할 수 있습니다. 사용자에 대한 암호를 재설정하려면 `PrivateConf.json`라는 파일을 만듭니다. 다음 예제에서는 VM에서 `myUsername`의 자격 증명을 `myPassword`에 지정된 값으로 다시 설정합니다. 고유한 값을 사용하여 다음 줄을 `PrivateConf.json` 파일에 입력합니다.

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

또는 사용자에 대한 SSH 키를 다시 설정하려면 먼저 `PrivateConf.json` 파일을 만듭니다. 다음 예제에서는 `myResourceGroup`의 `myVM`이라는 VM에서 `myUsername`의 자격 증명을 `myPassword`에 지정된 값으로 다시 설정합니다. 고유한 값을 사용하여 다음 줄을 `PrivateConf.json` 파일에 입력합니다.

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

json 파일을 만든 후에 json 파일을 지정하여 SSH 사용자 자격 증명을 다시 설정하는 `VMAccessForLinux` 확장을 호출하도록 Azure CLI를 사용합니다. 다음 예제에서는 `myResourceGroup`의 VM `myVM`에서 자격 증명을 다시 설정합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```

## <a name="use-the-azure-cli-10"></a>Azure CLI 1.0 사용
아직 수행하지 않은 경우 [Azure CLI 1.0을 설치하고 Azure 구독에 연결](../cli-install-nodejs.md)합니다. 다음과 같이 Resource Manager 모드를 사용하는지 확인합니다.

```azurecli
azure config mode arm
```

사용자 지정 Linux 디스크 이미지를 만들고 업로드한 경우 [Microsoft Azure Linux 에이전트](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 버전 2.0.5 이상을 설치해야 합니다. 갤러리 이미지를 사용하여 만든 VM의 경우 이 액세스 확장이 이미 설치되어 자동으로 구성됩니다.

### <a name="reset-ssh-configuration"></a>SSH 구성 다시 설정
SSHD 구성 자체가 잘못 구성되었거나 서비스에서 오류가 발생했습니다. SSH 구성 자체가 올바르도록 SSHD를 다시 설정할 수 있습니다. SSHD 다시 설정은 수행한 첫 번째 문제 해결 단계여야 합니다.

다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`에서 SSHD를 다시 설정합니다. 다음과 같이 고유한 VM 및 리소스 그룹 이름을 사용합니다.

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>사용자에 대한 SSH 자격 증명 다시 설정
SSHD가 정상적으로 작동하는 것으로 나타나면 지정된 사용자의 암호를 다시 설정할 수 있습니다. 다음 예제에서는 `myResourceGroup`의 `myVM`이라는 VM에서 `myUsername`의 자격 증명을 `myPassword`에 지정된 값으로 다시 설정합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

SSH 키 인증을 사용하는 경우 지정된 사용자의 SSH 키를 다시 설정할 수 있습니다. 다음 예제에서는 `myResourceGroup`의 `myVM`이라는 VM에서 `myUsername` 사용자를 위해 `~/.ssh/id_rsa.pub`에 저장된 SSH 키를 업데이트합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```


## <a name="restart-a-vm"></a>VM 다시 시작
SSH 구성 및 사용자 자격 증명을 다시 설정했거나 그 과정에서 오류가 발생한 경우 VM을 다시 시작하여 기본 계산 문제를 해결할 수 있습니다.

### <a name="azure-portal"></a>Azure 포털
Azure Portal을 사용하여 VM을 다시 시작하려면 다음 예제와 같이 VM을 선택하고 **다시 시작** 단추를 클릭합니다.

![Azure Portal에서 VM 다시 시작](./media/virtual-machines-linux-troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli-10"></a>Azure CLI 1.0
다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`을 다시 시작합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
다음 예제에서는 [az vm restart](/cli/azure/vm#restart)를 사용하여 `myResourceGroup`이라는 리소스 그룹의 `myVM`이라는 VM을 다시 시작합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>VM 재배포
Azure 내의 다른 노드로 VM을 재배포하여 기본 네트워킹 문제를 해결할 수 있습니다. VM을 다시 배포하는 방법에 대한 자세한 내용은 [새 Azure 노드로 가상 컴퓨터 다시 배포](virtual-machines-windows-redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

> [!NOTE]
> 이 작업이 완료되면 임시 디스크 데이터가 손실되고 가상 컴퓨터와 연결된 동적 IP 주소가 업데이트됩니다.
> 
> 

### <a name="azure-portal"></a>Azure 포털
Azure Portal을 사용하여 VM을 다시 배포하려면 VM을 선택하고 **지원 + 문제 해결** 섹션까지 아래로 스크롤합니다. 다음 예제와 같이 **다시 배포** 단추를 클릭합니다.

![Azure Portal에서 VM 다시 배포](./media/virtual-machines-linux-troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli-10"></a>Azure CLI 1.0
다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`을 다시 배포합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
다음 예제에서는 [az vm redeploy](/cli/azure/vm#redeploy)를 사용하여 `myResourceGroup`이라는 리소스 그룹의 `myVM`이라는 VM을 다시 배포합니다. 다음과 같이 사용자 고유의 값을 사용합니다.

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>클래식 배포 모델을 사용하여 만든 VM
클래식 배포 모델을 사용하여 만든 VM의 보다 일반적인 SSH 연결 오류를 해결하려면 다음 단계를 시도합니다. 각 단계 후 VM에 다시 연결을 시도합니다.

* [Azure 포털](https://portal.azure.com)에서 원격 액세스를 다시 설정합니다. Azure Portal에서 VM을 선택하고 **원격 다시 설정...** 단추를 클릭합니다.
* VM을 다시 시작합니다. [Azure portal](https://portal.azure.com)에서 VM을 선택하고 **다시 시작** 단추를 클릭합니다.
  
    또는
  
    [Azure 클래식 포털](https://manage.windowsazure.com)에서 **가상 컴퓨터** > **인스턴스** > **다시 시작**을 선택합니다.
* 새 Azure 노드에 VM을 다시 배포합니다. VM을 다시 배포하는 방법에 대한 자세한 내용은 [새 Azure 노드로 가상 컴퓨터 다시 배포](virtual-machines-windows-redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
  
    이 작업이 완료되면 임시 디스크 데이터가 손실되고 가상 컴퓨터와 연결된 동적 IP 주소가 업데이트됩니다.
* [Linux 기반 가상 컴퓨터의 암호 또는 SSH를 다시 설정하는 방법](linux/classic/reset-access.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)의 지침을 따르세요.
  
  * 암호 또는 SSH 키를 재설정합니다.
  * 새 *sudo* 사용자 계정을 만듭니다.
  * SSH 구성을 재설정합니다.
* VM 리소스 상태에 플랫폼 문제가 있는지 확인합니다.<br>
     VM을 선택하고 **설정** > **상태 확인**까지 아래로 스크롤합니다.

## <a name="additional-resources"></a>추가 리소스
* 후속 단계를 수행한 후에도 VM에 대해 SSH를 사용할 수 없는 경우 [자세한 문제 해결 단계](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하여 단계를 검토하고 문제를 해결할 수 있습니다.
* 응용 프로그램 액세스 문제를 해결하는 방법에 대한 자세한 내용은 [Azure 가상 컴퓨터에서 실행 중인 응용 프로그램에 대한 액세스 문제 해결](virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.
* 클래식 배포 모델을 사용하여 만든 가상 컴퓨터의 문제 해결 방법에 대한 자세한 내용은 [Linux 기반 가상 컴퓨터의 암호 또는 SSH를 다시 설정하는 방법](linux/classic/reset-access.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)을 참조하세요.


