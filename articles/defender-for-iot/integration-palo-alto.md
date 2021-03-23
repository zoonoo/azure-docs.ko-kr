---
title: Palo Alto 통합
description: IoT 용 Defender는 지속적인 ICS 위협 모니터링 플랫폼과 Palo Alto의 차세대 방화벽을 통합 하 여 중요 한 위협 요소를 더 빠르고 효율적으로 차단할 수 있습니다.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 49c00f9ad7cc1980a8690e35ed19351e20417c6a
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783934"
---
# <a name="about-the-palo-alto-integration"></a>Palo Alto 통합 정보

IoT 용 Defender는 지속적인 ICS 위협 모니터링 플랫폼과 Palo Alto의 차세대 방화벽을 통합 하 여 중요 한 위협 요소를 더 빠르고 효율적으로 차단할 수 있습니다.

사용할 수 있는 통합 유형은 다음과 같습니다.

- 자동 차단 옵션: IoT-Palo Alto 통합에 대 한 직접 Defender

- 중앙 관리 시스템에 대 한 차단 권장 사항 보내기: IoT-Panorama 통합을 위한 Defender

## <a name="configure-immediate-blocking-by-specified-palo-alto-firewall"></a>지정 된 Palo Alto 방화벽을 통해 즉각적인 차단 구성

맬웨어 관련 경고와 같은 중요 한 경우에는 자동 차단을 사용 하도록 설정할 수 있습니다. 이 작업은 특정 Palo Alto 방화벽으로 직접 차단 명령을 보내는 IoT 용 Defender에서 전달 규칙을 구성 하 여 수행 됩니다.

Defender for IoT는 심각한 위협을 식별 하는 경우 감염 된 원본을 차단 하는 옵션이 포함 된 경고를 보냅니다. 경고 세부 정보에서 **소스 차단** 을 선택 하면 전달 규칙이 활성화 되어 차단 명령이 지정 된 Palo Alto 방화벽으로 전송 됩니다.

구성 하려면:

1. 왼쪽 창에서 **전달** 을 선택 합니다.

   :::image type="content" source="media/integration-paloalto/forwarding.png" alt-text="전달 경고 화면입니다.":::

1. **전달 규칙 만들기** 를 선택 합니다.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="전달 규칙 만들기":::

1. Palo Alto NGFW 전달 규칙을 구성 하려면:  
 
   - 표준 규칙 매개 변수를 정의 하 고 **작업** 드롭다운 상자에서 **Palo Alto NGFW로 보내기를** 선택 합니다.
   
   :::image type="content" source="media/integration-paloalto/edit.png" alt-text="전달 규칙을 편집 합니다.":::

1. 작업 창에서 다음 매개 변수를 설정 합니다.

   - **호스트**: NGFW 서버 IP 주소를 입력 합니다.
   - **포트**: NGFW 서버 포트를 입력 합니다.
   - **사용자 이름**: NGFW 서버 사용자 이름을 입력 합니다.
   - **암호**: NGFW 서버 암호를 입력 합니다.
   - **구성**: 다음 옵션을 설정 하 여 Palo Alto 방화벽에서 의심 스러운 소스를 차단할 수 있도록 합니다.
     - **잘못 된 함수 코드 차단**: 프로토콜 위반-잘못 된 필드 값이 ICS 프로토콜 사양 (잠재적 악용)을 위반 합니다.
     - **권한 없는 PLC 프로그래밍/펌웨어 업데이트 차단**: 권한이 없는 PLC 변경 내용입니다.
     - **권한 없는 PLC stop**: PLC stop (가동 중지 시간)을 차단 합니다.
     - **맬웨어 관련 경고 차단**: 산업용 맬웨어 시도 (TRITON, NotPetya 등)를 차단 합니다. **자동 차단** 옵션을 선택할 수 있습니다. 이 경우 차단이 자동으로 즉시 실행 됩니다.
     - **무단 검사 차단**: 무단 검사 (잠재적 정찰).
     
1. **제출** 을 선택합니다.

의심 스러운 원본을 차단 하려면: 

1. **경고** 창에서 Palo Alto 통합과 관련 된 경고를 선택 합니다. **경고 정보** 대화 상자가 나타납니다.
   
   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="경고 세부 정보":::

1. 의심 스러운 원본을 자동으로 차단 하려면 **소스 차단** 을 선택 합니다. **확인 하세요** . 대화 상자가 나타납니다.

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="확인 화면에서 차단을 확인 합니다.":::

1. **확인** 을 선택 합니다. 대화 상자에서 **확인** 을 선택 합니다. 의심 스러운 소스는 Palo Alto 방화벽에 의해 차단 됩니다.

## <a name="sending-blocking-policies-to-palo-alto-panorama"></a>Palo Alto 파노라마로 차단 정책 보내기

