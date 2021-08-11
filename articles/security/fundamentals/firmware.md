---
title: 펌웨어 보안 - Azure Security
description: Microsoft에서 Azure 하드웨어 및 펌웨어를 보호하는 방법을 알아봅니다.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 8233cc714d977083f4d55716d35c0b7094a069ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94557958"
---
# <a name="firmware-security"></a>펌웨어 보안
이 문서에서는 Microsoft가 클라우드 하드웨어 에코시스템 및 공급망을 보호하는 방법을 설명합니다.

## <a name="securing-the-cloud-hardware-ecosystem"></a>클라우드 하드웨어 에코시스템 보안
Microsoft는 클라우드 하드웨어 에코시스템 내에서 적극적인 파트너가 되어 다음을 통해 지속적인 보안 향상을 추진합니다.

- Azure 하드웨어 및 펌웨어 보안 요구 사항을 충족하기 위한 Azure 하드웨어 및 펌웨어 파트너(구성 요소 제조업체 및 시스템 통합자 등)와의 협업

- 파트너가 다음과 같은 영역에서 Microsoft 정의 요구 사항으로 제품의 보안 태세를 지속적으로 평가 및 개선할 수 있도록 합니다.

  - 펌웨어 보안 부팅
  - 펌웨어 보안 복구
  - 펌웨어 보안 업데이트
  - 펌웨어 암호화
  - 잠긴 하드웨어
  - 세부적인 디버그 원격 분석
  - 계획 부팅을 사용하도록 설정하는 TPM 2.0 하드웨어에 대한 시스템 지원

- 사양 개발을 통해 [OCP(Open Compute Project)](https://www.opencompute.org/wiki/Security) 보안 프로젝트에 참여하고 기여합니다. 사양은 에코시스템의 보안 디자인 및 아키텍처에 대한 일관성과 명확성을 높입니다.

   > [!NOTE]
   > OCP 보안 프로젝트에 기여하는 예제는 [하드웨어 보안 부팅](https://docs.google.com/document/d/1Se1Dd-raIZhl_xV3MnECeuu_I0nF-keg4kqXyK4k4Wc/edit#heading=h.5z2d7x9gbhk0) 사양입니다.

## <a name="securing-hardware-and-firmware-supply-chains"></a>하드웨어 및 펌웨어 공급망 보안
Azure용 클라우드 하드웨어 공급업체 및 판매업체도 Microsoft에서 개발한 공급망 보안 프로세스 및 요구 사항을 준수해야 합니다. 하드웨어 및 펌웨어 개발과 배포 프로세스는 다음과 같은 Microsoft [SDL(보안 개발 수명 주기)](https://www.microsoft.com/securityengineering/sdl) 프로세스를 따라야 합니다.

- 위협 모델링
- 보안 디자인 검토
- 펌웨어 검토 및 침투 테스트
- 보안 빌드 및 테스트 환경
- 보안 취약성 관리 및 인시던트 대응

## <a name="next-steps"></a>다음 단계
플랫폼 무결성 및 보안을 추진하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [보안 부팅](secure-boot.md)
- [계획 부팅 및 호스트 증명](measured-boot-host-attestation.md)
- [프로젝트 Cerberus](project-cerberus.md)
- [미사용 암호화](encryption-atrest.md)
- [하이퍼바이저 보안](hypervisor.md)