---
title: Windows Virtual Desktop MSIX 앱 연결 - Azure
description: MSIX 앱 연결 용어 및 개념의 용어집입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: c5c596735ad91f38d5ba4217135a9373d2856182
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538460"
---
# <a name="msix-app-attach-glossary"></a>MSIX 앱 연결 용어집

이 문서는 MSIX 앱 연결과 관련된 주요 용어 및 개념에 관한 정의 목록입니다.

## <a name="msix-container"></a>MSIX 컨테이너

MSIX 컨테이너는 MSIX 앱이 실행되는 위치입니다. 자세히 알아보려면 [MSIX 컨테이너](/windows/msix/msix-container)를 참조하세요.

## <a name="msix-application"></a>MSIX 애플리케이션 

.MSIX 파일에 저장된 애플리케이션입니다.

## <a name="msix-package"></a>MSIX 패키지 

MSIX 패키지는 MSIX 파일 또는 애플리케이션입니다.

## <a name="msix-share"></a>MSIX 공유

MSIX 공유는 확장된 MSIX 패키지를 포함하는 네트워크 공유입니다. MSIX 공유는 SMB 3 이상을 지원해야 합니다. 또한 공유는 호스트 풀 시스템 계정에서 VM(Virtual Machines)에 액세스할 수 있어야 합니다. MSIX 패키지는 애플리케이션 파일을 시스템 드라이브로 이동하지 않고도 MSIX 공유에서 스테이징됩니다. 

## <a name="msix-image"></a>MSIX 이미지

MSIX 이미지는 하나 이상의 MSIX 패키지된 애플리케이션을 포함하는 VHD, VHDx 또는 CIM 파일입니다. 각 애플리케이션은 MSIXMGR 도구를 사용하여 MSIX 이미지로 제공됩니다.

## <a name="repackage"></a>다시 패키지

다시 패키지는 MSIX가 아닌 애플리케이션을 사용하며 MPT(MSIX 패키징 도구)를 사용하여 해당 애플리케이션을 MSIX로 변환합니다. 자세한 내용은 [MSIX 패키징 도구 개요](/windows/msix/packaging-tool/tool-overview)를 참조하세요.

## <a name="expand-an-msix-package"></a>MSIX 패키지 확장

MSIX 패키지 확장은 여러 단계 프로세스입니다. 확장은 MSIX 파일을 사용하며 해당 콘텐츠를 VHD(x) 또는 CIM 파일에 넣습니다. 

MSIX 패키지를 확장하려면:

