---
title: Cisco ISE pxGrid 통합에 대한 정보
description: Cisco ISE pxGrid를 통해 Defender for IoT의 기능을 브리징하여 보안 팀에 엔터프라이즈 에코시스템에 대한 전례 없는 디바이스 가시성을 제공합니다.
ms.date: 12/28/2020
ms.topic: how-to
ms.openlocfilehash: 00151f2e407c65d024f3bd59bdaa85a08ae677f4
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113019020"
---
# <a name="about-the-cisco-ise-pxgrid-integration"></a>Cisco ISE pxGrid 통합에 대한 정보

Defender for IoT는 중요한 국가 인프라를 방어한 실적을 가진 블루 팀 전문가가 빌드한 유일한 ICS 및 IoT 사이버 보안 플랫폼과 특허받은 ICS 인식 위협 분석 및 기계 학습을 갖춘 유일한 플랫폼을 제공합니다. Defender for IoT는 다음을 제공합니다.

- ICS 디바이스, 취약성, 알려진 위협, 제로 데이 위협에 대한 즉각적인 인사이트.

- OT 프로토콜, 디바이스, 애플리케이션, 해당 동작에 대한 심층적인 ICS 인식 정보.

- 독점 분석을 통해 대상 ICS 공격의 가장 가능성이 높은 경로를 예측하는 자동화된 ICS 위협 모델링 기술.

## <a name="powerful-device-visibility-and-control"></a>강력한 디바이스 가시성 및 제어

Defender for IoT의 Cisco ISE pxGrid와의 통합을 통해 OT 환경에 대한 새로운 수준의 중앙 집중화된 가시성, 모니터링, 제어 기능을 제공합니다.

이 브리지된 플랫폼을 통해 이전에는 연결할 수 없었던 ICS 및 IIoT 디바이스에 대한 자동 디바이스 가시성과 보호 기능을 사용할 수 있습니다.

### <a name="ics-and-iiot-device-visibility-comprehensive-and-deep"></a>포괄적이고 심층적인 ICS 및 IIoT 디바이스 가시성

특허받은 Defender for IoT 기술은 엔터프라이즈 데이터에 대한 포괄적이고 심층적인 ICS 및 IIoT 디바이스 검색과 인벤토리 관리를 보장합니다.

디바이스 유형, 제조업체, 오픈 포트, 일련 번호, 펌웨어 수정 사항, IP 주소, MAC 주소 데이터 등은 실시간으로 업데이트됩니다. Defender for IoT는 중요한 엔터프라이즈 데이터 원본과 통합하여 이 기준에서 가시성, 검색, 분석을 더욱 향상할 수 있습니다. 예를 들어 CMDB, DNS, 방화벽, Web API가 있습니다.

또한 Defender for IoT 플랫폼은 수동 모니터링과 선택 사항인 선택적 프로빙을 결합하여 산업 및 중요 인프라 조직에서 디바이스에 대한 가장 정확하고 세부적인 인벤토리를 제공합니다.

### <a name="bridged-capabilities"></a>브리지 기능

Cisco ISE pxGrid를 통해 해당 기능을 브리징하여 보안 팀에 엔터프라이즈 에코시스템에 대한 전례 없는 디바이스 가시성을 제공합니다.

Cisco ISE pxGrid와 원활하고 강력한 통합을 통해 모든 OT 디바이스가 검색 가능하고 디바이스 정보가 누락되지 않도록 합니다.

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoint-categories.png" alt-text="엔드포인트 범주 OUI 샘플입니다.":::

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoints.png" alt-text="시스템의 샘플 엔드포인트":::  

:::image type="content" source="media/integration-cisco-isepxgrid-integration/attributes.png" alt-text="시스템에 있는 특성의 스크린샷.":::  

### <a name="use-case-coverage-ise-policies-based-on-defender-for-iot-attributes"></a>사용 사례 범위: Defender for IoT 특성 기반 ISE 정책

Defender for IoT 딥 러닝을 기반으로 하는 강력한 ISE 정책을 사용하여 ICS 및 IoT 사용 사례 요구 사항을 처리합니다.

정책을 사용하면 보안 주기를 종료하고 실시간으로 네트워크가 규정을 준수하도록 할 수 있습니다.

예를 들어 고객은 Defender for IoT ICS 및 IoT 특성을 사용하여 다음과 같은 사용 사례를 다루는 정책을 만들 수 있습니다.

- 허용되는 특성을 기준으로 중요한 영역(예: Rockwell Automation PLC의 특정 펌웨어 버전)에 대해 알려진 디바이스 및 승인된 디바이스를 허용하는 권한 부여 정책을 만듭니다.

- 비 OT 영역에서 ICS 디바이스가 검색되면 보안 팀에 알립니다.

- 만료된 또는 비규격 공급업체를 통해 머신을 재구성합니다.

- 필요에 따라 디바이스를 격리하고 차단합니다.

- 알려진 취약성이 있는 펌웨어를 실행하는 PLC 또는 RTU에 대한 보고서를 생성합니다(CVE).

### <a name="about-this-article"></a>이 문서의 내용

이 문서에서는 Defender for IoT가 OT 특성을 Cisco ISE에 삽입하도록 pxGrid 및 Defender for IoT를 설정하는 방법을 설명합니다.

### <a name="getting-more-information"></a>추가 정보 보기

Cisco ISE pxGrid 통합 요구 사항에 대한 자세한 내용은 <https://www.cisco.com/c/en/us/products/security/pxgrid.html> 페이지를 참조하세요.

## <a name="integration-system-requirements"></a>통합 시스템 요구 사항

이 문서에서는 통합 시스템 요구 사항을 설명합니다.

Defender for IoT 요구 사항

- Defender for IoT 버전 2.5

Cisco 요구 사항

