---
title: 관리 도구 배포 - Azure
description: Windows Virtual Desktop 미리 보기 리소스를 관리하는 사용자 인터페이스 도구 설치 방법을 설명합니다.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-chjenk
ms.openlocfilehash: 275fec5fb696a7e1352bbddccd288863e984b796
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304519"
---
# <a name="tutorial-deploy-a-management-tool"></a>자습서: 관리 도구 배포

관리 도구는 Microsoft Virtual Desktop 미리 보기 리소스를 관리하는 UI(사용자 인터페이스)를 제공합니다. 이 자습서에서는 관리 도구를 배포하고 연결하는 방법을 알아봅니다.

>[!NOTE]
>이 지침은 조직의 기존 프로세스에 사용할 수 있는 Windows Virtual Desktop 미리 보기 관련 구성에 대한 것입니다.

## <a name="important-considerations"></a>중요 고려 사항

앱을 사용하려면 Windows Virtual Desktop과 상호 작용하도록 동의가 필요하므로 이 도구는 B2B 시나리오를 지원하지 않습니다. AAD(Azure Active Directory) 테넌트의 구독마다 별도의 관리 도구를 배포해야 합니다.

이 관리 도구는 샘플입니다. Microsoft에서 중요한 보안 및 품질 업데이트를 제공할 것입니다. [소스 코드는 GitHub에서 받을 수 있습니다](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). 고객과 파트너는 각자 비즈니스 요구 사항에 맞게 도구를 사용자 지정할 수 있습니다.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 실행하기 위해 필요한 것

Azure Resource Manager 템플릿을 배포하려면 먼저 Azure Active Directory 사용자가 관리 UI를 배포해야 합니다. 이 사용자가 다음 조건을 충족해야 합니다.

- Azure MFA(Multi-Factor Authentication)를 사 용하지 않도록 설정
- Azure 구독에서 리소스를 만드는 데 필요한 권한 보유
- Azure AD 애플리케이션을 만드는 데 필요한 권한 보유 다음 단계에 따라 사용자에게 [필요한 권한](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)이 있는지 확인합니다.

Azure Resource Manager 템플릿을 배포한 후에는 관리 UI를 시작하여 유효성을 검사합니다. 이 사용자가 다음 조건을 충족해야 합니다.
- Windows Virtual Desktop 테넌트를 보거나 편집할 수 있는 역할이 할당됨

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>Azure Resource Manager 템플릿을 실행하여 관리 UI 프로비저닝