1. MSIX 패키지(MSIX 파일)를 가져옵니다.
2. MSIX 파일의 이름을 .zip 파일로 바꿉니다.
3. 폴더에서 결과 .zip 파일의 압축을 풉니다.
4. 폴더와 동일한 크기의 VHD를 만듭니다.
5. VHD를 탑재합니다.
6. 디스크를 초기화합니다.
7. 파티션을 만듭니다.
8. 파티션을 포맷합니다.
9. 압축을 푼 콘텐츠를 VHD에 복사합니다.
10. MSIXMGR 도구를 사용하여 VHD의 콘텐츠에 ACL을 적용합니다.
11. VHD(x) 또는 [CIM](#cim)을 분리합니다.

## <a name="upload-an-msix-package"></a>MSIX 패키지 업로드 

MSIX 패키지를 업로드하려면 확장된 MSIX 패키지를 포함하는 VHD(x) 또는 [CIM](#cim)을 MSIX 공유에 업로드해야 합니다.

Windows Virtual Desktop에서 업로드는 MSIX 공유당 한 번 수행됩니다. 패키지를 업로드하면 동일한 구독의 모든 호스트 풀이 해당 패키지를 참조할 수 있습니다.

## <a name="add-an-msix-package"></a>MSIX 패키지 추가

Windows Virtual Desktop에서 MSIX 패키지를 추가하면 해당 패키지가 호스트 풀에 연결됩니다.

## <a name="publish-an-msix-package"></a>MSIX 패키지 게시 

Windows Virtual Desktop에서 게시된 MSIX 패키지는 AD DS(Active Directory Domain Service) 또는 Azure AD(Azure Active Directory) 사용자 또는 사용자 그룹에 할당해야 합니다.

## <a name="staging"></a>스테이징

스테이징에는 다음 두 가지 항목이 포함됩니다.

- VHD(x) 또는 [CIM](#cim)을 VM에 탑재.
- MSIX 패키지를 등록할 수 있음을 OS에 알림.

## <a name="registration"></a>등록

등록하면 사용자가 스테이징된 MSIX 패키지를 사용할 수 있습니다. 등록은 사용자별 기준으로 수행됩니다. 특정 사용자에 대해 앱을 명시적으로 등록하지 않은 경우 사용자가 앱을 실행할 수 없습니다.

등록에는 두 가지 유형인 일반 및 지연이 있습니다.

### <a name="regular-registration"></a>일반 등록

일반 등록에서는 사용자에게 할당된 각 애플리케이션이 완전히 등록됩니다. 등록은 사용자가 세션에 로그인하는 동안 수행되므로 Windows Virtual Desktop 사용을 시작하는 데 걸리는 시간에 영향을 줄 수 있습니다.

### <a name="delayed-registration"></a>지연 등록

지연 등록에서는 사용자에게 할당된 각 애플리케이션이 부분적으로만 등록됩니다. 부분 등록은 시작 메뉴 타일 및 두 번 클릭 파일 연결이 등록됨을 의미합니다. 등록은 사용자가 세션에 로그인하는 동안 수행되므로 Windows Virtual Desktop 사용을 시작하는 데 걸리는 시간에 미치는 영향을 최소화합니다. 등록은 사용자가 MSIX 패키지에서 애플리케이션을 실행할 때만 완료됩니다.

지연 등록은 현재 MSIX 앱 연결의 기본 구성입니다.

## <a name="deregistration"></a>등록 취소

등록 취소는 사용자에 대해 등록되었지만 실행되고 있지 않은 MSIX 패키지를 제거합니다. 등록 취소는 사용자가 세션에서 로그 아웃하는 동안 수행됩니다. 등록을 취소하는 동안 MSIX 앱 연결은 사용자와 관련된 애플리케이션 데이터를 로컬 사용자 프로필에 푸시합니다.

## <a name="destage"></a>스테이징 취소

스테이징 취소는 현재 실행되고 있지 않고 사용자에 대해 스테이징되지 않은 MSIX 패키지 또는 애플리케이션을 분리할 수 있음을 OS에 알립니다. 이 작업은 OS에서 이에 대한 모든 참조를 제거합니다.

## <a name="cim"></a>CIM

.CIM은 CimFS(Composite Image Files System)와 연결된 새 파일 확장명입니다. CIM 파일 탑재 및 분리는 VHD 파일보다 더 빠릅니다. 또한 CIM은 VHD보다 더 적은 CPU와 메모리를 사용합니다.

CIM 파일은 메타데이터와 함께 실제 파일이 포함된 6개 이상 추가 파일을 포함하는 .CIM 확장명이 있는 파일입니다. CIM 파일 내의 파일에는 확장명이 없습니다. 다음 표는 CIM 내에서 찾을 예제 파일 목록입니다.

| 파일 이름 | 내선 번호 | 크기 |
|-----------|-----------|------|
| VSC | CIM | 1KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | 해당 없음 | 27KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | 해당 없음 | 20KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | 해당 없음 | 42KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | 해당 없음 | 428KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | 해당 없음 | 217KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | 해당 없음 | 264,132KB |

다음 표는 VHD와 CimFS 간 성능 비교입니다. 이 수치는 DSv4 머신에서 실행되는 각 형식의 300MB 파일 500개를 사용한 테스트 실행의 결과였습니다.

|  사양                          | VHD                    | CimFS   |
|---------------------------------|--------------------------|-----------|
| 평균 탑재 시간     | 356ms                     | 255ms      |
| 평균 분리 시간   | 1615ms                    | 36ms       |
| 메모리 사용량 | 6%(8GB)                      | 2%(8GB)       |
| CPU(스파이크 계산)          | 여러 번 최댓값 도달 | 영향 없음 |

## <a name="next-steps"></a>다음 단계

MSIX 앱 연결에 관해 자세히 알아보려면 [개요](what-is-app-attach.md) 및 [FAQ](app-attach-faq.md)를 확인하세요. 그러지 않으면 [앱 연결 설정](app-attach.md)을 시작합니다.
