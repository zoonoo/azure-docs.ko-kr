---
title: 펌웨어 보안 부팅-Azure 보안
description: Azure 펌웨어 보안 부팅에 대 한 기술 개요입니다.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: feb28b1d448d0146046ed789d1389a3a42f344de
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557846"
---
# <a name="secure-boot"></a>보안 부팅

보안 부팅은 모든 하위 수준 펌웨어 및 소프트웨어 구성 요소를 로드 하기 전에 확인 해야 하는 UEFI ( [UEFI(Unified Extensible Firmware Interface)](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) )의 기능입니다. 부팅 하는 동안 UEFI 보안 부팅은 UEFI 펌웨어 드라이버 (옵션 Rom이 라고도 함), EFI (Extensible 펌웨어 인터페이스) 응용 프로그램 및 운영 체제 드라이버와 이진 파일을 포함 하 여 부팅 소프트웨어의 각 부분에 대 한 서명을 확인 합니다. 서명이 유효 하거나 OEM (원본 장비 제조업체)에서 신뢰할 수 있는 경우 컴퓨터가 부팅 되 고 펌웨어에서 운영 체제에 대 한 제어를 제공 합니다.

## <a name="components-and-process"></a>구성 요소 및 프로세스

보안 부팅은 다음과 같은 중요 한 구성 요소에 의존 합니다.

- PK (플랫폼 키)-플랫폼 소유자 (Microsoft)와 펌웨어 간의 트러스트를 설정 합니다. 공용 절반은 PKpub이 고 개인 절반은 Pkpub입니다.
- KEK (키 등록 키 데이터베이스)-OS와 플랫폼 펌웨어 간의 트러스트를 설정 합니다. 공개 절반은 KEKpub이 고 개인 절반은 Kekpub입니다.
- 서명 데이터베이스 (db)-플랫폼 펌웨어와 상호 작용할 수 있는 권한 있는 펌웨어 및 소프트웨어 코드 모듈의 신뢰할 수 있는 서명자 (공개 키 및 인증서)에 대 한 다이제스트를 보유 합니다.
- 해지 된 서명 데이터베이스 (.dbx) – 악의적 이거나 취약 하거나 손상 되거나 신뢰할 수 없는 것으로 확인 된 코드 모듈의 해지 된 다이제스트를 보유 합니다. 해시가 서명 db에 있고 해지 된 서명 db에 있으면 해지 된 서명 데이터베이스의 우선 순위가 사용 됩니다.

다음 그림 및 프로세스는 이러한 구성 요소를 업데이트 하는 방법을 설명 합니다.

![보안 부팅 구성 요소를 보여 주는 다이어그램입니다.](./media/secure-boot/secure-boot.png)

OEM은 제조 당시 컴퓨터의 비휘발성 RAM (NV RAM)에 보안 부팅 다이제스트를 저장 합니다.

1. 서명 데이터베이스 (db)는 UEFI 응용 프로그램의 서명자 또는 이미지 해시, 운영 체제 로더 (예: Microsoft 운영 체제 로더 또는 부팅 관리자) 및 신뢰할 수 있는 UEFI 드라이버를 사용 하 여 채워집니다.
2. 해지 된 서명 데이터베이스 (.dbx)는 더 이상 신뢰할 수 없는 모듈의 다이제스트로 채워집니다.
3. KEK (키 등록 키) 데이터베이스는 서명 데이터베이스를 업데이트 하 고 서명 데이터베이스를 해지 하는 데 사용할 수 있는 서명 키로 채워집니다. 올바른 키로 서명 된 업데이트 또는 펌웨어 메뉴를 사용 하 여 실제로 표시 된 권한 있는 사용자가 업데이트를 통해 데이터베이스를 편집할 수 있습니다.
4. Db, .dbx 및 KEK 데이터베이스를 추가 하 고 최종 펌웨어 유효성 검사 및 테스트가 완료 되 면 OEM은 편집에서 펌웨어를 잠그고 플랫폼 키 (PK)를 생성 합니다. PK를 사용 하 여 KEK에 대 한 업데이트에 서명 하거나 보안 부팅을 해제할 수 있습니다.

부팅 프로세스의 각 단계에서 펌웨어, 부팅 로더, 운영 체제, 커널 드라이버 및 기타 부팅 체인 아티팩트의 다이제스트를 계산 하 고 허용 되는 값과 비교 합니다. 신뢰할 수 있는 것으로 검색 된 펌웨어 및 소프트웨어는 로드할 수 없습니다. 따라서 낮은 수준의 맬웨어 삽입 또는 부팅 전 맬웨어 공격을 차단할 수 있습니다.

## <a name="secure-boot-on-the-azure-fleet"></a>Azure 제에서의 보안 부팅
오늘날 고객 작업을 호스트 하기 위해 등록 및 Azure compute 분야에 배포 되는 모든 컴퓨터는 보안 부팅이 사용 하도록 설정 된 공장 바닥에서 제공 됩니다. 대상 도구와 프로세스는 하드웨어 buildout 및 통합 파이프라인의 모든 단계에서 발생 합니다 .이를 통해 보안 부팅을 사용할 수 있는지 여부는 실수나 악의적인 의도를 통해 되돌리지 않습니다.

Db 및 .dbx 다이제스트가 올바른지 확인 하는 것은 다음을 보장 합니다.

- Db 항목 중 하나에 부팅 로더 있음
- 부팅 로더 시그니처가 올바릅니다.
- 신뢰할 수 있는 소프트웨어를 사용 하 여 호스트 부팅

 KEKpub와 PKpub의 서명의 유효성을 검사 하 여 신뢰할 수 있는 당사자만 신뢰할 수 있는 것으로 간주 되는 소프트웨어의 정의를 수정할 수 있는 권한이 있는지 확인할 수 있습니다. 마지막으로, 보안 부팅이 활성 상태 인지 확인 하 여 이러한 정의가 적용 되는지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
플랫폼 무결성 및 보안을 추진 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

- [펌웨어 보안](firmware.md)
- [측정 된 부팅 및 호스트 증명](measured-boot-host-attestation.md)
- [프로젝트 Cerberus](project-cerberus.md)
- [미사용 암호화](encryption-atrest.md)
- [하이퍼바이저 보안](hypervisor.md)