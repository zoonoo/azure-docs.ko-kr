---
title: 펌웨어 무결성-Azure 보안
description: 펌웨어 무결성을 보장 하기 위한 암호화 측정에 대해 알아봅니다.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f085858a9d550623704efd4f051ed525e55a37e0
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557855"
---
# <a name="project-cerberus"></a>프로젝트 Cerberus

Cerberus는 복제할 수 없는 id를 사용 하는 NIST 800-193 규격 하드웨어 신뢰 수준입니다. Cerberus는 펌웨어 무결성에 대 한 강력한 트러스트 앵커를 제공 하 여 Azure 인프라의 보안 상태를 추가로 발생 시 키도 록 설계 되었습니다.

## <a name="enabling-an-anchor-of-trust"></a>트러스트 앵커 사용
모든 Cerberus 칩에는 Microsoft CA (인증 기관)를 기반으로 하는 서명 된 인증서 체인을 사용 하 여 설정 되는 고유한 암호화 id가 있습니다. Cerberus에서 얻은 측정값은 다음과 같은 구성 요소의 무결성을 확인 하는 데 사용할 수 있습니다.

- 호스트
- 베이스 보드 관리 컨트롤러(BMC)
- 네트워크 인터페이스 카드 및 SoC ( [시스템-칩](https://en.wikipedia.org/wiki/System_on_a_chip) )를 비롯 한 모든 주변 장치

트러스트의이 앵커는 플랫폼 펌웨어를 보호 하는 데 도움이 됩니다.

- 플랫폼에서 실행 되는 손상 된 펌웨어 이진 파일
- 운영 체제, 응용 프로그램 또는 하이퍼바이저에서 버그를 악용 하는 맬웨어 및 해커
- 특정 유형의 공급망 공격 (제조, 어셈블리, 전송)
- 관리 권한이 있는 악의적인 참가자 또는 하드웨어 액세스

## <a name="cerberus-attestation"></a>Cerberus 증명
Cerberus는 플랫폼 펌웨어 매니페스트 (PFM)를 사용 하 여 서버 구성 요소의 펌웨어 무결성을 인증 합니다. PFM은 권한 있는 펌웨어 버전의 목록을 정의 하 고 Azure [호스트 증명 서비스](measured-boot-host-attestation.md)에 플랫폼 측정을 제공 합니다. 호스트 증명 서비스는 측정의 유효성을 검사 하 고 신뢰할 수 있는 호스트만 Azure 분야에 가입 하 고 고객 작업을 호스트 하도록 허용 하도록 결정 합니다.

호스트 증명 서비스와 함께 Cerberus ' 기능은 매우 안전한 Azure 프로덕션 인프라를 강화 하 고 승격 시킵니다.

> [!NOTE]
> 자세히 알아보려면 GitHub의 [Project Cerberus](https://github.com/opencomputeproject/Project_Olympus/tree/master/Project_Cerberus) 정보를 참조 하세요.

## <a name="next-steps"></a>다음 단계
플랫폼 무결성 및 보안을 추진 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

- [펌웨어 보안](firmware.md)
- [보안 부팅](secure-boot.md)
- [측정 된 부팅 및 호스트 증명](measured-boot-host-attestation.md)
- [미사용 암호화](encryption-atrest.md)
- [하이퍼바이저 보안](hypervisor.md)