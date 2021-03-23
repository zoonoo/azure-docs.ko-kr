---
title: 온-프레미스 관리 콘솔 활성화 및 설정
description: 관리 콘솔을 활성화 하면 센서가 Azure에 등록 되 고, 온-프레미스 관리 콘솔로 정보를 전송 하 고, 온-프레미스 관리 콘솔에서 연결 된 센서에 대 한 관리 작업을 수행 합니다.
ms.date: 3/18/2021
ms.topic: how-to
ms.openlocfilehash: 89ce6da3521248ff7373e23ae8831106cbee74de
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784631"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>온-프레미스 관리 콘솔 활성화 및 설정 

온-프레미스 관리 콘솔을 활성화 하 고 설정 하면 다음이 수행 됩니다.

- 연결 된 센서를 통해 모니터링 하는 네트워크 장치는 Azure 계정에 등록 됩니다.

- 센서는 온-프레미스 관리 콘솔로 정보를 보냅니다.

- 온-프레미스 관리 콘솔은 연결 된 센서에 대 한 관리 작업을 수행 합니다.

- SSL 인증서를 설치 했습니다.

## <a name="sign-in-for-the-first-time"></a>처음으로 로그인

관리 콘솔에 로그인 하려면 다음을 수행 합니다.

1. 시스템 설치 중에 온-프레미스 관리 콘솔에 대해 받은 IP 주소로 이동 합니다.
 
1. 시스템 설치 중에 온-프레미스 관리 콘솔에 대해 받은 사용자 이름 및 암호를 입력 합니다. 


