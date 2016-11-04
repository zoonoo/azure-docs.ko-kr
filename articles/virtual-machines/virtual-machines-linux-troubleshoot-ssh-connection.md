---
title: Linux VM에 대한 SSH 연결이 거부되거나 실패하거나 오류가 발생함 | Microsoft Docs
description: Linux를 실행하는 Azure 가상 컴퓨터에 대해 SSH 연결 실패 또는 SSH 연결 거부와 같은 SSH 오류 문제를 해결합니다.
keywords: ssh 연결 거부, ssh 오류, azure ssh, SSH 연결 실패
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: iainfou

---
# 실패하거나 오류가 발생하거나 거부되는 Azure Linux VM에 대한 SSH 연결 문제 해결
Linux 기반 Azure VM(가상 컴퓨터)에 연결하려고 할 때 다양한 이유로 인해 SSH(Secure Shell) 오류, SSH 연결 실패 또는 연결 거부 문제가 발생할 수 있습니다. 이 문서는 문제를 찾아 해결하는 데 도움이 됩니다.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](http://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](http://azure.microsoft.com/support/options/)로 가서 **지원 받기**를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](http://azure.microsoft.com/support/faq/)를 참조하세요.

## Resource Manager 배포 모델을 사용하여 만든 VM
Azure CLI 명령을 직접 사용하거나 [Azure VMAccessForLinux 확장](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)을 사용하여 자격 증명 또는 SSHD를 다시 설정할 수 있습니다. 각 문제 해결 단계 후 VM에 다시 연결을 시도합니다.

### Azure CLI 사전 요구 사항
아직 수행하지 않은 경우 [Azure CLI를 설치하고 Azure 구독에 연결](../xplat-cli-install.md)합니다. `azure login` 명령을 사용하여 로그인하고 Resource Manager 모드(`azure config mode arm`)인지 확인합니다.

[Microsoft Azure Linux Agent](virtual-machines-linux-agent-user-guide.md) 버전 2.0.5 이상이 설치되어 있는지 확인합니다.

### SSHD 재설정
SSHD 구성 자체가 잘못 구성되었거나 서비스에서 오류가 발생했습니다. SSH 구성 자체가 올바르도록 SSHD를 다시 설정할 수 있습니다.

#### Azure CLI
```bash
azure vm reset-access -g <resource group> -n <vm name> -r
```

#### VM 액세스 확장
액세스 확장은 SSH 다시 설정, SSH 키 다시 설정, 새 사용자 추가 등, 수행할 작업을 정의하는 json 파일에서 읽어옵니다. 먼저 다음과 같은 내용으로 PrivateConf.json이라는 파일을 만듭니다.

```bash
{  
    "reset_ssh":"True"
}
```

그런 다음 `VMAccessForLinux` 확장을 수동으로 실행하여 SSHD 연결을 다시 설정합니다.

```bash
azure vm extension set <resource group> <vm name> VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
```

### 사용자에 대한 SSH 자격 증명 다시 설정
SSHD가 정상적으로 작동하는 것으로 나타나면 지정된 사용자의 암호를 다시 설정할 수 있습니다.

#### Azure CLI
```bash
azure vm reset-access -g <resource group> <vm name> -u <username> -p <new password>
```

SSH 키 인증을 사용하는 경우 지정된 사용자의 SSH 키를 다시 설정할 수 있습니다.

```bash
azure vm reset-access -g <resource group> -n <vm name> -u <username> -M <~/.ssh/azure_id_rsa.pub>
```

#### VM 액세스 확장
다음과 같은 내용으로 PrivateConf.json이라는 파일을 만듭니다.

```bash
{
    "username":"Username", "password":"NewPassword"
}
```

또는 지정된 사용자의 SSH 키를 다시 설정하려면 다음과 같은 내용으로 PrivateConf.json이라는 파일을 만듭니다.

```bash
{
    "username":"Username", "ssh_key":"ContentsOfNewSSHKey"
}
```

위 단계를 수행한 후에 `VMAccessForLinux` 확장을 수동으로 실행하여 SSH 사용자 자격 증명을 다시 설정합니다.

```
azure vm extension set <resource group> <vmname> VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
```

### VM 재배포
Azure 내의 다른 노드로 VM을 재배포하여 기본 네트워킹 문제를 해결할 수 있습니다. Azure 포털을 사용하여 VM을 다시 배포하려면 **찾아보기** > **가상 컴퓨터** > *Linux 가상 컴퓨터* > **재배포**를 선택합니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [새 Azure 노드로 가상 컴퓨터 다시 배포](virtual-machines-windows-redeploy-to-new-node.md)를 참조하세요. 현재 Azure CLI를 사용하여 VM을 다시 배포할 수 없습니다.

> [!NOTE]
> 이 작업이 완료되면 임시 디스크 데이터가 손실되고 가상 컴퓨터와 연결된 동적 IP 주소가 업데이트됩니다.
> 
> 

## 클래식 배포 모델을 사용하여 만든 VM
클래식 배포 모델을 사용하여 만든 VM의 보다 일반적인 SSH 연결 오류를 해결하려면 다음 단계를 시도합니다. 각 단계 후 VM에 다시 연결을 시도합니다.

* [Azure 포털](https://portal.azure.com)에서 원격 액세스를 다시 설정합니다. Azure 포털에서 **찾아보기** > **가상 컴퓨터(클래식)** > *Linux 가상 컴퓨터* > **원격 액세스 다시 설정**을 클릭합니다.
* VM을 다시 시작합니다. [Azure 포털](https://portal.azure.com)에서 **찾아보기** > **가상 컴퓨터(클래식)** > *Linux 가상 컴퓨터* > **다시 시작**을 클릭합니다.
  
    또는
  
    [Azure 클래식 포털](https://manage.windowsazure.com)에서 **가상 컴퓨터** > **인스턴스** > **다시 시작**을 선택합니다.
* 새 Azure 노드에 VM을 다시 배포합니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [새 Azure 노드로 가상 컴퓨터 다시 배포](virtual-machines-windows-redeploy-to-new-node.md)를 참조하세요.
  
    이 작업이 완료되면 임시 디스크 데이터가 손실되고 가상 컴퓨터와 연결된 동적 IP 주소가 업데이트됩니다.
* [Linux 기반 가상 컴퓨터의 암호 또는 SSH를 다시 설정하는 방법](virtual-machines-linux-classic-reset-access.md)의 지침을 따르세요.
  
  * 암호 또는 SSH 키를 재설정합니다.
  * 새 *sudo* 사용자 계정을 만듭니다.
  * SSH 구성을 재설정합니다.
* VM 리소스 상태에 플랫폼 문제가 있는지 확인합니다.<br> **찾아보기** > **가상 컴퓨터(클래식)** > *Linux 가상 컴퓨터* > **설정** > **상태 검사**를 선택합니다.

## 추가 리소스
* 후속 단계를 수행한 후에도 VM에 대해 SSH를 수행할 수 없는 경우 [자세한 문제 해결 단계](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md)를 수행하여 추가 네트워킹 구성 및 단계를 검토한 후 문제를 해결할 수 있습니다.
* 응용 프로그램 액세스 문제를 해결하는 방법에 대한 자세한 내용은 [Azure 가상 컴퓨터에서 실행 중인 응용 프로그램에 대한 액세스 문제 해결](virtual-machines-linux-troubleshoot-app-connection.md)을 참조하세요.
* 클래식 배포 모델을 사용하여 만든 가상 컴퓨터의 문제 해결 방법에 대한 자세한 내용은 [Linux 기반 가상 컴퓨터의 암호 또는 SSH를 다시 설정하는 방법](virtual-machines-linux-classic-reset-access.md)을 참조하세요.

<!---HONumber=AcomDC_0803_2016-->