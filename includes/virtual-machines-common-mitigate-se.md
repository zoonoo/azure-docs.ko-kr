---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/12/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: a9146099951aba223a7b201c1613e1ec0ba617d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96509216"
---
**마지막 문서 업데이트**: 2019년 11월 12일 오전 10시 PST.

투기적 실행 사이드 채널 공격으로 알려진 공개된 [새로운 종류의 CPU 취약점](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)으로 인해 고객은 명확하게 이해하기 위해 질문하게 되었습니다.  

Microsoft는 모든 클라우드 서비스에서 완화 기능을 배포했습니다. Azure를 실행하고 고객 워크로드를 서로 격리하는 인프라가 보호됩니다. 즉, 동일한 인프라를 사용하는 잠재적 공격자가 이러한 취약점을 사용하여 애플리케이션을 공격할 수 없습니다.

Azure에서는 가능한 경우 [메모리 보존 유지 관리](../articles/virtual-machines/maintenance-and-updates.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json%252c%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json%253ftoc%253d%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#maintenance-that-doesnt-require-a-reboot)를 사용하여 고객 영향을 최소화하고 다시 부팅할 필요를 제거합니다. Azure는 호스트에 대한 시스템 전반을 업데이트하고 고객을 보호할 때 이러한 메서드를 계속 활용합니다.

Azure의 모든 측면에 보안을 통합하는 방법에 대한 자세한 정보는 [Azure 보안 설명서](../articles/security/index.yml) 사이트에서 사용할 수 있습니다. 

> [!NOTE] 
> 이 문서를 처음 게시한 이후 이 취약점 종류의 여러 변형이 공개되었습니다. Microsoft에서는 고객을 보호하고 지침을 제공하는 데 계속 투자하고 있습니다. 계속 추가 수정을 릴리스하므로 이 페이지가 업데이트됩니다. 
> 
> 2019년 11월 12일, Intel은 Intel® TSX(Intel® 트랜잭션 동기화 확장) TAA(트랜잭션 비동기 중단) 취약성([CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135))에 대한 기술 자문을 [발표](https://software.intel.com/security-software-guidance/insights/deep-dive-intel-transactional-synchronization-extensions-intel-tsx-asynchronous-abort)했습니다. 이 취약점은 Intel® Core® 프로세서 및 Intel® Xeon® 프로세서에 영향을 줍니다.  Microsoft Azure는 운영 체제 업데이트를 출시했으며 새 마이크로코드를 배포하고 있습니다. 이는 Intel에서 사용할 수 있게 만들었으며, 이러한 새로운 취약점으로부터 고객을 보호할 수 있습니다.   Azure는 플랫폼에 공식 출시하기 전에 새 마이크로코드를 테스트하고 유효성을 검사하기 위해 Intel과 긴밀하게 협력하고 있습니다. 
>
> **VM 내에서 신뢰할 수 없는 코드를 실행하는 고객** 은 모든 추측 실행 부채널 취약점(Microsoft 권고 고급 [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018) 및 [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013))에 대한 추가 지침을 참조하여 이러한 취약성으로부터 보호하기 위한 조치를 취해야 합니다.
>
> 다른 고객은 심층 방어적 관점에서 이러한 취약성을 평가하고 선택된 구성이 보안 및 성능에 미치는 영향을 고려해야 합니다.
> 



## <a name="keeping-your-operating-systems-up-to-date"></a>운영 체제를 최신 상태로 유지

OS 업데이트가 다른 Azure 고객으로부터 Azure에서 실행되는 애플리케이션을 격리하지 않아도 되지만 항상 소프트웨어를 최신 상태로 유지하는 것이 좋습니다. Windows용 최신 보안 롤업에는 여러 투기적 실행 쪽 채널 취약점에 대한 완화가 포함됩니다. 마찬가지로 Linux 배포판은 이러한 취약점을 해결하기 위해 여러 업데이트를 릴리스했습니다. 운영 체제를 업데이트하기 위해 권장된 조치는 다음과 같습니다.

| 제품 | 권장 작업  |
|----------|---------------------|
| Azure Cloud Services  | [자동 업데이트](../articles/cloud-services/cloud-services-how-to-configure-portal.md)를 사용하거나 최신 게스트 OS를 실행합니다. |
| Azure Linux Virtual Machines | 운영 체제 공급자로부터 업데이트를 설치합니다. 자세한 내용은 이 문서의 뒷부분에 나오는 [Linux](#linux)를 참조하세요. |
| Azure Windows Virtual Machines  | 최신 보안 롤업을 설치합니다.
| 기타 Azure PaaS 서비스 | 이러한 서비스를 사용하는 고객에게 필요한 작업은 없습니다. Azure에서는 자동으로 OS 버전을 최신 상태로 유지합니다. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>신뢰할 수 없는 코드를 실행하는 경우 추가 지침 

신뢰할 수 없는 사용자가 임의의 코드를 실행하도록 허용하는 고객은 해당 Azure Virtual Machines 또는 Cloud Services 내에서 몇 가지 추가 보안 기능을 구현하려고 할 수 있습니다. 이러한 기능은 몇 가지 투기적 실행 취약점에서 설명하는 프로세스 내 공개 벡터로부터 보호합니다.

추가 보안 기능을 권장하는 예제 시나리오:

- 신뢰할 수 없는 코드를 VM 내에서 실행할 수 있습니다.  
    - *예를 들어 고객 중 한 사람이 애플리케이션 내에서 실행하는 이진 파일 또는 스크립트를 업로드하도록 허용합니다*. 
- 신뢰할 수 없는 사용자가 권한이 낮은 계정을 사용하여 VM에 로그인하도록 허용합니다.   
    - *예를 들어 권한이 낮은 사용자가 원격 데스크톱 또는 SSH를 사용하여 VM 중 하나에 로그인하도록 허용합니다*.  
- 신뢰할 수 없는 사용자가 중첩된 가상화를 통해 구현되는 가상 머신에 액세스하도록 허용합니다.  
    - *예를 들어 Hyper-V 호스트를 제어하지만 신뢰할 수 없는 사용자에게 VM을 할당합니다*. 

신뢰할 수 없는 코드를 포함하는 시나리오를 구현하지 않는 고객은 이러한 추가 보안 기능을 사용할 필요가 없습니다. 

## <a name="enabling-additional-security"></a>추가 보안 사용 

신뢰할 수 없는 코드를 실행하는 경우 VM 또는 클라우드 서비스 내에서 추가 보안 기능을 사용하도록 설정할 수 있습니다. 이와 함께 VM 또는 클라우드 서비스 내에서 보안 기능을 사용할 수 있도록 운영 체제가 최신 상태인지 확인합니다.

### <a name="windows"></a>Windows 

대상 운영 체제를 최신 상태로 유지하여 이러한 추가 보안 기능을 사용하도록 설정해야 합니다. 다양한 투기적 실행 쪽 채널 완화를 기본적으로 사용하는 반면 여기에 설명된 추가 기능은 수동으로 활성화해야 하며 성능에 영향이 발생할 수 있습니다. 


**1단계: VM에서 하이퍼 스레딩 사용 안 함** -하이퍼 스레드 VM에서 신뢰할 수 없는 코드를 실행하는 고객은 하이퍼 스레딩을 사용하지 않도록 설정하거나 하이퍼 스레드 이외의 VM 크기로 변경해야 합니다. 하이퍼 스레드 VM 크기 목록을 보려면 [이 문서](../articles/virtual-machines/acu.md)를 참조하세요. 여기서 vCPU 대 Core의 비율은 2:1입니다. VM에서 하이퍼 스레딩을 사용하도록 설정했는지 확인하려면 VM 내에서 Windows 명령줄을 사용하여 아래 스크립트를 참조하세요.

`wmic`를 입력하여 대화형 인터페이스로 들어갑니다. 그런 다음, 아래와 같이 입력하여 VM의 물리적 및 논리적 프로세서의 양을 확인합니다.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

논리적 프로세서 수가 물리적 프로세서(코어)보다 큰 경우 하이퍼 스레딩이 활성화됩니다.  하이퍼 스레드 VM을 실행하는 경우 [Azure 지원에 문의](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)하여 하이퍼 스레딩을 사용하지 않도록 설정하세요.  하이퍼 스레딩을 사용하지 않도록 설정하면 **전체 VM 재부팅이 필요** 합니다. VM 코어 수가 감소한 이유를 이해하려면 [코어 수](#core-count)를 참조하세요.


**2단계**: 1단계와 함께, [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) 지침에 따라 [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell 모듈을 사용하여 보호가 설정되었는지 확인합니다.

> [!NOTE]
> 이전에 이 모듈을 다운로드한 경우 최신 버전을 설치해야 합니다.
>


PowerShell 스크립트의 출력에는 이러한 취약성에 대해 사용하도록 설정된 보호의 유효성을 검사하는 아래 값이 있습니다.

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
Windows OS support for TAA mitigation is enabled: True
```

출력에 `MDS mitigation is enabled: False`이 표시되는 경우 [Azure 지원에 문의](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)하여 사용 가능한 위험 완화 옵션을 확인하세요.



**3단계**: KVAS(커널 가상 주소 섀도잉) 및 BTI(분기 대상 삽입) OS 지원을 사용하도록 설정하려면 [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)의 지침에 따라 `Session Manager` 레지스트리 키를 사용하여 보호를 사용하도록 설정합니다. 다시 부팅해야 합니다.


**4단계**: [중첩된 가상화](../articles/virtual-machines/windows/nested-virtualization.md)(D3 및 E3에만 해당)를 사용 중인 배포의 경우 Hyper-V 호스트로 사용하는 VM 내에서 이러한 지침이 적용됩니다.

1.  [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) 지침에 따라 `MinVmVersionForCpuBasedMitigations` 레지스트리 키를 통해 보호를 사용하도록 설정합니다.
2.  [여기](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types)에 있는 지침에 따라 하이퍼바이저 스케줄러 형식을 `Core`로 설정합니다.


### <a name="linux"></a>Linux

<a name="linux"></a> 내부 추가 보안 기능 집합을 사용하도록 설정하면 대상 운영 체제를 최신 상태로 유지해야 합니다. 일부 완화는 기본적으로 활성화됩니다. 다음 섹션에서는 기본적으로 해제되어 있고 하드웨어 지원(마이크로코드)을 사용하는 기능을 설명합니다. 이러한 기능을 활성화하면 성능에 영향이 발생할 수 있습니다. 자세한 지침은 운영 체제 공급자의 설명서를 참조합니다.


**1단계: VM에서 하이퍼 스레딩 사용 안 함** -하이퍼 스레드 VM에서 신뢰할 수 없는 코드를 실행하는 고객은 하이퍼 스레딩을 사용하지 않도록 설정하거나 하이퍼 스레드 이외의 VM으로 변경해야 합니다.  하이퍼 스레드 VM 크기 목록을 보려면 [이 문서](../articles/virtual-machines/acu.md)를 참조하세요. 여기서 vCPU 대 Core의 비율은 2:1입니다. 하이퍼 스레드 VM을 실행하고 있는지 확인하려면 Linux VM에서 `lscpu` 명령을 실행합니다. 

`Thread(s) per core = 2`면 하이퍼 스레딩을 사용하도록 설정된 것입니다. 

`Thread(s) per core = 1`이면 하이퍼 스레딩을 사용하도록 설정된 것이 아닙니다. 

 
하이퍼 스레딩을 사용하도록 설정된 VM에 대한 샘플 출력: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

하이퍼 스레드 VM을 실행하는 경우 [Azure 지원에 문의](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)하여 하이퍼 스레딩을 사용하지 않도록 설정하세요.  하이퍼 스레딩을 사용하지 않도록 설정하면 **전체 VM 재부팅이 필요** 합니다. VM 코어 수가 감소한 이유를 이해하려면 [코어 수](#core-count)를 참조하세요.



**2단계**: 아래의 추측 실행 부채널 취약성에 대한 위험을 완화하기 위해 운영 체제 공급자의 설명서를 참조하세요.   
 
- [Redhat 및 CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>코어 수

하이퍼 스레드 VM이 생성되면 Azure는 코어 당 2개의 스레드를 할당합니다. 이를 vCPU라고 합니다. 하이퍼 스레딩을 사용하지 않도록 설정하면 Azure는 스레드를 제거하 고 단일 스레드 코어(물리적 코어)를 표시합니다. vCPU와 CPU의 비율은 2:1이므로 하이퍼 스레딩을 사용하지 않도록 설정하면 VM의 CPU 수가 절반으로 감소한 것으로 나타납니다. 예를 들어 D8_v3 VM은 8개 vCPU(코어 당 2개 스레드 x 4코어)에서 실행되는 하이퍼 스레드 VM입니다.  하이퍼 스레딩이 사용하지 않도록 설정된 경우 CPU는 코어 당 1개 스레드를 사용하여 4개의 물리적 코어로 줄어듭니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 많은 모던 프로세서에 영향을 주는 아래의 추측 실행 부채널 공격에 대한 지침을 제공합니다.

[스펙터 멜트다운](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715 - BTI(분기 대상 삽입)  
- CVE-2017-5754 - KPTI(커널 페이지 테이블 격리)
- CVE-2018-3639 – KPTI(추측 저장 우회) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – Windows 커널 정보 – Spectre Variant 1의 변형
 
[L1TF(L1 터미널 오류)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615 - Intel SGX(Intel Software Guard Extensions)
- CVE-2018-3620 - OS(운영 체제) 및 SMM(시스템 관리 모드)
- CVE-2018-3646 – VMM(Virtual Machine Manager) 영향

[Microarchitectural 데이터 샘플링](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019-11091 - MDSUM(Microarchitectural 데이터 샘플링 캐시 불가능 메모리)
- CVE-2018-12126 - MSBDS(Microarchitectural 저장소 버퍼 데이터 샘플링)
- CVE-2018-12127 - MLPDS(Microarchitectural 로드 포트 데이터 샘플링)
- CVE-2018-12130 - MFBDS(Microarchitectural 채우기 버퍼 데이터 샘플링)

Intel® TSX(트랜잭션 동기화 확장) 트랜잭션 비동기 중단:  
- [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) – TSX TAA(트랜잭션 비동기 중단)