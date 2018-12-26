---
title: Ansible을 사용하여 Azure 웹앱 만들기
description: Ansible을 사용하여 Linux의 App Service에서 Java 8 및 Tomcat 컨테이너 런타임을 포함하는 웹앱을 만드는 방법 알아보기
ms.service: ansible
keywords: Ansible, Azure, Devops, Bash, 플레이북, Azure App Service, Web App, Java
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: a7e7c04b458575cdc9f2608d0c84f0df105bf202
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53261758"
---
# <a name="create-azure-app-service-web-apps-by-using-ansible"></a>Ansible을 사용하여 Azure App Service 웹앱 만들기
[Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview)(또는 간단히 Web Apps)는 웹 애플리케이션, REST API 및 모바일 백 엔드를 호스팅합니다. .NET, .NET Core, Java, Ruby, Node.js, PHP 또는 Python 등 원하는 언어로 개발할 수 있습니다.

Ansible을 사용하면 사용자 환경에서 리소스의 배포 및 구성을 자동화할 수 있습니다. 이 문서에서는 Ansible을 사용하여 Java 런타임을 포함하는 웹앱을 만드는 방법을 보여줍니다. 

## <a name="prerequisites"></a>필수 조건
- **Azure 구독** - Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.7은 이 자습서에서 다음의 샘플 플레이북을 실행해야 합니다.

## <a name="create-a-simple-app-service"></a>간단한 앱 서비스 만들기
이 섹션에서는 다음 리소스를 정의하는 샘플 Ansible 플레이북을 제공합니다.
- 리소스 그룹, App Service 계획 및 웹앱을 배포할 위치
- Linux의 App Service에서 Java 8 및 Tomcat 컨테이너 런타임을 포함하는 웹앱

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    webapp_name: myfirstWebApp
    plan_name: myAppServicePlan
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```
위의 플레이북을 **firstwebapp.yml**로 저장합니다.

플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.
```bash
ansible-playbook firstwebapp.yml
```

Ansible 플레이북을 실행하여 얻은 출력은 웹앱이 성공적으로 만들어졌음을 보여줍니다.

```Output
PLAY [localhost] *************************************************

TASK [Gathering Facts] *************************************************
ok: [localhost]

TASK [Create a resource group] *************************************************
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] *************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP *************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-service-by-using-traffic-manager"></a>Traffic Manager를 사용하여 앱 서비스 만들기
[Azure Traffic Manager](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager)를 사용하면 웹 클라이언트의 요청을 Azure App Service의 앱에 분산하는 방법을 제어할 수 있습니다. App Service 엔드포인트가 Azure Traffic Manager 프로필에 추가되면 Traffic Manager는 App Service 앱의 상태를 추적합니다. 상태에는 실행 중, 중지됨 및 삭제됨이 있습니다. 그런 다음, Traffic Manager는 트래픽을 수신할 엔드포인트를 결정할 수 있습니다.

App Service의 앱은 [App Service 계획](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview
)에서 실행됩니다. App Service 계획은 실행할 웹앱에 대한 계산 리소스 집합을 정의합니다. 다른 그룹에서 App Service 계획 및 웹앱을 관리할 수 있습니다.

이 섹션에서는 다음 리소스를 정의하는 샘플 Ansible 플레이북을 제공합니다.
- 리소스 그룹, App Service 계획을 배포할 위치
- App Service 계획
- 두 번째 리소스 그룹, 웹앱을 배포할 위치
- Linux의 App Service에서 Java 8 및 Tomcat 컨테이너 런타임을 포함하는 웹앱
- Traffic Manager 프로필
- Traffic Manager 엔드포인트, 만든 웹 사이트 사용

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group_webapp: myResourceGroupWebapp
    resource_group: myResourceGroup
    webapp_name: myLinuxWebApp
    plan_name: myAppServicePlan
    location: eastus
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group_webapp }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group_webapp }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ webapp_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group_webapp }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"
```
위의 플레이북을 **webapp.yml**로 저장하거나 [플레이북을 다운로드](https://github.com/Azure-Samples/ansible-playbooks/blob/master/webapp.yml)합니다.

플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.
```bash
ansible-playbook webapp.yml
```

Ansible 플레이북을 실행하여 얻은 결과는 App Service 계획, 웹앱, Traffic Manager 프로필 및 엔드포인트가 성공적으로 만들어졌음을 보여 줍니다.
```Output
PLAY [localhost] *************************************************

TASK [Gathering Facts] *************************************************
ok: [localhost]

TASK [Create resource group] ****************************************************************************
changed: [localhost]

TASK [Create resource group for app service plan] ****************************************************************************
changed: [localhost]

TASK [Create App Service Plan] ****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] ****************************************************************************
changed: [localhost]

TASK [Get web app facts] *****************************************************************************
ok: [localhost]

TASK [Create Traffic Manager Profile] *****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] *****************************************************************************
changed: [localhost]

TASK [Get Traffic Manager Profile facts] ******************************************************************************
ok: [localhost]

PLAY RECAP ******************************************************************************
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [Ansible을 사용하여 Azure App Service 웹앱 크기 조정](https://docs.microsoft.com/azure/ansible/ansible-scale-azure-web-apps)