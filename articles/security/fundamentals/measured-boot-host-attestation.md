---
title: 펌웨어 측정 부팅 및 호스트 증명-Azure 보안
description: Azure 펌웨어가 측정 된 부팅 및 호스트 증명에 대 한 기술 개요입니다.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 73ae811c17a578cafc557b0cda9e98b101dd5c03
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557822"
---
# <a name="measured-boot-and-host-attestation"></a>측정 된 부팅 및 호스트 증명
이 문서에서는 Microsoft가 측정 된 부팅 및 호스트 증명을 통해 호스트의 무결성 및 보안을 보장 하는 방법을 설명 합니다.

## <a name="measured-boot"></a>측정 된 부팅

TPM ( [신뢰할 수 있는 플랫폼 모듈](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-top-node) )은 신뢰할 수 있는 타사에서 제공 하는 펌웨어가 포함 된 변조 방지 증명 보안 감사 구성 요소입니다. 부팅 구성 로그에는 호스트가 부트스트랩 시퀀스를 마지막으로 클러스터가 거쳤다 때 PCR (플랫폼 구성 레지스터)에 기록 된 해시 체인 측정이 포함 됩니다. 다음 그림에서는이 기록 프로세스를 보여 줍니다. 이전에 해시 된 측정값을 다음 측정의 해시에 증분 방식으로 추가 하 고 공용 구조체에서 해싱 알고리즘을 실행 하면 해시 체인이 수행 됩니다.

![호스트 증명 서비스 해시 체인을 보여 주는 다이어그램입니다.](./media/measured-boot-host-attestation/hash-chaining.png)

TCGLog (부트 구성 로그)를 사용 하 여 호스트가 해당 구성 상태 증명을 furnishes 증명을 수행 합니다. TPM이 읽기 및 확장 작업 이외의 PCR 값을 노출 하지 않기 때문에 부팅 로그 위조는 어렵습니다. 또한 호스트 증명 서비스에서 제공 하는 자격 증명은 특정 PCR 값으로 봉인 됩니다. 해시 체인을 사용 하면 자격 증명을 대역 외로 스푸핑 하거나 봉인 수 있습니다.

## <a name="host-attestation-service"></a>호스트 증명 서비스

호스트 증명 서비스는 호스트 컴퓨터가 고객 데이터 또는 작업과 상호 작용 하도록 허용 되기 전에 신뢰할 수 있는지 확인 하는 예방 조치입니다. 증명 서비스는 증명 정책 (보안 상태 정의)에 대해 각 호스트에서 보낸 규정 준수 문 (호스트의 호환성 증명 확인 증명)의 유효성을 검사 하 여 확인 합니다. 이 시스템의 무결성은 TPM에서 제공 하는 신뢰할 수 있는 [루트](https://www.uefi.org/sites/default/files/resources/UEFI%20RoT%20white%20paper_Final%208%208%2016%20%28003%29.pdf) 에 의해 보장 됩니다.

호스트 증명 서비스는 특수 한 잠금 환경 내에 있는 각 Azure 클러스터에 있습니다. 잠긴 환경에는 호스트 컴퓨터 부트스트래핑 프로토콜에 참여 하는 다른 게이트 키퍼 서비스가 포함 됩니다. PKI (공개 키 인프라)는 증명 요청의 provenance 유효성을 검사 하기 위한 중개자 역할을 하며, 성공 호스트 증명에 따라 결정 됩니다. 증명 호스트에 발급 된 증명 후 자격 증명은 해당 id로 봉인 됩니다. 요청 하는 호스트만 자격 증명을 봉인 하 고이를 활용 하 여 증분 권한을 얻을 수 있습니다. 이렇게 하면 메시지 가로채기 (man-in-the-middle) 공격을 방지할 수 있습니다.

보안 구성이 잘못 된 공장에서 Azure 호스트를 수신 하거나 데이터 센터에서 변조 된 경우 해당 TCGLog에는 다음 증명에 대 한 호스트 증명 서비스에 의해 플래그가 지정 된 손상 표시기가 포함 되어 증명 오류가 발생 합니다. 증명 오류로 인해 Azure가 잘못 된 호스트를 신뢰 하지 못합니다. 이 방지 기능을 통해 호스트와의 모든 통신을 효과적으로 차단 하 고 인시던트 워크플로를 트리거할 수 있습니다. 근본 원인과 잠재적 손상 가능성을 파악 하기 위해 조사 및 자세한 사후 분석을 수행 합니다. 분석이 완료 되 고 호스트가 재구성 된 후에만 Azure 분야를 참여 시키고 고객 작업을 수행할 수 있는 기회가 있습니다.

다음은 호스트 증명 서비스의 상위 수준 아키텍처입니다.

![호스트 증명 서비스 아키텍처를 보여 주는 다이어그램입니다.](./media/measured-boot-host-attestation/host-attestation-arch.png)

## <a name="attestation-measurements"></a>증명 측정

현재 캡처된 많은 측정값의 예는 다음과 같습니다.

### <a name="secure-boot-and-secure-boot-keys"></a>보안 부팅 및 보안 부팅 키
호스트 증명 서비스는 서명 데이터베이스 및 해지 된 서명 데이터베이스 다이제스트가 올바른지 유효성을 검사 하 여 클라이언트 에이전트가 적절 한 소프트웨어를 신뢰할 수 있다고 간주 합니다. 호스트 증명 서비스는 공개 키 등록 키 데이터베이스 및 공용 플랫폼 키의 서명의 유효성을 검사 하 여 신뢰할 수 있는 당사자만 신뢰할 수 있는 것으로 간주 되는 소프트웨어의 정의를 수정할 수 있음을 확인 합니다. 마지막으로, 보안 부팅이 활성 상태 인지 확인 하 여 호스트 증명 서비스는 이러한 정의가 적용 되 고 있는지 확인 합니다.

### <a name="debug-controls"></a>디버그 컨트롤
디버거는 개발자를 위한 강력한 도구입니다. 그러나 메모리 및 기타 디버그 명령에 대 한 무제한적인 액세스는 신뢰할 수 없는 파티에 지정 된 경우 데이터 보호 및 시스템 무결성을 약화 시킬 수 있습니다. 호스트 증명 서비스를 사용 하면 프로덕션 컴퓨터에서 부팅할 때 모든 종류의 디버깅이 비활성화 됩니다.

### <a name="code-integrity"></a>코드 무결성
UEFI [보안 부팅](secure-boot.md) 을 사용 하면 신뢰할 수 있는 하위 수준 소프트웨어만 부팅 시퀀스 중에 실행할 수 있습니다. 그러나 동일한 검사를 부팅 후 환경에서 커널 모드 액세스를 사용 하는 드라이버 및 기타 실행 파일에도 적용 해야 합니다. 이를 위해 CI (코드 무결성) 정책을 사용 하 여 유효 하 고 유효 하지 않은 서명을 지정 하 여 신뢰할 수 있는 것으로 간주 되는 드라이버, 이진 파일 및 기타 실행 파일을 정의 합니다. 이러한 정책이 적용 됩니다. 정책 위반은 조사를 위해 보안 인시던트 응답 팀에 경고를 생성 합니다.

## <a name="next-steps"></a>다음 단계
플랫폼 무결성 및 보안을 추진 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

- [펌웨어 보안](firmware.md)
- [보안 부팅](secure-boot.md)
- [프로젝트 Cerberus](project-cerberus.md)
- [미사용 암호화](encryption-atrest.md)
- [하이퍼바이저 보안](hypervisor.md)