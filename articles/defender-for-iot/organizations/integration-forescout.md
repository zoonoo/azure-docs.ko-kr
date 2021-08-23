---
title: Forescout 통합에 대한 정보
description: Forescout 플랫폼과의 Azure Defender for IoT 통합은 IoT 및 OT 환경에 대한 새로운 수준의 중앙 집중화된 가시성, 모니터링, 제어 기능을 제공합니다.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 07e5187970d193502b95b49c5517a8e3824767be
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113019007"
---
# <a name="about-the-forescout-integration"></a>Forescout 통합에 대한 정보

Azure Defender for IoT는 중요한 국가 인프라에 보안을 제공한 이력이 있는 블루 팀 전문가가 빌드한 ICS 및 IoT 사이버 보안 플랫폼을 제공합니다. Defender for IoT는 특허 받은 ICS 인식 위협 분석 및 기계 학습을 사용하는 유일한 플랫폼입니다. Defender for IoT는 다음을 제공합니다.

- 특성에 대한 광범위한 세부 정보와 함께 디바이스 환경인 ICS에 대한 즉각적인 인사이트.
- OT 프로토콜, 디바이스, 애플리케이션, 해당 동작에 대한 심층적인 ICS 인식 정보.
- 취약성, 알려진 위협 및 제로 데이 위협에 대한 즉각적인 인사이트.
- 독점 분석을 통해 대상 ICS 공격의 가장 가능성이 높은 경로를 예측하는 자동화된 ICS 위협 모델링 기술.

Defender for IoT와 Forescout 플랫폼의 통합은 IoT 및 OT 환경에 대한 새로운 수준의 중앙 집중화된 가시성, 모니터링, 제어를 제공합니다.

이 브리지된 플랫폼을 통해 이전에는 연결할 수 없었던 ICS 디바이스와 사일로된 워크플로에 대한 자동 디바이스 가시성 및 관리를 수행할 수 있습니다.

이 통합은 SOC 분석가에게 산업 환경에 배포된 OT 프로토콜에 대한 다단계 가시성을 제공합니다. Azure Defender for IoT 독점 기술을 기반으로 하는 펌웨어, 디바이스 유형, 운영 체제, 위험 분석 점수 등에 대한 세부 정보도 얻을 수 있습니다.

> [!Note]
> CyberX에 대한 참조는 Azure Defender for IoT를 확인하세요.
## <a name="devices"></a>디바이스

### <a name="device-visibility-and-management"></a>디바이스 가시성 및 관리

디바이스의 인벤토리는 Defender for IoT 플랫폼에서 검색된 중요한 특성을 통해 강화됩니다. 이렇게 하면 다음을 확인할 수 있습니다.

- 단일 창에서 OT 디바이스 환경에 대한 포괄적이고 지속적인 가시성을 얻을 수 있습니다.
- OT 위험에 대한 실시간 인텔리전스를 얻을 수 있습니다.

:::image type="content" source="media/integration-forescout/forescout-device-inventory.png" alt-text="디바이스 인벤토리":::

:::image type="content" source="media/integration-forescout/forescout-device-details.png" alt-text="디바이스 세부 정보":::

### <a name="device-control"></a>디바이스 제어

Forescout 통합을 사용하면 산업 및 중요 인프라 조직이 사이버 위협에 대한 검색, 조사, 조치를 수행하는 데 필요한 시간을 줄일 수 있습니다.

- Azure Defender for IoT의 OT 디바이스 인텔리전스를 사용하여 Forescout 정책 작업을 트리거함으로써 보안 주기를 닫습니다. 예를 들어 특정 프로토콜이 검색되거나 펌웨어 세부 정보가 변경될 때 자동으로 SOC 관리자에게 경고 메일을 보낼 수 있습니다.

- Defender for IoT 정보를 모니터링, 인시던트 관리, 디바이스 제어를 감독하는 다른 *Forescout eyeExtended* 모듈과 상호 연결합니다.

## <a name="system-requirements"></a>시스템 요구 사항

- Azure Defender for IoT 버전 2.4 이상
- Forescout 버전 8.0 이상
- Azure Defender for IoT 플랫폼에 대한 *Forescout eyeExtend* 모듈의 라이선스.

