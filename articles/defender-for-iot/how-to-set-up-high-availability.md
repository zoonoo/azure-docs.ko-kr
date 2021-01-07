---
title: 고가용성 설정
description: 온-프레미스 관리 콘솔 고가용성 어플라이언스를 설치 하 여 IoT 배포용 Defender의 복원 력을 늘립니다. 고가용성 배포는 관리 되는 센서가 활성 온-프레미스 관리 콘솔에 지속적으로 보고 되도록 합니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 6540b5f93bebfe39253a88dc495a3613568f8926
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839734"
---
# <a name="about-high-availability"></a>고가용성 정보

온-프레미스 관리 콘솔 고가용성 어플라이언스를 설치 하 여 IoT 배포용 Defender의 복원 력을 늘립니다. 고가용성 배포는 관리 되는 센서가 활성 온-프레미스 관리 콘솔에 지속적으로 보고 되도록 합니다.

이 배포는 기본 및 보조 어플라이언스를 포함 하는 온-프레미스 관리 콘솔 쌍을 사용 하 여 구현 됩니다.

## <a name="about-primary-and-secondary-communication"></a>기본 및 보조 통신 정보

기본 및 보조 온-프레미스 관리 콘솔이 쌍으로 연결 된 경우:

- 기본 및 보조 어플라이언스 간에 보안 연결을 만들기 위해 온-프레미스 관리 콘솔 SLL 인증서가 적용 됩니다. SLL은 기본적으로 설치 되는 자체 서명 된 인증서 또는 고객이 설치한 인증서 일 수 있습니다.

- 기본 온-프레미스 관리 콘솔 데이터는 10 분 마다 보조 온-프레미스 관리 콘솔로 자동 백업 됩니다. 온-프레미스 관리 콘솔 구성 및 장치 데이터를 백업 합니다. PCAP 파일 및 로그는 백업에 포함 되지 않습니다. PCAPs 및 로그를 수동으로 백업 하 고 복원할 수 있습니다.

- 관리 콘솔의 기본 설정은 보조 복제본에서 복제 됩니다. 예를 들어 시스템 설정입니다. 이러한 설정이 주 복제본에서 업데이트 되 면 보조 데이터베이스 에서도 업데이트 됩니다.

- 보조 복제본이 만료 되기 전에 라이선스를 업데이트 하기 위해이를 기본으로 정의 해야 합니다.

## <a name="about-failover-and-failback"></a>장애 조치(failover) 및 장애 복구(failback) 정보

센서가 기본 온-프레미스 관리 콘솔에 연결할 수 없는 경우 보조 데이터베이스에 자동으로 연결 됩니다. 센서의 절반 미만이 보조 데이터베이스와 통신 하는 경우에는 기본 및 보조 모두에서 시스템이 동시에 지원 됩니다. 보조 복제본은 센서의 절반 이상이 통신 하는 경우에 사용 됩니다. 주 데이터베이스에서 보조 데이터베이스로 장애 조치 (failover)는 약 3 분이 걸립니다. 장애 조치 (failover)가 발생 하면 기본 온-프레미스 관리 콘솔이 중단 됩니다. 이런 경우 동일한 로그인 자격 증명을 사용 하 여 보조 데이터베이스에 로그인 할 수 있습니다.

장애 조치 (failover) 중에 센서는 계속 해 서 기본 어플라이언스와 통신을 시도 합니다. 절반 이상의 관리 센서에서 주 복제본과 통신 하는 데 성공한 경우 주 복제본이 복원 됩니다. 주 데이터베이스가 복원 되 면 보조 콘솔에 다음과 같은 메시지가 나타납니다.

:::image type="content" source="media/how-to-set-up-high-availability/secondary-console-message.png" alt-text="주 데이터베이스를 복원할 때 보조 콘솔에 표시 되는 메시지의 스크린샷":::

리디렉션 후 기본 어플라이언스로 다시 로그인 합니다.

## <a name="high-availability-setup-overview"></a>고가용성 설정 개요

설치 및 구성 절차는 다음 네 가지 기본 단계로 수행 됩니다.

1. 온-프레미스 관리 콘솔 기본 어플라이언스를 설치 합니다. 

2. 온-프레미스 관리 콘솔 기본 어플라이언스를 구성 합니다. 예: 예약 된 백업 설정, VLAN 설정. 자세한 내용은 온-프레미스 관리 콘솔 사용자 가이드를 참조 하세요. 모든 설정은 페어링 후 자동으로 보조 어플라이언스에 적용 됩니다.

3. 온-프레미스 관리 콘솔 보조 어플라이언스를 설치 합니다. 자세한 내용은 [IoT 설치용 Defender 정보](how-to-install-software.md)를 참조 하세요.

4. [여기](/create-the-primary-and-secondary-pair.md)에 설명 된 대로 기본 및 보조 온-프레미스 관리 콘솔 어플라이언스를 페어링 합니다. 기본 온-프레미스 관리 콘솔은 설치를 수행 하기 위해 두 개 이상의 센서를 관리 해야 합니다.

## <a name="high-availability-requirements"></a>고가용성 요구 사항

