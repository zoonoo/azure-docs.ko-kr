---
title: 'Azure Digital Twins 요청이 실패 했습니다 (상태: 403 (사용할 수 없음)).'
description: "' 서비스 요청에 대 한 원인과 해결 방법이 실패 했습니다. 상태: Azure Digital Twins에서 403 (사용할 수 없음) '이 (가) 있습니다."
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: aeae1f1a99d1fa574df8202efd2405232855628b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93091806"
---
# <a name="service-request-failed-status-403-forbidden"></a>서비스 요청이 실패 했습니다. 상태: 403 (사용할 수 없음)

이 문서에서는 서비스 요청에서 Azure Digital Twins로 403 오류를 수신 하는 원인 및 해결 단계를 설명 합니다. 

## <a name="symptoms"></a>증상

이 오류는 인증을 필요로 하는 다양 한 유형의 서비스 요청에 발생할 수 있습니다. 그러면 API 요청이 실패 하 고의 오류 상태가 반환 됩니다 `403 (Forbidden)` .

## <a name="causes"></a>원인

### <a name="cause-1"></a>원인 #1

일반적으로이 오류는 서비스에 대 한 Azure RBAC (역할 기반 액세스 제어) 권한이 올바르게 설정 되지 않았음을 나타냅니다. Azure Digital Twins 인스턴스에 대 한 많은 작업을 수행 하려면 **관리 하려는 인스턴스에** *Azure Digital Twins 데이터 소유자* 역할이 있어야 합니다. 

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

### <a name="cause-2"></a>원인 #2

[앱 등록](how-to-create-app-registration.md)을 사용 하 여 인증 하는 Azure Digital twins와 통신 하기 위해 클라이언트 앱을 사용 하는 경우 앱 등록에 Azure Digital twins 서비스에 대해 설정 된 권한이 없기 때문에이 오류가 발생할 수 있습니다.

앱 등록에는 Azure Digital Twins Api에 대해 구성 된 액세스 권한이 있어야 합니다. 그런 다음 응용 프로그램 등록에 대해 클라이언트 앱이 인증 되 면 앱 등록이 구성 된 사용 권한이 부여 됩니다.

## <a name="solutions"></a>솔루션

### <a name="solution-1"></a>솔루션 #1

첫 번째 솔루션은 Azure 사용자가 관리 하려는 인스턴스에 _**Azure Digital Twins 데이터 소유자**_ 역할을 보유 하 고 있는지 확인 하는 것입니다. 이 역할이 없으면 설정 합니다.

이 역할은와 다릅니다.
* 미리 보기 중에이 역할의 이전 이름입니다. *Azure Digital Twins 소유자 (미리 보기)* (역할은 동일 하지만 이름이 변경 됨)
* 전체 Azure 구독에 대 한 *소유자* 역할입니다. *Azure Digital Twins 데이터 소유자* 는 Azure Digital twins 내에서 역할 이며이 개별 Azure 디지털 쌍 인스턴스로 범위가 지정 됩니다.
* Azure Digital Twins의 *소유자* 역할. 이러한 두 가지 Azure Digital Twins 관리 역할은 *Azure Digital Twins 데이터 소유자* 는 미리 보기 중 관리에 사용 해야 하는 역할입니다.

#### <a name="check-current-setup"></a>현재 설치 확인

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>문제 해결 

이 역할 할당이 없는 경우 **azure 구독** 에 소유자 역할을 가진 사용자는 다음 명령을 실행 하 여 azure 사용자에 게 azure digital twins **인스턴스에서** Azure *digital twins 데이터 소유자* 역할을 제공 해야 합니다. 

구독에 대 한 소유자 인 경우이 명령을 직접 실행할 수 있습니다. 그렇지 않은 경우 소유자에 게 문의 하 여 사용자를 대신 하 여이 명령을 실행 합니다.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Data Owner"
```

이 역할 요구 사항과 할당 프로세스에 대 한 자세한 내용은 *방법: 인스턴스 및 인증 설정 (CLI 또는 포털)* 의 [ *사용자 액세스 권한 설정* 섹션](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) 을 참조 하세요.

이 역할 할당이 이미 *있고* Azure AD 앱 등록을 사용 하 여 클라이언트 앱을 인증 하는 경우이 솔루션에서 403 문제를 해결 하지 않은 경우 다음 솔루션을 계속 진행할 수 있습니다.

### <a name="solution-2"></a>솔루션 #2

Azure AD 앱 등록을 사용 하 여 클라이언트 앱을 인증 하는 경우 두 번째 가능한 해결 방법은 앱 등록에 Azure Digital Twins 서비스에 대해 구성 된 권한이 있는지 확인 하는 것입니다. 구성 되지 않은 경우 설정 합니다.

#### <a name="check-current-setup"></a>현재 설치 확인

사용 권한이 올바르게 구성 되었는지 확인 하려면 Azure Portal에서 [AZURE AD 앱 등록 개요 페이지로](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) 이동 합니다. 포털 검색 창에서 *앱 등록* 을 검색 하 여이 페이지로 직접 이동할 수 있습니다.

*모든 응용 프로그램* 탭으로 전환 하 여 구독에 생성 된 모든 앱 등록을 확인 합니다.

목록에서 방금 만든 앱 등록이 표시 됩니다. 이를 선택 하 여 세부 정보를 엽니다.

:::image type="content" source="media/troubleshoot-error-403/app-registrations.png" alt-text="Azure Portal 앱 등록 페이지":::

먼저, 등록에 Azure Digital Twins 권한 설정이 올바르게 설정 되었는지 확인 합니다. 이렇게 하려면 메뉴 모음에서 *매니페스트* 를 선택 하 여 앱 등록의 매니페스트 코드를 확인 합니다. 코드 창의 아래쪽으로 스크롤하고 아래에서 이러한 필드를 찾습니다 `requiredResourceAccess` . 값은 아래 스크린샷에 있는 값과 일치 해야 합니다.

:::image type="content" source="media/troubleshoot-error-403/verify-manifest.png" alt-text="Azure Portal 앱 등록 페이지":::

그런 다음 메뉴 모음에서 *API 권한* 을 선택 하 여이 앱 등록에 Azure Digital twins에 대 한 읽기/쓰기 권한이 포함 되어 있는지 확인 합니다. 다음과 같은 항목이 표시 됩니다.

:::image type="content" source="media/troubleshoot-error-403/verify-api-permissions.png" alt-text="Azure Portal 앱 등록 페이지":::

#### <a name="fix-issues"></a>문제 해결

설명 된 것과 다르게 표시 되는 경우 [*방법: 앱 등록 만들기*](how-to-create-app-registration.md)에서 앱 등록을 설정 하는 방법에 대 한 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

새 Azure Digital Twins 인스턴스를 만들고 인증 하는 방법에 대 한 설치 단계를 읽으십시오.
* [*방법: 인스턴스 및 인증 설정 (CLI)*](how-to-set-up-instance-cli.md)

Azure Digital Twins의 보안 및 사용 권한에 대해 자세히 알아보세요.
* [*개념: Azure Digital Twins 솔루션에 대 한 보안*](concepts-security.md)