### <a name="getting-more-forescout-information"></a>Forescout에 대한 자세한 정보

Forescout 플랫폼에 대한 자세한 내용은 [Forescout 리소스 센터](https://www.forescout.com/company/resources/#resource_filter_group)를 참조하세요.

## <a name="system-setup"></a>시스템 설정

이 문서에서는 Defender for IoT 플랫폼과 Forescout 플랫폼 간의 통신을 설정하는 방법을 설명합니다.

### <a name="set-up-the-defender-for-iot-platform"></a>Defender for IoT 플랫폼 설정

Defender for IoT에서 Forescout로 통신하려면 Defender for IoT에서 액세스 토큰을 생성합니다.

액세스 토큰을 통해 외부 시스템은 Defender for IoT에서 검색된 데이터에 액세스하고, 외부 REST API를 사용하여 SSL 연결을 통해 해당 데이터에 대한 작업을 수행할 수 있습니다. Azure Defender for IoT REST API에 액세스하기 위해 액세스 토큰을 생성할 수 있습니다.

토큰을 생성하려면 다음을 수행합니다.

1. Forescout에서 쿼리할 Defender for IoT 센서에 로그인합니다.

1. **시스템 설정** 을 선택한 다음 **일반** 섹션에서 **액세스 토큰** 을 선택합니다. **액세스 토큰** 대화 상자가 열립니다.
   :::image type="content" source="media/integration-forescout/generate-access-tokens-screen.png" alt-text="액세스 토큰":::
1. **액세스 토큰** 대화 상자에서 **새 토큰 생성** 을 선택합니다.
1. **새 액세스 토큰** 대화 상자에 토큰 설명을 입력합니다.
   :::image type="content" source="media/integration-forescout/new-forescout-token.png" alt-text="새 액세스 토큰":::
1. **다음** 을 선택합니다. 대화 상자에 해당 토큰이 표시됩니다. :::image type="content" source="media/integration-forescout/forescout-access-token-display-screen.png" alt-text="토큰 보기":::
   > [!NOTE]
   > 해당 토큰을 안전한 장소에 기록합니다. 이는 Forescout 플랫폼을 구성할 때 필요합니다.
1. **마침** 을 선택합니다.

   :::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="토큰 추가 완료":::

### <a name="set-up-the-forescout-platform"></a>Forescout 플랫폼 설정

Forescout 플랫폼을 Defender for IoT 센서와 통신하도록 설정할 수 있습니다.

설정하려면 다음을 수행합니다.

1. Forescout 플랫폼에 ‘CyberX용 Forescout eyeExtend 모듈’을 설치합니다.

1. CounterACT 콘솔에 로그인하고 **도구** 메뉴에서 **옵션** 을 선택합니다. **옵션** 대화 상자가 열립니다.

1. **모듈** 폴더로 이동하여 선택합니다.

1. **모듈** 창에서 **CyberX 플랫폼** 을 선택합니다. Defender for IoT 플랫폼 창이 열립니다.

   :::image type="content" source="media/integration-forescout/settings-for-module.png" alt-text="Azure Defender for IoT 모듈 설정":::

   다음 정보를 입력합니다.

   - **서버 주소** - Forescout 어플라이언스에서 쿼리할 Defender for IoT 센서의 IP 주소를 입력합니다.
   - **액세스 토큰** - Forescout 어플라이언스에 연결할 Defender for IoT 센서에 대해 생성된 액세스 토큰을 입력합니다. 토큰을 생성하려면 [Defender for IoT 플랫폼 설정](#set-up-the-defender-for-iot-platform)을 참조하세요.

1. **적용** 을 선택합니다.

Forescout 플랫폼이 다른 센서와 통신하도록 하려면 다음을 수행합니다.

1. 관련 Defender for IoT 센서에서 새 액세스 토큰을 만듭니다.

1. **Forescout 모듈** >  **CyberX 플랫폼** 대화 상자에서 다음을 수행합니다.

   - 표시된 정보를 삭제합니다.
   
   - 새 센서 IP 주소와 새 액세스 토큰 정보를 입력합니다.

### <a name="verify-communication"></a>통신 확인

Defender for IoT 및 Forescout을 구성한 후에는 Defender for IoT에서 센서의 액세스 토큰 대화 상자를 엽니다.

이 토큰에 대해 **사용됨** 필드에 **N/A** 가 표시되면 센서와 Forescout 어플라이언스 간의 연결이 작동하지 않습니다.

**사용됨** 은 이 토큰을 사용한 마지막 호출이 수신된 시간을 나타냅니다.

:::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="토큰을 받았는지 확인합니다.":::

## <a name="view-defender-for-iot-detections-in-forescout"></a>Forescout에서 Defender for IoT 검색 보기

디바이스의 특성을 보려면 다음을 수행합니다.

1. Forescout 플랫폼에 로그인한 후 **자산 인벤토리** 로 이동합니다.

1. **CyberX 플랫폼** 으로 이동합니다. Defender for IoT에서 검색한 OT 디바이스에 대해 다음과 같은 디바이스 특성이 표시됩니다.

   | 항목 | Description |
   |--|--|
   | Azure Defender for IoT에서 권한 부여 | 네트워크 학습 기간 동안 네트워크에서 Defender for IoT에 의해 검색된 디바이스입니다. |
   | 펌웨어 | 디바이스의 펌웨어 세부 정보입니다. 예를 들어, 모델 및 버전 세부 정보입니다. |
   | Name | 서버의 이름입니다. |
   | 운영 체제 | 디바이스의 운영 체제입니다. |
   | 유형 | 디바이스의 유형입니다. 예를 들어 PLC, Historian 또는 엔지니어링 스테이션입니다. |
   | Vendor | 디바이스의 공급업체입니다. 예를 들어 Rockwell Automation입니다. |
   | 위험 수준 | Defender for IoT에서 계산된 위험 수준입니다. |
   | 프로토콜 | 디바이스에 의해 생성된 트래픽에서 검색된 프로토콜입니다. |

:::image type="content" source="media/integration-forescout/device-firmware-attributes-in-forescout.png" alt-text="펌웨어 특성 보기.":::

:::image type="content" source="media/integration-forescout/vendor-attributes-in-forescout.png" alt-text="공급업체 특성 보기.":::

### <a name="viewing-more-details"></a>자세한 내용 보기

Defender for IoT에서 지시하는 디바이스에 대한 추가 디바이스 정보를 확인합니다. 예를 들어, Forescout 규정 준수 및 정책 정보를 확인합니다.

이를 수행하려면 **디바이스 인벤토리 호스트** 섹션에서 디바이스를 마우스 오른쪽 단추로 클릭합니다. 추가 정보를 포함하는 호스트 세부 정보 대화 상자가 열립니다.

:::image type="content" source="media/integration-forescout/details-dialog-box-in-forescout.png" alt-text="호스트 세부 정보":::

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>Forescout에서 Azure Defender for IoT 정책 만들기

Forescout 정책을 사용하여 Defender for IoT에서 검색된 디바이스의 제어 및 관리를 자동화할 수 있습니다. 예제:

- 특정 펌웨어 버전이 검색되면 SOC 관리자에게 자동으로 메일을 보냅니다.

- 다른 SIEM 통합과 같은 인시던트 및 보안 워크플로의 추가 처리를 위해 특정 Defender for IoT 검색 디바이스를 Forescout 그룹에 추가합니다.

조건 속성을 사용하여 Defender for IoT로 Forescout 사용자 지정 정책을 만듭니다.

Defender for IoT 속성에 액세스하려면 다음을 수행합니다.

1. **정책 조건** 대화 상자에서 **속성 트리** 로 이동합니다.

1. **속성 트리에서** **CyberX 플랫폼** 폴더를 확장합니다. Defender for IoT 다음 속성을 사용할 수 있습니다.

:::image type="content" source="media/integration-forescout/forescout-property-tree.png" alt-text="속성":::

## <a name="next-steps"></a>다음 단계

[경고 정보를 전달](how-to-forward-alert-information-to-partners.md)하는 방법을 알아봅니다.
