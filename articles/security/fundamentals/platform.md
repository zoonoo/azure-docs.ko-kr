---
title: Azure 플랫폼 무결성 및 보안 - Azure 보안
description: Azure 플랫폼 무결성 및 보안에 대 한 기술 개요입니다.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 4755bc19a645d196487f0b8e0f4d1ef2120723ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94557982"
---
# <a name="platform-integrity-and-security-overview"></a>플랫폼 무결성 및 보안 개요
Azure 플릿은 매일 추가되는 수천 개의 서버(호스트)로 구성됩니다. 수천 개의 호스트 또한 재부팅, 운영 체제 새로 고침 또는 복구를 통해 매일 유지 관리를 수행 합니다. Microsoft는 호스트가 플릿에 참여하고 고객 워크로드 수락을 시작하기 전에, 호스트가 안전하고 신뢰할 수 있는 상태인지 확인합니다. 이 확인을 통해 공급망 또는 유지 관리 워크플로 중에 부팅 시퀀스 구성 요소에서 악의적이거나 부주의한 변경이 발생하지 않았는지 확인할 수 있습니다.

## <a name="securing-azure-hardware-and-firmware"></a>Azure 하드웨어 및 펌웨어 보안
이 문서 시리즈에서는 Microsoft가 제조에서 일몰까지 수명 주기의 다양한 단계를 통해 호스트의 무결성 및 보안을 보장하는 방법을 설명합니다. 아티클을 다음을 확인합니다.
 
- [펌웨어 보안](firmware.md)
- [UEFI 보안 부팅](secure-boot.md)
- [계획 부팅 및 호스트 증명](measured-boot-host-attestation.md)
- [프로젝트 Cerberus](project-cerberus.md)
- [미사용 암호화](encryption-atrest.md)
- [하이퍼바이저 보안](hypervisor.md)
 
## <a name="next-steps"></a>다음 단계

- Microsoft가 클라우드 하드웨어 에코시스템 내에서 적극적으로 협력하여 지속적인 [펌웨어 보안 개선 사항](firmware.md)을 추진하는 방법을 알아보세요.

- [클라우드에서의 공유 책임](shared-responsibility.md)을 이해합니다.