다음과 같은 고가용성 요구 사항을 충족 하는지 확인 합니다.

- 인증서 요구 사항

- 소프트웨어 및 하드웨어 요구 사항

- 네트워크 액세스 요구 사항

### <a name="software-and-hardware-requirements"></a>소프트웨어 및 하드웨어 요구 사항

- 기본 및 보조 온-프레미스 관리 콘솔 어플라이언스 모두 동일한 하드웨어 모델 및 소프트웨어 버전을 실행 해야 합니다.

- 고가용성 시스템은 IoT 사용자 용 Defender에서 CLI 도구를 사용 하 여 설정할 수 있습니다.

### <a name="network-access-requirements"></a>네트워크 액세스 요구 사항

조직 보안 정책을 사용 하 여 기본 및 보조 온-프레미스 관리 콘솔에서 다음 서비스에 액세스할 수 있는지 확인 해야 합니다. 이러한 서비스를 통해 센서와 보조 온-프레미스 관리 콘솔 간에 연결할 수 있습니다.

|포트|서비스|Description|
|----|-------|-----------|
|**443 또는 TCP**|HTTPS|온-프레미스 관리 콘솔 웹 콘솔에 대 한 액세스 권한을 부여 합니다.|
|**22 또는 TCP**|SSH|기본 및 보조 온-프레미스 관리 콘솔 어플라이언스 간에 데이터를 동기화 합니다.|
|**123 또는 UDP**|NTP| 온-프레미스 관리 콘솔의 NTP 시간 동기화입니다. 활성 및 수동 기기가 동일한 표준 시간대로 정의 되어 있는지 확인 합니다.|

## <a name="create-the-primary-and-secondary-pair"></a>기본 및 보조 쌍 만들기

절차를 시작 하기 전에 기본 및 보조 온-프레미스 관리 콘솔 어플라이언스를 모두 켜 졌는 지 확인 합니다.  

### <a name="on-the-primary"></a>주 서버의

1. IoT 사용자에 대 한 Defender로 CLI에 로그인 합니다.

2. 기본에서 다음 명령을 실행 합니다.

```azurecli-interactive
sudo cyberx-management-trusted-hosts-add -ip <Secondary IP>
```

>[!NOTE]
>이 문서에서는 주 온-프레미스 관리 콘솔을 기본으로 참조 하 고, 에이전트를 보조 라고 합니다.

3. 필드에 보조 어플라이언스의 IP 주소를 입력 ```<Secondary ip>``` 하 고 Enter 키를 선택 합니다. 그런 다음 IP 주소는 유효성을 검사 하 고 SSL 인증서는 주 복제본으로 다운로드 됩니다. IP 주소를 입력 하면 센서가 보조 어플라이언스에 연결 됩니다.

4. 주 복제본에서 다음 명령을 실행 하 여 인증서가 제대로 설치 되었는지 확인 합니다.

```azurecli-interactive
sudo cyberx-management-trusted-hosts-apply
```

5. 주 복제본에서 다음 명령을 실행 합니다. **Sudo로 실행 하지 않습니다.**

```azurecli-interactive
cyberx-management-deploy-ssh-key <Secondary IP>
```

이를 통해 기본 어플라이언스와 보조 어플라이언스 간에 연결을 연결 하 여 백업 및 복원할 수 있습니다.

6. 보조의 IP 주소를 입력 하 고 Enter 키를 선택 합니다.

### <a name="on-the-secondary"></a>보조 데이터베이스

1. IoT 사용자에 대 한 Defender로 CLI에 로그인 합니다.

2. 보조에서 다음 명령을 실행 합니다. **Sudo를 사용 하 여 실행 안 함**:

```azurecli-interactive
cyberx-management-deploy-ssh-key <Primary ip>
```

이렇게 하면 기본 어플라이언스와 보조 어플라이언스 간에 연결을 연결 하 여 백업 및 복원할 수 있습니다.

3. 기본의 IP 주소를 입력 하 고 enter 키를 누릅니다.

### <a name="track-high-availability-activity"></a>고가용성 활동 추적

핵심 응용 프로그램 로그를 IoT 용 Defender 지원 팀으로 내보내 고가용성 문제를 처리할 수 있습니다.  

핵심 로그에 액세스 하려면:

1. **시스템 설정** 창에서 **내보내기** 를 선택 합니다.

## <a name="update-the-on-premises-management-console-with-high-availability"></a>고가용성을 사용 하 여 온-프레미스 관리 콘솔 업데이트

다음 순서로 고가용성 업데이트를 수행 합니다. 새 단계를 시작 하기 전에 각 단계가 완료 되었는지 확인 합니다.

고가용성으로 업데이트 하려면:

1. 기본 온-프레미스 관리 콘솔을 업데이트 합니다.

2. 보조 온-프레미스 관리 콘솔을 업데이트 합니다.

3. 센서를 업데이트 합니다.

## <a name="see-also"></a>참조

[온-프레미스 관리 콘솔 활성화 및 설정](how-to-activate-and-set-up-your-on-premises-management-console.md)