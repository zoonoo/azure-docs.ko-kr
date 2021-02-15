---
title: 사용자 만들기 및 관리
description: 센서의 사용자 및 온-프레미스 관리 콘솔을 만들고 관리 합니다. 사용자는 관리자, 보안 분석가 또는 읽기 전용 사용자의 역할을 할당할 수 있습니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/3/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: fd0c7b74bea979737644824f93b4dce7a2364b99
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522345"
---
# <a name="about-defender-for-iot-console-users"></a>IoT 콘솔 사용자를 위한 Defender 정보

이 문서에서는 센서 사용자 및 온-프레미스 관리 콘솔을 만들고 관리 하는 방법을 설명 합니다. 사용자 역할에는 관리자, 보안 분석가 또는 읽기 전용 사용자가 포함 됩니다. 각 역할은 센서 또는 온-프레미스 관리 콘솔에 대 한 도구에 대 한 사용 권한 범위와 연결 됩니다. 역할은 IoT 용 Azure Defender에 대 한 세부적인 보안 액세스를 용이 하 게 하기 위해 설계 되었습니다.

기능을 사용 하 여 사용자 활동을 추적 하 고 Active Directory 로그인 할 수도 있습니다.

기본적으로 각 센서 및 온-프레미스 관리 콘솔은 *cyberx 및 지원* 사용자와 함께 설치 됩니다. 이러한 사용자는 문제 해결 및 설치를 위한 고급 도구에 액세스할 수 있습니다. 관리자 사용자는 이러한 사용자 자격 증명을 사용 하 여 로그인 하 고, 관리자 사용자를 만든 후 보안 분석가 및 읽기 전용 사용자를 위한 추가 사용자를 만들어야 합니다.

## <a name="role-based-permissions"></a>역할 기반 권한
사용할 수 있는 사용자 역할은 다음과 같습니다.

- **읽기 전용**: 읽기 전용 사용자는 장치 맵의 경고 및 장치 보기와 같은 작업을 수행 합니다. 이러한 사용자는 **탐색** 아래에 표시 되는 옵션에 액세스할 수 있습니다.

- **보안 분석가**: 보안 분석가에 게는 읽기 전용 사용자 권한이 있습니다. 또한 장치에 대 한 작업을 수행 하 고, 경고를 승인 하 고, 조사 도구를 사용할 수 있습니다. 이러한 사용자는 **탐색** 및 **분석** 에 표시 되는 옵션에 액세스할 수 있습니다.

- **관리자**: 관리자는 시스템 구성 정의, 사용자 만들기 및 관리 등의 모든 도구에 액세스할 수 있습니다. 이러한 사용자는 **탐색**, **분석** 및 **관리** 아래에 표시 되는 옵션에 액세스할 수 있습니다.

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>온-프레미스 관리 콘솔 도구에 대 한 역할 기반 권한

이 섹션에서는 온-프레미스 관리 콘솔의 관리자, 보안 분석가 및 읽기 전용 사용자가 사용할 수 있는 권한에 대해 설명 합니다.  

| 사용 권한 | 읽기 전용 | 보안 분석가 | 관리자 |
|--|--|--|--|
| Enterprise 맵 보기 및 필터링 | ✓ | ✓ | ✓ |
| 사이트 빌드 |  |  | ✓ |
| 사이트 관리 (영역 추가 및 편집) |  |  | ✓ |
| 장치 인벤토리 보기 및 필터링 | ✓ | ✓ | ✓ |
| 경고 보기 및 관리: 승인, 학습 및 고정 | ✓ | ✓ | ✓ |
| 보고서 생성 |  | ✓ | ✓ |
| 위험 평가 보고서 보기 |  | ✓ | ✓ |
| 경고 제외 설정 |  | ✓ | ✓ |
| 액세스 그룹 보기 또는 정의 |  |  | ✓ |
| 시스템 설정 관리 |  |  | ✓ |
| 사용자 관리 |  |  | ✓ |
| 파트너에 게 경고 데이터 보내기 |  |  | ✓ |
| 인증서 관리 |  |  | ✓ |
| 사용자가 활성화 되어 있지 않은 경우 세션 시간 초과 | 30분 | 30분  | 30분  |

