---
title: Forescout 통합 정보
titleSuffix: Azure Defender for IoT
description: Forescout 플랫폼과의 IoT 용 Azure Defender 통합은 IoT 및 OT의 중앙 집중화 된 가시성, 모니터링 및 제어를 제공 합니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: faa53c770d0d6caac471e770c80b4dfd5c5ff603
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98558028"
---
# <a name="about-the-forescout-integration"></a>Forescout 통합 정보

IoT 용 Azure Defender는 중요 한 국가 인프라를 방어 하는 트랙 레코드를 사용 하 여 파란색 팀 전문가가 만든 ICS 및 IoT 사이버 보안 플랫폼을 제공 합니다. IoT 용 Defender는 특허 된 ICS 인식 위협 분석 및 기계 학습을 사용 하는 유일한 플랫폼입니다. IoT 용 Defender는 다음을 제공 합니다.

- 특성에 대 한 다양 한 세부 정보를 사용 하 여 장치 환경을 ICS에 대 한 즉각적인 통찰력.
- ICS-프로토콜, 장치, 응용 프로그램 및 해당 동작에 대 한 심층 임베디드 정보를 인식 합니다.
- 취약성에 대 한 즉각적인 통찰력 및 알려져 있고 0 일 위협.
- 소유 분석을 통해 대상이 지정 된 ICS 공격의 가장 가능성이 높은 경로를 예측 하는 자동화 된 ICS 위협 모델링 기술입니다.

Forescout 플랫폼과의 IoT 용 Defender 통합은 IoT 및 OT에 대 한 새로운 수준의 중앙 집중식 가시성, 모니터링 및 제어를 제공 합니다.

이러한 브리지 된 플랫폼을 사용 하면 이전에 연결할 수 없는 ICS 장치와 사일로 워크플로를 자동으로 장치를 표시 하 고 관리할 수 있습니다.

통합은 산업 환경에 배포 된 OT 프로토콜에 대 한 다단계 가시성을 갖춘 SOC 분석가를 제공 합니다. 세부 정보는 IoT 기술용 독점 Azure Defender를 기반으로 하는 펌웨어, 장치 유형, 운영 체제 및 위험 분석 점수와 같은 항목에 사용할 수 있습니다.

> [!Note]
> CyberX에 대 한 참조는 IoT 용 Azure Defender를 참조 합니다.
## <a name="devices"></a>디바이스

### <a name="device-visibility-and-management"></a>장치 표시 유형 및 관리

장치의 인벤토리는 IoT 플랫폼용 Defender에 의해 검색 된 중요 한 특성으로 보강 됩니다. 이렇게 하면 다음과 같은 작업을 수행 합니다.

- 단일 창 유리에서 장치 환경에 대 한 포괄적이 고 지속적인 가시성을 얻을 수 있습니다.
- 위험에 대 한 실시간 인텔리전스를 얻습니다.

:::image type="content" source="media/integration-forescout/forescout-device-inventory.png" alt-text="장치 인벤토리":::

:::image type="content" source="media/integration-forescout/forescout-device-details.png" alt-text="디바이스 세부 정보":::

### <a name="device-control"></a>디바이스 제어

Forescout 통합을 사용 하면 산업 및 중요 인프라 조직이 사이버 위협에 대 한 검색, 조사 및 조치를 수행 하는 데 필요한 시간을 줄일 수 있습니다.

- IoT OT 용 Azure Defender 장치 인텔리전스를 사용 하 여 Forescout 정책 작업을 트리거하여 보안 주기를 닫습니다. 예를 들어 특정 프로토콜이 검색 되거나 펌웨어 세부 정보가 변경 될 때 자동으로 SOC 관리자에 게 경고 전자 메일을 보낼 수 있습니다.

- 모니터링, 인시던트 관리 및 장치 제어를 감독 하는 다른 *Forescout eyeExtended* 모듈과 IoT 정보에 대 한 Defender의 상관 관계를 바꿉니다.

## <a name="system-requirements"></a>시스템 요구 사항

- IoT 용 Azure Defender 버전 2.4 이상
- Forescout 버전 8.0 이상
- IoT 용 Azure Defender 플랫폼용 *Forescout eyeExtend* 모듈의 라이선스

