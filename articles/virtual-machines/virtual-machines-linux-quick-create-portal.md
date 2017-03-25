---
title: "Azure 빠른 시작 - VM 만들기 포털 | Microsoft Docs"
description: "Azure 빠른 시작 - VM 만들기 포털"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 348407b57bbb3329d7d27a6f38623e052aecc58b
ms.lasthandoff: 03/14/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Azure Portal을 사용하여 Linux 가상 컴퓨터 만들기

Azure Portal을 통해 Azure Virtual Machines를 만들 수 있습니다. 이 메서드는 VM 및 관련된 모든 Azure 리소스를 만들고 구성하기 위한 브라우저 기반 사용자 인터페이스를 제공합니다.

시작하기 전에 개인 및 공용 SSH 키가 둘 다 필요합니다. Azure에 대한 SSH 키 만들기에 대한 자세한 내용은 [Azure용 SSH 키 만들기](./virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

## <a name="log-in-to-azure"></a>Azure에 로그인 

Azure Portal(http://portal.azure.com)에 로그인합니다.

## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기

2. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.

3. **새로 만들기** 블레이드에서 **Compute**를 선택하고 **Compute** 블레이드에서 **Ubuntu Server 16.04 LTS**를 선택한 후 **만들기** 단추를 클릭합니다.

4. 가상 컴퓨터 **기본 사항** 양식을 채웁니다. **인증 유형**의 경우 SSH가 권장됩니다. **SSH 공용 키**를 붙여 넣을 때 선행 또는 후행 공백을 모두 제거해야 합니다. **리소스 그룹**에 대해 새 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 만들어지고 전체적으로 관리되는 논리적 컨테이너입니다. 완료되면 **확인**을 클릭합니다.

    ![포털 블레이드에서 VM에 대한 기본 정보 입력](./media/virtual-machine-quick-start/create-vm-portal-basic-blade.png)  

5. VM의 크기를 선택하고 **선택**을 클릭합니다. 

    ![포털 블레이드에서 VM에 대한 크기 선택](./media/virtual-machine-quick-start/create-vm-portal-size-blade.png)

6. 설정 블레이드의 **Managed Disks 사용**에서 **예**를 선택하고 나머지 설정에 대해 기본값을 그대로 적용한 후 **확인**을 클릭합니다.

7. 요약 페이지에서 **확인**을 클릭하여 가상 컴퓨터 배포를 시작합니다.

## <a name="connect-to-virtual-machine"></a>가상 컴퓨터에 연결

배포가 완료된 후 가상 컴퓨터에 대한 SSH 연결을 만듭니다.

1. 가상 컴퓨터를 클릭합니다. Azure Portal의 홈 화면에서 또는 왼쪽 메뉴에 있는 **가상 컴퓨터**를 선택하여 VM을 찾을 수 있습니다.

2. **연결** 단추를 클릭합니다. 연결 단추를 클릭하면 가상 컴퓨터에 연결하는 데 사용할 수 있는 SSH 연결 문자열이 표시됩니다.

    ![포털 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

3. 다음 명령을 실행하여 SSH 세션을 만듭니다. 이 연결 문자열을 Azure Portal에서 복사한 것으로 바꿉니다.

```bash 
ssh <replace with IP address>
```
## <a name="delete-virtual-machine"></a>가상 컴퓨터 삭제

더 이상 필요하지 않을 때 리소스 그룹, 가상 컴퓨터 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 가상 컴퓨터 블레이드에서 해당 리소스 그룹을 선택하고 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

[고가용성 가상 컴퓨터 만들기 자습서](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[VM 배포 CLI 샘플 탐색](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

