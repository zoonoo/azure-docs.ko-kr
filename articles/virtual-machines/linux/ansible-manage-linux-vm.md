---
title: Azure에서 Ansible을 사용하여 Linux 가상 머신 관리
description: Azure에서 Ansible을 사용하여 Linux 가상 머신을 관리하는 방법 알아보기
ms.service: ansible
keywords: Ansible, Azure, DevOps, Bash, cloudshell, 플레이북, Bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 09/27/2018
ms.openlocfilehash: ef19043465cb67401786cddec4972f67231995bb
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077813"
---
# <a name="use-ansible-to-manage-a-linux-virtual-machine-in-azure"></a>Azure에서 Ansible을 사용하여 Linux 가상 머신 관리
Ansible을 사용하면 사용자 환경에서 리소스의 배포 및 구성을 자동화할 수 있습니다. Ansible을 사용하여 다른 리소스와 동일한 방식으로 Azure 가상 머신을 관리할 수 있습니다. 이 문서에서는 Ansible 플레이북을 사용하여 Linux 가상 머신을 시작 및 중지하는 방법을 보여줍니다. 

## <a name="prerequisites"></a>필수 조건

- **Azure 구독** - Azure 구독이 아직 없는 경우 [무료 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

## <a name="use-ansible-to-deallocate-stop-an-azure-virtual-machine"></a>Ansible을 사용하여 Azure 가상 머신의 할당 취소(중지)
이 섹션에서는 Ansible을 사용하여 Azure 가상 머신의 할당을 취소(중지)하는 방법을 보여줍니다.

1.  [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1.  [Cloud Shell](/azure/cloud-shell/overview)을 엽니다.

1.  다음과 같이 플레이북을 포함할 `azure-vm-stop.yml`이라는 파일을 만들고 VI 편집기에서 엽니다.

    ```azurecli-interactive
    vi azure-vm-stop.yml
    ```

1.  **I** 키를 선택하여 삽입 모드를시작합니다.

1.  다음 샘플 코드를 편집기에 붙여넣습니다.

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
      - name: Deallocate the virtual machine
        azure_rm_virtualmachine:
          resource_group: myResourceGroup
          name: myVM
          allocated: no
    ```

1.  **Esc** 키를 선택하여 삽입 모드를 종료합니다.

1.  파일을 저장하고 다음 명령을 입력하여 vi 편집기를 종료합니다.

    ```bash
    :wq
    ```

1.  샘플 Ansible 플레이북을 실행합니다.

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1.  출력은 가상 머신이 성공적으로 할당 취소(중지)되었음을 보여주는 다음 예제와 유사합니다.

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="use-ansible-to-start-a-deallocated-stopped-azure-virtual-machine"></a>할당 취소된(중지된) Azure 가상 머신을 Ansible로 시작
이 섹션에서는 할당 취소된(중지된) Azure 가상 머신을 Ansible로 시작하는 방법을 보여줍니다.

1.  [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1.  [Cloud Shell](/azure/cloud-shell/overview)을 엽니다.

1.  다음과 같이 플레이북을 포함할 `azure-vm-start.yml`이라는 파일을 만들고 VI 편집기에서 엽니다.

    ```azurecli-interactive
    vi azure-vm-start.yml
    ```

1.  **I** 키를 선택하여 삽입 모드를시작합니다.

1.  다음 샘플 코드를 편집기에 붙여넣습니다.

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
      - name: Start the virtual machine
        azure_rm_virtualmachine:
          resource_group: myResourceGroup
          name: myVM
    ```

1.  **Esc** 키를 선택하여 삽입 모드를 종료합니다.

1.  파일을 저장하고 다음 명령을 입력하여 vi 편집기를 종료합니다.

    ```bash
    :wq
    ```

1.  샘플 Ansible 플레이북을 실행합니다.

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1.  출력은 가상 머신이 성공적으로 시작되었음을 보여주는 다음 예제와 유사합니다.

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [Ansible을 사용하여 Azure 동적 인벤토리 관리](~/articles/ansible/ansible-manage-azure-dynamic-inventories.md)