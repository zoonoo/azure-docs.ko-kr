---
title: Azure 선단의 하이퍼바이저 보안 -Azure 보안
description: Azure 선단의 하이퍼바이저 보안 기술 개요.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/24/2021
ms.openlocfilehash: e8da9ae3d8f8c13bf52c5c0a2ea61b38f316a8b2
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895637"
---
# <a name="hypervisor-security-on-the-azure-fleet"></a>Azure 선단의 하이퍼바이저 보안

Azure 하이퍼바이저 시스템은 Windows Hyper-V를 기반으로 합니다. 하이퍼바이저 시스템을 사용하면 컴퓨터 관리자가 별도의 주소 공간을 사용하는 게스트 파티션을 지정할 수 있습니다. 별도의 주소 공간을 사용하면 컴퓨터의 루트 파티션에서 실행되는 (호스트) 운영 체제와 병렬로 작동하는 운영 체제 및 애플리케이션을 로드할 수 있습니다. 호스트 OS(권한 있는 루트 파티션)는 시스템의 모든 물리적 디바이스 및 주변 장치(저장소 컨트롤러, 네트워킹 적응)에 직접 액세스할 수 있습니다. 호스트 OS를 사용하면 게스트 파티션이 각 게스트 파티션에 "가상 디바이스"를 노출하여 이러한 물리적 디바이스의 사용을 공유할 수 있습니다. 따라서 게스트 파티션에서 실행 중인 운영 체제는 루트 파티션에서 실행되는 가상화 서비스에서 제공하는 가상화된 주변 장치에 액세스할 수 있습니다.

Azure 하이퍼바이저는 다음과 같은 보안 목표를 염두에 두기 위해 빌드되었습니다.

| Objective | 원본 |
|--|--|
| 격리 | 보안 정책에서는 VM 간에 정보 전송을 하지 않습니다. 이 제약 조건에는 메모리, 디바이스, 네트워크 및 관리되는 리소스(예: 지속형 데이터)의 격리를 위해 VMM(Virtual Machine Manager) 및 하드웨어의 기능이 필요합니다. |
| VMM 무결성 | 전반적인 시스템 무결성을 위해 개별 하이퍼바이저 구성 요소의 무결성을 설정하고 유지합니다. |
| 플랫폼 무결성 | 하이퍼바이저의 무결성은 기반이 되는 하드웨어 및 소프트웨어의 무결성에 따라 달라집니다. 하이퍼바이저는 플랫폼의 무결성을 직접 제어하지는 않지만 Azure는 기본 플랫폼 무결성을 보호 및 검색하는 [Cerberus](project-cerberus.md) 칩과 같은 하드웨어 및 펌웨어 메커니즘을 사용합니다. 플랫폼 무결성이 손상되면 VMM 및 게스트를 실행할 수 없습니다. |
| 제한된 액세스 | 관리 기능은 보안 연결을 통해 연결된 권한 있는 관리자만 수행합니다. 최소 권한의 원칙은 Azure RBAC(역할 기반 액세스 제어) 메커니즘에 의해 적용됩니다. |
| 감사 | Azure를 사용하면 감사 기능이 시스템에서 발생하는 상황에 대한 데이터를 캡처하고 보호하여 나중에 검사할 수 있습니다. |

Azure 하이퍼바이저 및 가상화 하위 시스템을 강화하는 Microsoft의 접근 방식은 다음과 같은 세 가지 범주로 나눌 수 있습니다.

## <a name="strongly-defined-security-boundaries-enforced-by-the-hypervisor"></a>하이퍼바이저에서 적용하는 강력하게 정의된 보안 경계

Azure 하이퍼바이저는 다음 사이에 여러 보안 경계를 적용합니다.

- 가상화된 "게스트" 파티션 및 권한 있는 파티션("호스트")
- 여러 게스트
- 자신 및 호스트
- 자신 및 모든 게스트

하이퍼바이저 보안 경계는 기밀성, 무결성 및 가용성을 보장합니다. 경계는 측면 채널 정보 유출, 서비스 거부 및 권한 상승을 비롯한 다양한 공격으로부터 보호합니다.

또한 하이퍼바이저 보안 경계는 네트워크 트래픽, 가상 디바이스, 스토리지, 계산 리소스 및 기타 모든 VM 리소스에 대한 테넌트 간의 조각화를 제공합니다.

## <a name="defense-in-depth-exploit-mitigations"></a>심층 방어를 통한 악용 완화

드물지만 보안 경계에 취약성이 있는 경우 Azure 하이퍼바이저에는 다음과 같은 여러 완화 계층이 포함됩니다.

- VM 간 구성 요소를 호스팅하는 호스트 기반 프로세스 격리
- 보안 세계에서 사용자 및 커널 모드 구성 요소의 무결성을 보장하기 위한 VBS(가상화 기반 보안)
- 여러 수준의 악용 완화. 완화에는 ASLR(Address Space Layout Randomization), DEP(데이터 실행 방지), 임의 코드 가드, 제어 흐름 무결성 및 데이터 손상 방지가 포함됩니다.
- 컴파일러 수준에서 스택 변수 자동 초기화
- Hyper-V에서 생성된 커널 힙 할당을 자동으로 제로 초기화하는 커널 API

이러한 완화는 VM 간 취약성에 대한 악용 개발을 실행할 수 없도록 고안되었습니다.

## <a name="strong-security-assurance-processes"></a>강력한 보안 보증 프로세스

하이퍼바이저와 관련된 공격에 대한 취약성에는 소프트웨어 네트워킹, 가상 디바이스 및 모든 VM 간 취약성이 포함됩니다. 공격에 대한 취약성은 정기적인 보안 검토를 트리거하는 자동화된 빌드 통합을 통해 추적됩니다.

모든 VM 공격에 대한 취약성은 보안 경계 위반에 대해 RED 팀을 통해 위협 모델링, 코드 검토, 퍼즈 테스트 및 테스트를 거쳤습니다. Microsoft에는 Microsoft Hyper-V에 적합한 제품 버전에서 관련 취약성 보고에 대한 상을 수여하는 [버그 장려금 프로그램](https://www.microsoft.com/msrc/bounty-hyper-v)이 있습니다.

> [!NOTE]
> Hyper-V에서 [강력한 보안 보증 프로세스](../../azure-government/azure-secure-isolation-guidance.md#strong-security-assurance-processes)에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계
플랫폼 무결성 및 보안을 추진하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [펌웨어 보안](firmware.md)
- [플랫폼 코드 무결성](code-integrity.md)
- [보안 부팅](secure-boot.md)
- [계획 부팅 및 호스트 증명](measured-boot-host-attestation.md)
- [프로젝트 Cerberus](project-cerberus.md)
- [휴지 상태의 암호화](encryption-atrest.md)