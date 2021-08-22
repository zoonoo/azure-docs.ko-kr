---
title: Palo Alto 통합
description: Defender for IoT는 연속 ICS 위협 모니터링 플랫폼을 Palo Alto의 차세대 방화벽과 통합하여 중요한 위협 요소를 더 빠르고 효율적으로 차단하고 있습니다.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 49c00f9ad7cc1980a8690e35ed19351e20417c6a
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018884"
---
# <a name="about-the-palo-alto-integration"></a>Palo Alto 통합 정보

Defender for IoT는 연속 ICS 위협 모니터링 플랫폼을 Palo Alto의 차세대 방화벽과 통합하여 중요한 위협 요소를 더 빠르고 효율적으로 차단하고 있습니다.

다음 통합 유형을 사용할 수 있습니다.

- 자동 차단 옵션: 직접 Defender for IoT-Palo Alto 통합

- 중앙 관리 시스템 차단에 대한 권장 사항 보내기: Defender for IoT-Panorama 통합

## <a name="configure-immediate-blocking-by-specified-palo-alto-firewall"></a>지정된 Palo Alto 방화벽을 통해 즉각적인 차단 구성

맬웨어 관련 경고와 같은 중요한 경우에는 자동 차단을 사용하도록 설정할 수 있습니다. 이 작업은 특정 Palo Alto 방화벽으로 직접 차단 명령을 보내는 Defender for IoT에서 전달 규칙을 구성하여 수행됩니다.

Defender for IoT는 심각한 위협을 식별하는 경우 감염된 소스를 차단하는 옵션이 포함된 경고를 보냅니다. 경고 세부 정보에서 **소스 차단** 을 선택하면 전달 규칙이 활성화되어 지정된 Palo Alto 방화벽으로 차단 명령이 전송됩니다.

구성하려면

1. 왼쪽 창에서 **전달** 을 선택합니다.

   :::image type="content" source="media/integration-paloalto/forwarding.png" alt-text="전달 경고 화면":::

1. **전달 규칙 만들기** 를 선택합니다.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="전달 규칙 만들기":::

1. Palo Alto NGFW 전달 규칙을 구성하려면  
 
   - 표준 규칙 매개 변수를 정의하고 **작업** 드롭다운 상자에서 **Palo Alto NGFW로 보내기** 를 선택합니다.
   
   :::image type="content" source="media/integration-paloalto/edit.png" alt-text="전달 규칙 편집":::

1. 작업 창에서 다음 매개 변수를 설정합니다.

   - **호스트**: NGFW 서버 IP 주소를 입력합니다.
   - **포트**: NGFW 서버 포트를 입력합니다.
   - **사용자 이름**: NGFW 서버 사용자 이름을 입력합니다.
   - **암호**: NGFW 서버 암호를 입력합니다.
   - **구성**: 다음 옵션을 설정하여 Palo Alto 방화벽에서 의심스러운 소스를 차단할 수 있도록 합니다.
     - **잘못된 함수 코드 차단**: 프로토콜 위반 - ICS 프로토콜 사양을 위반하는 잘못된 필드 값(잠재적 악용)
     - **권한이 없는 PLC 프로그래밍/펌웨어 업데이트 차단**: 권한이 없는 PLC 변경
     - **권한이 없는 PLC 중지 차단**: PLC 중단(가동 중지 시간)
     - **맬웨어 관련 경고 차단**: 산업용 맬웨어 시도(TRITON, NotPetya 등)를 차단합니다. **자동 차단** 옵션을 선택할 수 있습니다. 이 경우 차단이 자동으로 즉시 실행됩니다.
     - **권한이 없는 검색 차단**: 권한이 없는 검색(잠재적 정찰)
     
1. **제출** 을 선택합니다.

의심스러운 소스를 차단하려면 

1. **경고** 창에서 Palo Alto 통합과 관련된 경고를 선택합니다. **경고 세부 정보** 대화 상자가 표시됩니다.
   
   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="경고 세부 정보":::

1. 의심스러운 소스를 자동으로 차단하려면 **소스 차단** 을 선택합니다. **확인** 대화 상자가 표시됩니다.

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="확인 화면에서 차단을 확인합니다.":::

1. **확인** 대화 상자에서 **확인** 을 선택합니다. 의심스러운 소스는 Palo Alto 방화벽에 의해 차단됩니다.

## <a name="sending-blocking-policies-to-palo-alto-panorama"></a>Palo Alto Panorama로 차단 정책 보내기

Defender for IoT 및 Palo Alto Networks에는 Palo Alto Networks NMS, Panorama에 새 정책을 자동으로 만드는 기성 통합이 있습니다. 이 통합에는 Panorama Administrator의 확인이 필요하며 자동 차단은 허용되지 않습니다.