IoT 및 Palo Alto 네트워크용 Defender에는 Palo Alto Networks NMS, 파노라마에 새 정책을 자동으로 만드는 일회용 통합이 있습니다. 이 통합에는 파노라마 관리자의 확인이 필요 하며 자동 차단은 허용 되지 않습니다.

통합은 다음과 같은 인시던트에 사용 됩니다.

- **권한 없는 PLC 변경 내용:** 사다리 논리 또는 장치의 펌웨어에 대 한 업데이트입니다. 합법적인 작업을 나타내거나 장치를 손상 시키려는 시도를 나타낼 수 있습니다. 이러한 손상은 안전 하지 않은 방식으로 작동 하기 위해 원격 액세스 트로이 (RAT) 또는 물리적 프로세스를 야기 하는 매개 변수 (예: 회전 터빈)와 같은 악성 코드를 삽입 하 여 발생할 수 있습니다.

- **프로토콜 위반:** 프로토콜 사양을 위반 하는 패킷 구조 또는 필드 값입니다. 이는 잘못 구성 된 응용 프로그램 또는 장치 손상을 악의적 시도를 나타낼 수 있습니다. 예를 들어 대상 장치에서 버퍼 오버플로 조건을 발생 시킵니다.

- **PLC 중지:** 장치가 작동을 중지 하 여 PLC가 제어 하는 실제 프로세스에 위험을 발생 시키는 명령입니다.

- **ICS 네트워크에서 발견 된 산업용 맬웨어:** TRITON 및 Industroyer와 같은 기본 프로토콜을 사용 하 여 ICS 장치를 조작 하는 맬웨어 또한 Defender for IoT는 수평을 ICS 및 SCADA 환경 (예: Conficker, WannaCry 및 NotPetya)으로 이동한 IT 맬웨어를 검색 합니다.

- **맬웨어 검색:** 공격 전 단계에서 시스템 구성에 대 한 데이터를 수집 하는 정찰 도구입니다. 예를 들어 Havex 트로이 목마는 Windows 기반 SCADA 시스템에서 ICS 장치와 통신 하는 데 사용 하는 표준 프로토콜인 OPC를 사용 하는 장치에 대 한 산업용 네트워크를 검색 합니다.

## <a name="the-process"></a>프로세스

IoT 용 Defender가 미리 구성 된 사용 사례를 검색 하면 **소스 차단** 단추가 경고에 추가 됩니다. 그런 다음 **CyberX** 사용자가 **소스 차단** 단추를 선택 하면 IoT 용 Defender는 미리 정의 된 전달 규칙을 전송 하 여 파노라마에서 정책을 만듭니다.

이 정책은 파노라마 관리자가 네트워크의 관련 NGFW로 푸시하는 경우에만 적용 됩니다.

IT 네트워크에는 동적 IP 주소가 있을 수 있습니다. 따라서 해당 서브넷에 대해 정책은 IP 주소가 아닌 FQDN (DNS 이름)을 기반으로 해야 합니다. IoT 용 Defender는 역방향 조회를 수행 하 고 동적 IP 주소를 사용 하는 장치를 구성 된 시간 수 마다 FQDN (DNS 이름)과 일치 시킵니다.

또한 Defender for IoT는 관련 파노라마 사용자에 게 전자 메일을 보내 IoT 용 Defender에서 만든 새 정책이 승인 대기 중임을 알립니다. 아래 그림은 IoT-Panorama 통합 아키텍처용 Defender를 나타냅니다.

:::image type="content" source="media/integration-paloalto/structure.png" alt-text="CyberX-파노라마 통합 아키텍처":::

## <a name="create-panorama-blocking-policies-in-defender-for-iot-configuration"></a>IoT 구성에 대해 Defender에서 파노라마 차단 정책 만들기

### <a name="to-configure-dns-lookup"></a>DNS 조회를 구성 하려면

1. 왼쪽 창에서 **시스템 설정** 을 선택 합니다.

1. **시스템 설정** 창에서 **DNS 설정** 을 선택 :::image type="icon" source="media/integration-paloalto/settings.png"::: 합니다.

   :::image type="content" source="media/integration-paloalto/configuration.png" alt-text="DNS 설정을 구성 합니다.":::

1. **DNS 설정 편집** 대화 상자에서 다음 매개 변수를 설정 합니다.

   - **상태**: DNS 확인 자의 상태입니다.

   - **Dns 서버 주소**: 네트워크 DNS 서버의 IP 주소 또는 FQDN을 입력 합니다.
   - **Dns 서버 포트**: dns 서버를 쿼리 하는 데 사용 되는 포트를 입력 합니다.
   - **서브넷**: 동적 IP 주소 서브넷 범위를 설정 합니다. IoT 용 Defender가 현재 FQDN 이름과 일치 하도록 DNS 서버의 IP 주소를 조회 하는 범위입니다.
   - **역방향 조회 예약**: 다음과 같이 일정 옵션을 정의 합니다.
     - 특정 시간 기준: 매일 역방향 조회를 수행 하는 경우를 지정 합니다.
     - 고정 간격 (시간) 기준: 역방향 조회를 수행 하는 빈도를 설정 합니다.
   - **레이블 수**: IoT for IoT에서 네트워크 IP 주소를 장치 fqdn으로 자동으로 확인 하도록 지시 합니다. <br />DNS FQDN 확인을 구성 하려면 표시할 도메인 레이블 수를 추가 합니다. 왼쪽에서 오른쪽으로 최대 30 자까지 표시 됩니다.
