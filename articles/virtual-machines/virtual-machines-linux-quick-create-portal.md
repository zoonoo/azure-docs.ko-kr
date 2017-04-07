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
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/21/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: bcfd830a5e2f39f36460990cae7e84b04d9a5fbb
ms.lasthandoff: 03/22/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Azure Portal을 사용하여 Linux 가상 컴퓨터 만들기

Azure Portal을 통해 Azure Virtual Machines를 만들 수 있습니다. 이 메서드는 가상 컴퓨터 및 관련된 모든 리소스를 만들고 구성하기 위한 브라우저 기반 사용자 인터페이스를 제공합니다. 이 빠른 시작은 Azure Portal을 사용하여 가상 컴퓨터를 만드는 과정을 설명합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/) 을 만듭니다.

## <a name="create-ssh-key-pair"></a>SSH 키 쌍 만들기

이 빠른 시작을 완료하려면 SSH 키 쌍이 필요합니다. 기존 SSH 키 쌍을 사용하는 경우 이 단계를 건너뛸 수 있습니다. Windows 컴퓨터를 사용하는 경우 [여기](./virtual-machines-linux-ssh-from-windows.md)에서 찾은 지침을 따릅니다. 

Bash 셸에서 이 명령을 실행하고 화면에 나타나는 지시를 따릅니다. 명령 출력은 공개 키 파일의 파일 이름을 포함합니다. 이 파일의 내용은 가상 컴퓨터를 만들 때 필요합니다.

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="log-in-to-azure"></a>Azure에 로그인 

Azure Portal( http://portal.azure.com )에 로그인합니다.

## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기

1. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.

2. **새로 만들기** 블레이드에서 **Compute**를 선택하고 **Compute** 블레이드에서 **Ubuntu Server 16.04 LTS**를 선택한 후 **만들기** 단추를 클릭합니다.

3. 가상 컴퓨터 **기본 사항** 양식을 채웁니다. **인증 유형**으로 **SSH**를 선택합니다. **SSH 공용 키**를 붙여 넣을 때 선행 또는 후행 공백을 모두 제거해야 합니다. **리소스 그룹**에 대해 새 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 만들어지고 전체적으로 관리되는 논리적 컨테이너입니다. 완료되면 **확인**을 클릭합니다.

    ![포털 블레이드에서 VM에 대한 기본 정보 입력](./media/virtual-machine-quick-start/create-vm-portal-basic-blade.png)  

4. VM의 크기를 선택하고 **선택**을 클릭합니다. 

    ![포털 블레이드에서 VM에 대한 크기 선택](./media/virtual-machine-quick-start/create-vm-portal-size-blade.png)

5. 설정 블레이드의 **Managed Disks 사용**에서 **예**를 선택하고 나머지 설정에 대해 기본값을 그대로 적용한 후 **확인**을 클릭합니다.

6. 요약 페이지에서 **확인**을 클릭하여 가상 컴퓨터 배포를 시작합니다.

7. 배포 상태를 모니터링하려면 가상 컴퓨터를 클릭합니다. Azure Portal 대시보드에서 또는 왼쪽 메뉴에 있는 **가상 컴퓨터**를 선택하여 VM을 찾을 수 있습니다. VM을 만들면 상태가 **배포 중**에서 **실행 중**으로 변경됩니다.

## <a name="connect-to-virtual-machine"></a>가상 컴퓨터에 연결

배포가 완료된 후 가상 컴퓨터에 대한 SSH 연결을 만듭니다.

1. 가상 컴퓨터 블레이드에서 **연결** 단추를 클릭합니다. 연결 단추를 클릭하면 가상 컴퓨터에 연결하는 데 사용할 수 있는 SSH 연결 문자열이 표시됩니다.

    ![포털 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

2. 다음 명령을 실행하여 SSH 세션을 만듭니다. 이 연결 문자열을 Azure Portal에서 복사한 것으로 바꿉니다.

```bash 
ssh <replace with IP address>
```
## <a name="delete-virtual-machine"></a>가상 컴퓨터 삭제

더 이상 필요하지 않을 때 리소스 그룹, 가상 컴퓨터 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 가상 컴퓨터 블레이드에서 해당 리소스 그룹을 선택하고 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

[고가용성 가상 컴퓨터 만들기 자습서](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[VM 배포 CLI 샘플 탐색](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