이 통합은 다음과 같은 인시던트에 사용됩니다.

- **권한이 없는 PLC 변경:** 디바이스의 사다리 논리 또는 펌웨어에 대한 업데이트입니다. 합법적인 작업이나 디바이스를 손상시키려는 시도를 나타낼 수 있습니다. 이러한 손상은 RAT(원격 액세스 트로이) 또는 물리적 프로세스를 야기하는 요소(예: 회전 터빈)와 같은 악성 코드를 삽입하여 안전하지 않은 방식으로 작동할 수 있습니다.

- **프로토콜 위반:** 프로토콜 사양을 위반하는 패킷 구조 또는 필드 값입니다. 이것은 잘못 구성된 애플리케이션 또는 디바이스를 손상시키려는 악의적 시도를 나타낼 수 있습니다. 대상 디바이스에서 버퍼 오버플로 조건을 발생시키는 경우를 예로 들 수 있습니다.

- **PLC 중지:** 디바이스가 작동을 중지하여 PLC가 제어하는 실제 프로세스에 위험을 발생시키는 명령입니다.

- **ICS 네트워크에서 발견된 산업용 맬웨어:** 기본 프로토콜을 사용하여 ICS 디바이스를 조작하는 맬웨어(예: TRITON 및 Industroyer) 또한 Defender for IoT는 ICS 및 SCADA 환경으로 수평 이동한 IP 맬웨어(예: Conficker, WannaCry 및 NotPetya)도 검색합니다.

- **맬웨어 검색:** 공격 전 단계에서 시스템 구성에 대한 데이터를 수집하는 정찰 도구입니다. 예를 들어 Havex 트로이 목마는 Windows 기반 SCADA 시스템에서 ICS 디바이스와 통신하는 데 사용하는 표준 프로토콜인 OPC를 사용하는 디바이스를 산업용 네트워크에서 검색합니다.

## <a name="the-process"></a>프로세스

Defender for IoT가 미리 구성된 사용 사례를 검색하면 **소스 차단** 단추가 경고에 추가됩니다. 그런 다음, **CyberX** 사용자가 **소스 차단** 단추를 선택하면 Defender for IoT는 미리 정의된 전달 규칙을 전송하여 Panorama에서 정책을 만듭니다.

이 정책은 Panorama 관리자가 네트워크의 관련 NGFW로 푸시하는 경우에만 적용됩니다.

IT 네트워크에는 동적 IP 주소가 있을 수 있습니다. 따라서 해당 서브넷에 대해 정책은 IP 주소가 아닌 FQDN(DNS 이름)을 기준으로 해야 합니다. Defender for IoT는 역방향 조회를 수행하고 동적 IP 주소를 사용하는 디바이스를 구성된 시간마다 FQDN(DNS 이름)과 일치시킵니다.

또한 Defender for IoT는 관련 Panorama 사용자에게 메일을 보내 Defender for IoT에서 만든 새 정책이 승인 대기 중임을 알립니다. 아래 그림은 Defender for IoT-Panorama 통합 아키텍처를 나타냅니다.

:::image type="content" source="media/integration-paloalto/structure.png" alt-text="CyberX-Panorama 통합 아키텍처":::

## <a name="create-panorama-blocking-policies-in-defender-for-iot-configuration"></a>Defender for IoT 구성에서 Panorama 차단 정책 만들기

### <a name="to-configure-dns-lookup"></a>DNS 조회를 구성하려면

1. 왼쪽 창에서 **시스템 설정** 을 선택합니다.

1. **시스템 설정** 창에서 **DNS 설정** :::image type="icon" source="media/integration-paloalto/settings.png":::를 선택합니다.

   :::image type="content" source="media/integration-paloalto/configuration.png" alt-text="DNS 설정을 구성합니다.":::

1. **DNS 설정 편집** 대화 상자에서 다음 매개 변수를 설정합니다.

   - **상태**: DNS 확인자의 상태입니다.

   - **DNS 서버 주소**: 네트워크 DNS 서버의 IP 주소 또는 FQDN을 입력합니다.
   - **DNS 서버 포트**: DNS 서버를 쿼리하는 데 사용되는 포트를 입력합니다.
   - **서브넷**: 동적 IP 주소 서브넷 범위를 설정합니다. Defender for IoT가 현재 FQDN 이름과 일치하도록 DNS 서버의 IP 주소를 역방향으로 조회하는 범위입니다.
   - **역방향 조회 예약**: 다음과 같이 일정 옵션을 정의합니다.
     - 특정 시간 기준: 매일 역방향 조회를 수행할 시간을 지정합니다.
     - 고정 간격(시간) 기준: 역방향 조회를 수행할 빈도를 설정합니다.
   - **레이블 수**: Defender for IoT에 자동으로 네트워크 IP 주소를 디바이스 FQDN으로 확인하도록 지시합니다. <br />DNS FQDN 확인을 구성하려면 표시할 도메인 레이블 수를 추가합니다. 왼쪽에서 오른쪽으로 최대 30자까지 표시됩니다.