#### <a name="assign-users-to-access-groups"></a>사용자에 게 액세스 그룹 할당

관리자는 특정 *액세스 그룹* 에 사용자를 할당 하 여 Defender IoT의 사용자 액세스 제어를 향상 시킬 수 있습니다. 액세스 그룹은 센서가 있는 영역, 사이트, 지역 및 업무 단위에 할당 됩니다. 관리자는 사용자에 게 액세스 그룹을 할당 하 여 사용자가 장치 검색을 관리 하 고 분석 하는 위치를 제어할 수 있습니다. 

이러한 방식으로 작업 하면 전역 조직 보안 정책에 따라 사용자 권한이 복잡 하거나 결정 될 수 있는 대기업을 사용할 수 있습니다. 자세한 내용은 [전역 액세스 제어 정의](how-to-define-global-user-access-control.md)를 참조 하세요.

### <a name="role-based-permissions-to-sensor-tools"></a>센서 도구에 대 한 역할 기반 권한

이 섹션에서는 센서 관리자, 보안 분석가 및 읽기 전용 사용자가 사용할 수 있는 권한에 대해 설명 합니다.  

| 사용 권한 | 읽기 전용 | 보안 분석가 | 관리자 |
|--|--|--|--|
| 대시보드 보기 | ✓ | ✓ | ✓ |
| 지도 확대/축소 뷰 컨트롤 |  |  | ✓ |
| 경고 보기 | ✓ | ✓ | ✓ |
| 경고 관리: 승인, 학습 및 고정 |  | ✓ | ✓ |
| 타임 라인에서 이벤트 보기 |  | ✓ | ✓ |
| 장치 권한 부여, 알려진 검색 장치, 프로그래밍 장치 |  | ✓ | ✓ |
| 조사 데이터 보기 | ✓ | ✓ | ✓ |
| 시스템 설정 관리 |  |  | ✓ |
| 사용자 관리 |  |  | ✓ |
| 역방향 조회를 위한 DNS 서버 |  |  | ✓ |
| 파트너에 게 경고 데이터 보내기 |  | ✓ | ✓ |
| 경고 주석 만들기 |  | ✓ | ✓ |
| 프로그래밍 변경 기록 보기 | ✓ | ✓ | ✓ |
| 사용자 지정 된 경고 규칙 만들기 |  | ✓ | ✓ |
| 동시에 여러 알림 관리 |  | ✓ | ✓ |
| 인증서 관리 |  |  | ✓ |
| 사용자가 활성화 되어 있지 않은 경우 세션 시간 초과 | 30분 | 30분 | 30분 |

## <a name="define-users"></a>사용자 정의

이 섹션에서는 사용자를 정의 하는 방법을 설명 합니다. Cyberx, 지원 및 관리자가 다른 사용자 정의를 추가, 제거 및 업데이트할 수 있습니다.

사용자를 정의 하려면:

