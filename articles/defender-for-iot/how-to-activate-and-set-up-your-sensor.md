---
title: 센서 활성화 및 설정
description: 이 문서에서는 센서 콘솔을 로그인 하 고 활성화 하는 방법을 설명 합니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/26/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9305609b624a96e4d785657dfb63af6639e132c4
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97840326"
---
# <a name="activate-and-set-up-your-sensor"></a>센서 활성화 및 설정

이 문서에서는 센서를 활성화 하 고 초기 설치를 수행 하는 방법을 설명 합니다.

관리자 사용자는 처음으로 로그인 할 때 및 정품 인증 관리가 필요할 때 활성화를 수행 합니다. 설치 프로그램이 센서를 최적으로 검색 하 고 경고 하도록 구성 합니다.

보안 분석가와 읽기 전용 사용자는 센서를 활성화 하거나 새 암호를 생성할 수 없습니다.

## <a name="sign-in-and-activation-for-administrator-users"></a>관리자 사용자에 대 한 로그인 및 활성화

처음으로 로그인 하는 관리자는 센서 온 보 딩 중에 다운로드 된 활성화 및 암호 복구 파일에 액세스할 수 있는지 확인 해야 합니다. 그렇지 않은 경우 azure 보안 관리자, 구독 참가자 또는 구독 소유자 권한이 있어야 IoT 포털 용 Azure Defender에서 이러한 파일을 생성할 수 있습니다.

### <a name="first-time-sign-in-and-activation-checklist"></a>첫 번째 로그인 및 활성화 검사 목록

센서 콘솔에 로그인 하기 전에 관리자 사용자는 다음에 액세스할 수 있어야 합니다.

- 설치 하는 동안 정의 된 센서 IP 주소입니다.

- 센서에 대 한 사용자 로그인 자격 증명입니다. 센서에 대 한 ISO를 다운로드 한 경우 설치 중에 받은 기본 자격 증명을 사용 합니다. 활성화 한 후 새 *관리자* 를 만드는 것이 좋습니다.

- 초기 암호입니다. 화살표를 통해 미리 구성 된 센서를 구매한 경우 처음으로 로그인 할 때 암호를 생성 해야 합니다.

- 이 센서와 연결 된 정품 인증 파일입니다. IoT 포털 용 Defender에서 센서 온 보 딩 중에 파일이 생성 되 고 다운로드 되었습니다.

- 회사에서 요구 하는 SSL/TLS CA 서명 인증서입니다.

### <a name="about-activation-files"></a>활성화 파일 정보

센서가 특정 관리 모드에서 IoT 용 Azure Defender로 등록 되었습니다.

| 모드 유형 | Description |
|--|--|
| **클라우드 연결 모드** | 센서가 감지한 정보는 센서 콘솔에 표시 됩니다. 또한 경고 정보는 IoT hub를 통해 제공 되며, Azure 센티널과 같은 다른 Azure 서비스와 공유할 수 있습니다. |
| **로컬 연결 모드** | 센서가 감지한 정보는 센서 콘솔에 표시 됩니다. 또한 검색 정보는 온-프레미스 관리 콘솔과 공유 됩니다 (센서가 연결 된 경우). |

온 보 딩 중에이 센서에 대해 로컬로 연결 되거나 클라우드로 연결 된 정품 인증 파일이 생성 되 고 다운로드 되었습니다. 정품 인증 파일에는 센서의 관리 모드에 대 한 지침이 포함 되어 있습니다. *배포 하는 각 센서에 고유한 활성화 파일을 업로드 해야 합니다.*  처음 로그인 할 때이 센서에 대 한 관련 활성화 파일을 업로드 해야 합니다.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-activation-file-download-button.png" alt-text="IoT 용 Azure Defender 포털, 등록 센서":::

### <a name="about-certificates"></a>인증서 정보

센서 설치 후에는 로컬 자체 서명 된 인증서가 생성 되어 센서 콘솔에 액세스 하는 데 사용 됩니다. 관리자가 콘솔에 처음으로 로그인 하면 해당 사용자에 게 SSL/TLS 인증서를 등록 하 라는 메시지가 표시 됩니다.

두 가지 수준의 보안을 사용할 수 있습니다.

