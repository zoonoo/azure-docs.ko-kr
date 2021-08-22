---
title: 펌웨어 보안 부팅 - Azure Security
description: Azure 펌웨어 보안 부팅에 대한 기술 개요입니다.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/24/2021
ms.openlocfilehash: e14bd525f2f81cfdf0a61f29df919f5579f0be17
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112893945"
---
# <a name="secure-boot"></a>보안 부팅

보안 부팅은 로드하기 전에 모든 낮은 수준의 펌웨어 및 소프트웨어 구성 요소를 확인해야 하는 [UEFI(Unified Extensible Firmware Interface)](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface)의 기능입니다. 부팅하는 동안 UEFI 보안 부팅은 UEFI 펌웨어 드라이버(옵션 ROM이라고도 함), EFI(Extensible Firmware Interface) 애플리케이션, 운영 체제 드라이버 및 이진 등 부팅 소프트웨어의 각 부분의 서명을 확인합니다. 서명이 OEM(주문자 상표 부착 방식)에서 유효하거나 신뢰할 수 있는 경우 컴퓨터가 부팅되고 펌웨어가 운영 체제에 제어 권한을 부여합니다.

## <a name="components-and-process"></a>구성 요소 및 프로세스

보안 부팅은 다음과 같은 중요 구성 요소를 기반으로 합니다.

- PK(플랫폼 키) - 플랫폼 소유자(Microsoft)와 펌웨어 간의 신뢰를 설정합니다. 공용 절반은 PKpub이고 프라이빗 절반은 PKpriv입니다.
- KEK(키 등록 키) 데이터베이스 - OS와 플랫폼 펌웨어 간의 신뢰를 설정합니다. 공용 절반은 KEKpub이고 프라이빗 절반은 KEKpriv입니다.
- 서명 데이터베이스(db) - 플랫폼 펌웨어와 상호 작용할 권한이 있는 펌웨어 및 소프트웨어 코드 모듈의 신뢰할 수 있는 서명자(공개 키 및 인증서)에 대한 다이제스트를 보유합니다.
- 해지된 서명 데이터베이스(dbx) – 악의적이거나, 취약하거나, 손상되거나, 신뢰할 수 없는 것으로 확인된 코드 모듈의 해지된 다이제스트를 보유합니다. 해시가 서명 db 및 해지된 서명 db에 있는 경우 해지된 서명 데이터베이스가 가장 중요합니다.

다음 그림 및 프로세스에서는 이러한 구성 요소를 업데이트하는 방법을 설명합니다.

![보안 부팅 구성 요소를 보여주는 다이어그램](./media/secure-boot/secure-boot.png)

OEM은 제조 시 컴퓨터의 비발전 RAM(NV-RAM)에 보안 부팅 다이제스트를 저장합니다.

1. 서명 데이터베이스(db)는 UEFI 애플리케이션, 운영 체제 로더(Microsoft 운영 체제 로더 또는 부팅 관리자 등) 및 신뢰할 수 있는 UEFI 드라이버의 서명자 또는 이미지 해시로 채워집니다.
2. 해지된 서명 데이터베이스(.dbx)는 더 이상 신뢰할 수 없는 모듈의 다이제스트로 채워집니다.
3. KEK(키 등록 키) 데이터베이스는 서명 데이터베이스를 업데이트하 고 서명 데이터베이스를 해지하는 데 사용할 수 있는 서명 키로 채워집니다. 올바른 키로 서명된 업데이트 또는 펌웨어 메뉴를 사용하여 실제로 존재하는 권한 있는 사용자가 업데이트를 통해 데이터베이스를 편집할 수 있습니다.
4. db, .dbx 및 KEK 데이터베이스를 추가하고 최종 펌웨어 유효성 검사 및 테스트가 완료되면 OEM은 편집에서 펌웨어를 잠그고 플랫폼 키(PK)를 생성합니다. PK를 사용하여 KEK에 대한 업데이트에 서명하거나 보안 부팅을 비활성화할 수 있습니다.

부팅 프로세스의 각 단계에서 펌웨어, 부팅 로더, 운영 체제, 커널 드라이버 및 기타 부팅 체인 아티팩트의 다이제스트가 계산되고 허용되는 값과 비교됩니다. 신뢰할 수 없는 것으로 검색된 펌웨어 및 소프트웨어는 로드할 수 없습니다. 따라서 낮은 수준의 맬웨어 주입 또는 사전 부팅 맬웨어 공격을 차단할 수 있습니다.

## <a name="secure-boot-on-the-azure-fleet"></a>Azure 플릿에서 보안 부팅
오늘날 고객 워크로드를 호스트하기 위해 Azure Compute 플릿에 온보딩되고 배포되는 모든 컴퓨터는 보안 부팅을 사용하도록 설정된 센터에서 제공됩니다. 대상 도구와 프로세스는 하드웨어 구축 및 통합 파이프라인의 모든 단계에서 발생하며,이를 통해 보안 부팅의 사용이 사고나 악의적인 의도를 통해 되돌려지지 않도록 합니다.

db 및 .dbx 다이제스트의 유효성을 확인하는 것은 다음을 보장 합니다.

- db 항목 중 하나에 부팅 로더가 있습니다
- 부팅 로더의 서명이 유효합니다
- 신뢰할 수 있는 소프트웨어를 사용하여 호스트 부팅

 KEKpub와 PKpub의 서명의 유효성을 검사하여 신뢰할 수 있는 것으로 간주되는 소프트웨어의 정의를 수정할 수 있는 사용 권한이 신뢰할 수 있는 당사자에게만 있는지 확인할 수 있습니다. 마지막으로, 보안 부팅이 활성 상태인지 확인하여 이러한 정의가 적용되는지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
플랫폼 무결성 및 보안을 추진하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [펌웨어 보안](firmware.md)
- [플랫폼 코드 무결성](code-integrity.md)
- [계획 부팅 및 호스트 증명](measured-boot-host-attestation.md)
- [프로젝트 Cerberus](project-cerberus.md)
- [미사용 암호화](encryption-atrest.md)
- [하이퍼바이저 보안](hypervisor.md)