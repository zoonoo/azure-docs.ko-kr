---
title: "SCP를 사용하여 Linux VM에서 파일 송수신 | Microsoft Docs"
description: "SCP 및 SSH 키 쌍을 사용하여 Linux VM에서 파일을 안전하게 송수신합니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 5ca865475b82b73e5c7e1ab21f098a87548605d9
ms.openlocfilehash: fe07ea13f202f19e399a8ede163fcba932490418


---

# <a name="moving-files-to-and-from-a-linux-vm-using-scp"></a>SCP를 사용하여 Linux VM에서 파일 송수신

이 문서에서는 보안 복사본(SCP)를 사용하여 워크스테이션에서 Azure Linux VM으로 또는 Azure Linux VM에서 워크스테이션으로 파일을 이동하는 방법을 보여 줍니다.  예를 들어 Linux VM까지 Azure 구성 파일을 이동하고 로그 파일 디렉터리를 끌어오는 경우 모두 SCP 및 SSH 키를 사용하여 수행합니다.   

이 문서에 필요한 요구 사항은 다음과 같습니다.

- [Azure 계정](https://azure.microsoft.com/pricing/free-trial/)

- [SSH 공용 및 개인 키 파일](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>빠른 명령

Linux VM으로 파일 복사

```bash
scp file user@host:directory/targetfile
```

Linux VM에서 파일 복사

```bash
scp user@host:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>자세한 연습

파일을 워크스테이션 및 Linux VM 간에 앞뒤로 신속하고 안전하게 이동하는 것은 Azure 인프라의 중요한 부분입니다.  이 문서에서는 SSH 위에 구축되었고 Linux, Mac 및 Windows의 기본 Bash 셸에 포함되어 있는 도구인 SCP 사용 과정을 연습해봅니다.

## <a name="ssh-key-pair-authentication"></a>SSH 키 쌍 인증

SCP는 전송 계층에 대해 SSH를 사용합니다.  전송에 대해 SSH를 사용할 경우 SSH는 기본적으로 SSH가 제공되는 암호화된 터널에서 파일을 이동하면서 대상 호스트의 인증을 처리합니다.  SSH 인증의 경우, 사용자 이름 및 암호를 사용할 수 있지만 보안 모범 사례로 SSH 공용 및 개인 키 인증이 강력히 권장됩니다. SSH에서 연결을 인증하면 SCP는 파일 복사 프로세스를 시작합니다.  적절히 구성된 `~/.ssh/config`, SSH 공용 및 개인 키를 사용하면 사용자 이름을 사용하지 않고, 서버 이름만 사용해서 SCP 연결을 설정할 수 있습니다.  SSH 키가 하나만 있는 경우 SCP는 `~/.ssh/` 디렉터리에서 이 키를 찾은 후 기본적으로 VM에 로그인하는 데 사용합니다.

`~/.ssh/config`, SSH 공용 및 개인 키 구성에 대한 자세한 내용은 [SSH 키 만들기](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 문서를 참조하세요.

## <a name="scp-a-file-to-a-linux-vm"></a>파일을 Linux VM으로 SCP

첫 번째 예제에서는 Azure 자격 증명 파일을 Automation 배포에 사용되는 Linux VM까지 복사합니다.  이 파일에는 암호를 포함하는 Azure API 자격 증명이 포함되므로 보안이 매우 중요하고 암호화된 터널 SSH는 파일 내용을 제공하고 보호합니다.

```bash
scp ~/.azure/credentials myserver:/home/ahmet/.azure/credentials
```

## <a name="scp-a-directory-from-a-linux-vm"></a>디렉터리를 Linux VM에서 SCP

이 예제에서는 Linux VM에서 워크스테이션으로 로그 파일로 꽉 찬 디렉터리를 복사합니다.  로그 파일에는 중요한 비밀 데이터가 포함되어 있을 수도 있으며 SCP를 사용하면 로그 파일 내용이 암호화됩니다.  SCP를 사용하여 파일을 안전하게 전송하는 것이 로그 디렉터리 및 파일을 안전하게 워크스테이션으로 가져오는 가장 쉬운 방법입니다.

```bash
scp -r myserver:/home/ahmet/logs/ /tmp/.
```

`-r` cli 플래그는 명령에 나열된 디렉터리의 시점부터 파일 및 디렉터리를 재귀적으로 복사하도록 SCP에 지시합니다.  또한 명령줄 구문은 `cp` 복사 명령과 비슷합니다.

## <a name="next-steps"></a>다음 단계

* [VMAccess 확장을 사용하여 사용자, SSH 관리 및 Azure Linux VM의 디스크 검사 또는 복구](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SSHD를 구성하여 Linux VM에 SSH 암호 사용 안 함](virtual-machines-linux-mac-disable-ssh-password-usage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Dec16_HO3-->


