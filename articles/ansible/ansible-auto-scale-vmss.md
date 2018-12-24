---
title: Azure에서 Ansible을 사용하여 가상 머신 확장 집합의 크기를 자동으로 조정
description: Azure에서 Ansible을 사용하여 자동 크기 조정으로 가상 머신 확장 집합의 크기를 조정하는 방법 알아보기
ms.service: ansible
keywords: Ansible, Azure, DevOps, bash, 플레이북, 크기 조정, 자동 크기 조정, 가상 머신, 가상 머신 확장 집합, VMSS
author: tomarcher
manager: jeconnoc
ms.author: yuwzho, kyliel
ms.topic: tutorial
ms.date: 12/10/2018
ms.openlocfilehash: c6678d6df3a695d3a0471e5779bc3af4b6ba6c84
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53411165"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-azure-using-ansible"></a>Azure에서 Ansible을 사용하여 가상 머신 확장 집합의 크기를 자동으로 조정
Ansible을 사용하면 사용자 환경에서 리소스의 배포 및 구성을 자동화할 수 있습니다. Ansible을 사용하여 다른 Azure 리소스와 동일한 방식으로 Azure에서 가상 머신 확장 집합(VMSS)을 관리할 수 있습니다. 

확장 집합을 만들 때 실행하려는 VM 인스턴스 수를 정의합니다. 응용 프로그램 수요가 변경될 때는 VM 인스턴스 수를 자동으로 늘리거나 줄일 수 있습니다. 자동 크기 조정 기능을 사용하면 고객 수요에 따라 조정하거나 앱 수명 주기 동안 응용 프로그램 성능 변화에 대응할 수 있습니다. 이 문서에서는 자동 크기 조정 설정을 만들고 기존 가상 머신 확장 집합에 연결합니다. 자동 크기 조정 설정에서 원하는 대로 규모를 확장하고 감축하도록 규칙을 구성할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
- **Azure 구독** - Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- 기존 Azure 가상 머신 확장 집합입니다. - 설치되지 않은 경우 [Ansible을 사용하여 Azure에서 가상 머신 확장 집합을 만듭니다](https://docs.microsoft.com/azure/ansible/ansible-create-configure-vmss).

> [!Note]
> Ansible 2.7은 이 자습서에서 다음의 샘플 플레이북을 실행해야 합니다. 

## <a name="auto-scale-based-on-a-schedule"></a>일정에 따라 자동 크기 조정   
확장 집합에서 자동 크기 조정을 활성화하려면 먼저 자동 크기 조정 프로필을 정의합니다. 이 프로필은 기본, 최소, 최대 확장 집합 용량을 정의합니다. 이러한 제한을 통해 연속적으로 VM 인스턴스를 만들지 않고 비용을 제어하고, 축소 이벤트에 유지되는 최소 인스턴스 수로 허용 가능한 성능의 균형을 유지할 수 있습니다. 

되풀이 일정 또는 특정 날짜별로 Virtual Machine Scale Sets의 규모를 확장하고 감축할 수 있습니다. 이 섹션에서는 태평양 표준 시간대로 매주 월요일 오전 10시에 확장 집합에 있는 VM 인스턴스 수를 3으로 증가시키는 자동 크기 조정 설정을 만드는 샘플 Ansible 플레이북을 제공합니다. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks: 
    - name: Create auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

이 플레이북을 *vmss-auto-scale.yml*로 저장합니다. Ansible 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="auto-scale-based-on-performance-data"></a>성능 데이터에 따라 자동 크기 조정
애플리케이션 수요가 증가하면 확장 집합의 VM 인스턴스 부하가 증가합니다. 증가된 로드가 단순한 요구가 아닌 일관된 요구인 경우 확장 집합의 VM 인스턴스 수를 늘리도록 자동 크기 조정 규칙을 구성할 수 있습니다. 이러한 VM 인스턴스를 만들고 응용 프로그램을 배포하면 확장 집합이 부하 분산 장치를 통해 트래픽을 분산하기 시작합니다. 사용자는 모니터링할 메트릭(예: CPU 또는 디스크), 지정된 임계값을 애플리케이션 로드가 충족해야 하는 기간, 확장 집합에 추가할 VM 인스턴스 수를 제어할 수 있습니다.

되풀이 일정 또는 특정 날짜별로 성능 메트릭 임계값에 따라 Virtual Machine Scale Sets의 규모를 확장하고 감축할 수 있습니다. 이 섹션에서는 태평양 표준 시간대로 매주 월요일 18시 10분에 워크로드를 검사하고, CPU 비율 메트릭에 따라 확장 집합에 있는 VM 인스턴스 수를 4개로 확장하거나 1개로 감축하는 샘플 Ansible 플레이북을 나타냅니다. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get VMSS resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create auto scaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

이 플레이북을 *vmss-auto-scale-metrics.yml*로 저장합니다. Ansible 플레이북을 실행하려면 다음과 같이 **ansible-playbook** 명령을 사용합니다.

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-information-for-existing-autoscale-settings"></a>기존 자동 크기 조정 설정에 대한 정보 가져오기
다음과 같이 플레이북을 사용하여*azure_rm_autoscale_facts* 모듈을 통해 자동 크기 조정 설정의 세부 정보를 가져올 수 있습니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve auto scale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

## <a name="disable-the-autoscale-settings"></a>자동 크기 조정 설정 사용 안 함
`enabled: true`를 `enabled: false`로 변경하거나 다음과 같이 플레이북에서 자동 크기 조정 설정을 삭제하여 자동 크기 조정 설정을 비활성화할 수 있습니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [가상 머신 확장 집합에 대한 Ansible 샘플 플레이북](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)