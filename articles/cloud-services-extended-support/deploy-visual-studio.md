---
title: Cloud Services 사용(추가 지원)(미리 보기)
description: Visual Studio에서 Azure Resource Manager를 사용하여 Azure Cloud Service를 만들고 배포하는 방법을 알아봅니다.
author: ghogen
ms.service: cloud-services-extended-support
manager: jillfra
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: tutorial
ms.date: 10/5/2020
ms.author: ghogen
ms.openlocfilehash: 80aa160c53b278137467dba2afa41384c7c4f378
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722673"
---
# <a name="create-and-deploy-a-azure-cloud-service-extended-support-using-visual-studio"></a>Visual Studio를 사용하여 Azure Cloud Service(추가 지원) 만들기 및 배포

[Visual Studio 2019 버전 16.9](https://visualstudio.microsoft.com/vs/preview/)(현재 미리 보기 상태)부터 ARM(Azure Resource Manager)을 사용하여 클라우드 서비스에서 작업할 수 있으므로 Azure 리소스의 유지 관리 및 관리를 크게 간소화하고 현대화할 수 있습니다. 이는 Cloud Services(추가 지원)라고 하는 새로운 Azure 서비스에서 사용하도록 설정됩니다. 기존 클라우드 서비스는 Cloud Services(추가 지원)에 게시할 수 있습니다. 이 Azure 서비스에 대한 자세한 내용은 [Cloud Services(추가 지원) 설명서](overview.md)를 참조하세요.

> [!IMPORTANT]
> Cloud Services(추가 지원)는 현재 공개 미리 보기에 있습니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="register-the-feature-for-your-subscription"></a>구독에 대한 기능 등록
Cloud Services(추가 지원)는 현재 미리 보기에 있습니다. 다음과 같이 구독에 대한 기능을 등록합니다.

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```
자세한 내용은 [Cloud Services 배포를 위한 필수 구성 요소](deploy-prerequisite.md)를 참조하세요.

## <a name="create-a-project"></a>프로젝트 만들기

Visual Studio는 **Azure Cloud Service(추가 지원)** 라는 추가 지원이 포함된 Azure Cloud Service를 만들 수 있는 프로젝트 템플릿을 제공합니다. Cloud Service는 간단한 범용 Azure 서비스입니다. 프로젝트가 만들어지면 Visual Studio를 사용하여 Cloud Service를 구성하고, 디버그하고, Azure에 배포할 수 있습니다.

### <a name="to-create-an-azure-cloud-service-extended-support-project-in-visual-studio"></a>Visual Studio에서 Azure Cloud Service(추가 지원) 프로젝트 만들기

이 섹션에서는 하나 이상의 웹 역할을 사용하여 Visual Studio에서 Azure Cloud Service 프로젝트를 만드는 과정을 안내합니다.

1. 시작 창에서 **새 프로젝트 만들기** 를 선택합니다.

1. 검색 상자에서 *Cloud* 를 입력한 다음, **Azure Cloud Service(추가 지원)** 를 선택합니다.

   ![추가 지원이 포함된 새 Azure Cloud Service](./media/choose-project-template.png)

1. 프로젝트 이름을 지정하고 **만들기** 를 선택합니다.

   ![프로젝트 이름 지정](./media/configure-new-project.png)

1. **새 Microsoft Azure 클라우드 서비스** 대화 상자에서 추가하려는 역할을 선택하고 오른쪽 화살표 단추를 선택하여 솔루션에 역할을 추가합니다.

    ![새 Azure Cloud Service 역할 선택](./media/choose-roles.png)

1. 추가한 역할의 이름을 바꾸려면 **새 Microsoft Azure 클라우드 서비스** 대화 상자에서 역할 위에 마우스를 놓고 상황에 맞는 메뉴에서 **이름 바꾸기** 를 선택합니다. 역할을 추가한 후 솔루션 내에서(**솔루션 탐색기** 에서) 역할의 이름을 바꿀 수도 있습니다.

    ![Azure Cloud Service 역할 이름 바꾸기](./media/new-cloud-service-rename.png)

Visual Studio Azure 프로젝트에는 솔루션의 역할 프로젝트에 대한 연결이 있습니다. 또한 *서비스 정의 파일* 및 *서비스 구성 파일* 도 포함됩니다.

- **서비스 정의 파일** - 필요한 역할, 엔드포인트 및 가상 머신 크기를 포함하여 애플리케이션에 대한 런타임 설정을 정의합니다.
- **서비스 구성 파일** - 얼마나 많은 역할의 인스턴스가 실행되는지와 역할에 대해 정의된 설정의 값을 구성합니다.

이러한 파일에 대한 자세한 내용은 [Visual Studio에서 Azure Cloud Service 역할 구성](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service)을 참조하세요.

## <a name="publish-a-cloud-service"></a>Cloud Service 게시

1. Visual Studio에서 Azure Cloud Service 프로젝트를 만들거나 엽니다.

1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고, 상황에 맞는 메뉴에서 **게시** 를 선택합니다.

   ![로그인 페이지](./media/publish-step-1.png)

1. **계정** - 계정을 선택하거나 계정 드롭다운 목록에서 **계정 추가** 를 선택합니다.

1. **구독 선택** - 배포에 사용할 구독을 선택합니다. Cloud Services(추가 지원)를 배포하는 데 사용하는 구독에는 RBAC(역할 기반 액세스 제어)를 통해 할당된 소유자 또는 기여자 역할이 있어야 합니다. 이러한 역할 중 하나가 구독에 없는 경우 계속 진행하기 전에 [역할 할당을 추가하는 단계](../role-based-access-control/role-assignments-steps.md)를 참조하여 해당 역할을 추가합니다.

1. **다음** 을 선택하여 **설정** 페이지로 이동합니다.

   ![일반 설정](./media/publish-settings.png)

1. **클라우드 서비스** - 드롭다운을 사용하여 기존 Cloud Service를 선택하거나 **새로 만들기** 선택하여 Cloud Service를 만듭니다. 데이터 센터는 각 Cloud Service에 대한 괄호 안에 표시됩니다. Cloud Service의 데이터 센터 위치는 스토리지 계정의 데이터 센터 위치와 동일하게 설정하는 것이 좋습니다.

   새 Cloud Service를 만들도록 선택하는 경우 **Cloud Service(추가 지원) 만들기** 대화 상자가 표시됩니다. Cloud Service에 사용하려는 위치 및 리소스 그룹을 지정합니다.

   ![추가 지원이 포함된 Cloud Service 만들기](./media/extended-support-dialog.png)

1. **빌드 구성** - **디버그** 또는 **릴리스** 중 하나를 선택합니다.

1. **서비스 구성** - **클라우드** 또는 **로컬** 중 하나를 선택합니다.

1. **스토리지 계정** - 이 배포에 사용할 스토리지 계정을 선택하거나, **새로 만들기** 를 선택하여 스토리지 계정을 만듭니다. 지역은 각 스토리지 계정에 대한 괄호 안에 표시됩니다. 스토리지 계정의 데이터 센터 위치는 Cloud Service의 데이터 센터 위치와 동일하게 설정하는 것이 좋습니다(일반 설정).

   Azure Storage 계정은 애플리케이션 배포용 패키지를 저장합니다. 애플리케이션이 배포된 후 패키지는 스토리지 계정에서 제거됩니다.

1. **Key Vault** - 이 Cloud Service에 대한 비밀이 포함된 Key Vault를 지정합니다. 원격 데스크톱을 사용하도록 설정하거나 인증서를 구성에 추가하는 경우 이를 사용하도록 설정됩니다.

1. **모든 역할에 대해 원격 데스크톱 사용** - 원격으로 서비스에 연결하려면 이 옵션을 선택합니다. 자격 증명을 지정하라는 메시지가 표시됩니다.

   ![원격 데스크톱 설정](./media/remote-desktop-configuration.png)

1. **다음** 을 선택하여 **진단 설정** 페이지로 이동합니다.

   ![진단 설정](./media/diagnostics-settings.png)

   진단을 사용하면 Azure Cloud Service(또는 Azure 가상 머신)의 문제를 해결할 수 있습니다. 진단에 대한 자세한 내용은 [Azure Cloud Services 및 Virtual Machines에서 진단 구성](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)을 참조하세요. Application Insights에 대한 자세한 내용은 [Application Insights란?](../azure-monitor/app/app-insights-overview.md)을 참조하세요.

1. **다음** 을 선택하여 **요약** 페이지로 이동합니다.

   ![요약](./media/publish-summary.png)

1. **대상 프로필** - 선택한 설정에서 게시 프로필을 만들도록 선택할 수 있습니다. 예를 들어, 테스트 환경에 대한 하나의 프로필을 만들고 프로덕션용으로 다른 하나를 만들 수 있습니다. 이 프로파일을 저장하려면 **저장** 아이콘을 선택합니다. 마법사는 프로필을 만들고 Visual Studio 프로젝트에 저장합니다. 프로필 이름을 수정하려면 **대상 프로필** 목록을 연 다음 **관리...** 를 선택합니다.

   > [!Note]
   > 게시 프로필은 Visual Studio의 솔루션 탐색기에 나타나며 프로필 설정은 확장명이 .azurePubxml인 파일로 기록됩니다. 설정은 XML 태그의 특성으로 저장됩니다.

1. 프로젝트 배포에 대한 설정을 모두 구성했으면 대화 상자의 아래쪽에서 **게시** 를 선택합니다. Visual Studio의 **Azure 활동 로그** 출력 창에서 프로세스 상태를 모니터링할 수 있습니다.

축하합니다! 추가 지원 Cloud Service 프로젝트를 Azure에 게시했습니다. 동일한 설정을 사용하여 다시 게시하려면 게시 프로필을 다시 사용하거나 이러한 단계를 반복하여 새 게시 프로필을 만들 수 있습니다.

## <a name="clean-up-azure-resources"></a>Azure 리소스 정리

이 자습서에 따라 만든 Azure 리소스를 정리하려면 [Azure Portal](https://portal.azure.com)로 이동하여 **리소스 그룹** 을 선택하고, 서비스를 만드는 데 사용한 리소스 그룹을 찾아서 열고, **리소스 그룹 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

**게시** 화면에서 **구성** 단추를 사용하여 CI(연속 통합)를 설정합니다. 자세한 내용은 [Azure Pipelines 설명서](/azure/devops/pipelines)를 참조하세요.