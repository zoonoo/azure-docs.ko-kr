---
title: '미리 보기: Azure Vm에 대 한 신뢰할 수 있는 시작'
description: Azure virtual machines에 대 한 신뢰할 수 있는 시작에 대해 알아봅니다.
author: khyewei
ms.author: khwei
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: conceptual
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: template-concept; references_regions
ms.openlocfilehash: 449eb1d65e0104e6c5c74a78901cf29c5aeb3e57
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609093"
---
# <a name="trusted-launch-for-azure-virtual-machines-preview"></a>Azure virtual machines에 대 한 신뢰할 수 있는 시작 (미리 보기)

Azure는 [2 세대](generation-2.md) vm의 보안을 향상 시키기 위한 원활한 방법으로 신뢰할 수 있는 시작을 제공 합니다. 신뢰할 수 있는 시작은 고급 및 지속적인 공격 기법을 방지 합니다. 신뢰할 수 있는 시작은 독립적으로 사용 하도록 설정할 수 있는 몇 가지 조정 된 인프라 기술로 구성 됩니다. 각 기술은 정교한 위협에 대 한 또 다른 방어 계층을 제공 합니다.

> [!IMPORTANT]
> 신뢰할 수 있는 시작을 사용 하려면 새 가상 컴퓨터를 만들어야 합니다. 처음에는 사용 하지 않고 만든 기존 가상 머신에서는 신뢰할 수 있는 시작을 사용 하도록 설정할 수 없습니다.
>
> 신뢰할 수 있는 시작은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 
>
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


## <a name="benefits"></a>이점 

- 확인 된 부팅 로더, OS 커널 및 드라이버를 사용 하 여 가상 컴퓨터를 안전 하 게 배포 합니다.
- 가상 컴퓨터에서 키, 인증서 및 암호를 안전 하 게 보호 합니다.
- 전체 부팅 체인의 무결성에 대 한 통찰력을 확보 하세요.
- 워크 로드를 신뢰할 수 있고 확인할 수 있는지 확인 합니다.

## <a name="public-preview-limitations"></a>공용 미리 보기 제한 사항

**크기 지원**: 다음을 제외한 모든 [2 세대](generation-2.md) VM 크기

- Lsv2 시리즈 
- M 시리즈 
- Mv2 시리즈 
- NDv4 시리즈 
- NVv4 시리즈

**OS 지원**:
- Redhat Enterprise Linux 8.3
- SUSE 15 SP2
- Ubuntu 20.04 LTS
- Ubuntu 18.04 LTS
- Windows Server 2019
- Windows Server 2016
- Windows 10 Pro
- Windows 10 Enterprise

**지역**: 
- 미국 중남부
- 북유럽

**가격 책정**: 기존 VM 가격에 대 한 추가 비용은 없습니다.

**다음 기능은이 미리 보기에서 지원 되지 않습니다**.
- Backup
- Azure Site Recovery
- 공유 이미지 갤러리
- 임시 OS 디스크
- 공유 디스크
- 관리형 이미지
- Azure Dedicated Host 

## <a name="secure-boot"></a>보안 부팅

신뢰할 수 있는 시작의 루트에는 VM에 대 한 보안 부팅이 있습니다. 플랫폼 펌웨어에서 구현 되는이 모드는 맬웨어 기반 루트킷 및 부팅 키트의 설치를 방지 합니다. 보안 부팅은 서명 된 운영 체제 및 드라이버만 부팅할 수 있도록 하기 위해 작동 합니다. VM의 소프트웨어 스택에 대 한 "트러스트의 루트"를 설정 합니다. 보안 부팅을 사용 하도록 설정 하면 신뢰할 수 있는 게시자가 모든 OS 부팅 구성 요소 (부팅 로더, 커널, 커널 드라이버)에 서명 해야 합니다. Windows 및 select Linux 배포판은 모두 보안 부팅을 지원 합니다. 보안 부팅이 신뢰할 수 있는 게시자가 서명한 이미지를 인증 하지 못하는 경우 VM은 부팅을 허용 하지 않습니다. 자세한 내용은 [보안 부팅](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-secure-boot)을 참조하세요.

## <a name="vtpm"></a>vTPM

