---
title: Azure 가상 머신에 Ansible 설치
description: Ubuntu, CentOS 및 SLES에서 Azure 리소스를 관리하기 위해 Ansible을 설치 및 구성하는 방법을 알아봅니다.
ms.service: ansible
keywords: Ansible, Azure, DevOps, Bash, cloudshell, 플레이북, Bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: b714470cd12bb7a0cd2d2a00b4f09467726f505d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987053"
---
# <a name="install-ansible-on-azure-virtual-machines"></a>Azure 가상 머신에 Ansible 설치

Ansible을 사용하면 사용자 환경에서 리소스의 배포 및 구성을 자동화할 수 있습니다. Azure에서 Ansible을 사용하여 다른 리소스와 동일한 방식으로 VM(가상 머신)을 관리할 수 있습니다. 이 문서에는 가장 일반적인 Linux 배포판 중 일부에 대해 Ansible 및 필요한 Azure Python SDK 모듈을 설치하는 방법을 자세히 설명합니다. 설치된 패키지를 특정 플랫폼에 맞게 조정하여 다른 배포판에 Ansible을 설치할 수 있습니다. Azure 리소스를 안전하게 만들기 위해 Ansible에 대한 자격 증명을 만들고 정의하는 방법도 알아봅니다. Cloud Shell에서 사용할 수 있는 추가 도구 목록은 [Azure Cloud Shell의 Bash를 위한 기능 및 도구](../../cloud-shell/features.md#tools)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

- **Azure 구독** - Azure 구독이 아직 없는 경우 [무료 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

- **Linux 또는 Linux 가상 머신에 대한 액세스 권한** - Linux 머신에 액세스할 수 없는 경우 [Linux 가상 머신](https://docs.microsoft.com/azure/virtual-network/quick-create-cli)을 만듭니다.

- **Azure 서비스 주체**: [Create an Azure service principal with Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal)(Azure CLI 2.0으로 Azure 서비스 주체 만들기) 문서의 **Create the service principal(서비스 주체 만들기)** 섹션에 나온 지침을 따릅니다. **appId**, **displayName**, **암호**, **테넌트**를 기록해 둡니다.

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Azure Linux 가상 머신에 Ansible 설치

Linux 가상 머신에 로그인하고, Ansible 설치 방법에 대한 단계에서 다음 배포판 중 하나를 선택합니다.

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

터미널 또는 Bash 창에 다음 명령을 입력하여 Azure Python SDK 모듈 및 Ansible에 필요한 패키지를 설치합니다.

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

[Azure 자격 증명 만들기](#create-azure-credentials) 섹션에 설명된 지침을 따릅니다.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

터미널 또는 Bash 창에 다음 명령을 입력하여 Azure Python SDK 모듈 및 Ansible에 필요한 패키지를 설치합니다.


```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

[Azure 자격 증명 만들기](#create-azure-credentials) 섹션에 설명된 지침을 따릅니다.

### <a name="sles-12-sp2"></a>SLES 12 SP2

터미널 또는 Bash 창에 다음 명령을 입력하여 Azure Python SDK 모듈 및 Ansible에 필요한 패키지를 설치합니다.

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

[Azure 자격 증명 만들기](#create-azure-credentials) 섹션에 설명된 지침을 따릅니다.

## <a name="create-azure-credentials"></a>Azure 자격 증명 만들기

서비스 사용자 만들기에서 반환된 구독 ID와 서비스의 조합은 다음 두 방법 중 하나로 Ansible 자격 증명을 구성하는 데 사용됩니다.

- [Ansible 자격 증명 파일 만들기](#file-credentials)
- [Ansible 환경 변수 사용](#env-credentials)

Ansible Tower 또는 Jenkins 같은 도구를 사용하려면 서비스 사용자 값을 환경 변수로 선언하는 옵션을 사용해야 합니다.

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Ansible 자격 증명 파일 만들기

이 섹션에서는 Ansible에 자격 증명을 제공하는 로컬 자격 증명 파일을 만드는 방법을 설명합니다. Ansible 자격 증명을 정의하는 방법에 대한 자세한 내용은 [Azure 모듈에 자격 증명 제공](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules)을 참조하세요.

개발 환경의 경우 다음과 같이 호스트 가상 머신에서 Ansible의 *자격 증명* 파일을 만듭니다.

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

다음 줄을 *자격 증명* 파일에 삽입합니다. 자리 표시자를 서비스 사용자 만들기의 정보로 바꿉니다.

```bash
[default]
subscription_id=<your-subscription_id>
client_id=<security-principal-appid>
secret=<security-principal-password>
tenant=<security-principal-tenant>
```

파일을 저장하고 닫습니다.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Ansible 환경 변수 사용

이 섹션에서는 Ansible 자격 증명을 환경 변수로 내보내서 구성하는 방법을 설명합니다.

터미널 또는 Bash 창에 다음 명령을 실행합니다.

```bash
export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
export AZURE_CLIENT_ID=<security-principal-appid>
export AZURE_SECRET=<security-principal-password>
export AZURE_TENANT=<security-principal-tenant>
```

## <a name="verify-the-configuration"></a>구성 확인
이제 Ansible을 사용하여 리소스 그룹을 만들어보면 구성이 성공했는지 확인할 수 있습니다.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure에서 Ansible을 사용하여 Linux 가상 머신 만들기](./ansible-create-vm.md)