암호를 잊은 **경우 암호 복구 옵션을**  선택 하 고 암호를 복구 하는 방법에 대 한 지침은 [암호 복구](how-to-manage-the-on-premises-management-console.md#password-recovery) 를 참조 하세요.

## <a name="get-and-upload-an-activation-file"></a>활성화 파일 가져오기 및 업로드

처음으로 로그인 한 후에는 활성화 파일을 가져오고 업로드 하 여 온-프레미스 관리 콘솔을 활성화 해야 합니다. 

활성화 파일을 가져오려면 다음을 수행 합니다.

1. IoT 용 Azure Defender 포털의 **가격 책정** 페이지로 이동 합니다. 
1. 온-프레미스 관리 콘솔을 연결할 구독을 선택 합니다.
1. **관리 콘솔 탭의 활성화 파일 다운로드** 를 선택 합니다. 활성화 파일이 다운로드 됩니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="활성화 파일을 다운로드 합니다.":::

활성화 파일을 업로드 하려면:

1. 온-프레미스 관리 콘솔에서 **시스템 설정** 페이지로 이동 합니다.
1. **활성화** 아이콘을 선택 :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/activation-icon.png" border="false"::: 합니다.
1. **파일** 선택을 선택 하 고 다운로드 한 파일을 선택 합니다.

초기 활성화 후 모니터링 되는 장치 수는 온 보 딩 중에 정의 된 커밋된 장치 수를 초과할 수 있습니다. 이는 더 많은 센서를 관리 콘솔에 연결 하는 경우에 발생 합니다. 모니터링 되는 장치 수와 커밋된 장치 수 간에 불일치가 있는 경우 관리 콘솔에 경고가 표시 됩니다. 이 경우 새 활성화 파일을 업로드 합니다.

## <a name="set-up-a-certificate"></a>인증서 설정

관리 콘솔을 설치한 후에는 로컬 자체 서명 된 인증서가 생성 되 고 콘솔에 액세스 하는 데 사용 됩니다. 관리자가 관리 콘솔에 처음으로 로그인 하면 해당 사용자에 게 SSL/TLS 인증서를 등록 하 라는 메시지가 표시 됩니다. 

두 가지 수준의 보안을 사용할 수 있습니다.

- CA 서명 인증서를 업로드 하 여 조직에서 요청 하는 특정 인증서 및 암호화 요구 사항을 충족 합니다.
- 관리 콘솔과 연결 된 센서 간의 유효성 검사를 허용 합니다. 유효성 검사는 인증서 해지 목록 및 인증서 만료 날짜에 대해 평가 됩니다. *유효성 검사에 실패 하면 관리 콘솔과 센서 간의 통신이 중단 되 고 콘솔에 유효성 검사 오류가 표시 됩니다.* 이 옵션은 설치 후 기본적으로 사용 하도록 설정 됩니다.  

콘솔은 다음과 같은 유형의 인증서를 지원 합니다.

- 개인 및 엔터프라이즈 키 인프라 (사설 PKI)

- 공개 키 인프라 (공용 PKI)

- 어플라이언스에서 로컬로 생성 (로컬에서 자체 서명 됨) 

  > [!IMPORTANT]
  > 자체 서명 된 인증서를 사용 하지 않는 것이 좋습니다. 인증서는 안전 하지 않으며 테스트 환경에만 사용 해야 합니다. 인증서 소유자의 유효성을 검사할 수 없으며 시스템의 보안을 유지할 수 없습니다. 프로덕션 네트워크에는이 옵션을 사용 하지 마십시오.

인증서를 업로드 하려면:

1. 로그인 후 메시지가 표시 되 면 인증서 이름을 정의 합니다.
1. CRT 및 키 파일을 업로드 합니다.
1. 필요한 경우 암호를 입력 하 고 PEM 파일을 업로드 합니다.

CA 서명 인증서를 업로드 한 후 화면을 새로 고쳐야 할 수도 있습니다.

관리 콘솔과 연결 된 센서 간의 유효성 검사를 사용 하지 않도록 설정 하려면:

1. **다음** 을 선택합니다.
1. **시스템 차원의 유효성 검사 사용/사용** 안 함을 해제 합니다.

새 인증서, 지원 되는 인증서 파일 및 관련 항목을 업로드 하는 방법에 대 한 자세한 내용은 [온-프레미스 관리 콘솔 관리](how-to-manage-the-on-premises-management-console.md)를 참조 하세요.

## <a name="connect-sensors-to-the-on-premises-management-console"></a>온-프레미스 관리 콘솔에 센서 연결

센서에서 온-프레미스 관리 콘솔로 정보를 보내고, 온-프레미스 관리 콘솔에서 백업을 수행 하 고, 경고를 관리 하 고, 센서에서 다른 작업을 수행할 수 있는지 확인 해야 합니다. 이렇게 하려면 다음 절차를 사용 하 여 센서와 온-프레미스 관리 콘솔을 초기에 연결 했는지 확인 합니다.

IoT 센서 용 Azure Defender를 온-프레미스 관리 콘솔에 연결 하는 데 사용할 수 있는 두 가지 옵션은 다음과 같습니다.

- 센서 콘솔에서 연결

- 터널링을 사용 하 여 연결

연결한 후 이러한 센서를 사용 하 여 사이트를 설정 해야 합니다.

### <a name="connect-sensors-from-the-sensor-console"></a>센서 콘솔에서 센서 연결

센서 콘솔에서 특정 센서를 온-프레미스 관리 콘솔에 연결 하려면 다음을 수행 합니다.

1. 센서 콘솔의 왼쪽 창에서 **시스템 설정** 을 선택 합니다.

2. **관리에 대 한 연결을** 선택 합니다.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-not-connected.png" alt-text="연결 되지 않은 상태를 표시 하는 온-프레미스 관리 콘솔의 상태 창 스크린샷":::

3. **주소** 텍스트 상자에 연결 하려는 온-프레미스 관리 콘솔의 IP 주소를 입력 합니다.

4. **연결** 을 선택합니다. 상태는 다음과 같이 변경 됩니다.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-connected.png" alt-text="연결 됨을 보여 주는 온-프레미스 관리 콘솔의 상태 창 스크린샷":::

### <a name="connect-sensors-by-using-tunneling"></a>터널링을 사용 하 여 센서 연결

조직 센서와 온-프레미스 관리 콘솔 간에 보안 터널링 연결을 사용 하도록 설정 합니다. 이 설정은 조직 방화벽과의 상호 작용을 우회 공격 노출 영역을 줄입니다.

터널링을 사용 하면 IP 주소 및 단일 포트 (즉, 9000)에서 모든 센서로 온-프레미스 관리 콘솔에 연결할 수 있습니다.

온-프레미스 관리 콘솔에서 터널링을 설정 하려면 다음을 수행 합니다.

- 온-프레미스 관리 콘솔에 로그인 하 고 다음 명령을 실행 합니다.

  ```bash
  cyberx-management-tunnel-enable
  service apache2 reload
  sudo cyberx-management-tunnel-add-xsense --xsenseuid <sensorIPAddress> --xsenseport 9000
  service apache2 reload
  ```

센서에 터널링을 설정 하려면:

1. 센서 (네트워크. 속성)에서 TCP 포트 9000를 수동으로 엽니다. 포트가 열려 있지 않으면 센서가 온-프레미스 관리 콘솔에서 연결을 거부 합니다.

2. 각 센서에 로그인 하 고 다음 명령을 실행 합니다.

   ```bash
   sudo cyberx-xsense-management-connect -ip <centralmanagerIPAddress>
   sudo cyberx-xsense-management-tunnel
   sudo vi /var/cyberx/properties/network.properties
   opened_tcp_incoming_ports=22,80,443,102,9000
   sudo cyberx-xsense-network-validation
   sudo /etc/network/if-up.d/iptables-recover
   sudo iptables -nvL
   ```

## <a name="set-up-a-site"></a>사이트 설정

기본 기업 맵은 여러 지리적 위치에 따라 장치의 전체적인 보기를 제공 합니다.

조직 구조와 사용자 권한이 복잡 한 경우 장치 보기가 필요할 수 있습니다. 이러한 경우 사이트 설치는 표준 사이트 또는 영역 구조 외에도 전역 조직 구조에 의해 결정 될 수 있습니다.

이 환경을 지원 하려면 조직의 비즈니스 단위, 지역, 사이트 및 영역을 기반으로 하는 글로벌 비즈니스 토폴로지를 만들어야 합니다. 또한 액세스 그룹을 사용 하 여 이러한 엔터티에 대 한 사용자 액세스 권한을 정의 해야 합니다.

액세스 그룹을 사용 하면 사용자가 IoT 플랫폼용 Defender에서 장치를 관리 하 고 분석 하는 위치를 보다 효과적으로 제어할 수 있습니다.

### <a name="how-it-works"></a>작동 방법

각 사이트에 대해 사업부와 지역을 정의할 수 있습니다. 그런 다음 네트워크의 논리적 엔터티인 영역을 추가할 수 있습니다. 

각 영역에 대해 하나 이상의 센서를 할당 해야 합니다. 5 수준 모델은 조직의 구조를 반영 하는 보호 시스템을 제공 하는 데 필요한 유연성과 세분성을 제공 합니다.

지도 보기에서 직접 사이트를 편집할 수 있습니다. 지도 보기에서 사이트를 열면 각 영역 옆에 열린 경고 수가 표시 됩니다.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="베를린 데이터 오버레이가 포함 된 온-프레미스 관리 콘솔의 스크린샷":::

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="센서 및 지역 관계를 보여 주는 다이어그램":::

사이트를 설정 하려면:

1. 조직의 논리 구조를 반영 하는 새 사업부를 추가 합니다.

2. 조직의 지역을 반영 하기 위해 새 지역을 추가 합니다.

3. 사이트를 추가 합니다.

4. 사이트에 영역을 추가 합니다.

5. 센서를 연결 합니다.

6. 사이트 영역에 센서를 할당 합니다.

비즈니스 단위를 추가 하려면:

1. 엔터프라이즈 보기에서 **모든 사이트**  >  **비즈니스 단위 관리** 를 선택 합니다.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-business-unit-screen.png" alt-text="사업부 관리 보기를 보여 주는 스크린샷":::

2. 새 사업부 이름을 입력 하 고 **추가** 를 선택 합니다.

새 지역을 추가 하려면:

1. 엔터프라이즈 보기에서 **모든 지역** 지역  >  **관리** 를 선택 합니다.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-regions-screen.png" alt-text="영역 관리 보기를 보여 주는 스크린샷":::

2. 새 지역 이름을 입력 하 고 **추가** 를 선택 합니다.

새 사이트를 추가 하려면:

1. 엔터프라이즈 보기의 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false"::: 위쪽 표시줄에서를 선택 합니다. 커서는 더하기 기호 ()로 표시 됩니다 **+** .

2. **+** 새 사이트의 위치에를 배치 하 고 선택 합니다. **새 사이트 만들기** 대화 상자가 열립니다.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="새 사이트 만들기 뷰의 스크린샷":::

3. 새 사이트의 이름 및 실제 주소를 정의 하 고 **저장** 을 선택 합니다. 새 사이트가 사이트 맵에 표시 됩니다.

사이트를 삭제 하려면:

1. **사이트 관리** 창에서 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: 사이트 이름이 포함 된 막대를 선택 하 고 **사이트 삭제** 를 선택 합니다. 사이트를 삭제할 것인지 확인 하는 확인 상자가 표시 됩니다.

2. 확인 상자에서 **예** 를 선택 합니다. 확인 상자가 닫히고 사이트 **관리** 창이 삭제 된 사이트 없이 표시 됩니다.

## <a name="create-enterprise-zones"></a>엔터프라이즈 영역 만들기

영역은 여러 특성에 따라 사이트 내의 장치를 그룹으로 나눌 수 있도록 하는 논리적 엔터티입니다. 예를 들어 프로덕션 라인, 변전소, 사이트 영역 또는 장치 유형에 대 한 그룹을 만들 수 있습니다. 조직에 적합 한 특성을 기반으로 영역을 정의할 수 있습니다.

사이트 구성 프로세스의 일부로 영역을 구성 합니다.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/site-management-zones-screen-v2.png" alt-text="사이트 관리 영역 보기의 스크린샷":::

다음 표에서는 **사이트 관리** 창의 매개 변수에 대해 설명 합니다.

| 매개 변수 | Description |
|--|--|
| Name | 센서의 이름입니다. 이 이름은 센서 에서만 변경할 수 있습니다. 자세한 내용은 IoT 용 Defender 사용자 가이드를 참조 하세요. |
| IP | 센서 IP 주소입니다. |
| 버전 | 센서 버전입니다. |
| 연결 | 센서 연결 상태입니다. 상태를 연결 하거나 **연결** 을 **끊을** 수 있습니다. |
| 마지막 업그레이드 | 마지막 업그레이드 날짜입니다. |
| 업그레이드 진행률 | 진행률 표시줄은 다음과 같이 업그레이드 프로세스의 상태를 표시 합니다.<br />-패키지 업로드 중<br />-설치 준비 중<br />-프로세스를 중지 하는 중<br />-데이터 백업<br />-스냅숏 만들기<br />-구성을 업데이트 하는 중<br />-종속성 업데이트<br />-라이브러리를 업데이트 하는 중<br />-데이터베이스 패치<br />-프로세스 시작<br />-시스템 온전성 유효성 검사<br />-유효성 검사 성공<br />-성공<br />-실패<br />-업그레이드가 시작 됨<br />-설치 시작 중ogress bar shows the status of the upgrade process, as follows:<br />- Uploading package<br />- Preparing to install<br />- Stopping processes<br />- Backing up data<br />- Taking snapshot<br />- Updating configuration<br />- Updating dependencies<br />- Updating libraries<br />- Patching databases<br />- Starting processes<br />- Validating system sanity<br />- Validation succeeded<br />- Success<br />- Failure<br />- Upgrade started<br />- Starting installation<br /></br >업그레이드에 대 한 자세한 내용은 [Microsoft 지원](https://support.microsoft.com/) 도움말을 참조 하세요. |
| 디바이스 | 센서가 모니터링 하는 OT 장치 수입니다. |
| 경고 | 센서에 대 한 경고의 수입니다. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: | 영역에 센서를 할당할 수 있습니다. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::| 사이트에서 연결 되지 않은 센서를 삭제할 수 있습니다. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/sensor-icon.png" border="false"::: | 영역에 현재 연결 되어 있는 센서의 수를 나타냅니다. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/ot-assets-icon.png" border="false"::: | 영역에 현재 연결 되어 있는 OT 자산의 수를 나타냅니다. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/number-of-alerts-icon.png" border="false"::: | 영역에 할당 된 센서에서 보낸 경고 수를 나타냅니다. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: | 영역에서 센서를 할당 취소 합니다. |

사이트에 영역을 추가 하려면 다음을 수행 합니다.

1. **사이트 관리** 창에서 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: 사이트 이름이 포함 된 막대를 선택 하 고 **영역 추가** 를 선택 합니다. **새 영역 만들기** 대화 상자가 나타납니다.

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-zone-screen.png" alt-text="새 영역 만들기 뷰의 스크린샷":::

2. 영역 이름을 입력 합니다.

3. 새 영역에 대 한 설명을 입력 하 여 사이트를 영역으로 나누는 데 사용한 특징을 명확 하 게 설명 합니다.

4. **SAVE**(저장)를 선택합니다. 새 영역이이 영역이 속한 사이트의 **사이트 관리** 창에 표시 됩니다.

영역을 편집 하려면:

1. **사이트 관리** 창에서 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: 영역 이름을 포함 하는 표시줄을 선택 하 고 **영역 편집** 을 선택 합니다. **영역 편집** 대화 상자가 나타납니다.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="영역 편집 대화 상자를 표시 하는 스크린샷":::

2. 영역 매개 변수를 편집 하 고 **저장** 을 선택 합니다.

영역을 삭제하려면:

1. **사이트 관리** 창에서 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: 영역 이름을 포함 하는 표시줄을 선택 하 고 **영역 삭제** 를 선택 합니다.

2. 확인 상자에서 **예** 를 선택 합니다.

연결 상태에 따라 필터링 하려면:

- 왼쪽 위 모서리에서 연결 옆을 선택 하 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: 고 다음 옵션 중 하나를 선택 합니다.

  - **모두**:이 온-프레미스 관리 콘솔에 보고 하는 모든 센서를 표시 합니다.

  - **연결 됨**: 연결 된 센서만 표시 합니다.

  - **연결 끊김**: 연결 끊김 센서만 표시 합니다.

업그레이드 상태에 따라 필터링 하려면:

- 왼쪽 위 모서리에서 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: **업그레이드 상태** 옆을 선택 하 고 다음 옵션 중 하나를 선택 합니다.

  - **모두**:이 온-프레미스 관리 콘솔에 보고 하는 모든 센서를 표시 합니다.

  - **Valid**: 유효한 업그레이드 상태를 가진 센서를 표시 합니다.

  - **진행 중**: 업그레이드 프로세스에 있는 센서를 표시 합니다.

  - **실패**: 업그레이드 프로세스가 실패 한 센서를 표시 합니다.

## <a name="assign-sensors-to-zones"></a>영역에 센서 할당

각 영역에 대해 로컬 트래픽 분석 및 경고를 수행 하는 센서를 할당 해야 합니다. 온-프레미스 관리 콘솔에 연결 된 센서만 할당할 수 있습니다.

센서를 할당 하려면:

1. **사이트 관리** 를 선택 합니다. 할당 되지 않은 센서는 대화 상자의 왼쪽 위 모퉁이에 표시 됩니다.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassigned-sensors-view.png" alt-text="할당 되지 않은 센서 보기의 스크린샷":::

2. **연결 상태가 연결** 됨 인지 확인 합니다. 그렇지 않은 경우 연결에 대 한 자세한 내용은 [온-프레미스 관리 콘솔에 센서 연결](#connect-sensors-to-the-on-premises-management-console) 을 참조 하세요. 

3. :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false":::할당 하려는 센서에 대해를 선택 합니다.

4. **센서 할당** 대화 상자에서 할당할 사업부, 지역, 사이트 및 영역을 선택 합니다.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="센서 할당 보기의 스크린샷":::

5. **할당** 을 선택 합니다.

센서 할당을 취소 하 고 삭제 하려면:

1. 온-프레미스 관리 콘솔에서 센서의 연결을 끊습니다. 자세한 내용은 [온-프레미스 관리 콘솔에 센서 연결을](#connect-sensors-to-the-on-premises-management-console) 참조 하세요.

2. **사이트 관리** 창에서 센서를 선택 하 고를 선택 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: 합니다. 센서는 몇 분 후에 할당 되지 않은 센서 목록에 표시 됩니다.

3. 사이트에서 할당 되지 않은 센서를 삭제 하려면 할당 되지 않은 센서 목록에서 센서를 선택 하 고를 선택 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false"::: 합니다.

## <a name="see-also"></a>참고 항목

[센서 및 온-프레미스 관리 콘솔 문제 해결](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