- pxGrid 버전 2.0

- Cisco ISE 버전 2.4

네트워크 요구 사항

- Defender for IoT 어플라이언스에서 Cisco ISE 관리 인터페이스에 액세스할 수 있는지 확인합니다.

- 기업의 모든 Defender for IoT 어플라이언스에 대한 CLI 액세스 권한이 있는지 확인합니다.

> [!NOTE]
> MAC 주소가 있는 디바이스만 Cisco ISE pxGrid와 동기화됩니다.

## <a name="cisco-ise-pxgrid-setup"></a>Cisco ISE pxGrid 설정

이 문서에서는 다음과 같이 방법을 설명합니다.

- pxGrid와의 통신 설정

- 엔드포인트 디바이스 토픽 구독

- 인증서 생성

- pxGrid 설정 정의

## <a name="set-up-communication-with-pxgrid"></a>pxGrid와의 통신 설정

이 문서에서는 pxGrid와의 통신을 설정하는 방법을 설명합니다.

통신을 설정하려면 다음을 수행합니다.

1. Cisco ISE에 로그인합니다.

1. **관리**>**시스템** 및 **배포** 를 선택합니다.

1. 필요한 노드를 선택합니다. 일반 설정 탭에서 **pxGrid 확인란** 을 선택합니다.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/pxgrid.png" alt-text="pxGrid 확인란이 선택되었는지 확인합니다.":::

1. **프로파일링 구성** 탭을 선택합니다.

1. **pxGrid 확인란** 을 선택합니다. 설명을 추가합니다.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/profile-configuration.png" alt-text="설명 추가의 스크린샷":::

## <a name="subscribe-to-the-endpoint-device-topic"></a>엔드포인트 디바이스 토픽 구독

ISE pxGrid 노드가 엔드포인트 디바이스 토픽을 구독하는지 확인합니다. **관리**>**pxGrid 서비스**>**웹 클라이언트** 로 이동합니다. 여기서 ISE가 엔드포인트 디바이스 토픽을 구독하는지 확인할 수 있습니다.

## <a name="generate-certificates"></a>인증서 생성

이 문서에서는 인증서를 만드는 방법을 설명합니다.

인증서를 생성하려면 다음을 수행합니다.

1. **관리** > **pxGrid 서비스** 를 선택한 다음 **인증서** 를 선택합니다.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/certificates.png" alt-text="인증서를 생성하려면 인증서 탭을 선택합니다.":::

1. **원하는 대상** 필드에서 **인증서 서명 요청 없이 단일 인증서 생성** 을 선택합니다.

1. 남은 필드를 입력하고 **만들기** 를 선택합니다.

1. 클라이언트 인증서 키와 서버 인증서를 만든 다음 java 키 저장소 형식으로 변환합니다. 이를 네트워크의 각 Defender for IoT 센서로 복사해야 합니다.

## <a name="define-pxgrid-settings"></a>pxGrid 설정 정의

설정을 정의하려면 다음을 수행합니다.

1. **관리** > **pxGrid 서비스** 를 선택한 다음 **설정** 을 선택합니다.

1. **새 인증서 기반 계정을 자동으로 승인** 하고 **암호 기반 계정을 만들 수 있도록** 설정할 수 있습니다.

  :::image type="content" source="media/integration-cisco-isepxgrid-integration/allow-these.png" alt-text="두 확인란을 모두 선택했는지 확인합니다.":::

## <a name="set-up-the-defender-for-iot-appliances"></a>Defender for IoT 어플라이언스 설정

이 문서에서는 pxGrid와 통신하도록 Defender for IoT 어플라이언스를 설정하는 방법을 설명합니다. Cisco ISE에 데이터를 삽입하는 모든 Defender for IoT 어플라이언스에서 구성을 수행해야 합니다.

어플라이언스를 설정하려면 다음을 수행합니다.

1. 센서 CLI에 로그인합니다.

1. 이전에 센서에서 만든 `trust.jks`를 복사합니다. `/var/cyberx/properties/`로 복사합니다.

1. 편집 `/var/cyberx/properties/pxgrid.properties`:

    1. 키와 트러스트를 추가하고 파일 이름 및 암호를 저장합니다.

    2. pxGrid 인스턴스의 호스트 이름을 추가합니다.

    3. `Enabled=true`

1. 어플라이언스를 다시 시작합니다.

1. 데이터를 삽입할 기업의 각 어플라이언스에서 해당 단계들을 반복합니다.

## <a name="view-and-manage-detections-in-cisco-ise"></a>Cisco ISE에서 검색 보기 및 관리

1. 엔드포인트 검색은 ISE 컨텍스트 **가시성** > **엔드포인트** 탭에 표시됩니다.

1. **정책** > **프로파일링** > **추가** > **규칙** >  **+ 조건** >  **새 조건 만들기** 를 선택합니다.

1. **특성** 을 선택하고 IOT 디바이스 사전을 사용하여 삽입된 특성을 기반으로 프로파일링 규칙을 빌드합니다. 다음과 같은 특성이 삽입됩니다.

    - 디바이스 유형

    - 하드웨어 수정 버전

    - IP 주소

    - MAC 주소

    - 이름

    - Product ID

    - 프로토콜

    - 일련 번호

    - 소프트웨어 수정 버전

    - Vendor

MAC 주소가 있는 디바이스만 동기화됩니다.

## <a name="troubleshooting-and-logs"></a>문제 해결 및 로그

로그는 다음 위치에서 찾을 수 있습니다.

- `/var/cyberx/logs/pxgrid.log`

- `/var/cyberx/logs/core.log`

## <a name="next-steps"></a>다음 단계

[경고 정보를 전달](how-to-forward-alert-information-to-partners.md)하는 방법을 알아봅니다.
