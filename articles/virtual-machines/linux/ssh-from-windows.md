---
title: SSH 키를 사용 하 여 Linux Vm에 연결
description: Windows 컴퓨터에서 SSH 키를 생성 하 고 사용 하 여 Azure에서 Linux 가상 머신에 연결 하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.date: 07/09/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: 7e99c9191e93562211f6294cf671f431a5db455d
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87825568"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Azure에서 Windows를 통해 SSH 키를 사용하는 방법

이 문서는 Azure에서 Linux Vm (가상 머신)에 [연결](#connect-to-your-vm) 하기 위해 SSH ( *보안 셸* ) 키를 [만들고](#create-an-ssh-key-pair) 사용 하려는 Windows 사용자를 위한 것입니다. 포털에서 Vm을 만들 때 사용할 [SSH 키를 생성 하 고 Azure Portal에 저장할](../ssh-keys-portal.md) 수도 있습니다.


Linux 또는 macOS 클라이언트에서 SSH 키를 사용 하려면 [빠른](mac-create-ssh-keys.md)를 참조 하세요. SSH에 대 한 자세한 개요는 [Azure에서 LINUX VM에 인증 하기 위한 ssh 키 만들기 및 관리](create-ssh-keys-detailed.md)를 참조 하세요.

## <a name="overview-of-ssh-and-keys"></a>SSH 및 키에 대한 개요

[SSH](https://www.ssh.com/ssh/) 는 보안 되지 않은 연결에 대 한 보안 로그인을 허용 하는 암호화 된 연결 프로토콜입니다. SSH는 Azure에서 호스팅되는 Linux VM에 대한 기본 연결 프로토콜입니다. SSH 자체는 암호화 된 연결을 제공 하지만 SSH를 사용 하 여 암호를 사용 하면 VM이 무차별 암호 대입 공격에 취약 한 상태로 남아 있습니다. *Ssh 키*라고도 하는 공개-개인 키 쌍을 사용 하 여 ssh를 통해 VM에 연결 하는 것이 좋습니다. 

공개-개인 키 쌍은 앞으로 도어를 잠그는 것과 같습니다. 잠금은 **공개**에 노출 되며, 올바른 키가 있는 사용자는 도어를 열 수 있습니다. 키는 **비공개**이며, 도어 잠금을 해제 하는 데 사용할 수 있기 때문에 신뢰할 수 있는 사용자 에게만 제공 됩니다. 

- VM을 만들 때 *공개 키* 가 Linux vm에 배치 됩니다. 

- *프라이빗 키*는 로컬 시스템에 남아 있습니다. 이 프라이빗 키는 보호해야 하는 한편, 공유하면 안됩니다.

Linux VM에 연결 하는 경우 VM은 SSH 클라이언트를 테스트 하 여 올바른 개인 키가 있는지 확인 합니다. 클라이언트에 프라이빗 키가 있으면 VM에 액세스할 수 있습니다. 

조직의 보안 정책에 따라 단일 키 쌍을 다시 사용 하 여 여러 Azure Vm 및 서비스에 액세스할 수 있습니다. 각 VM에 대해 별도의 키 쌍이 필요 하지 않습니다. 

공개 키는 누구와도 공유할 수 있지만 사용자 (또는 로컬 보안 인프라)만 개인 키에 액세스할 수 있어야 합니다.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>SSH 클라이언트

최신 버전의 Windows 10에는 SSH 키를 만들고 사용 하 고 PowerShell 또는 명령 프롬프트에서 SSH 연결을 설정 하는 [OpenSSH client 명령이](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) 포함 되어 있습니다. 이는 Windows 컴퓨터에서 Linux VM에 대 한 SSH 연결을 만드는 가장 쉬운 방법입니다. 

[Azure Cloud Shell](../../cloud-shell/overview.md) 에서 Bash를 사용 하 여 VM에 연결할 수도 있습니다. [Azure 계정 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)을 사용 하 여 [웹 브라우저](https://shell.azure.com/bash), [Azure Portal](https://portal.azure.com)또는 터미널 Visual Studio Code에서 Cloud Shell를 사용할 수 있습니다.

또한 [Linux 용 Windows 하위 시스템](/windows/wsl/about) 을 설치 하 여 SSH를 통해 VM에 연결 하 고 Bash 셸 내에서 다른 네이티브 linux 도구를 사용할 수 있습니다.

## <a name="create-an-ssh-key-pair"></a>SSH 키 쌍 만들기

명령을 사용 하 여 SSH 키 쌍을 만듭니다 `ssh-keygen` . 파일 이름을 입력 하거나 괄호 안에 표시 된 기본값을 사용 합니다 (예: `C:\Users\username/.ssh/id_rsa` ).  암호를 사용 하지 않으려면 파일의 암호를 입력 하거나 암호를 비워 둡니다. 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>키를 사용 하 여 VM 만들기

인증을 위해 SSH 키를 사용 하는 Linux VM을 만들려면 VM을 만들 때 SSH 공개 키를 제공 합니다.

Azure CLI를 사용 하 여 및 매개 변수를 사용 하 여 공개 키의 경로와 파일 이름을 지정 `az vm create` `--ssh-key-value` 합니다.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

PowerShell을 사용 하 `New-AzVM` 고를 사용 하 여 SSH 키를 VM 구성에 추가 합니다. 예제는 [빠른 시작: PowerShell을 사용 하 여 Azure에서 Linux 가상 머신 만들기](quick-create-powershell.md)를 참조 하세요.

포털을 사용 하 여 많은 배포를 수행 하는 경우 포털에서 VM을 만들 때 쉽게 선택할 수 있도록 공개 키를 Azure에 업로드 하는 것이 좋습니다. 자세한 내용은 [SSH 키 업로드](../ssh-keys-portal.md#upload-an-ssh-key)를 참조 하세요.


## <a name="connect-to-your-vm"></a>VM에 연결

Azure VM에서 배포된 공개 키 및 로컬 시스템에서 배포된 프라이빗 키를 통해 IP 주소 또는 VM의 DNS 이름을 사용하여 VM에 SSH를 사용합니다. 다음 명령에서 *azureuser* 및 *10.111.12.123* 을 관리자 사용자 이름, IP 주소 (또는 정규화 된 도메인 이름) 및 개인 키에 대 한 경로로 바꿉니다.

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

키 쌍을 만들 때 암호를 구성한 경우 메시지가 표시 되 면 암호를 입력 합니다.

VM이 Just-In-Time 액세스 정책을 사용하는 경우에는 액세스 권한을 요청해야 VM에 연결할 수 있습니다. Just-In-Time 정책과 관련된 자세한 내용은 [Just-In-Time 정책을 사용하여 가상 머신 액세스 관리](../../security-center/security-center-just-in-time.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

- Azure Portal의 SSH 키에 대 한 자세한 내용은 포털에서 Vm을 만들 때 사용할 [AZURE PORTAL ssh 키 생성 및 저장](../ssh-keys-portal.md) 을 참조 하세요.

- SSH 키를 사용 하는 자세한 단계, 옵션 및 고급 예제는 [ssh 키 쌍을 만드는 자세한 단계](create-ssh-keys-detailed.md)를 참조 하세요.

- Azure Cloud Shell에서 PowerShell을 사용하여 Linux VM에 SSH 연결을 만들고 SSH 키를 생성할 수 있습니다. [PowerShell 빠른 시작](../../cloud-shell/quickstart-powershell.md#ssh)을 참조합니다.

- SSH를 사용하여 Linux VM을 연결하는 데 문제가 있으면 [Azure Linux VM에 대한 SSH 연결 문제 해결](../troubleshooting/troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json)을 참조하세요.