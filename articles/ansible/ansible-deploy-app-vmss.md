---
title: Ansible을 사용하여 Azure에서 가상 머신 확장 집합에 애플리케이션 배포
description: Ansible을 사용하여 가상 머신 확장 집합을 구성하고 Azure의 가상 머신 확장 집합에서 애플리케이션을 배포하는 방법 알아보기
ms.service: ansible
keywords: ansible, azure, devops, bash, 플레이북, 가상 머신, 가상 머신 확장 집합, vmss
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/11/2018
ms.openlocfilehash: 049fc711d0cf6a69b584ad3926bd9e9c0fc9e27d
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408377"
---
# <a name="deploy-applications-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Ansible을 사용하여 Azure에서 가상 머신 확장 집합에 애플리케이션 배포
Ansible을 사용하면 사용자 환경에서 리소스의 배포 및 구성을 자동화할 수 있습니다. Ansible을 사용하여 Azure에 애플리케이션을 배포할 수 있습니다. 이 문서에서는 Azure VMSS(가상 머신 확장 집합)에 Java 애플리케이션을 배포하는 방법을 보여줍니다.  

## <a name="prerequisites"></a>필수 조건
- **Azure 구독** - Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **가상 머신 확장 집합** - 가상 머신 확장 집합이 아직 없는 경우 [Ansible을 사용하여 가상 머신 확장 집합을 만들](ansible-create-configure-vmss.md) 수 있습니다. 
- **git** - [git](https://git-scm.com)는 이 자습서에 사용되는 Java 샘플을 다운로드하는 데 사용됩니다.
- **JDK(Java SE Development Kit)** - [JDK](https://aka.ms/azure-jdks)는 샘플 Java 프로젝트를 빌드하는 데 사용됩니다.
- **Apache Maven 빌드 도구** - [Apache Maven 빌드 도구](https://maven.apache.org/download.cgi)는 샘플 Java 프로젝트를 빌드하는 데 사용됩니다.

> [!Note]
> Ansible 2.6은 이 자습서에서 다음의 샘플 플레이북을 실행해야 합니다. 

## <a name="get-host-information"></a>호스트 정보 가져오기

이 섹션에서는 Ansible을 사용하여 Azure 가상 머신의 그룹에 대한 호스트 정보를 검색하는 방법을 보여줍니다. 다음은 샘플 Ansible 플레이북입니다. 코드는 지정된 리소스 그룹 내에서 공용 IP 주소 및 부하 분산 장치를 가져오고, 인벤토리에 **scalesethosts**라는 호스트 그룹을 만듭니다. 

다음 샘플 플레이북을 `get-hosts-tasks.yml`로 저장합니다. 

  ```yml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_facts.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>배포를 위한 애플리케이션 준비  

이 섹션에서는 git을 사용하여 GitHub에서 Java 샘플 프로젝트를 복제하고 프로젝트를 빌드합니다. 다음 플레이북을 `app.yml`로 저장합니다.

  ```yml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks: 
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

다음 명령을 사용하여 샘플 Ansible 플레이북을 실행합니다.

  ```bash
  ansible-playbook app.yml
  ```

Ansible 플레이북 명령의 출력은 GitHub에서 복제된 샘플 앱을 빌드한 다음과 비슷하게 표시됩니다.

  ```Output
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Git Clone sample app] ***************************************************************************
  changed: [localhost]

  TASK [Build sample app] ***************************************************
  changed: [localhost]

  PLAY RECAP ***************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-vmss"></a>VMSS에 애플리케이션 배포

Ansible 플레이북의 다음 섹션에서는 **saclesethosts**라는 호스트 그룹에 JRE(Java Runtime Environment)를 설치하고, **saclesethosts**라는 호스트 그룹에 Java 애플리케이션을 배포합니다. 

(`admin_password`를 고유한 암호로 변경합니다.)

  ```yml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      scaleset_name: myVMSS
      loadbalancer_name: myVMSSlb
      admin_username: azureuser
      admin_password: "your_password"
    tasks:   
    - include: get-hosts-tasks.yml

  - name: Install JRE on VMSS
    hosts: scalesethosts
    become: yes
    vars:
      workspace: ~/src/helloworld
      admin_username: azureuser

    tasks:
    - name: Install JRE
      apt:
        name: default-jre
        update_cache: yes

    - name: Copy app to Azure VM
      copy:
        src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
        dest: "/home/{{ admin_username }}/helloworld.jar"
        force: yes
        mode: 0755

    - name: Start the application
      shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
      async: 5000
      poll: 0
  ```

위의 샘플 Ansible 플레이북을 `vmss-setup-deploy.yml`로 저장하거나 [전체 샘플 플레이북을 다운로드](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss)할 수 있습니다. 

암호로 ssh 연결 형식을 사용하려면 sshpass 프로그램을 설치해야 합니다. 
  - Ubunto 16.04의 경우 `apt-get install sshpass` 명령을 실행합니다.
  - CentOS 7.4의 경우 `yum install sshpass` 명령을 실행합니다.

**호스트 키 확인이 활성화되었고 sshpass는 이를 지원하지 않으므로 키 대신 SSH 암호를 사용할 수 없습니다. 이 호스트를 관리하려면 known_hosts 파일에 이 호스트의 지문을 추가합니다.** 와 같은 오류가 표시될 수 있습니다. 이 오류가 표시되는 경우 `/etc/ansible/ansible.cfg` 파일 또는 `~/.ansible.cfg` 파일에 다음 줄을 추가하여 호스트 키 확인을 비활성화할 수 있습니다.
  ```bash
  [defaults]
  host_key_checking = False
  ```

다음 명령을 사용하여 플레이북을 실행합니다.

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Ansible 플레이북 명령 실행에서 출력은 샘플 Java 애플리케이션이 가상 머신 확장 집합의 호스트 그룹에 설치되었음을 나타냅니다.

  ```Output
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups] **********************************************
  ok: [localhost]

  TASK [Get loadbalancer info] ****************************************************************************
  ok: [localhost]

  TASK [Add all hosts] *****************************************************************************
  changed: [localhost] ...

  PLAY [Install JRE on VMSS] *****************************************************************************

  TASK [Gathering Facts] *****************************************************************************
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM] *****************************************************************************
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application] ********************************************************************
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP ************************************************************************************************
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

축하합니다. 이제 애플리케이션이 Azure에서 실행되고 있습니다. 이제 가상 머신 확장 집합에 대해 부하 분산 장치의 URL을 탐색할 수 있습니다.

![Azure의 가상 머신 확장 집합에서 실행되는 Java 앱](media/ansible-deploy-app-vmss/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [Ansible을 사용하여 가상 머신 확장 세트의 크기를 자동으로 조정](https://docs.microsoft.com/azure/ansible/ansible-auto-scale-vmss)