시작하기 전에, 표시된 AAD(Azure Active Directory)의 [Windows Virtual Desktop 동의 페이지](https://rdweb.wvd.microsoft.com)를 방문하여 서버 및 클라이언트 앱에서 동의를 받았는지 확인합니다.

다음 지침에 따라 Azure Resource Manager 템플릿을 배포합니다.

1. [GitHub Azure RDS 템플릿 페이지](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)로 이동합니다.
2. Azure에 템플릿을 배포합니다.
    - Enterprise 구독에서 배포하는 경우 아래로 스크롤하여 **Azure에 배포**를 선택합니다. [템플릿 매개 변수에 대한 지침](#guidance-for-template-parameters)을 참조하세요.
    - 클라우드 솔루션 공급자 구독에서 배포하는 경우 다음 지침에 따라 Azure에 배포합니다.
        1. 아래로 스크롤하여 **Azure에 배포**를 마우스 오른쪽 단추로 클릭한 다음, **링크 위치 복사**를 선택합니다.
        2. 메모장 같은 텍스트 편집기를 열고 링크를 붙여넣습니다.
        3. <https://portal.azure.com/> 바로 뒤에서 해시 태그(#) 앞에 @ 기호와 테넌트 도메인 이름을 차례로 입력합니다. <https://portal.azure.com/@Contoso.onmicrosoft.com#create/> 같은 형식입니다.
        4. 클라우드 솔루션 공급자 구독에 대한 관리자/기여자 권한이 있는 사용자로 Azure Portal에 로그인합니다.
        5. 텍스트 편집기에 복사한 링크를 주소 표시줄에 붙여넣습니다.

### <a name="guidance-for-template-parameters"></a>템플릿 매개 변수에 대한 지침
도구를 구성하는 매개 변수를 입력하는 방법은 다음과 같습니다.

- RD broker URL:  <https://rdbroker.wvd.microsoft.com/>
- 리소스 URL:  <https://mrs-prod.ame.gbl/mrs-RDInfra-prod>
- MFA가 해제된 AAD 자격 증명을 사용하여 Azure에 로그인합니다. [Azure Resource Manager 템플릿을 실행하기 위해 필요한 것](#what-you-need-to-run-the-azure-resource-manager-template)을 참조하세요.
- 관리 도구의 Azure Active Directory에 등록할 고유한 애플리케이션 이름(예: Apr3UX)을 사용합니다.

## <a name="provide-consent-for-the-management-tool"></a>관리 도구 사용 조건에 동의

GitHub Azure Resource Manager 템플릿을 완료하면 두 개의 앱 서비스가 들어 있는 리소스 그룹 하나와 Azure Portal의 앱 서비스 플랜 하나가 보일 것입니다.

로그인하여 관리 도구를 사용하려면 관리 도구와 연결된 새 Azure Active Directory 애플리케이션의 사용 조건에 동의해야 합니다. 동의할 경우 관리 도구가 도구에 로그인한 사용자를 대신하여 Windows Virtual Desktop 관리 호출을 수행할 수 있습니다.

![UI 관리 도구에 동의할 때 제공되는 권한을 보여주는 스크린샷.](media/management-ui-delegated-permissions.png)

도구에 로그인할 때 사용 가능한 사용자를 확인하려면 [Azure Active Directory 사용자 설정 페이지](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)로 이동하여 **사용자가 앱이 사용자 대신 회사 데이터에 액세스하는 것에 동의할 수 있음**의 값을 적어 둡니다.

![사용자가 해당 사용자에 대해서만 애플리케이션에 동의할 수 있는지 여부를 보여주는 스크린샷.](media/management-ui-user-consent-allowed.png)

- 이 값을 **예**로 설정하면 Azure Active Directory에서 아무 사용자 계정으로 로그인하여 해당 사용자에게만 동의를 제공할 수 있습니다. 그러나 나중에 다른 사용자로 관리 도구에 로그인하는 경우 같은 동의 과정을 다시 반복해야 합니다.
- 이 값을 **아니요**로 설정하면 Azure Active Directory에서 글로벌 관리자로 로그인하여 디렉터리의 모든 사용자에게 관리자 동의를 제공해야 합니다. 다른 사용자에게는 동의 프롬프트가 표시되지 않습니다.


동의를 제공할 때 사용할 사용자를 결정한 후에는 다음 지침에 따라 도구에 대한 동의를 제공합니다.

1. Azure 리소스를 이동하고, 템플릿에서 제공한 이름을 가진(예: Apr3UX) Azure App Services 리소스를 선택하고, 해당 리소스와 연결된 URL(예: <https://rdmimgmtweb-210520190304.azurewebsites.net>)로 이동합니다.
2. 적절한 Azure Active Directory 사용자 계정을 사용하여 로그인합니다.
3. 글로벌 관리자로 인증하면 **조직 대신 동의** 확인란을 선택할 수 있습니다. **동의**를 선택하여 동의합니다.
   
   ![사용자 또는 관리자에게 표시되는 전체 동의 페이지를 보여주는 스크린샷.](media/management-ui-consent-page.png)

그러면 관리 도구로 이동됩니다.

## <a name="use-the-management-tool"></a>관리 도구 사용

조직 또는 지정된 사용자 대신 동의한 후에는 언제든지 관리 도구에 액세스할 수 있습니다.

다음 지침에 따라 도구를 시작합니다.

1. 템플릿에서 제공한 이름을 가진(예: Apr3UX) Azure App Services 리소스를 선택하고, 해당 리소스와 연결된 URL(예: <https://rdmimgmtweb-210520190304.azurewebsites.net>)로 이동합니다.
2. Windows Virtual Desktop 자격 증명을 사용하여 로그인합니다.
3. 테넌트 그룹을 선택하라는 메시지가 표시되면 드롭다운 목록에서 **기본 테넌트 그룹**을 선택합니다.

> [!NOTE]
> 사용자 지정 테넌트 그룹이 있는 경우 드롭다운 목록에서 선택하지 말고 수동으로 이름을 입력합니다.

## <a name="next-steps"></a>다음 단계

관리 도구를 배포하고 연결하는 방법을 배웠으므로, 이제 Azure Service Health를 사용하여 서비스 이슈와 상태 권고를 모니터링하는 방법을 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [서비스 경고 설정 자습서](./set-up-service-alerts.md)
