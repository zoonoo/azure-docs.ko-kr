---
title: 시작
description: IoT 배포용 기본 워크플로 이해를 시작 하세요.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/26/2020
ms.author: shhazam
ms.openlocfilehash: 82fcf7f129d5caba471d6d72da83a317b26c638c
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807896"
---
# <a name="get-started-with-defender-for-iot"></a>IoT 용 Defender 시작

이 문서에서는 IoT 용 Azure Defender를 설정 하기 위해 수행 하는 단계에 대 한 개요를 제공 합니다. 이렇게 하려면 다음을 수행 해야 합니다.

- IoT 포털 용 Azure Defender에서 구독 및 센서를 등록 합니다.
- 센서 및 온-프레미스 관리 콘솔 소프트웨어를 설치 합니다.
- 센서 및 관리 콘솔의 초기 활성화를 수행 합니다.

## <a name="permission-requirements"></a>사용 권한 요구 사항

일부 설치 단계에는 특정 사용자 권한이 필요 합니다.

관리 사용자 권한은 센서 및 관리 콘솔을 활성화 하 고, SSL/TLS 인증서를 업로드 하 고, 새 암호를 생성 하는 데 필요 합니다.

다음 표에서는 IoT 포털 tools 용 Azure Defender에 대 한 사용자 액세스 권한을 설명 합니다.

| 사용 권한 | 보안 판독기 | 보안 관리자 | 구독 참가자 | 구독 소유자 |
|--|--|--|--|--|
| IoT 화면 및 데이터에 대 한 모든 Defender 보기 | ✓ | ✓ | ✓ | ✓ |
| 센서 온보딩  |  |  ✓ | ✓ | ✓ |
| 가격 업데이트  |  |  ✓ | ✓ | ✓ |
| 암호 복구  | ✓  |  ✓ | ✓ | ✓ |

## <a name="1-identify-the-solution-infrastructure"></a>1. 솔루션 인프라를 식별 합니다.

**네트워크 설정 요구 사항 명시**

네트워크 아키텍처, 모니터링 되는 대역폭 및 기타 네트워크 세부 정보를 조사 합니다. 자세한 내용은 [IoT 용 Azure Defender 네트워크 설정](how-to-set-up-your-network.md)정보를 참조 하세요.

**네트워크 로드를 처리 하는 데 필요한 센서 및 관리 콘솔 어플라이언스를 명시 합니다.**

IoT 용 Azure Defender는 물리적 및 가상 배포를 모두 지원 합니다. 물리적 배포의 경우 다양 한 인증 된 어플라이언스를 구입할 수 있습니다. 자세한 내용은 [필수 어플라이언스 식별](how-to-identify-required-appliances.md)을 참조 하세요.

모니터링 되는 장치의 대략적인 수를 계산 하는 것이 좋습니다. 나중에 Azure 구독을 포털에 등록 하면이 숫자를 입력 하 라는 메시지가 표시 됩니다. 1000 초 간격으로 숫자를 더할 수 있습니다. 모니터링 되는 장치 수를 *커밋된 장치* 라고 합니다.

## <a name="2-register-with-azure-defender-for-iot"></a>2. IoT 용 Azure Defender에 등록

등록에는 다음이 포함 됩니다.

- IoT 용 Defender에 Azure 구독을 온 보 딩 합니다.
- 커밋된 장치 정의
- 온-프레미스 관리 콘솔용 활성화 파일 다운로드

등록하려면 다음을 수행합니다.

1. IoT 포털 용 Azure Defender로 이동 합니다.
1. **등록 구독** 을 선택 합니다.
1. **가격 책정** 페이지에서 구독을 선택 하거나 새 구독을 만들고 커밋된 장치 수를 추가 합니다.
1. **온-프레미스 관리 콘솔 다운로드** 탭을 선택 하 고 다운로드 한 활성화 파일을 저장 합니다. 이 파일에는 사용자가 정의한 집계 된 커밋된 장치가 포함 되어 있습니다. 초기 로그인 후에 관리 콘솔에 파일이 업로드 됩니다.

## <a name="3-install-and-set-up-the-on-premises-management-console"></a>3. 온-프레미스 관리 콘솔 설치 및 설정

온-프레미스 관리 콘솔 어플라이언스를 얻은 후에는 다음을 수행 합니다.

