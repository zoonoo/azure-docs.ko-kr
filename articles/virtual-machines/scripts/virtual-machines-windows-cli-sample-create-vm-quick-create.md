---
title: "Azure CLI 스크립트 샘플 - Windows Server 2016 VM 빠른 생성 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - Windows Server 2016 VM 빠른 생성"
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rickstercdn
translationtype: Human Translation
ms.sourcegitcommit: 675d4fa48c5119a9b15a478d2b8105f8517bcac7
ms.openlocfilehash: 4be594b87c0a94c33be7e417b2fb15bc94d649c6
ms.lasthandoff: 03/01/2017

---

# <a name="quick-create-a-virtual-machine-with-the-azure-cli"></a>Azure CLI를 사용하여 가상 컴퓨터 빠른 생성

여기에 제공된 샘플 스크립트로 Windows Server 2016 운영 체제에서 Azure Virtual Machine을 만듭니다. 스크립트가 성공적으로 실행되면 RDP를 통해 가상 컴퓨터에 액세스할 수 있습니다.

이 스크립트를 실행하기 전에 `az login` 명령을 사용하여 Azure와의 연결이 생성되었는지 확인합니다. 또한 스크립트의 시작 부분에 있는 $AdminPassword 변수를 고유하고 적절한 암호 복잡성 요구 사항에 맞게 변경해야 합니다.

이 샘플은 Bash 셸에서 작동합니다. Windows에서 Azure CLI 스크립트 실행과 관련된 옵션은 [Windows에서 Azure CLI 실행](../virtual-machines-windows-cli-options.md)을 참조하세요.

## <a name="create-vm-sample"></a>VM 샘플 만들기

[!code-azurecli[기본](../../../cli_scripts/virtual-machine/create-vm-quick/create-windows-vm-quick.sh "VM 빠른 생성")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 컴퓨터 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 가상 컴퓨터를 만들고 네트워크 카드, 가상 네트워크, 서브넷 및 네트워크 보안 그룹에 연결합니다. 또한 이 명령은 사용할 가상 컴퓨터 이미지와 관리 자격 증명을 지정합니다.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 가상 컴퓨터 CLI 스크립트 샘플은 [Azure Windows VM 설명서](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 확인할 수 있습니다.