1. **SAVE**(저장)를 선택합니다.
1. DNS 설정이 올바른지 확인 하려면 **조회 테스트** 를 선택 합니다. 테스트를 통해 DNS 서버 IP 주소 및 DNS 서버 포트가 올바르게 설정 되었는지 확인 합니다.

### <a name="to-configure-a-forwarding-rule-to-blocks-suspected-traffic-with-the-palo-alto-firewall"></a>Palo Alto 방화벽을 사용 하 여 의심 되는 트래픽을 차단 하도록 전달 규칙을 구성 하려면

1. 왼쪽 창에서 **전달** 을 선택 합니다. 전달 창이 표시 됩니다.

   :::image type="content" source="media/integration-paloalto/forward.png" alt-text="전달 화면입니다.":::

1. **전달** 창에서 **전달 규칙 만들기** 를 선택 합니다.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="전달 규칙 만들기":::

1. Palo Alto 파노라마 전달 규칙을 구성 하려면:

   표준 규칙 매개 변수를 정의 하 고 **작업** 드롭다운 상자에서 **Palo Alto 파노라마로 보내기를** 선택 합니다. 동작 세부 정보 창이 나타납니다.

   :::image type="content" source="media/integration-paloalto/details.png" alt-text="작업 선택":::

1. 작업 창에서 다음 매개 변수를 설정 합니다.

   - **호스트**: 파노라마 서버 IP 주소를 입력 합니다.

   - **포트**: 파노라마 서버 포트를 입력 합니다.
   - **사용자 이름**: 파노라마 서버 사용자 이름을 입력 합니다.
   - **암호**: 파노라마 서버 암호를 입력 합니다.
   - **보고서 주소**: 다음과 같이 차단이 실행 되는 방법을 정의 합니다.
   
     - **Ip 주소로**: 항상 ip 주소에 따라 파노라마에 차단 정책을 만듭니다.
     
     - **Fqdn 또는 Ip 주소** 를 기준으로: fqdn (있는 경우)을 기반으로 하 고, 그렇지 않은 경우 IP 주소를 사용 하 여 차단 정책을 만듭니다.
     
   - **전자 메일**: 정책 알림 전자 메일에 대 한 전자 메일 주소를 설정 합니다.
     > [!NOTE]
     > IoT 용 Defender에서 메일 서버를 구성 했는지 확인 합니다. 전자 메일 주소를 입력 하지 않은 경우 Defender for IoT는 알림 전자 메일을 보내지 않습니다.
   - **경고 검색 시 DNS 조회 실행 (확인란)**: 보고서 주소에 By FQDN 또는 IP 주소 옵션이 설정 된 경우 이 확인란은 기본적으로 선택 되어 있습니다. IP 주소만 설정 된 경우에는이 옵션을 사용할 수 없습니다.
   - **구성**: 다음 옵션을 설정 하 여 Palo Alto 파노라마의 의심 스러운 원본을 차단할 수 있습니다.
   
     - **잘못 된 함수 코드 차단**: 프로토콜 위반-잘못 된 필드 값이 ICS, 프로토콜 사양 (잠재적 악용)을 위반 합니다.
     
     - **권한 없는 PLC 프로그래밍/펌웨어 업데이트 차단**: 권한이 없는 PLC 변경 내용입니다.
     
     - **권한 없는 PLC stop**: PLC stop (가동 중지 시간)을 차단 합니다.
     
     - **맬웨어 관련 경고 차단**: 산업용 맬웨어 시도 (TRITON, NotPetya 등)를 차단 합니다. **자동 차단** 옵션을 선택할 수 있습니다. 이 경우 차단이 자동으로 즉시 실행 됩니다.
     
     - **무단 검사 차단**: 무단 검사 (잠재적 정찰).
     
1. **제출** 을 선택합니다.

### <a name="to-block-the-suspicious-source"></a>의심 스러운 소스를 차단 하려면

1. **경고** 창에서 Palo Alto 통합과 관련 된 경고를 선택 합니다. **경고의 세부 정보** 대화 상자가 나타납니다.

   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="경고 세부 정보":::        

1. 의심 스러운 원본을 자동으로 차단 하려면 **소스 차단** 을 선택 합니다.

1. **확인** 을 선택 합니다. 대화 상자에서 **확인을 선택 합니다.**

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="지":::

## <a name="next-steps"></a>다음 단계

[경고 정보를 전달](how-to-forward-alert-information-to-partners.md)하는 방법을 알아봅니다.
