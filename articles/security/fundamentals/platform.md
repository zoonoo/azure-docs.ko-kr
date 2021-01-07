---
title: Azure 플랫폼 무결성 및 보안-Azure 보안
description: Azure 플랫폼 무결성 및 보안에 대 한 기술 개요입니다.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 4755bc19a645d196487f0b8e0f4d1ef2120723ca
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557982"
---
# <a name="platform-integrity-and-security-overview"></a>플랫폼 무결성 및 보안 개요
Azure는 매일 추가 된 수천 개의 서버 (호스트)로 구성 됩니다. 또한 다시 부팅, 운영 체제 새로 고침 또는 복구를 통해 매일 유지 관리를 수행 합니다. 호스트가 제 수를 참여 시키고 고객 작업을 수락 하기 전에 Microsoft는 호스트가 안전 하 고 신뢰할 수 있는 상태 인지 확인 합니다. 이 확인은 공급망 또는 유지 관리 워크플로 중에 부팅 시퀀스 구성 요소에서 악의적 이거나 부주의 한 변경이 발생 하지 않았는지 확인 합니다.

## <a name="securing-azure-hardware-and-firmware"></a>Azure 하드웨어 및 펌웨어 보안
이 문서 시리즈에서는 Microsoft가 제조에서 일몰까지 수명 주기의 다양 한 단계를 통해 호스트의 무결성 및 보안을 보장 하는 방법을 설명 합니다. 아티클을 다음을 확인합니다.
 
- [펌웨어 보안](firmware.md)
- [UEFI 보안 부팅](secure-boot.md)
- [측정 된 부팅 및 호스트 증명](measured-boot-host-attestation.md)
- [프로젝트 Cerberus](project-cerberus.md)
- [미사용 암호화](encryption-atrest.md)
- [하이퍼바이저 보안](hypervisor.md)
 
## <a name="next-steps"></a>다음 단계

- Microsoft가 클라우드 하드웨어 에코 시스템 내에서 적극적으로 협력 하 여 지속적인 [펌웨어 보안 개선 사항을](firmware.md)추진 하는 방법을 알아보세요.

- [클라우드의 공유 책임](shared-responsibility.md)을 이해 합니다.