- CA 서명 인증서를 업로드 하 여 조직에서 요청 하는 특정 인증서 및 암호화 요구 사항을 충족 합니다.
- 관리 콘솔과 연결 된 센서 간의 유효성 검사를 허용 합니다. 유효성 검사는 인증서 해지 목록 및 인증서 만료 날짜에 대해 평가 됩니다. *유효성 검사에 실패 하면 관리 콘솔과 센서 간의 통신이 중단 되 고 콘솔에 유효성 검사 오류가 나타납니다.* 이 옵션은 설치 후 기본적으로 사용 하도록 설정 됩니다.  

콘솔은 다음과 같은 인증서 유형을 지원 합니다.

- 개인 및 엔터프라이즈 키 인프라 (사설 PKI)
- 공개 키 인프라 (공용 PKI)
- 어플라이언스에서 로컬로 생성 (로컬에서 자체 서명 됨) 

  > 중요 한 기본 자체 서명 된 인증서를 사용 하지 않는 것이 좋습니다. 인증서는 안전 하지 않으며 테스트 환경에만 사용 해야 합니다. 인증서 소유자의 유효성을 검사할 수 없으며 시스템의 보안을 유지할 수 없습니다. 프로덕션 네트워크에는이 옵션을 사용 하지 마십시오.

### <a name="sign-in-and-activate-the-sensor"></a>로그인 및 센서 활성화

로그인 및 활성화 하려면:

1. 설치 중에 정의 된 IP를 사용 하 여 브라우저에서 센서 콘솔로 이동 합니다. 로그인 대화 상자가 열립니다.

    :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="IoT 용 Azure Defender 센서":::

