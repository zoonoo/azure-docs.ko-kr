---
title: '방법: Microsoft ID 플랫폼을 사용하여 최근에 삭제한 애플리케이션 복원 또는 제거 | Azure'
titleSuffix: Microsoft identity platform
description: 이 방법에서는 Microsoft ID 플랫폼에 등록되고 최근에 삭제된 애플리케이션을 복원하거나 영구적으로 삭제하는 방법을 알아봅니다.
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
ms.openlocfilehash: dbe3e16bf56c5480fbe4aff8dad78bbbb5591f67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079915"
---
# <a name="restore-or-remove-a-recently-deleted-application-with-the-microsoft-identity-platform"></a>Microsoft ID 플랫폼을 사용하여 최근에 삭제한 애플리케이션 복원 또는 제거
앱 등록을 삭제하면 30일 동안 앱이 일시 중단된 상태로 유지됩니다. 30일 동안 앱 등록 및 모든 속성을 복원할 수 있습니다. 30일이 지나면 앱 등록을 복원할 수 없으며 영구 삭제 프로세스가 자동으로 시작될 수 있습니다.  이 기능은 디렉터리에 연결된 애플리케이션에만 적용됩니다.  복원할 수 없는 개인 Microsoft 계정의 애플리케이션에는 사용할 수 없습니다.

Azure Portal의 Azure AD(Azure Active Directory)에서 앱 등록 환경을 사용하여 삭제된 애플리케이션을 보거나, 삭제된 애플리케이션을 복원하거나, 애플리케이션을 영구적으로 삭제할 수 있습니다.

사용자와 Microsoft 고객 지원팀 모두 영구적으로 삭제된 애플리케이션 또는 삭제한지 30일이 지난 애플리케이션을 복원할 수 없습니다.

> [!IMPORTANT]
> 삭제된 애플리케이션 포털 UI 기능 [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="required-permissions"></a>필요한 사용 권한
애플리케이션을 영구히 삭제하려면 다음 역할 중 하나가 있어야 합니다.

- 글로벌 관리자
- 애플리케이션 관리자
- 클라우드 애플리케이션 관리자
- 하이브리드 ID 관리자
- 애플리케이션 소유자

애플리케이션을 영구적으로 복원하려면 다음 역할 중 하나가 있어야 합니다.

- 글로벌 관리자
- 애플리케이션 소유자

### <a name="view-your-deleted-applications"></a>삭제된 애플리케이션 보기
일시 삭제된 상태의 모든 애플리케이션을 볼 수 있습니다.  삭제한지 30일이 지나지 않은 애플리케이션만 복원할 수 있습니다.

#### <a name="to-view-your-restorable-applications"></a>복원 가능한 애플리케이션을 보려면
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Azure Active Directory** 를 검색하여 선택하고 **앱 등록** 을 선택한 다음 **삭제된 애플리케이션(미리 보기)** 탭을 선택합니다.

애플리케이션 목록 검토하기. 삭제한지 30일이 되지 않은 애플리케이션만 복원할 수 있습니다. 앱 등록 검색 미리 보기를 사용하는 경우 '삭제된 날짜' 열을 기준으로 필터링하여 해당 애플리케이션만 볼 수 있습니다.

## <a name="restore-a-recently-deleted-application"></a>최근에 삭제된 애플리케이션 복원

조직에서 앱 등록을 삭제하면 앱은 일시 중단된 상태가 되며 구성이 유지됩니다. 앱 등록을 복원하면 해당 구성도 복원됩니다.  그러나 애플리케이션의 홈 테넌트에 대한 **엔터프라이즈 애플리케이션** 의 조직별 설정이 있는 경우에는 해당 설정이 복원되지 않습니다.  

이는 조직별 설정이 서비스 주체라고 하는 별도 개체에 저장되기 때문입니다.  서비스 주체에게 제공되는 설정은 특정 조직에 대한 권한 동의 및 사용자 및 그룹 할당을 포함합니다. 해당 구성은 앱을 복원할 때 복원되지 않습니다. 자세한 내용은 [애플리케이션 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요. 


### <a name="to-restore-an-application"></a>애플리케이션을 복원하려면
1. **삭제된 애플리케이션(미리 보기)** 탭에서 삭제된지 30일이 되지 않은 애플리케이션 중 하나를 검색하여 선택합니다.
2. **앱 등록 복원** 을 선택합니다.

## <a name="permanently-delete-an-application"></a>애플리케이션 영구 삭제
조직에서 애플리케이션을 수동으로 영구 삭제할 수 있습니다. 사용자나 다른 관리자 또는 Microsoft 고객 지원에서 영구적으로 삭제된 애플리케이션을 복원할 수 없습니다.

### <a name="to-permanently-delete-an-application"></a>애플리케이션을 영구 삭제하려면

1. **삭제된 애플리케이션(미리 보기)** 탭에서 사용 가능한 애플리케이션 중 하나를 검색하여 선택합니다.
2. **영구적으로 삭제** 를 선택합니다.
3. 경고 텍스트를 읽고 **예** 를 선택합니다.

## <a name="next-steps"></a>다음 단계
앱을 복원하거나 영구적으로 삭제한 후에는 다음을 수행할 수 있습니다.

- [애플리케이션 추가](quickstart-register-app.md).
- Microsoft ID 플랫폼의 [애플리케이션 및 서비스 주체 개체](app-objects-and-service-principals.md)에 대해 자세히 알아봅니다.