1. 센서 또는 온-프레미스 관리 콘솔의 왼쪽 창에서 **사용자** 를 선택 합니다.
1. **사용자** 창에서 **사용자 만들기** 를 선택 합니다.
1. **사용자 만들기** 창에서 다음 매개 변수를 정의 합니다.

   - **사용자 이름**: 사용자 이름을 입력 합니다.
   - **전자 메일**: 사용자의 이메일 주소를 입력 합니다.
   - **이름**: 사용자의 이름을 입력 합니다.
   - **성**: 사용자의 성을 입력 합니다.
   - **역할**: 사용자의 역할을 정의 합니다. [역할 기반 권한](#role-based-permissions)을 참조 하세요.
   - **액세스 그룹**: 온-프레미스 관리 콘솔에 대 한 사용자를 만드는 경우 사용자의 액세스 그룹을 정의 합니다. [전역 액세스 제어 정의](how-to-define-global-user-access-control.md)를 참조 하세요.
   - **암호**: 다음과 같이 사용자 유형을 선택 합니다.
     - **로컬 사용자**: 센서 또는 온-프레미스 관리 콘솔의 사용자에 대 한 암호를 정의 합니다. 암호는 6 자 이상 이어야 하며 문자와 숫자를 포함 해야 합니다.
     - **Active Directory 사용자**: Active Directory 자격 증명을 사용 하 여 사용자가 센서 또는 관리 콘솔에 로그인 하도록 허용할 수 있습니다. 정의 된 Active Directory 그룹은 특정 사용 권한 수준에 연결할 수 있습니다. 예를 들어 특정 Active Directory 그룹을 구성 하 고 그룹의 모든 사용자를 읽기 전용 사용자 형식에 할당 합니다.

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="사용자를 관리 합니다.":::

## <a name="user-session-timeout"></a>사용자 세션 제한 시간

사용자가 특정 시간 동안 키보드 또는 마우스에서 활성화 되지 않은 경우 세션에서 로그 아웃 되며 다시 로그인 해야 합니다.

사용자가 30 분 동안 콘솔 마우스나 키보드를 사용 하 여 작업 하지 않은 경우 세션 로그 아웃이 강제 적용 됩니다.

이 기능은 기본적으로 사용 하도록 설정 되어 있지만 업그레이드 시 사용 하지 않도록 설정할 수 있습니다. 또한 세션 계산 시간을 업데이트할 수 있습니다. 세션 시간은 초 단위로 정의 됩니다. 정의는 센서 및 온-프레미스 관리 콘솔 별로 적용 됩니다.

세션 시간 제한 메시지는 비활성 시간 제한이 경과 되 면 콘솔에 표시 됩니다.

### <a name="control-inactivity-sign-out"></a>비활성 로그 아웃 제어

관리자 사용자는 비활성 로그 아웃을 사용 및 사용 하지 않도록 설정 하 고 비활성 임계값을 조정할 수 있습니다.

명령에 액세스 하려면:

1. IoT 관리 자격 증명에 대해 Defender를 사용 하 여 센서 또는 온-프레미스 관리 콘솔에 대 한 CLI에 로그인 합니다.

1. `sudo nano /var/cyberx/properties/authentication`를 입력합니다.

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

기능을 사용 하지 않도록 설정 하려면 `infinity_session_expiration = true` 을로 변경 `infinity_session_expiration = false` 합니다.

로그 아웃 계산 기간을 업데이트 하려면 `= <number>` 값을 필요한 시간으로 조정 합니다.

## <a name="track-user-activity"></a>사용자 활동 추적 

각 센서의 이벤트 타임 라인에서 사용자 활동을 추적할 수 있습니다. 타임 라인에는 이벤트 또는 영향을 받는 장치 및 사용자가 활동을 수행한 시간과 날짜가 표시 됩니다.

사용자 활동을 보려면:

1. 센서에 로그인 합니다.
1. 이벤트 타임 라인에서 **사용자 작업** 옵션을 사용 하도록 설정 합니다. 

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="사용자의 활동을 봅니다.":::

## <a name="integrate-with-active-directory-servers"></a>Active Directory 서버와 통합 

Active Directory와 함께 작동 하도록 센서 또는 온-프레미스 관리 콘솔을 구성 합니다. 이를 통해 사용자 Active Directory Active Directory 자격 증명을 사용 하 여 IoT 콘솔에 대 한 Defender에 액세스할 수 있습니다.

다음 두 가지 유형의 LDAP 기반 인증이 지원 됩니다.

- **전체 인증**: LDAP 서버에서 사용자 세부 정보를 검색 합니다. 예를 들면 이름, 성, 전자 메일 및 사용자 권한이 있습니다.

- **신뢰할 수 있는 사용자**: 사용자 암호만 검색 됩니다. 검색 된 다른 사용자 정보는 센서에 정의 된 사용자를 기반으로 합니다.

### <a name="active-directory-and-defender-for-iot-permissions"></a>IoT 용 Active Directory 및 Defender 사용 권한

여기에 정의 된 Active Directory 그룹을 특정 권한 수준에 연결할 수 있습니다. 예를 들어 특정 Active Directory 그룹을 구성 하 고 해당 그룹의 모든 사용자에 게 RO 사용 권한을 할당 합니다. 자세한 내용은 [사용자 만들기 및 관리](how-to-create-and-manage-users.md) 를 참조 하세요.

Active Directory를 구성 하려면:

1. 왼쪽 창에서 **시스템 설정** 을 선택 합니다.

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="Active Directory 시스템 설정을 봅니다.":::

1. **시스템 설정** 창에서 **Active Directory** 을 선택 합니다.

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="Active Directory 구성을 편집 합니다.":::

1. **Active Directory 구성 편집** 대화 상자에서 **통합 사용**  >  **저장** Active Directory을 선택 합니다. **Active Directory 구성 편집** 대화 상자가 확장 되 고 이제 Active Directory를 구성 하는 매개 변수를 입력할 수 있습니다.

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="Active Directory를 구성 하는 매개 변수를 입력 합니다.":::

    > [!NOTE]
    > - LDAP 매개 변수는 Active Directory에 표시 된 대로 정확 하 게 정의 해야 합니다.
    > - 모든 Active Directory 매개 변수에 대해서는 소문자만 사용 합니다. Active Directory의 구성이 대문자를 사용 하는 경우에도 소문자를 사용 합니다.
    > - 동일한 도메인에 대해 LDAP와 LDAPS를 모두 구성할 수는 없습니다. 그러나 여러 도메인에 대해 동시에 둘 다 사용할 수 있습니다.

1. 다음과 같이 Active Directory 서버 매개 변수를 설정 합니다.

   | 서버 매개 변수 | Description |
   |--|--|
   | 도메인 컨트롤러 FQDN | FQDN (정규화 된 도메인 이름)은 LDAP 서버에 표시 된 대로 정확 하 게 설정 합니다. 예를 들어 다음과 같이 입력합니다. `host1.subdomain.domain.com` |
   | 도메인 컨트롤러 포트 | LDAP가 구성 된 포트를 정의 합니다. |
   | 주 도메인 | LDAP 구성에 따라 도메인 이름 (예: `subdomain.domain.com` ) 및 연결 유형을 설정 합니다. |
   | Active Directory 그룹 | LDAP 서버의 Active Directory 구성에 정의 된 그룹 이름을 입력 합니다. |
   | 트러스트 된 도메인 | 트러스트 된 도메인을 추가 하려면 트러스트 된 도메인의 도메인 이름 및 연결 유형을 추가 합니다. <br />사용자에 정의 된 사용자에 대해서만 트러스트 된 도메인을 구성할 수 있습니다. |

1. **저장** 을 선택합니다.

1. 트러스트 된 서버를 추가 하려면 **서버 추가** 를 선택 하 고 다른 서버를 구성 합니다.

## <a name="resetting-a-users-password-for-the-sensor-or-on-premises-management-console"></a>센서 또는 온-프레미스 관리 콘솔에 대 한 사용자 암호 재설정

### <a name="cyberx-or-support-user"></a>CyberX 또는 지원 사용자

**CyberX** 및 **지원** 사용자만 **암호 복구** 기능에 액세스할 수 있습니다. **CyberX** 또는 **지원** 사용자가 암호를 잊은 경우 Defender for IoT 로그인 페이지의 **암호 복구** 옵션을 통해 암호를 다시 설정할 수 있습니다.

CyberX 또는 지원 사용자에 대 한 암호를 다시 설정 하려면:

1. IoT 용 Defender 로그인 화면에서  **암호 복구** 를 선택 합니다. **암호 복구** 화면이 열립니다.

1. **CyberX** 또는 **Support** 를 선택 하 고 고유한 식별자를 복사 합니다.

1. Azure Portal로 이동 하 여 **사이트 및 센서** 를 선택 합니다.  

1. 상단 도구 모음에서 **구독 필터** 아이콘 :::image type="icon" source="media/password-recovery-images/subscription-icon.png" border="false":::  을 선택 하 고 센서가 연결 된 구독을 선택 합니다.

1. **온-프레미스 관리 콘솔 암호 복구** 탭을 선택 합니다.

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="복구 파일을 다운로드 하려면 온-프레미스 관리 관리 단추를 선택 합니다.":::

1. **암호 복구** 화면에서 받은 고유 식별자를 입력 하 고 **복구** 를 선택 합니다. `password_recovery.zip`파일이 다운로드 됩니다.

    > [!NOTE]
    > 암호 복구 파일을 변경 하지 마세요. 서명 된 파일이 며 변조 하는 경우 작동 하지 않습니다.

1. **암호 복구** 화면에서 **업로드** 를 선택 합니다. **암호 복구 파일 업로드** 창이 열립니다.

1. **찾아보기** 를 선택 하 여 `password_recovery.zip` 파일을 찾거나를 `password_recovery.zip` 창으로 끌어 놓습니다.

    > [!NOTE]
    > 파일이 잘못 되었음을 나타내는 오류 메시지가 표시 될 수 있습니다. 이 오류 메시지를 해결 하려면를 다운로드 하기 전에 올바른 구독을 선택 했는지 확인 하 `password_recovery.zip` 고 다시 다운로드 합니다.  

1. 다음 **을 선택 하면** 관리 콘솔에 대 한 사용자 및 시스템 생성 암호가 표시 됩니다.

### <a name="administrator-security-analyst-and-read-only-user"></a>관리자, 보안 분석가 및 읽기 전용 사용자

읽기 전용 및 보안 분석가는 자신의 암호를 재설정할 수 없으며 관리자, 지원 또는 CyberX 역할을 사용 하 여 사용자에 게 연락 하 여 암호를 재설정 해야 합니다. 관리자는 **CyberX** 또는 **지원** 사용자에 게 문의 하 여 암호를 재설정 해야 합니다.

센서에 대 한 사용자의 암호를 재설정 하려면:

1. 관리자, 지원 또는 CyberX 역할 사용자가 센서에 로그인 해야 합니다.

1. 왼쪽 패널에서 **사용자** 를 선택 합니다.

   :::image type="content" source="media/password-recovery-images/sensor-page.png" alt-text="왼쪽 창에서 사용자 옵션을 선택 합니다.":::

1. 사용자를 찾고 **작업** 드롭다운 메뉴에서 **편집** 을 선택 합니다.

   :::image type="content" source="media/password-recovery-images/edit.png" alt-text="작업 드롭다운 메뉴에서 편집을 선택 합니다.":::

1. **새 암호 및** **새 암호 확인** 필드에 새 암호를 입력 합니다.

1. **업데이트** 를 선택합니다.

온-프레미스 관리 콘솔에서 사용자의 암호를 다시 설정 하려면 다음을 수행 합니다.

1. 관리자, 지원 또는 CyberX 역할 사용자가 센서에 로그인 해야 합니다.

1. 왼쪽 패널에서 **사용자** 를 선택 합니다.

   :::image type="content" source="media/password-recovery-images/console-page.png" alt-text="왼쪽 패널에서 사용자의 옵션을 선택 합니다.":::

1. 사용자를 찾고 편집 아이콘을 선택 :::image type="icon" source="media/password-recovery-images/edit-icon.png" border="false"::: 합니다.

1. **새 암호 및** **새 암호 확인** 필드에 새 암호를 입력 합니다.

1. **업데이트** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

센서 활성화 및 [설정](how-to-activate-and-set-up-your-sensor.md) 
 [온-프레미스 관리 콘솔](how-to-activate-and-set-up-your-on-premises-management-console.md) 
 활성화 및 설정 [센서 작업 추적](how-to-track-sensor-activity.md)
