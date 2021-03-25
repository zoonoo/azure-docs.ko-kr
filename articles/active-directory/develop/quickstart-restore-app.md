---
title: '방법: Microsoft id 플랫폼을 사용 하 여 최근에 삭제 된 응용 프로그램 복원 또는 제거 | Microsoft'
titleSuffix: Microsoft identity platform
description: 이 방법에서는 Microsoft id 플랫폼에 등록 된 최근에 삭제 된 응용 프로그램을 복원 하거나 영구적으로 삭제 하는 방법에 대해 알아봅니다.
services: active-directory
author: arcrowe
manager: dastrock
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 3/22/2021
ms.author: arcrowe
ms.custom: aaddev
ms.openlocfilehash: a5cf8f42bb15d3ce566506d85fac192027a1c69f
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105039017"
---
# <a name="restore-or-remove-a-recently-deleted-application-with-the-microsoft-identity-platform"></a>Microsoft id 플랫폼을 사용 하 여 최근에 삭제 한 응용 프로그램 복원 또는 제거
앱 등록을 삭제 하면 앱은 30 일 동안 일시 중단 된 상태로 유지 됩니다. 30 일 기간 동안 모든 속성과 함께 앱 등록을 복원할 수 있습니다. 30 일 기간이 지나면 앱 등록을 복원할 수 없으며 영구 삭제 프로세스가 자동으로 시작 될 수 있습니다.  이 기능은 디렉터리에 연결 된 응용 프로그램에만 적용 됩니다.  복원할 수 없는 개인 Microsoft 계정의 응용 프로그램에는 사용할 수 없습니다.

Azure Portal의 Azure Active Directory (Azure AD)에서 앱 등록 환경을 사용 하 여 삭제 된 응용 프로그램을 보거나, 삭제 된 응용 프로그램을 복원 하거나, 응용 프로그램을 영구적으로 삭제할 수 있습니다.

사용자와 Microsoft 고객 지원팀이 모두 영구적으로 삭제 된 응용 프로그램 또는 30 일 이전에 삭제 한 응용 프로그램을 복원할 수 없습니다.

## <a name="required-permissions"></a>필요한 사용 권한
응용 프로그램을 영구적으로 삭제 하려면 다음 역할 중 하나가 있어야 합니다.

- 전역 관리자

- 애플리케이션 관리자

- 클라우드 애플리케이션 관리자

- 하이브리드 ID 관리자

- 애플리케이션 소유자

응용 프로그램을 복원 하려면 다음 역할 중 하나가 있어야 합니다.

- 전역 관리자

- 애플리케이션 소유자

## <a name="deleted-applications-ui--preview"></a>삭제 된 응용 프로그램 UI | 미리 보기

> [!IMPORTANT]
> 삭제 된 응용 프로그램 포털 UI 기능 [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

### <a name="view-your-deleted-applications"></a>삭제 된 응용 프로그램 보기
일시 삭제 된 상태의 모든 응용 프로그램을 볼 수 있습니다.  30 일 미만 전에 삭제 된 응용 프로그램만 복원할 수 있습니다.

#### <a name="to-view-your-restorable-applications"></a>복원 가능한 응용 프로그램을 보려면
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **Azure Active Directory** 검색 하 여 선택 하 고 **앱 등록** 을 선택한 다음 **삭제 된 응용 프로그램 (미리 보기)** 탭을 선택 합니다.

    응용 프로그램의 목록을 검토 합니다. 지난 30 일 이내에 삭제 된 응용 프로그램만 복원할 수 있습니다. 앱 등록 검색 미리 보기를 사용 하는 경우 ' 삭제 된 날짜 ' 열을 기준으로 필터링 하 여 이러한 응용 프로그램만 볼 수 있습니다.

### <a name="restore-a-recently-deleted-application"></a>최근에 삭제 한 응용 프로그램 복원

조직에서 앱 등록을 삭제 하면 앱은 일시 중단 된 상태가 되며 해당 구성이 유지 됩니다. 앱 등록을 복원 하면 해당 구성도 복원 됩니다.  그러나 응용 프로그램의 홈 테 넌 트에 대 한 **엔터프라이즈 응용 프로그램** 의 조직별 설정이 있는 경우에는 해당 설정이 복원 되지 않습니다.  

이는 조직별 설정이 서비스 주체 라고 하는 별도의 개체에 저장 되기 때문입니다.  서비스 사용자에 게 제공 되는 설정은 특정 조직에 대 한 권한 동의 및 사용자 및 그룹 할당을 포함 합니다. 이러한 구성은 앱을 복원할 때 복원 되지 않습니다. 자세한 내용은 [응용 프로그램 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조 하세요. 


#### <a name="to-restore-an-application"></a>응용 프로그램을 복원 하려면
1. 삭제 된 **응용 프로그램 (미리 보기)** 탭에서 30 일 전에 삭제 된 응용 프로그램 중 하나를 검색 하 여 선택 합니다.

2. **앱 등록 복원** 을 선택 합니다.

### <a name="permanently-delete-an-application"></a>응용 프로그램을 영구적으로 삭제
조직에서 응용 프로그램을 수동으로 영구적으로 삭제할 수 있습니다. 영구적으로 삭제 된 응용 프로그램은 사용자, 다른 관리자 또는 Microsoft 고객 지원부에서 복원할 수 없습니다.

#### <a name="to-permanently-delete-an-application"></a>응용 프로그램을 영구적으로 삭제 하려면

1. 삭제 된 **응용 프로그램 (미리 보기)** 탭에서 사용 가능한 응용 프로그램 중 하나를 검색 하 여 선택 합니다.

2. **영구적으로 삭제** 를 선택합니다.

3. 경고 텍스트를 읽고 **예** 를 선택 합니다.

## <a name="next-steps"></a>다음 단계
앱을 복원 하거나 영구적으로 삭제 한 후에는 다음을 수행할 수 있습니다.

- [애플리케이션 추가](quickstart-register-app.md)

- Microsoft ID 플랫폼의 [애플리케이션 및 서비스 주체 개체](app-objects-and-service-principals.md)에 대해 자세히 알아봅니다.
