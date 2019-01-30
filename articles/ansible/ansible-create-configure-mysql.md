---
title: Ansible을 사용하여 Azure Database for MySQL 서버 만들기 및 구성
description: Ansible을 사용하여 Azure Database for MySQL 서버를 만들고 구성하는 방법 알아보기
ms.service: ansible
keywords: Ansible, Azure, DevOps, Bash, 플레이북, MySQK, 데이터베이스
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/23/2018
ms.openlocfilehash: 85f3957af599c80c46871a126681d29dfa513431
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051019"
---
# <a name="create-and-configure-an-azure-database-for-mysql-server-by-using-ansible"></a>Ansible을 사용하여 Azure Database for MySQL 서버 만들기 및 구성
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/)은 클라우드에서 고가용성 MySQL 데이터베이스를 실행, 관리 및 확장하는 데 사용하는 관리되는 서비스입니다. Ansible을 사용하면 사용자 환경에서 리소스의 배포 및 구성을 자동화할 수 있습니다. 

이 문서에서는 Ansible을 사용하여 Azure Database for MySQL 서버를 만들고 해당 방화벽 규칙을 구성하는 방법을 보여줍니다. Azure Portal을 사용하여 약 5분 이내에 이러한 작업을 완료할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
- **Azure 구독** - Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.7은 이 자습서에서 다음의 샘플 플레이북을 실행해야 합니다. 

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.  

다음 예제에서는 **eastus** 위치에 **myResourceGroup**이라는 리소스 그룹을 만듭니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

위의 플레이북을 **rg.yml**로 저장합니다. 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.
```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>MySQL 서버 및 데이터베이스 만들기
다음 예제에서는 **mysqlserveransible**이라는 MySQL 서버와 **mysqldbansible**이라는 Azure Database for MySQL 인스턴스를 만듭니다. 이는 vCore 1개를 포함하는 Gen 5 Basic Purpose 서버입니다. 

**mysqlserver_name** 값은 고유해야 합니다. 지역당 및 계층당 유효한 값을 이해하려면 [가격 책정 계층 설명서](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers)를 참조합니다. 암호를 `<server_admin_password>`로 대체합니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

위의 플레이북을 **mysql_create.yml**로 저장합니다. 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.
```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>방화벽 규칙 구성
서버 수준 방화벽 규칙을 사용하면 외부 애플리케이션에서 Azure MySQL 서비스 방화벽을 통해 서버에 연결할 수 있습니다. 외부 애플리케이션에는 **mysql** 명령줄 도구 또는 MySQL Workbench 등이 있습니다.
다음 예제는 외부 IP 주소에서 연결할 수 있는 **extenalaccess**라는 방화벽 규칙을 만듭니다. 

**startIpAddress** 및 **endIpAddress**에 대한 값을 직접 입력합니다. 연결할 위치에 해당하는 IP 주소 범위를 사용합니다. 

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

> [!NOTE]
> Azure Database for MySQL에 대한 연결은 포트 3306을 통해 통신합니다. 회사 네트워크 내에서 연결하려고 하면 3306 포트를 통한 아웃바운드 트래픽이 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 3306 포트를 열지 않으면 서버에 연결할 수 없습니다.
> 

여기에서 **azure_rm_resource** 모듈은 이 작업을 수행하는 데 사용되어 REST API를 직접 사용할 수 있습니다.

위의 플레이북을 **mysql_firewall.yml**로 저장합니다. 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.
```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server-by-using-the-command-line-tool"></a>명령줄 도구를 사용하여 서버에 연결
[MySQL을 다운로드](https://dev.mysql.com/downloads/)하여 컴퓨터에 설치할 수 있습니다. 대신 코드 예제에서 **시도** 단추, 또는 Azure Portal의 오른쪽 상단 도구 모음에서 **>_** 단추를 선택하고 **Azure Cloud Shell**을 열 수 있습니다.

다음 명령을 입력합니다. 

1. **mysql** 명령줄 도구를 사용하여 서버에 연결
```azurecli-interactive
 mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
```

2. 서버 상태 보기:
```sql
 mysql> status
```

모든 작업이 제대로 진행되었다면 명령줄 도구에서 다음 텍스트가 출력될 것입니다.

```
demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65233
Server version: 5.6.39.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper

Connection id:          65233
Current database:
Current user:           mysqladmin@13.76.42.93
SSL:                    Cipher in use is AES256-SHA
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         5.6.39.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    utf8
Conn.  characterset:    utf8
TCP port:               3306
Uptime:                 36 min 21 sec

Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
--------------
```

## <a name="using-facts-to-query-mysql-servers"></a>팩트를 사용하여 MySQL 서버 쿼리
다음 예제에서는 **myResourceGroup**에서 MySQL 서버를 쿼리한 후 서버에서 모든 데이터베이스를 쿼리합니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

위의 플레이북을 **mysql_query.yml**로 저장합니다. 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.

```bash
ansible-playbook mysql_query.yml
```

그러면 MySQL 서버의 경우 다음 출력이 나타납니다. 
```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

또한 MySQL 데이터베이스의 경우 다음 출력이 나타납니다.
```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>리소스 정리

이러한 리소스가 필요하지 않은 경우 다음 예제를 실행하여 삭제할 수 있습니다. **myResourceGroup**이라는 리소스 그룹을 삭제합니다. 

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

위의 플레이북을 **rg_delete.yml**로 저장합니다. 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.
```bash
ansible-playbook rg_delete.yml
```

새로 만든 MySQL 서버만 삭제하려는 경우 다음 예를 실행합니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Delete MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        state: absent
```

위의 플레이북을 **mysql_delete.yml**로 저장합니다. 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.
```bash
ansible-playbook mysql_delete.yml
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [Azure의 Ansible](https://docs.microsoft.com/azure/ansible/)
