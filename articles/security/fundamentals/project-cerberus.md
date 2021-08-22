---
title: 펌웨어 무결성 - Azure Security
description: 펌웨어 무결성을 보장하기 위한 암호화 측정에 대해 알아봅니다.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/24/2021
ms.openlocfilehash: cda9aacfc3a207aa43c63cc26189da64fac87e85
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112893927"
---
# <a name="project-cerberus"></a>프로젝트 Cerberus

Cerberus는 복제할 수 없는 ID를 사용하는 NIST 800-193 준수 하드웨어 신뢰 루트입니다. Cerberus는 펌웨어 무결성을 위한 강력한 신뢰 앵커를 제공하여 Azure 인프라의 보안 태세를 향상하도록 설계되었습니다.

## <a name="enabling-an-anchor-of-trust"></a>신뢰 앵커 사용
모든 Cerberus 칩에는 Microsoft CA(인증 기관)로 루팅된 서명된 인증서 체인을 사용하여 설정되는 고유한 암호화 ID가 있습니다. Cerberus에서 얻은 측정값은 다음과 같은 구성 요소의 무결성을 확인하는 데 사용할 수 있습니다.

- 호스트
- 베이스 보드 관리 컨트롤러(BMC)
- 네트워크 인터페이스 카드 및 SoC ([단일 칩 시스템](https://en.wikipedia.org/wiki/System_on_a_chip))를 비롯한 모든 주변 장치

신뢰 앵커는 다음 대상로부터 플랫폼 펌웨어를 보호하는 데 유용합니다.

- 플랫폼에서 실행되는 손상된 펌웨어 이진 파일
- 운영 체제, 애플리케이션 또는 하이퍼바이저에서 버그를 악용하는 맬웨어 및 해커
- 특정 유형의 공급망 공격(제조, 어셈블리, 전송)
- 관리자 권한이나 하드웨어 액세스가 있는 악의적인 참가자

## <a name="cerberus-attestation"></a>Cerberus 증명
Cerberus는 PFM(플랫폼 펌웨어 매니페스트)를 사용하여 서버 구성 요소의 펌웨어 무결성을 인증합니다. PFM은 권한 있는 펌웨어 버전의 목록을 정의하고 Azure [호스트 증명 서비스](measured-boot-host-attestation.md)에 플랫폼 측정을 제공합니다. 호스트 증명 서비스는 측정의 유효성을 검사하고 신뢰할 수 있는 호스트만 Azure 플릿에 가입하고 고객 작업을 호스트하게 허용하도록 결정합니다.

Cerberus의 기능은 호스트 증명 서비스와 함께 작용하여 매우 안전한 Azure 프로덕션 인프라를 자동으로 보정하고 향상시킵니다.

> [!NOTE]
> 자세히 알아보려면 GitHub의 [Project Cerberus](https://github.com/opencomputeproject/Project_Olympus/tree/master/Project_Cerberus) 정보를 참조하세요.

## <a name="next-steps"></a>다음 단계
플랫폼 무결성 및 보안을 추진하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [펌웨어 보안](firmware.md)
- [플랫폼 코드 무결성](code-integrity.md)
- [보안 부팅](secure-boot.md)
- [계획 부팅 및 호스트 증명](measured-boot-host-attestation.md)
- [미사용 암호화](encryption-atrest.md)
- [하이퍼바이저 보안](hypervisor.md)