또한 신뢰할 수 있는 시작은 Azure Vm에 대 한 vTPM을 도입 합니다. TPM 2.0 사양을 준수 하는 하드웨어 [신뢰할 수 있는 플랫폼 모듈](/windows/security/information-protection/tpm/trusted-platform-module-overview)의 가상화 된 버전입니다. 키 및 측정을 위한 전용 보안 자격 증명 모음 역할을 합니다. 신뢰할 수 있는 시작은 vm의 범위 외부에 있는 보안 환경에서 실행 되는 고유한 전용 TPM 인스턴스를 VM에 제공 합니다. VTPM은 VM (UEFI, OS, 시스템 및 드라이버)의 전체 부팅 체인을 측정 하 여 [증명](/windows/security/information-protection/tpm/tpm-fundamentals#measured-boot-with-support-for-attestation) 을 사용 하도록 설정 합니다. 

신뢰할 수 있는 시작은 클라우드에서 원격 증명을 수행 하는 데 vTPM을 사용 합니다. 이는 플랫폼 상태 검사 및 트러스트 기반 결정을 내리는 데 사용 됩니다. 상태 검사를 통해 신뢰할 수 있는 시작은 VM이 올바르게 부팅 되었음을 암호화 하 여 인증할 수 있습니다. VM이 인증 되지 않은 구성 요소를 실행 하 고 있기 때문에 프로세스가 실패 하는 경우 무결성 경고를 Azure Security Center 합니다. 경고에는 무결성 검사를 통과 하지 못한 구성 요소에 대 한 세부 정보가 포함 됩니다.

## <a name="virtualization-based-security"></a>가상화 기반 보안

VBS ( [가상화 기반 보안](/windows-hardware/design/device-experiences/oem-vbs) )는 하이퍼바이저를 사용 하 여 안전 하 고 격리 된 메모리 영역을 만듭니다. Windows는 이러한 영역을 사용 하 여 취약성 및 악의적인 악용에 대 한 보호 기능이 향상 된 다양 한 보안 솔루션을 실행 합니다. 신뢰할 수 있는 시작을 통해 HVCI (하이퍼바이저 코드 무결성) 및 Windows Defender Credential Guard를 사용 하도록 설정할 수 있습니다.

HVCI는 악성 또는 확인 되지 않은 코드의 삽입 및 실행에 대해 Windows 커널 모드 프로세스를 보호 하는 강력한 시스템 완화 기능입니다. 커널 모드 드라이버와 이진 파일이 실행 되기 전에 검사 하 여 서명 되지 않은 파일이 메모리로 로드 되는 것을 방지 합니다. 이렇게 하면 해당 실행 코드를 로드할 수 있는 경우 수정할 수 없습니다. VBS 및 HVCI에 대 한 자세한 내용은 [vbs (가상화 기반 보안) 및 hvci (하이퍼바이저 적용 코드 무결성)](https://techcommunity.microsoft.com/t5/windows-insider-program/virtualization-based-security-vbs-and-hypervisor-enforced-code/m-p/240571)를 참조 하세요.

신뢰할 수 있는 시작 및 VBS를 사용 하면 Windows Defender Credential Guard를 사용 하도록 설정할 수 있습니다. 이 기능은 권한 있는 시스템 소프트웨어만 액세스할 수 있도록 암호를 격리 하 고 보호 합니다. PtH (Pass-Hash) 공격과 같은 비밀 및 자격 증명 도난 공격에 대 한 무단 액세스를 방지 하는 데 도움이 됩니다. 자세한 내용은 [Credential Guard](https://docs.microsoft.com/windows/security/identity-protection/credential-guard/credential-guard)를 참조 하세요.


## <a name="security-center-integration"></a>Security Center 통합

신뢰할 수 있는 시작은 Vm이 올바르게 구성 되었는지 확인 하기 위해 Azure Security Center와 통합 됩니다. Azure Security Center은 계속 해 서 호환 되는 Vm을 평가 하 고 관련 권장 사항을 발급 합니다.

- **보안 부팅을 사용 하도록 설정 하는 권장** 사항-이 권장 사항은 신뢰할 수 있는 시작을 지 원하는 vm에만 적용 됩니다. Azure Security Center는 보안 부팅을 사용할 수 있지만 사용 하지 않도록 설정 된 Vm을 식별 합니다. 이 기능을 사용 하도록 설정 하는 데 낮은 심각도 권장 사항을 발급 합니다.
- **VTPM 사용에 대 한 권장 사항** -VM에서 vtpm을 사용 하도록 설정 된 경우 게스트 증명을 수행 하 고 고급 위협 패턴을 식별 하는 데 사용할 수 Azure Security Center. Azure Security Center 신뢰할 수 있는 시작을 지원 하 고 vTPM을 사용 하지 않도록 설정 된 Vm을 식별 하는 경우 낮은 심각도 권장 사항을 발급 하 여 사용 하도록 설정 합니다. 
- **증명 상태 평가** -Vm에서 vtpm을 사용 하는 경우 Azure Security Center 확장은 vm이 정상 방식으로 부팅 되었는지 원격으로 확인할 수 있습니다. 이를 원격 증명 이라고 합니다. Azure Security Center은 원격 증명의 상태를 나타내는 평가를 발급 합니다.

## <a name="azure-defender-integration"></a>Azure Defender 통합

Vm이 신뢰할 수 있는 시작으로 올바르게 설정 된 경우 Azure Defender는 VM 상태 문제를 감지 하 고 경고할 수 있습니다.

- **Vm 증명 실패에 대 한 경고** -Azure Defender는 주기적으로 vm에 대 한 증명을 수행 합니다. VM이 부팅 된 후에도이 문제가 발생 합니다. 증명에 오류가 발생 하면 보통 심각도 경고가 트리거됩니다.
    VM 증명은 다음과 같은 이유로 실패할 수 있습니다.
    - 부팅 로그를 포함 하는 증명 된 정보는 신뢰할 수 있는 기준선에서 다릅니다. 이는 신뢰할 수 없는 모듈이 로드 되었으며 OS가 손상 되었을 수 있음을 나타낼 수 있습니다.
    - 증명 된 VM의 vTPM에서 가져온 증명 견적을 확인할 수 없습니다. 이는 맬웨어가 있고 vTPM에 대 한 트래픽을 가로챌 수 있음을 나타낼 수 있습니다.
    - VM의 증명 확장이 응답 하지 않습니다. 이는 맬웨어에의 한 서비스 거부 공격 또는 OS 관리자를 나타낼 수 있습니다.

    > [!NOTE]
    >  이 경고는 vTPM을 사용 하도록 설정 하 고 증명 확장이 설치 된 Vm에 사용할 수 있습니다. 증명을 통과 하려면 보안 부팅을 사용 하도록 설정 해야 합니다. 보안 부팅을 사용 하지 않도록 설정 하면 증명에 오류가 발생 합니다. 보안 부팅을 사용 하지 않도록 설정 해야 하는 경우 거짓 긍정을 방지 하기 위해이 경고를 표시 하지 않을 수 있습니다.

- **신뢰할 수 없는 Linux 커널 모듈에 대 한 경고** -보안 부팅이 사용 하도록 설정 된 신뢰할 수 있는 시작의 경우 커널 드라이버가 유효성 검사에 실패 하 고 로드가 금지 된 경우에도 VM이 부팅 될 수 있습니다. 이 문제가 발생 하는 경우 Azure Defender는 낮은 심각도 경고를 발급 합니다. 신뢰할 수 없는 드라이버가 로드 되지 않았기 때문에 즉각적인 위협은 없지만 이러한 이벤트를 조사 해야 합니다. 다음을 살펴보세요.
    - 실패 한 커널 드라이버는 무엇입니까? 이 드라이버에 익숙하고 로드 될 것으로 생각 하나요?
    - 예상 되는 드라이버 버전이 정확한 가요? 드라이버 이진 파일이 그대로 유지 되나요? 타사 드라이버인 경우 공급 업체에서 OS 준수 테스트를 통과 하 여 서명 했습니까?


## <a name="faq"></a>FAQ

신뢰할 수 있는 시작에 대 한 질문과 대답입니다.

### <a name="why-should-i-use-trusted-launch-what-does-trusted-launch-guard-against"></a>트러스트 된 실행을 사용 해야 하는 이유는 무엇 인가요? 트러스트 된 출시는 무엇을 보호 하나요?

신뢰할 수 있는 시작은 부팅 키트, 루트킷 및 커널 수준 맬웨어에 대해 보호 합니다. 이러한 고급 유형의 맬웨어는 커널 모드에서 실행 되 고 사용자에 게는 숨겨진 상태를 유지 합니다. 예를 들면 다음과 같습니다.
- 펌웨어 루트킷: 이러한 키트는 가상 머신의 BIOS의 펌웨어를 덮어쓰므로 OS 이전에 루트킷를 시작할 수 있습니다. 
- 부팅 키트: 이러한 키트는 os의 부팅 로더를 대체 하 여 가상 머신이 OS 이전에 부팅 키트를 로드 하도록 합니다.
- 커널 루트: 이러한 키트는 os가 로드 될 때 루트킷가 자동으로 시작 될 수 있도록 OS 커널의 일부를 대체 합니다.
- 드라이버 루트킷: 이러한 키트는 OS에서 가상 컴퓨터의 구성 요소와 통신 하는 데 사용 하는 신뢰할 수 있는 드라이버 중 하나입니다.

### <a name="what-are-the-differences-between-secure-boot-and-measured-boot"></a>보안 부팅 및 측정 된 부팅 간의 차이점은 무엇 인가요?

보안 부팅 체인에서 부팅 프로세스의 각 단계는 이후 단계의 암호화 서명을 확인 합니다. 예를 들어, BIOS는 로더에서 서명을 확인 하 고 로더는 로드 되는 모든 커널 개체에 대 한 서명을 검사 하는 식입니다. 개체 중 하나라도 손상 되 면 서명이 일치 하지 않고 VM이 부팅 되지 않습니다. 자세한 내용은 [보안 부팅](/windows-hardware/design/device-experiences/oem-secure-boot)을 참조하세요. 계획 부팅은 부팅 프로세스를 중단 하지 않으며 체인에서 다음 개체의 해시를 측정 하거나 계산 하 고 vTPM의 플랫폼 구성 레지스터 (PCRs)에 해시를 저장 합니다. 측정 된 부팅 레코드는 부팅 무결성 모니터링에 사용 됩니다.

### <a name="what-happens-when-an-integrity-fault-is-detected"></a>무결성 오류가 검색 되 면 어떻게 되나요?

Azure 가상 컴퓨터에 대 한 신뢰할 수 있는 시작이 고급 위협에 대해 모니터링 됩니다. 이러한 위협이 감지 되 면 경고가 트리거됩니다. 경고는 Azure Security Center [표준 계층](/azure/security-center/security-center-pricing) 에서만 사용할 수 있습니다.
Azure Security Center 정기적으로 증명을 수행 합니다. 증명에 오류가 발생 하면 보통 심각도 경고가 트리거됩니다. 신뢰할 수 있는 시작 증명은 다음과 같은 이유로 실패할 수 있습니다. 
- TCB (신뢰할 수 있는 컴퓨팅 기반)의 로그가 포함 된 증명 된 정보는 신뢰할 수 있는 기준 (예: 보안 부팅을 사용 하는 경우)에서 다릅니다. 이는 신뢰할 수 없는 모듈이 로드 되었으며 OS가 손상 되었을 수 있음을 나타낼 수 있습니다.
- 증명 된 VM의 vTPM에서 가져온 증명 견적을 확인할 수 없습니다. 이는 맬웨어가 있으며 TPM으로 트래픽을 가로챌 수 있음을 나타낼 수 있습니다. 
- VM의 증명 확장이 응답 하지 않습니다. 이는 맬웨어에의 한 서비스 거부 공격 또는 OS 관리자를 나타낼 수 있습니다.

  
### <a name="how-does-trusted-launch-compared-to-hyper-v-shielded-vm"></a>Hyper-v 보호 된 VM에 대해 신뢰할 수 있는 시작이 어떻게 비교 되나요?
Hyper-v 보호 된 VM은 현재 Hyper-v 에서만 사용할 수 있습니다. [Hyper-v 차폐 VM](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms) 은 일반적으로 보호 된 패브릭과 함께 배포 됩니다. 보호 된 패브릭은 HGS (호스트 보호 서비스), 하나 이상의 보호 된 호스트 및 보호 된 Vm 집합으로 구성 됩니다. Hyper-v 보호 된 Vm은 패브릭 관리자와 Hyper-v 호스트에서 실행 되는 신뢰할 수 없는 소프트웨어에서 가상 컴퓨터의 데이터와 상태를 보호 해야 하는 패브릭에서 사용 하기 위한 것입니다. 반면에 신뢰할 수 있는 시작은 HGS를 추가 배포 하 고 관리 하지 않고도 Azure의 독립 실행형 가상 머신 또는 가상 머신 확장 집합으로 배포할 수 있습니다. 배포 코드를 간단 하 게 변경 하거나 Azure Portal 확인란을 선택 하 여 모든 신뢰할 수 있는 시작 기능을 사용 하도록 설정할 수 있습니다.  

### <a name="how-can-i-convert-existing-vms-to-trusted-launch"></a>기존 Vm을 신뢰할 수 있는 시작으로 변환 하려면 어떻게 해야 하나요?
2 세대 VM의 경우 신뢰할 수 있는 시작으로 변환 하는 마이그레이션 경로는 GA (일반 공급)를 대상으로 합니다.

## <a name="next-steps"></a>다음 단계

[포털을 사용 하 여 신뢰할 수 있는 시작 VM](trusted-launch-portal.md)을 배포 합니다.
