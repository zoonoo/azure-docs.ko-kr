---
title: IoT 용 Azure Defender의 새로운 기능
description: 이 문서에서는 최신 IoT for IoT 릴리스의 새로운 기능을 확인할 수 있습니다.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2021
ms.author: shhazam
ms.openlocfilehash: ef72be60b6294ad4e1fca2ce9c0e3c66b64ac687
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493962"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>IoT 용 Azure Defender의 새로운 기능

이 문서에는 IoT 용 Defender에 대 한 새로운 기능 및 향상 된 기능이 나열 되어 있습니다.

언급 된 기능은 미리 보기 상태입니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
## <a name="march-2021"></a>2021년 3월

### <a name="sensor---enhanced-custom-alert-rules-public-preview"></a>센서-향상 된 사용자 지정 경고 규칙 (공개 미리 보기)

이제 일, 일 그룹 및 네트워크 활동이 검색 된 기간을 기준으로 사용자 지정 경고 규칙을 만들 수 있습니다.  날짜 및 시간 규칙 조건에 대 한 작업은 경고 심각도가 발생 하는 시간에 의해 경고 심각도가 파생 되는 경우 등에 유용 합니다. 예를 들어 주말 또는 저녁에 네트워크 활동이 검색 될 때 높은 심각도 경고를 트리거하는 사용자 지정 규칙을 만듭니다.

이 기능은 버전 10.2의 릴리스와 함께 센서에서 사용할 수 있습니다.

### <a name="on-premises-management-console---export-alerts-public-preview"></a>온-프레미스 관리 콘솔-경고 내보내기 (공개 미리 보기)

이제 온-프레미스 관리 콘솔에서 경고 정보를 .csv 파일로 내보낼 수 있습니다. 필터링 된 보기를 기반으로 검색 된 모든 경고 또는 내보내기 정보에 대 한 정보를 내보낼 수 있습니다.

이 기능은 버전 10.2의 릴리스와 함께 온-프레미스 관리 콘솔에서 사용할 수 있습니다.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>온-프레미스 관리 콘솔에 두 번째 네트워크 인터페이스 추가 (공개 미리 보기)

이제 온-프레미스 관리 콘솔에 두 번째 네트워크 인터페이스를 추가 하 여 배포의 보안을 향상 시킬 수 있습니다. 이 기능을 사용 하면 온-프레미스 관리에서 하나의 보안 네트워크에 연결 된 센서를 사용할 수 있으며, 사용자가 두 번째 별도 네트워크 인터페이스를 통해 온-프레미스 관리 콘솔에 액세스할 수 있습니다.

이 기능은 버전 10.2의 릴리스와 함께 온-프레미스 관리 콘솔에서 사용할 수 있습니다.
### <a name="device-builder---new-micro-agent-public-preview"></a>장치 빌더-새 마이크로 에이전트 (공개 미리 보기)

새 장치 빌더 모듈을 사용할 수 있습니다. 마이크로 에이전트 라고 하는 모듈을 사용 하면 다음을 수행할 수 있습니다.

- **Iot 용 Azure IoT Hub 및 Azure defender와 통합** -iot 용 Azure IoT Hub 및 azure defender에서 제공 하는 모니터링 옵션과 통합 하 여 더 강력한 끝점 보안을 iot 장치에 직접 빌드 하세요.
- **표준 iot 운영 체제에 대 한 지원을 포함 한 유연한 배포 옵션** -Linux 및 AZURE rtos와 같은 표준 iot 운영 체제에 대 한 지원을 통해 이진 패키지나 수정 가능한 소스 코드로 배포할 수 있습니다.
- **Os 커널 종속성이 없는 최소 리소스 요구 사항** -적은 공간, 낮은 CPU 사용량 및 os 커널 종속성이 없습니다.
- **보안 상태 관리** – IoT 장치의 보안 상태를 사전에 모니터링 합니다.
- **연속 실시간 IoT/OT 위협 감지** -봇 넷, 무차별 암호 대입 시도, crypto 마이너 및 의심 스러운 네트워크 작업과 같은 위협 감지

사용 되지 않는 Defender-마이크로 에이전트 설명서는 *장치 빌더>클래식 폴더에 대 한 에이전트 기반 솔루션* 으로 이동 됩니다.

이 기능 집합은 현재 공개 미리 보기 클라우드 릴리스와 함께 사용할 수 있습니다.

## <a name="january-2021"></a>2021년 1월

