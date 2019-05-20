---
title: 빠른 시작 - Azure의 Linux 가상 머신에 Ansible 설치 | Microsoft Docs
description: 이 빠른 시작에서는 Ubuntu, CentOS 및 SLES에서 Azure 리소스를 관리하기 위해 Ansible을 설치 및 구성하는 방법을 알아봅니다.
keywords: Ansible, Azure, DevOps, Bash, cloudshell, 플레이북, Bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 60cefe24feab9de4262e81eb1bc313aeadc7eb05
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409250"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>빠른 시작: Azure의 Linux 가상 머신에 Ansible 설치

Ansible을 사용하면 사용자 환경에서 리소스의 배포 및 구성을 자동화할 수 있습니다. 이 문서에서는 가장 일반적인 Linux 배포판 중 일부에 사용되는 Ansible을 구성하는 방법을 보여줍니다. 다른 배포판에 Ansible을 설치하려면 설치된 패키지를 해당 플랫폼에 맞게 조정합니다. 

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Linux 또는 Linux 가상 머신에 대한 액세스 권한** - Linux 머신에 액세스할 수 없는 경우 [Linux 가상 머신](/azure/virtual-network/quick-create-cli)을 만듭니다.

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Azure Linux 가상 머신에 Ansible 설치

Linux 가상 머신에 로그인하고, Ansible 설치 방법에 대한 단계에서 다음 배포판 중 하나를 선택합니다.

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

이 섹션에서는 Ansible을 사용하도록 CentOS를 구성합니다.

1. 터미널 창을 엽니다.

1. 다음 명령을 입력하여 Azure Python SDK 모듈에 필요한 패키지를 설치합니다.

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. 다음 명령을 입력하여 Ansible에 필요한 패키지를 설치합니다.

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Azure 자격 증명을 만듭니다](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

이 섹션에서는 Ansible을 사용하도록 Ubuntu를 구성합니다.

1. 터미널 창을 엽니다.

1. 다음 명령을 입력하여 Azure Python SDK 모듈에 필요한 패키지를 설치합니다.

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. 다음 명령을 입력하여 Ansible에 필요한 패키지를 설치합니다.

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Azure 자격 증명을 만듭니다](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

이 섹션에서는 Ansible을 사용하도록 SLES를 구성합니다.

1. 터미널 창을 엽니다.

1. 다음 명령을 입력하여 Azure Python SDK 모듈에 필요한 패키지를 설치합니다.

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. 다음 명령을 입력하여 Ansible에 필요한 패키지를 설치합니다.

    ```bash
    sudo pip install ansible[azure]
    ```

1. 다음 명령을 입력하여 충돌하는 Python 암호화 패키지를 제거합니다.

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Azure 자격 증명을 만듭니다](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Azure 자격 증명 만들기

Ansible 자격 증명을 구성하려면 다음 정보가 필요합니다.

* Azure 구독 ID 
* 서비스 주체 값

Ansible Tower 또는 Jenkins를 사용하는 경우 서비스 주체 값을 환경 변수로 선언합니다.

다음 방법 중 하나를 사용하여 Ansible 자격 증명을 구성합니다.

- [Ansible 자격 증명 파일 만들기](#file-credentials)
- [Ansible 환경 변수 사용](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Ansible 자격 증명 파일 만들기

이 섹션에서는 Ansible에 자격 증명을 제공하는 로컬 자격 증명 파일을 만듭니다. 

Ansible 자격 증명 정의에 대한 자세한 내용은 [Azure 모듈에 자격 증명 제공](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules)을 참조하세요.

1. 개발 환경의 경우 다음과 같이 호스트 가상 머신에 `credentials`라는 파일을 만듭니다.

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. 다음 줄을 파일에 추가합니다. 자리 표시자를 서비스 주체 값으로 바꿉니다.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. 파일을 저장하고 닫습니다.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Ansible 환경 변수 사용

이 섹션에서는 서비스 주체 값을 내보내서 Ansible 자격 증명을 구성합니다.

1. 터미널 창을 엽니다.

1. 다음과 같이 서비스 주체 값을 내보냅니다.

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>구성 확인

구성이 성공했는지 확인하려면 Ansible을 사용하여 Azure 리소스 그룹을 만듭니다.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [빠른 시작: Azure에서 Ansible을 사용하여 Linux 가상 머신 구성](./ansible-create-vm.md)