---
title: B2B 협업 사용자 일괄 초대 자습서 - AD
description: 이 자습서에서는 PowerShell 및 CSV 파일을 사용하여 외부 Azure AD B2B 협업 사용자에게 일괄 초대를 보내는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0f88b310bc00881e66ee8e8b5f2d40616d60315
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926926"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>자습서: Azure AD B2B 협업 사용자 일괄 초대

Azure AD(Azure Active Directory) B2B 협업을 사용하여 외부 파트너와 함께 작업하는 경우 동시에 여러 게스트 사용자를 조직에 초대할 수 있습니다. 이 자습서에서는 Azure Portal을 사용하여 외부 사용자에게 일괄 초대를 보내는 방법을 알아봅니다. 특히 다음을 수행합니다.

> [!div class="checklist"]
> * **사용자 일괄 초대**를 사용하여 사용자 정보 및 초대 기본 설정이 포함된 쉼표로 구분된 값(.csv) 파일을 준비합니다.
> * .csv 파일을 Azure AD에 업로드
> * 사용자가 디렉터리에 추가되었는지 확인

Azure Active Directory가 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="understand-the-csv-template"></a>CSV 템플릿 이해

대량 업로드 CSV 템플릿을 다운로드하고 입력하여 대량으로 Azure AD 게스트 사용자를 성공적으로 만들 수 있습니다. 다운로드한 CSV 템플릿은 다음 예제와 같이 표시될 수 있습니다.

![업로드용 스프레드시트 및 각 행과 열에 대한 용도 및 값에 대한 설명](media/tutorial-bulk-invite/understand-template.png)

### <a name="csv-template-structure"></a>CSV 템플릿 구조

다운로드된 CSV 템플릿의 행은 다음과 같습니다.

- **버전 번호**: 버전 번호가 포함된 첫 번째 행은 CSV 업로드에 포함되어야 합니다.
- **열 머리글**: 열 머리글의 형식은 &lt;*항목 이름*&gt; [PropertyName] &lt;*필수 또는 비워 둠*&gt;입니다. `Email address to invite [inviteeEmail] Required`)을 입력합니다. 템플릿의 일부 이전 버전은 약간 다를 수 있습니다.
- **예제 행**: 템플릿에는 각 열에 대해 허용 가능한 값의 예제 행이 포함되어 있습니다. 예제 행을 제거하고 원하는 항목으로 바꾸어야 합니다.

### <a name="additional-guidance"></a>추가 지침

- 업로드 템플릿의 처음 두 행은 제거하거나 수정하면 안 됩니다. 이렇게 하면 업로드를 처리할 수 없습니다.
- 필수 열이 먼저 나열됩니다.
- 템플릿에 새 열을 추가하지 않는 것이 좋습니다. 새로 추가하는 열은 무시되고 처리되지 않습니다.
- 가능한 자주 최신 버전의 CSV 템플릿을 다운로드하는 것이 좋습니다.

## <a name="prerequisites"></a>사전 요구 사항

초대를 보낼 수 있는 두 개 이상의 테스트 메일 계정이 필요합니다. 계정은 조직 외부에서 가져와야 합니다. gmail.com 또는 outlook.com 주소와 같은 소셜 계정을 포함하여 모든 계정 유형을 사용할 수 있습니다.

## <a name="invite-guest-users-in-bulk"></a>게스트 사용자 일괄 초대

1. 조직에서 사용자 관리자인 계정으로 Azure Portal에 로그인합니다.
2. 탐색 창에서 **Azure Active Directory**를 선택합니다.
3. **관리**에서 **사용자** > **일괄 초대**를 선택합니다.
4. **사용자 일괄 초대** 페이지에서 **다운로드**를 선택하여 초대 속성이 포함된 올바른 .csv 템플릿을 가져옵니다.

    ![일괄 초대 다운로드 단추](media/tutorial-bulk-invite/bulk-invite-button.png)

5. .csv 템플릿을 열고 각 게스트 사용자에 대한 줄을 추가합니다. 필요한 값은 다음과 같습니다.

   * **초대할 이메일 주소** - 초대를 받을 사용자

   * **리디렉션 URL** - 초대를 승인한 후 초대된 사용자가 전달되는 URL

    ![게스트 사용자가 입력한 CSV 파일의 예제](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > **사용자 지정된 초대 메시지**에 쉼표를 사용하지 마세요. 메시지가 성공적으로 구문 분석되지 않습니다.

6. 파일을 저장합니다.
7. **사용자 일괄 초대** 페이지의 **csv 파일 업로드**에서 해당 파일을 찾습니다. 파일을 선택하면 .csv 파일의 유효성 검사가 시작됩니다. 
8. 파일 콘텐츠의 유효성 검사가 완료되면 **파일 업로드 성공**이 표시됩니다. 오류가 있는 경우 해당 오류를 해결해야 작업을 제출할 수 있습니다.
9. 파일이 유효성 검사를 통과하면 **제출**을 선택하여 초대를 추가하는 Azure 대량 작업을 시작합니다. 
10. 작업 상태를 보려면 **각 작업의 상태를 보려면 여기를 클릭**을 선택합니다. 또는 **작업** 섹션에서 **대량 작업 결과**를 선택할 수 있습니다. 대량 작업 내의 각 줄 항목에 대한 자세한 내용은 **# Success**, **# Failure** 또는 **Total Requests** 열의 값을 선택합니다. 오류가 발생하면 실패 이유가 나열됩니다.

    ![대량 작업 결과의 예제](media/tutorial-bulk-invite/bulk-operation-results.png)

11. 작업이 완료되면 대량 작업이 성공했다는 알림이 표시됩니다.

## <a name="verify-guest-users-in-the-directory"></a>디렉터리의 게스트 사용자 확인

추가한 게스트 사용자가 Azure Portal에서 또는 PowerShell을 사용하여 디렉터리에 있는지 확인합니다.

### <a name="view-guest-users-in-the-azure-portal"></a>Azure Portal의 게스트 사용자 보기

1. 조직에서 사용자 관리자인 계정으로 Azure Portal에 로그인합니다.
2. 탐색 창에서 **Azure Active Directory**를 선택합니다.
3. **관리**에서 **사용자**를 선택합니다.
4. **표시**에서 **게스트 사용자만**을 선택하고 추가한 사용자가 나열되는지 확인합니다.

### <a name="view-guest-users-with-powershell"></a>PowerShell을 사용하여 게스트 사용자 보기

다음 명령 실행:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

초대한 사용자가 *emailaddress*#EXT#\@*domain* 형식의 UPN(사용자 계정 이름)과 함께 나열되어야 합니다. 예를 들어 *lstokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com*에서 contoso.onmicrosoft.com은 초대를 보낸 조직입니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 게스트 사용자 옆의 확인란을 선택한 다음, **삭제**를 선택하여 사용자 페이지의 Azure Portal에 있는 디렉터리에서 테스트 사용자 계정을 삭제할 수 있습니다. 

또는 다음 PowerShell 명령을 실행하여 사용자 계정을 삭제할 수 있습니다.

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

예: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>다음 단계

이 자습서에서는 조직 외부의 게스트 사용자에게 일괄 초대를 보냈습니다. 다음으로, 초대 사용 프로세스가 어떻게 진행되는지 알아봅니다.

> [!div class="nextstepaction"]
> [Azure AD B2B 협업 초대 사용 프로세스에 대해 알아보기](redemption-experience.md)