1. **SAVE**(저장)를 선택합니다.
1. DNS 설정이 올바른지 확인하려면 **조회 테스트** 를 선택합니다. 테스트를 통해 DNS 서버 IP 주소 및 DNS 서버 포트가 올바르게 설정되었는지 확인합니다.

### <a name="to-configure-a-forwarding-rule-to-blocks-suspected-traffic-with-the-palo-alto-firewall"></a>Palo Alto 방화벽을 사용하여 의심되는 트래픽을 차단하도록 전달 규칙을 구성하려면

1. 왼쪽 창에서 **전달** 을 선택합니다. 전달 창이 나타납니다.

   :::image type="content" source="media/integration-paloalto/forward.png" alt-text="전달 화면":::

1. **전달** 창에서 **전달 규칙 만들기** 를 선택합니다.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="전달 규칙 만들기":::

1. Palo Alto Panorama 전달 규칙을 구성하려면

   표준 규칙 매개 변수를 정의하고 **작업** 드롭다운 상자에서 **Palo Alto Panorama로 보내기** 를 선택합니다. 작업 세부 정보 창이 나타납니다.

   :::image type="content" source="media/integration-paloalto/details.png" alt-text="작업 선택":::

1. 작업 창에서 다음 매개 변수를 설정합니다.

   - **호스트**: Panorama 서버 IP 주소를 입력합니다.

   - **포트**: Panorama 서버 포트를 입력합니다.
   - **사용자 이름**: Panorama 서버 사용자 이름을 입력합니다.
   - **암호**: Panorama 서버 암호를 입력합니다.
   - **보고서 주소**: 다음과 같이 차단이 실행되는 방법을 정의합니다.
   
     - **IP 주소**: 항상 IP 주소에 따라 파노라마에 차단 정책을 만듭니다.
     
     - **FQDN 또는 IP 주소**: FQDN이 있는 경우 이에 따라 파노라마에 차단 정책을 만들고, 그렇지 않으면 IP 주소를 따라 만듭니다.
     
   - **메일**: 정책 알림 메일에 대한 메일 주소를 설정합니다.
     > [!NOTE]
     > Defender for IoT에서 메일 서버를 구성했는지 확인합니다. 메일 주소를 입력하지 않으면 Defender for IoT에서 알림 메일을 보내지 않습니다.
   - **경고 검색 시 DNS 조회 실행(확인란)** : 보고서 주소에서 FQDN 기준 또는 IP 주소 옵션이 설정된 경우입니다. 이 확인란은 기본적으로 선택되어 있습니다. IP 주소만 설정된 경우에는 이 옵션을 사용할 수 없습니다.
   - **구성**: 다음 옵션을 설정하여 Palo Alto Panorama에서 의심스러운 소스를 차단할 수 있도록 합니다.
   
     - **잘못된 함수 코드 차단**: 프로토콜 위반 - ICS 프로토콜 사양을 위반하는 잘못된 필드 값(잠재적 악용)
     
     - **권한이 없는 PLC 프로그래밍/펌웨어 업데이트 차단**: 권한이 없는 PLC 변경
     
     - **권한이 없는 PLC 중지 차단**: PLC 중단(가동 중지 시간)
     
     - **맬웨어 관련 경고 차단**: 산업용 맬웨어 시도(TRITON, NotPetya 등)를 차단합니다. **자동 차단** 옵션을 선택할 수 있습니다. 이 경우 차단이 자동으로 즉시 실행됩니다.
     
     - **권한이 없는 검색 차단**: 권한이 없는 검색(잠재적 정찰)
     
1. **제출** 을 선택합니다.

### <a name="to-block-the-suspicious-source"></a>의심스러운 소스를 차단하려면

1. **경고** 창에서 Palo Alto 통합과 관련된 경고를 선택합니다. **경고 세부 정보** 대화 상자가 표시됩니다.

   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="경고 세부 정보":::        

1. 의심스러운 소스를 자동으로 차단하려면 **소스 차단** 을 선택합니다.

1. **확인** 대화 상자에서 **확인** 을 선택합니다.

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="확인":::

## <a name="next-steps"></a>다음 단계

[경고 정보를 전달](how-to-forward-alert-information-to-partners.md)하는 방법을 알아봅니다.