- IoT 포털 용 Azure Defender에서 ISO 패키지를 다운로드 합니다.
- 소프트웨어를 설치 합니다.
- 초기 관리 콘솔 설치를 활성화 하 고 수행 합니다.

설치 및 설정 하려면:

1. IoT 포털 용 Defender에서 **시작** 을 선택 합니다.
1. **온-프레미스 관리 콘솔** 탭을 선택 합니다.
1. 버전을 선택 하 고 **다운로드** 를 선택 합니다.
1. 온-프레미스 관리 콘솔 소프트웨어를 설치 합니다. 자세한 내용은 [IoT 설치용 Defender](how-to-install-software.md)를 참조 하세요.
1. 관리 콘솔을 활성화 하 고 설정 합니다. 자세한 내용은 [온-프레미스 관리 콘솔 활성화 및 설정](how-to-activate-and-set-up-your-on-premises-management-console.md)을 참조 하세요.

## <a name="4-onboard-a-sensor"></a>4. 센서 등록

IoT 용 Azure Defender에 등록 하 고 센서 활성화 파일을 다운로드 하 여 센서를 등록 합니다.

1. 센서 이름을 정의 하 고 구독과 연결 합니다.
1. 센서 관리 모드 선택:

   - **클라우드 연결 센서**: 센서 검색 정보가 센서 콘솔에 표시 됩니다. 또한 경고 정보는 IoT hub를 통해 전달 되며 Azure 센티널과 같은 다른 Azure 서비스와 공유할 수 있습니다.

   - **로컬로 관리 되는 센서**: 센서 검색 정보가 센서 콘솔에 표시 됩니다. Gapped 네트워크에서 작업 중이 고 로컬로 관리 되는 여러 센서에서 검색 된 모든 정보에 대 한 통합 보기를 사용 하는 경우 온-프레미스 관리 콘솔을 사용 합니다. 

1. 센서 활성화 파일을 다운로드 합니다.

자세한 내용은 [IoT 포털의 Defender에서 센서 등록 및 관리](how-to-manage-sensors-on-the-cloud.md)를 참조 하세요.

## <a name="5-install-and-set-up-the-sensor"></a>5. 센서 설치 및 설정

Azure Defender for IoT 포털에서 ISO 패키지를 다운로드 하 고, 소프트웨어를 설치 하 고, 센서를 설정 합니다.

1. IoT 포털 용 Defender에서 **시작** 을 선택 합니다.
1. **센서 설정** 을 선택 합니다.
1. 버전을 선택 하 고 **다운로드** 를 선택 합니다.
1. 센서 소프트웨어를 설치 합니다. 자세한 내용은 [IoT 설치용 Defender](how-to-install-software.md)를 참조 하세요.
1. 센서를 활성화 하 고 설정 합니다. 자세한 내용은 [센서 로그인 및 정품 인증](how-to-activate-and-set-up-your-sensor.md)을 참조 하세요.

## <a name="6-connect-sensors-to-an-on-premises-management-console"></a>6. 센서를 온-프레미스 관리 콘솔에 연결

관리 콘솔에 센서를 연결 하 여 다음을 확인 합니다.

- 센서는 온-프레미스 관리 콘솔에 경고 및 장치 인벤토리 정보를 보냅니다.

- 온-프레미스 관리 콘솔에서 센서 백업을 수행 하 고, 센서에서 감지 하는 경고를 관리 하 고, 센서 연결을 조사 하 고, 연결 된 센서에서 기타 작업을 수행할 수 있습니다.

단일 영역에서 동일한 네트워크를 모니터링 하는 여러 센서를 그룹화 하는 것이 좋습니다. 이렇게 하면 여러 센서에 의해 수집 된 정보가 병합 됩니다.

자세한 내용은 [센서를 온-프레미스 관리 콘솔에 연결](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)을 참조 하세요.

## <a name="7-populate-azure-sentinel-with-alert-information-optional"></a>7. 경고 정보를 사용 하 여 Azure 센티널 채우기 (선택 사항)

Azure 센티널을 구성 하 여 경고 정보를 Azure 센티널로 보냅니다. [IoT 용 Defender의 데이터를 Azure 센티널에 연결을](how-to-configure-with-sentinel.md)참조 하세요.

## <a name="see-also"></a>참고 항목

- [IoT 용 Azure Defender 시작](overview.md)

- [IoT 용 Azure Defender 아키텍처](architecture.md)
