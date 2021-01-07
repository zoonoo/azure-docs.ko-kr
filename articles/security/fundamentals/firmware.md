---
title: 펌웨어 보안-Azure 보안
description: Microsoft에서 Azure 하드웨어 및 펌웨어를 보호 하는 방법을 알아봅니다.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 8233cc714d977083f4d55716d35c0b7094a069ea
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557958"
---
# <a name="firmware-security"></a>펌웨어 보안
이 문서에서는 Microsoft가 클라우드 하드웨어 에코 시스템 및 공급망을 보호 하는 방법을 설명 합니다.

## <a name="securing-the-cloud-hardware-ecosystem"></a>클라우드 하드웨어 에코 시스템 보안
Microsoft는 클라우드 하드웨어 에코 시스템에 적극적으로 협력 하 여 다음과 같은 방식으로 지속적인 보안 기능을 추진 합니다.

- Azure 하드웨어 및 펌웨어 보안 요구 사항을 충족 하기 위해 Azure 하드웨어 및 펌웨어 파트너 (예: 구성 요소 제조업체 및 시스템 통합 업체)와 협력 합니다.

- 파트너는 다음과 같은 영역에서 Microsoft 정의 요구 사항을 사용 하 여 제품의 보안 상태를 지속적으로 평가 하 고 향상 시킬 수 있습니다.

  - 펌웨어 보안 부팅
  - 펌웨어 보안 복구
  - 펌웨어 보안 업데이트
  - 펌웨어 암호화
  - 잠긴 하드웨어
  - 세부적인 디버그 원격 분석
  - 측정 된 부팅을 사용 하도록 설정 하는 TPM 2.0 하드웨어에 대 한 시스템 지원

- 사양 개발을 통해 [OCP (Open Compute project](https://www.opencompute.org/wiki/Security) ) 보안 프로젝트에 참여 하 고 참여 합니다. 사양은 에코 시스템의 보안 설계 및 아키텍처에 대 한 일관성 및 명확성을 승격 합니다.

   > [!NOTE]
   > OCP 보안 프로젝트에 기여 하는 예는 [하드웨어 보안 부팅](https://docs.google.com/document/d/1Se1Dd-raIZhl_xV3MnECeuu_I0nF-keg4kqXyK4k4Wc/edit#heading=h.5z2d7x9gbhk0) 사양입니다.

## <a name="securing-hardware-and-firmware-supply-chains"></a>하드웨어 및 펌웨어 공급 체인 보안
Azure 용 클라우드 하드웨어 공급 업체 및 공급 업체도 Microsoft에서 개발한 공급망 보안 프로세스 및 요구 사항을 준수 하는 데 필요 합니다. 하드웨어 및 펌웨어 개발 및 배포 프로세스는 다음과 같은 Microsoft SDL ( [보안 개발 수명 주기](https://www.microsoft.com/securityengineering/sdl) ) 프로세스를 따르는 데 필요 합니다.

- 위협 모델링
- 안전한 디자인 검토
- 펌웨어 검토 및 침투 테스트
- 보안 빌드 및 테스트 환경
- 보안 취약성 관리 및 인시던트 대응

## <a name="next-steps"></a>다음 단계
플랫폼 무결성 및 보안을 추진 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

- [보안 부팅](secure-boot.md)
- [측정 된 부팅 및 호스트 증명](measured-boot-host-attestation.md)
- [프로젝트 Cerberus](project-cerberus.md)
- [미사용 암호화](encryption-atrest.md)
- [하이퍼바이저 보안](hypervisor.md)