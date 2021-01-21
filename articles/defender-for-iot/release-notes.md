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
ms.date: 01/06/2021
ms.author: shhazam
ms.openlocfilehash: 72a6e50134647194679055a886b50b01f42e212d
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629982"
---
# <a name="whats-new"></a>새로운 기능

Defender for IoT 10.0는 보안, 관리 및 유용성을 개선 하는 향상 된 기능을 제공 합니다.

## <a name="security"></a>보안

이 릴리스에 대 한 인증서 및 암호 복구 기능이 향상 되었습니다.

### <a name="certificates"></a>인증서
  
이 버전을 통해 다음을 수행할 수 있습니다.

- SSL 인증서를 센서 및 온-프레미스 관리 콘솔에 직접 업로드 합니다.
- 온-프레미스 관리 콘솔과 연결 된 센서 간, 그리고 관리 콘솔과 고가용성 관리 콘솔 간의 유효성 검사를 수행 합니다. 유효성 검사는 만료 날짜, 루트 CA 신뢰성 및 인증서 해지 목록을 기반으로 합니다.  유효성 검사에 실패 하면 세션이 계속 되지 않습니다.

업그레이드의 경우

- 업그레이드 하는 동안 SSL 인증서 또는 유효성 검사 기능이 변경 되지 않습니다.
- 업그레이드 후에는 센서 및 온-프레미스 관리 콘솔 관리자가 SSL 인증서를 대체 하거나, 시스템 설정, SSL 인증서 창에서 SSL 인증서 유효성 검사를 활성화할 수 있습니다.  

새로 설치 하는 경우:

- 처음 로그인 하는 동안 사용자는 SSL 인증서 (권장) 또는 로컬로 생성 된 자체 서명 된 인증서를 사용 해야 합니다 (권장 하지 않음).
- 인증서 유효성 검사는 새로 설치 시 기본적으로 설정 됩니다.

### <a name="password-recovery"></a>암호 복구
  
센서 및 온-프레미스 관리 콘솔 관리자가 이제 IoT 포털 용 Azure Defender에서 암호를 복구할 수 있습니다. 이전에 암호 복구에는 지원 팀의 개입이 필요 했습니다.

## <a name="onboarding"></a>온보딩

### <a name="on-premises-management-console---committed-devices"></a>온-프레미스 관리 콘솔-커밋된 장치

온-프레미스 관리 콘솔에 대 한 초기 로그인 후에는 이제 사용자가 활성화 파일을 업로드 해야 합니다. 이 파일은 조직 네트워크에서 모니터링할 장치의 집계 수를 포함 합니다. 이 번호를 커밋된 장치 수 라고 합니다.
커밋된 장치는 활성화 파일이 생성 되는 IoT 포털 용 Azure Defender의 온 보 딩 프로세스 중에 정의 됩니다.
활성화 파일을 업로드 하려면 처음으로 업그레이드 해야 하는 사용자와 사용자가 필요 합니다.
초기 활성화 후 네트워크에서 검색 된 장치 수가 커밋된 장치 수를 초과할 수 있습니다. 예를 들어 관리 콘솔에 추가 센서를 연결 하는 경우이 이벤트가 발생할 수 있습니다. 검색 된 장치의 수와 커밋된 장치 수가 일치 하지 않는 경우 관리 콘솔에 경고가 표시 됩니다. 이 이벤트가 발생 하는 경우 새 활성화 파일을 업로드 해야 합니다.

### <a name="pricing-page-options"></a>가격 책정 페이지 옵션

가격 책정 페이지에서 IoT 용 Azure Defender에 새 구독을 등록 하 고 네트워크에서 커밋된 장치를 정의할 수 있습니다.  
또한 이제 가격 책정 페이지에서 센서와 연결 된 기존 구독을 관리 하 고 장치 약정을 업데이트할 수 있습니다.

### <a name="view-and-manage-onboarded-sensors"></a>등록 센서 보기 및 관리

새 사이트 및 센서 포털 페이지를 통해 다음을 수행할 수 있습니다.

- 센서에 대 한 설명 정보를 추가 합니다. 예를 들어 센서와 연결 된 영역 또는 자유 텍스트 태그입니다.
- 센서 정보를 보고 필터링 합니다. 예를 들어 클라우드로 연결 되거나 로컬로 관리 되는 센서에 대 한 세부 정보를 보거나 특정 영역에서 센서에 대 한 정보를 볼 수 있습니다.  

## <a name="usability"></a>사용 편의성

### <a name="azure-sentinel-new-connector-page"></a>Azure 센티널 새 커넥터 페이지

Azure 센티널의 IoT 용 Azure Defender 데이터 커넥터 페이지가 다시 디자인 되었습니다. 데이터 커넥터는 이제 IoT Hub가 아닌 구독을 기반으로 합니다. 고객이 Azure 센티널에 대 한 구성 연결을 보다 효율적으로 관리할 수 있도록 합니다.

### <a name="azure-portal-permission-updates"></a>Azure Portal 사용 권한 업데이트  

보안 읽기 권한자 및 보안 관리자 지원이 추가 되었습니다.

## <a name="other-updates"></a>기타 업데이트

### <a name="access-group---zone-permissions"></a>액세스 그룹 영역 권한
  
온-프레미스 관리 콘솔 액세스 그룹 규칙에는 특정 영역에 대 한 액세스 권한을 부여 하는 옵션이 포함 되지 않습니다. 사이트, 지역 및 비즈니스 단위를 사용 하는 규칙을 정의 하는 변경 내용은 없습니다.   업그레이드 후에는 특정 영역에 대 한 액세스를 허용 하는 규칙이 포함 된 액세스 그룹이 모든 영역을 포함 하 여 부모 사이트에 대 한 액세스를 허용 하도록 수정 됩니다.

### <a name="terminology-changes"></a>용어 변경

용어 자산은 센서 및 온-프레미스 관리 콘솔, 보고서 및 기타 솔루션 인터페이스에서 이름이 변경 되었습니다.
센서 및 온-프레미스 관리 콘솔 경고에서이 이벤트를 관리 하는 용어는 수정 단계로 지정 되었습니다.

## <a name="next-steps"></a>다음 단계

[IoT 용 Defender 시작](getting-started.md)
