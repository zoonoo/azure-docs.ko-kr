---
title: "Azure CLI를 사용하여 사용자 지정 VM 이미지 만들기 | Microsoft Docs"
description: "자습서 - Azure CLI를 사용하여 사용자 지정 VM 이미지 만들기"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/21/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: f7fd7d63e6bb1da7022cef782e3b84ea20a64c31
ms.lasthandoff: 04/26/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-the-cli"></a>CLI를 사용하여 Azure VM의 사용자 지정 이미지 만들기

이 자습서에서는 Azure Virtual Machine의 사용자 지정 이미지를 만드는 방법에 대해 알아봅니다. 사용자 지정 이미지는 Marketplace 이미지와 같지만 직접 만듭니다. 응용 프로그램 사전 로드, 응용 프로그램 구성 및 기타 OS 구성과 같은 부트스트랩 구성에 사용자 지정 이미지를 사용할 수 있습니다. 사용자 지정 이미지를 만들 때 VM 및 연결된 모든 디스크가 이미지에 포함됩니다. 

최신 [Azure CLI 2.0](/cli/azure/install-azure-cli)을 사용하여 이 자습서의 단계를 완료할 수 있습니다.

이 자습서의 예제를 완료하려면 기존 가상 컴퓨터가 있어야 합니다. 필요한 경우 이 [스크립트 샘플](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md)을 사용하여 가상 컴퓨터를 만들 수 있습니다. 이 자습서를 진행할 때 필요한 경우 리소스 그룹 및 VM 이름을 바꿉니다.

## <a name="create-an-image"></a>이미지 만들기

가상 컴퓨터의 이미지를 만들려면 프로비전을 해제하고 할당을 취소한 후 원본 VM을 일반화된 것으로 표시하여 VM을 준비해야 합니다.

### <a name="deprovision-the-vm"></a>VM 프로비전 해제 

프로비전을 해제하면 컴퓨터별 정보를 제거하여 VM을 일반화합니다. 이 일반화를 통해 단일 이미지에서 여러 VM을 배포할 수 있습니다. 프로비전을 해제하는 동안 호스트 이름이 `localhost.localdomain`으로 다시 설정됩니다. SSH 호스트 키, 이름 서버 구성, 루트 암호 및 캐시된 DHCP 임대도 삭제됩니다.

VM 프로비전을 해제하려면 Azure VM 에이전트(waagent)를 사용합니다. Azure VM 에이전트는 VM에 설치되고 Azure 패브릭 컨트롤러와의 상호 작용과 프로비전을 관리합니다. 자세한 내용은 [Azure Linux 에이전트 사용자 가이드](agent-user-guide.md)를 참조하세요.

SSH를 사용하여 VM에 연결하고 VM 프로비전 해제 명령을 실행합니다. `+user` 인수를 사용하면 마지막으로 프로비전된 사용자 계정 및 관련 데이터도 삭제됩니다. 예제 IP 주소를 VM의 공용 IP 주소로 바꿉니다.

```bash
ssh azureuser@52.174.34.95 sudo waagent -deprovision+user -force
```
SSH 세션을 닫습니다.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>할당을 취소하고 VM을 일반화된 것으로 표시

이미지를 만들려면 VM을 할당 취소해야 합니다. [az vm deallocate](/cli//azure/vm#deallocate)를 사용하여 VM의 할당을 취소합니다. 
   
```azurecli
az vm deallocate --resource-group myRGCaptureImage --name myVM
```

마지막으로 [az vm generalize](/cli//azure/vm#generalize)를 사용하여 VM 상태를 일반화된 것으로 설정합니다.
   
```azurecli
az vm generalize --resource-group myResourceGroupImages --name myVM
```

### <a name="capture-the-image"></a>이미지 캡처

이제 [az image create](/cli//azure/image#create)를 사용하여 VM의 이미지를 만들 수 있습니다. 다음 예제에서는 `myVM`라는 VM에서 `myImage`라는 이미지를 만듭니다.
   
```azurecli
az image create --resource-group myResourceGroupImages --name myImage --source myVM
```
 
## <a name="create-a-vm-from-an-image"></a>이미지에서 VM 만들기

[az vm create](/cli/azure/vm#create)를 사용하여 이미지에서 VM을 만듭니다. 다음 예제에서는 `myImage`라는 이미지에서 `myVMfromImage`라는 VM을 만듭니다.

```azurecli
az vm create --resource-group myResourceGroupImages --name myVMfromImage --image myImage --admin-username azureuser --generate-ssh-keys
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 사용자 지정 VM 이미지 만들기에 대해 알아보았습니다. 고가용성 Virtual Machines에 대해 자세히 알아보려면 다음 자습서로 이동합니다.

[고가용성 VM 만들기](tutorial-availability-sets.md)