- [보안](#security)
- [온보딩](#onboarding)
- [유용성](#usability)
- [기타 업데이트](#other-updates)
### <a name="security"></a>보안

이 릴리스에 대 한 인증서 및 암호 복구 기능이 향상 되었습니다.

#### <a name="certificates"></a>인증서
  
이 버전을 통해 다음을 수행할 수 있습니다.

- SSL 인증서를 센서 및 온-프레미스 관리 콘솔에 직접 업로드 합니다.
- 온-프레미스 관리 콘솔과 연결 된 센서 간, 그리고 관리 콘솔과 고가용성 관리 콘솔 간의 유효성 검사를 수행 합니다. 유효성 검사는 만료 날짜, 루트 CA 신뢰성 및 인증서 해지 목록을 기반으로 합니다.  유효성 검사에 실패 하면 세션이 계속 되지 않습니다.

업그레이드의 경우

- 업그레이드 하는 동안 SSL 인증서 또는 유효성 검사 기능이 변경 되지 않습니다.
- 업그레이드 후에는 센서 및 온-프레미스 관리 콘솔 관리자가 SSL 인증서를 대체 하거나, 시스템 설정, SSL 인증서 창에서 SSL 인증서 유효성 검사를 활성화할 수 있습니다.  

새로 설치 하는 경우:

- 처음 로그인 하는 동안 사용자는 SSL 인증서 (권장) 또는 로컬로 생성 된 자체 서명 된 인증서를 사용 해야 합니다 (권장 하지 않음).
- 인증서 유효성 검사는 새로 설치 시 기본적으로 설정 됩니다.

#### <a name="password-recovery"></a>암호 복구
  
센서 및 온-프레미스 관리 콘솔 관리자가 이제 IoT 포털 용 Azure Defender에서 암호를 복구할 수 있습니다. 이전에 암호 복구에는 지원 팀의 개입이 필요 했습니다.

### <a name="onboarding"></a>온보딩

#### <a name="on-premises-management-console---committed-devices"></a>온-프레미스 관리 콘솔-커밋된 장치

온-프레미스 관리 콘솔에 대 한 초기 로그인 후에는 이제 사용자가 활성화 파일을 업로드 해야 합니다. 이 파일은 조직 네트워크에서 모니터링할 장치의 집계 수를 포함 합니다. 이 번호를 커밋된 장치 수 라고 합니다.
커밋된 장치는 활성화 파일이 생성 되는 IoT 포털 용 Azure Defender의 온 보 딩 프로세스 중에 정의 됩니다.
활성화 파일을 업로드 하려면 처음으로 업그레이드 해야 하는 사용자와 사용자가 필요 합니다.
초기 활성화 후 네트워크에서 검색 된 장치 수가 커밋된 장치 수를 초과할 수 있습니다. 예를 들어 관리 콘솔에 추가 센서를 연결 하는 경우이 이벤트가 발생할 수 있습니다. 검색 된 장치의 수와 커밋된 장치 수가 일치 하지 않는 경우 관리 콘솔에 경고가 표시 됩니다. 이 이벤트가 발생 하는 경우 새 활성화 파일을 업로드 해야 합니다.

#### <a name="pricing-page-options"></a>가격 책정 페이지 옵션

가격 책정 페이지에서 IoT 용 Azure Defender에 새 구독을 등록 하 고 네트워크에서 커밋된 장치를 정의할 수 있습니다.  
또한 이제 가격 책정 페이지에서 센서와 연결 된 기존 구독을 관리 하 고 장치 약정을 업데이트할 수 있습니다.

#### <a name="view-and-manage-onboarded-sensors"></a>등록 센서 보기 및 관리

새 사이트 및 센서 포털 페이지를 통해 다음을 수행할 수 있습니다.

- 센서에 대 한 설명 정보를 추가 합니다. 예를 들어 센서와 연결 된 영역 또는 자유 텍스트 태그입니다.
- 센서 정보를 보고 필터링 합니다. 예를 들어 클라우드로 연결 되거나 로컬로 관리 되는 센서에 대 한 세부 정보를 보거나 특정 영역에서 센서에 대 한 정보를 볼 수 있습니다.  

### <a name="usability"></a>사용 편의성

#### <a name="azure-sentinel-new-connector-page"></a>Azure 센티널 새 커넥터 페이지

Azure 센티널의 IoT 용 Azure Defender 데이터 커넥터 페이지가 다시 디자인 되었습니다. 데이터 커넥터는 이제 IoT Hub가 아닌 구독을 기반으로 합니다. 고객이 Azure 센티널에 대 한 구성 연결을 보다 효율적으로 관리할 수 있도록 합니다.

#### <a name="azure-portal-permission-updates"></a>Azure Portal 사용 권한 업데이트  

보안 읽기 권한자 및 보안 관리자 지원이 추가 되었습니다.

### <a name="other-updates"></a>기타 업데이트

#### <a name="access-group---zone-permissions"></a>액세스 그룹 영역 권한
  
온-프레미스 관리 콘솔 액세스 그룹 규칙에는 특정 영역에 대 한 액세스 권한을 부여 하는 옵션이 포함 되지 않습니다. 사이트, 지역 및 비즈니스 단위를 사용 하는 규칙을 정의 하는 변경 내용은 없습니다.   업그레이드 후에는 특정 영역에 대 한 액세스를 허용 하는 규칙이 포함 된 액세스 그룹이 모든 영역을 포함 하 여 부모 사이트에 대 한 액세스를 허용 하도록 수정 됩니다.

#### <a name="terminology-changes"></a>용어 변경

자산의 용어는 센서와 온-프레미스 관리 콘솔, 보고서 및 기타 솔루션 인터페이스에서 이름이 변경 되었습니다.
센서 및 온-프레미스 관리 콘솔 경고에서이 이벤트를 관리 하는 용어는 수정 단계로 지정 되었습니다.

## <a name="next-steps"></a>다음 단계

[IoT 용 Defender 시작](getting-started.md)
