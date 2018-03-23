---
title: Azure에서 Linux VM용 SSH 키 쌍 만들기 및 사용 | Microsoft Docs
description: Azure에서 인증 프로세스의 보안을 개선하기 위해 Linux VM용 SSH 공용 및 개인 키를 만들고 사용하는 방법입니다.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: ecd3a01ee5591cb09140edb1b1290ff2d4510200
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="how-to-create-and-use-an-ssh-public-and-private-key-pair-for-linux-vms-in-azure"></a>Azure에서 Linux VM용 SSH 공개 및 개인 키 쌍을 만들고 사용하는 방법
SSH(보안 셸) 키 쌍을 사용하면 인증을 위해 SSH 키를 사용하는 VM(가상 머신)을 Azure에서 만들 수 있으며 로그인하기 위해 암호가 필요하지 않게 됩니다. 이 문서에서는 Linux VM용 SSH 프로토콜 버전 2 RSA 공개 및 개인 키 파일 쌍을 신속하게 생성하고 사용하는 방법을 보여줍니다. Linux에서 Azure Cloud Shell, macOS 또는 Linux 호스트 또는 Windows 하위 시스템을 사용하여 이러한 단계를 완료할 수 있습니다. 자세한 단계 및 추가 예제는 [SSH 키 쌍 및 인증서를 만드는 자세한 단계](create-ssh-keys-detailed.md)를 참조하세요.

## <a name="create-an-ssh-key-pair"></a>SSH 키 쌍 만들기
`ssh-keygen` 명령을 사용하여 `~/.ssh` 디렉터리에 기본적으로 생성되는 SSH 공용 및 개인 키 파일을 만듭니다. 요청될 경우 다른 위치 및 추가 암호(개인 키 파일에 액세스하기 위한 암호)를 지정할 수 있습니다. SSH 키 쌍이 현재 위치에 있으면 덮어쓰여집니다.

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="use-the-ssh-key-pair"></a>SSH 키 쌍 사용
Azure에서 Linux VM에 배치하는 공개 키는 만들 때 위치를 변경하지 않는다면, 기본적으로 `~/.ssh/id_rsa.pub`에 저장됩니다. [Azure CLI 2.0](/cli/azure)을 사용하여 사용자의 VM을 만드는 경우 [az vm create](/cli/azure/vm#az_vm_create)와 `--ssh-key-path` 옵션을 사용할 때 이 공개 키의 위치를 지정합니다. Azure Portal 또는 Resource Manager 템플릿에서 사용할 공개 키 파일의 내용을 복사하여 붙여넣으려는 경우 추가 공백은 복사하지 않아야 합니다. 예를 들어, OS X을 사용하는 경우 공개 키 파일(기본적으로 **~/.ssh/id_rsa.pub**)을 **pbcopy**로 파이프하여 내용을 복사할 수 있습니다(`xclip`처럼 동일한 작업을 수행하는 다른 Linux 프로그램이 있음).

SSH 공개 키를 잘 모르는 경우 다음과 같이 `cat`을 실행하여 공개 키를 확인할 수 있습니다. 이때 `~/.ssh/id_rsa.pub`를 사용자 고유의 공개 키 파일 위치로 대체합니다.

```bash
cat ~/.ssh/id_rsa.pub
```

Azure VM에서 공개 키를 통해 VM의 IP 주소 및 DNS 이름을 사용하여 VM에 SSH를 사용합니다(아래 `azureuser` 및 `myvm.westus.cloudapp.azure.com`을 관리자 사용자 이름 및 정규화된 도메인 이름 또는 IP 주소로 대체해야 함).

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

키 쌍을 만들 때 암호를 제공한 경우 로그인 프로세스 중에 메시지가 표시되면 암호를 입력합니다. (서버가 `~/.ssh/known_hosts` 폴더에 추가되고 Azure VM에서 공개 키가 변경되거나 서버 이름이 `~/.ssh/known_hosts`에서 제거될 까지 다시 연결할지 묻는 메시지가 표시되지 않습니다.)

## <a name="next-steps"></a>다음 단계

SSH 키를 사용하여 만든 VM은 기본적으로 비활성화된 암호를 사용하여 구성되고 강제 무차별 암호 추측 시도를 더욱 비용이 많이 들고 결과적으로 어렵게 합니다. 이 항목에서는 빠른 사용을 위해 간단한 SSH 키 쌍을 만드는 방법에 대해 설명합니다. SSH 키 쌍 만들기에 도움이 필요하거나 추가 인증서가 필요한 경우 [SSH 키 쌍 및 인증서를 만드는 자세한 단계](create-ssh-keys-detailed.md)를 참조하세요.

Azure Portal, CLI 및 템플릿을 사용하여 SSH 키 쌍을 사용하는 VM을 만들 수 있습니다.

* [Azure Portal을 사용하여 보안 Linux VM 만들기](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI 2.0을 사용하여 보안 Linux VM 만들기](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure 템플릿을 사용하여 보안 Linux VM 만들기](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