### <a name="getting-more-forescout-information"></a>Forescout 정보 얻기

Forescout 플랫폼에 대 한 자세한 내용은 [Forescout 리소스 센터](https://www.forescout.com/company/resources/#resource_filter_group)를 참조 하세요.

## <a name="system-setup"></a>시스템 설정

이 문서에서는 IoT 플랫폼과 Forescout 플랫폼 간의 통신을 설정 하는 방법을 설명 합니다.

### <a name="set-up-the-defender-for-iot-platform"></a>IoT 플랫폼용 Defender를 설정 합니다.

IoT 용 Defender에서 Forescout로의 통신을 보장 하려면 IoT 용 Defender에서 액세스 토큰을 생성 합니다.

액세스 토큰을 통해 외부 시스템은 IoT 용 Defender에서 검색 된 데이터에 액세스 하 고, 외부 REST API를 사용 하 여 SSL 연결을 통해 해당 데이터로 작업을 수행할 수 있습니다. IoT 용 Azure Defender REST API에 액세스 하기 위해 액세스 토큰을 생성할 수 있습니다.

토큰을 생성 하려면:

1. Forescout에서 쿼리 하는 IoT 센서에 대해 Defender에 로그인 합니다.

1. **시스템 설정** 을 선택 하 고 **일반** 섹션에서 **액세스 토큰** 을 선택 합니다. **액세스 토큰** 대화 상자가 열립니다.
   :::image type="content" source="media/integration-forescout/generate-access-tokens-screen.png" alt-text="액세스 토큰":::
1. **액세스 토큰** 대화 상자에서 **새 토큰 생성** 을 선택 합니다.
1. **새 액세스 토큰** 대화 상자에 토큰 설명을 입력 합니다.
   :::image type="content" source="media/integration-forescout/new-forescout-token.png" alt-text="새 액세스 토큰":::
1. **다음** 을 선택합니다. 토큰은 대화 상자에 표시 됩니다. :::image type="content" source="media/integration-forescout/forescout-access-token-display-screen.png" alt-text="토큰 보기":::
   > [!NOTE]
   > *토큰을 안전한 장소에 기록 합니다. Forescout 플랫폼을 구성 하는 경우 필요* 합니다.
1. **마침** 을 선택합니다.

   :::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="토큰 추가 완료":::

### <a name="set-up-the-forescout-platform"></a>Forescout 플랫폼 설정

IoT 센서 용 Defender와 통신 하도록 Forescout 플랫폼을 설정할 수 있습니다.

설정하려면 다음을 수행합니다.

1. Forescout 플랫폼에 *CyberX에 대 한 Forescout eyeExtend 모듈* 을 설치 합니다.

1. 콘솔에 로그인 하 고 **도구** 메뉴에서 **옵션** 을 선택 합니다. **옵션** 대화 상자가 열립니다.

1. 로 이동 하 여 **Modules** 폴더를 선택 합니다.

1. **모듈** 창에서 **CyberX Platform** 을 선택 합니다. IoT 용 Defender 플랫폼 창이 열립니다.

   :::image type="content" source="media/integration-forescout/settings-for-module.png" alt-text="IoT 용 Azure Defender 모듈 설정":::

   다음 정보를 입력합니다.

   - **서버 주소** -Forescout 어플라이언스에서 쿼리할 IoT 센서의 Defender에 대 한 IP 주소를 입력 합니다.
   - **액세스 토큰** -Forescout 어플라이언스에 연결할 IoT 용 Defender 센서에 대해 생성 된 액세스 토큰을 입력 합니다. 토큰을 생성 하려면 [IoT 플랫폼용 Defender를 설정](#set-up-the-defender-for-iot-platform)하는 방법을 참조 하세요.

1. **적용** 을 선택합니다.

Forescout 플랫폼이 다른 센서와 통신 하도록 하려면 다음을 수행 합니다.

1. IoT 센서에 대 한 관련 Defender에서 새 액세스 토큰을 만듭니다.

1. **Forescout 모듈**  >  **CyberX 플랫폼** 대화 상자에서 다음을 수행 합니다.

   - 표시 된 정보를 삭제 합니다.
   
   - 새 센서 IP 주소와 새 액세스 토큰 정보를 입력 합니다.

### <a name="verify-communication"></a>통신 확인

IoT 및 Forescout 용 Defender를 구성한 후에는 IoT 용 Defender에서 센서의 액세스 토큰 대화 상자를 엽니다.

이 토큰에 대해 **사용** 된 필드에 **N/A** 가 표시 되 면 센서와 Forescout 어플라이언스 간의 연결이 작동 하지 않습니다.

**사용** 됨이 토큰을 사용 하는 외부 호출을 마지막으로 받은 시간을 나타냅니다.

:::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="토큰을 받았는지 확인 합니다.":::

## <a name="view-defender-for-iot-detections-in-forescout"></a>Forescout에서 IoT 검색에 대 한 Defender 보기

장치의 특성을 보려면:

1. Forescout 플랫폼에 로그인 한 후 **자산 인벤토리** 로 이동 합니다.

1. **CyberX 플랫폼** 으로 이동 합니다. IoT 용 Defender에서 감지한 장치에 대해 다음과 같은 장치 특성이 표시 됩니다.

   | 항목 | Description |
   |--|--|
   | IoT 용 Azure Defender에서 권한 부여 | 네트워크 학습 기간 동안 IoT 용 Defender에서 네트워크에서 검색 된 장치입니다. |
   | 펌웨어 | 장치의 펌웨어 세부 정보입니다. 예를 들어, 모델 및 버전 정보를 참조 하세요. |
   | Name | 서버의 이름입니다. |
   | 운영 체제 | 장치의 운영 체제입니다. |
   | Type | 디바이스의 유형입니다. 예를 들어 PLC, Historian 또는 엔지니어링 스테이션이 있습니다. |
   | Vendor | 장치의 공급 업체입니다. 예: Rockwell Automation. |
   | 위험 수준 | IoT 용 Defender에서 계산 된 위험 수준입니다. |
   | 프로토콜 | 장치에 의해 생성 된 트래픽에 검색 된 프로토콜입니다. |

:::image type="content" source="media/integration-forescout/device-firmware-attributes-in-forescout.png" alt-text="펌웨어 특성을 봅니다.":::

:::image type="content" source="media/integration-forescout/vendor-attributes-in-forescout.png" alt-text="공급 업체 특성을 봅니다.":::

### <a name="viewing-more-details"></a>자세한 내용 보기

IoT 용 Defender에서 지시 하는 장치에 대 한 추가 장치 정보를 확인 합니다. 예를 들어 Forescout 준수 및 정책 정보를 확인 합니다.

이를 수행 하려면 **장치 인벤토리 호스트** 섹션에서 장치를 마우스 오른쪽 단추로 클릭 합니다. 추가 정보를 포함 하는 호스트 세부 정보 대화 상자가 열립니다.

:::image type="content" source="media/integration-forescout/details-dialog-box-in-forescout.png" alt-text="호스트 세부 정보":::

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>Forescout에서 IoT 정책에 대 한 Azure Defender 만들기

Forescout 정책을 사용 하 여 Defender for IoT에서 검색 된 장치의 제어 및 관리를 자동화할 수 있습니다. 예제:

- 특정 펌웨어 버전이 검색 되 면 SOC 관리자에 게 자동으로 전자 메일을 보냅니다.

- 다른 SIEM 통합과 같은 인시던트 및 보안 워크플로의 추가 처리를 위해 IoT에서 검색 된 장치에 대 한 특정 Defender를 Forescout 그룹에 추가 합니다.

조건 속성을 사용 하 여 IoT 용 Defender를 사용 하 여 Forescout 사용자 지정 정책을 만듭니다.

IoT 용 Defender 속성에 액세스 하려면:

1. **정책 조건** 대화 상자에서 **속성 트리로** 이동 합니다.

1. **속성 트리에서** **CyberX Platform** 폴더를 확장 합니다. IoT 용 Defender 다음 속성을 사용할 수 있습니다.

:::image type="content" source="media/integration-forescout/forescout-property-tree.png" alt-text="속성":::

## <a name="next-steps"></a>다음 단계

[경고 정보를 전달](how-to-forward-alert-information-to-partners.md)하는 방법을 알아봅니다.