1. 센서 설치 중에 정의 된 자격 증명을 입력 합니다. 화살표를 통해 미리 구성 된 센서를 구매한 경우 먼저 암호를 생성 합니다. 암호 복구에 대 한 자세한 내용은 [초기 로그인 시 암호 실패 조사](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md#investigate-password-failure-at-initial-sign-in)를 참조 하세요.

1. 로그인 하면 **활성화** 대화 상자가 열립니다. **업로드** 를 선택 하 고 센서 온 보 딩 중에 다운로드 한 정품 인증 파일로 이동 합니다.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/activation-upload-screen-with-upload-button.png" alt-text="업로드를 선택 하 고 활성화 파일로 이동 합니다.":::

1. 활성화 하기 전에 센서 네트워크 구성을 변경 하려면 **센서 네트워크 구성** 링크를 선택 합니다. [활성화 하기 전에 센서 네트워크 구성 업데이트](#update-sensor-network-configuration-before-activation)를 참조 하세요.

1. 사용 약관에 동의합니다.

1. **활성화** 를 선택합니다. SSL/TLS 인증서 대화 상자가 열립니다.

1. 인증서 이름을 정의 합니다.
1. CRT 및 키 파일을 업로드 합니다.
1. 필요한 경우 암호를 입력 하 고 PEM 파일을 업로드 합니다.
1. **다음** 을 선택합니다. 유효성 검사 화면이 열립니다. 기본적으로 관리 콘솔과 연결 된 센서 간의 유효성 검사가 사용 됩니다.
1. **시스템 차원의 유효성 검사 사용** /사용 안 함을 해제 합니다. 유효성 검사를 사용 하도록 설정 하는 것이 좋습니다.
1. **저장** 을 선택합니다.  

CA 서명 인증서를 업로드 한 후 화면을 새로 고쳐야 할 수도 있습니다.

새 인증서를 업로드 하는 방법, 지원 되는 인증서 매개 변수 및 CLI 인증서 명령을 사용 하는 방법에 대 한 자세한 내용은 [개별 센서 관리](how-to-manage-individual-sensors.md)를 참조 하세요.

#### <a name="update-sensor-network-configuration-before-activation"></a>정품 인증 전에 센서 네트워크 구성 업데이트  

센서 네트워크 구성 매개 변수는 소프트웨어를 설치 하는 동안 또는 미리 구성 된 센서를 구매한 경우에 정의 됩니다. 다음 매개 변수가 정의되었습니다.

- IP 주소
- DNS
- 기본 게이트웨이
- 서브넷 마스크
- 호스트 이름

센서를 활성화 하기 전에이 정보를 업데이트 하는 것이 좋습니다. 예를 들어 화살표로 정의한 미리 구성 된 매개 변수를 변경 해야 할 수 있습니다. 센서를 활성화 하기 전에 프록시 설정을 정의할 수도 있습니다.

센서 네트워크 구성 매개 변수를 업데이트 하려면:

1. **인증** 대화 상자에서 **센서 네트워크 구성** 링크를 선택 합니다.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/editable-network-configuration-screen-v2.png" alt-text="센서 네트워크 구성입니다.":::

2. 설치 중에 정의된 매개 변수가 표시됩니다. 프록시를 정의 하는 옵션을 사용할 수도 있습니다. 필요에 따라 설정을 업데이트 하 고 **저장** 을 선택 합니다.

### <a name="subsequent-sign-ins"></a>후속 로그인

처음 활성화 한 후에는 활성화 파일 없이 로그인 한 후에 Azure Defender IoT 센서 콘솔이 열립니다. 로그인 자격 증명만 필요 합니다.

로그인 하면 IoT 용 Azure Defender 콘솔이 열립니다.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-log-in-screen-dashboard-v2.png" alt-text="IoT 용 Azure Defender 콘솔.":::

## <a name="initial-setup-and-learning-for-administrators"></a>초기 설정 및 학습 (관리자 용)

처음 로그인 하면 IoT 용 Azure Defender 센서가 네트워크를 자동으로 모니터링 하기 시작 합니다. 네트워크 자산은 자산 지도 및 asset inventory 섹션에 표시 됩니다. IoT 용 Azure Defender는 네트워크에서 발생 하는 모든 보안 및 운영 인시던트에 대 한 검색 및 경고를 시작 합니다. 그런 다음 검색 된 정보를 기반으로 보고서 및 쿼리를 만들 수 있습니다.

처음에는이 작업을 학습 모드에서 수행 합니다. 그러면 센서에 네트워크의 일반적인 작업에 대해 알아봅니다. 예를 들어 센서는 네트워크에서 검색 된 자산, 네트워크에서 검색 된 프로토콜 및 특정 자산 간에 발생 하는 파일 전송을 알아냅니다. 이 활동은 네트워크의 기본 활동이 됩니다.

### <a name="review-and-update-basic-system-settings"></a>기본 시스템 설정 검토 및 업데이트

센서의 시스템 설정을 검토 하 여 센서가 최적으로 검색 하 고 경고 하도록 구성 되었는지 확인 합니다.

센서의 시스템 설정을 정의 합니다. 예를 들면 다음과 같습니다.

- ICS (또는 IoT) 및 분리 서브넷을 정의 합니다.

- 사이트별 프로토콜에 대 한 포트 별칭을 정의 합니다.

- 사용 중인 Vlan 및 이름을 정의 합니다.

- DHCP를 사용 하는 경우 합법적인 DHCP 범위를 정의 합니다.

- Active Directory 및 메일 서버와의 통합을 정의 합니다.

### <a name="disable-learning-mode"></a>학습 모드 사용 안 함

시스템 설정을 조정한 후에는 시스템 검색에서 네트워크 작업을 정확 하 게 반영 하는 것으로 보일 때까지 Azure Defender for IoT 센서가 학습 모드에서 실행 되도록 할 수 있습니다.

학습 모드는 네트워크 크기 및 복잡도에 따라 약 2 ~ 6 주 동안 실행 해야 합니다. 학습 모드를 사용 하지 않도록 설정한 후에는 기준 활동과 다른 활동이 발생 하면 경고가 트리거됩니다.

학습 모드를 사용 하지 않도록 설정 하려면:

- **시스템 설정** 을 선택 하 고 **학습** 옵션을 해제 합니다.

## <a name="first-time-sign-in-for-security-analysts-and-read-only-users"></a>보안 분석가 및 읽기 전용 사용자에 대 한 첫 번째 로그인

로그인 하기 전에 다음이 있는지 확인 합니다.

- 센서 IP 주소입니다.
- 관리자가 제공한 로그인 자격 증명입니다.

## <a name="console-tools-overview"></a>콘솔 도구: 개요

콘솔 도구는 측면 메뉴에서 액세스할 수 있습니다.

**탐색** 

| 시간 범위 | 아이콘 | Description |
| -----------|--|--|
| 대시보드 | :::image type="icon" source="media/concept-sensor-console-overview/dashboard-icon-azure.png" border="false"::: | 네트워크 보안 상태의 직관적인 스냅숏을 표시 합니다. |
| 장치 맵 | :::image type="icon" source="media/concept-sensor-console-overview/asset-map-icon-azure.png" border="false"::: | 지도에서 네트워크 장치, 장치 연결 및 장치 속성을 봅니다. 다양 한 확대/축소, 강조 표시 및 필터 옵션을 사용 하 여 네트워크를 표시할 수 있습니다. |
| 장치 인벤토리 | :::image type="icon" source="media/concept-sensor-console-overview/asset-inventory-icon-azure.png" border="false":::  | 장치 인벤토리는이 센서가 검색 하는 광범위 한 장치 특성을 표시 합니다. 옵션은 다음에서 사용할 수 있습니다. <br /> -테이블 필드에 따라 정보를 필터링 하 고 표시 되는 필터링 된 정보를 확인 합니다. <br /> -CSV 파일로 정보를 내보냅니다. <br /> -Windows 레지스트리 세부 정보를 가져옵니다.|
| 경고 | :::image type="icon" source="media/concept-sensor-console-overview/alerts-icon-azure.png" border="false"::: | 정책 위반이 발생 하거나, 기준 동작의 편차가 발생 하거나, 네트워크에서 의심 스러운 활동이 검색 되 면 경고를 표시 합니다. |
| 보고서 | :::image type="icon" source="media/concept-sensor-console-overview/reports-icon-azure.png" border="false"::: | 데이터 마이닝 쿼리를 기반으로 하는 보고서를 봅니다. |

**분석**

| 시간 범위| 아이콘 | Description |
|---|---|---|
| 이벤트 타임 라인 | :::image type="icon" source="media/concept-sensor-console-overview/event-timeline-icon-azure.png" border="false"::: | 경고, 네트워크 이벤트 (정보) 및 사용자 작업 (예: 사용자 로그인 및 사용자 삭제)에 대 한 정보가 포함 된 타임 라인을 봅니다.|

**탐색**

| 시간 범위 | 아이콘 | Description |
|---|---|---|
| 데이터 마이닝 | :::image type="icon" source="media/concept-sensor-console-overview/data-mining-icon-azure.png" border="false"::: | 다양 한 계층에서 네트워크 장치에 대 한 포괄적이 고 세부적인 정보를 생성 합니다. |
| 추세 및 통계 | :::image type="icon" source="media/concept-sensor-console-overview/trends-and-statistics-icon-azure.jpg" border="false"::: | 광범위 한 위젯에서 추세와 통계를 봅니다. |
| 위험 평가 | :::image type="icon" source="media/concept-sensor-console-overview/vulnerabilities-icon-azure.png" border="false"::: | **취약성** 창을 표시 합니다. |

**관리자**

| 시간 범위 | 아이콘 | Description |
|---|---|---|
| 사용자 | :::image type="icon" source="media/concept-sensor-console-overview/users-icon-azure.png" border="false"::: | 다양 한 액세스 수준을 사용 하 여 사용자 및 역할을 정의 합니다. |
| 전송 | :::image type="icon" source="media/concept-sensor-console-overview/forwarding-icon-azure.png" border="false"::: | IoT 용 Defender, 전자 메일 주소, webhook 서버 등에 통합 하는 파트너에 게 경고 정보를 전달 합니다. <br /> 자세한 내용은 [경고 정보 전달](how-to-forward-alert-information-to-partners.md) 을 참조 하세요. |
| 시스템 설정 | :::image type="icon" source="media/concept-sensor-console-overview/system-settings-icon-azure.png" border="false"::: | 시스템 설정을 구성 합니다. 예를 들어 DHCP 설정을 정의 하거나 메일 서버 세부 정보를 제공 하거나 포트 별칭을 만듭니다. |
| 설정 가져오기 | :::image type="icon" source="media/concept-sensor-console-overview/import-settings-icon-azure.png" border="false"::: | **가져오기 설정** 창을 표시 합니다. 장치 정보를 수동으로 변경할 수 있습니다.<br /> 자세한 내용은 [장치 정보 가져오기](how-to-import-device-information.md) 를 참조 하세요. |

**지원**

| 시간 범위| 아이콘 | Description |
|----|---|---|
| 지원 | :::image type="icon" source="media/concept-sensor-console-overview/support-icon-azure.png" border="false"::: | 도움이 필요 하면 [Microsoft 지원](https://support.microsoft.com/) 문의 하세요. |

### <a name="see-also"></a>참조

[센서 온보딩](getting-started.md#4-onboard-a-sensor)

[센서 활성화 파일 관리](how-to-manage-individual-sensors.md#manage-sensor-activation-files)

[모니터링 되는 트래픽 제어](how-to-control-what-traffic-is-monitored.md)
