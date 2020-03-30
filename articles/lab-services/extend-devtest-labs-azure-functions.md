---
title: Azure 함수를 사용하여 Azure DevTest 랩 확장 | 마이크로 소프트 문서
description: Azure 함수를 사용하여 Azure DevTest 랩을 확장하는 방법에 대해 알아봅니다.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: dd1fc4c1076d89c12b25837db9fa6a0ac3e1f3a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70014361"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Azure Functions를 사용하여 DevTest Labs 연장
Azure 함수를 사용하여 DevTest Labs에서 이미 지원중인 시나리오 이외의 추가 시나리오를 지원할 수 있습니다. Azure 함수를 사용하여 비즈니스별 요구 사항을 충족하기 위해 서비스의 기본 제공 기능을 확장할 수 있습니다. 다음 목록은 몇 가지 가능한 시나리오를 제공합니다. 이 문서에서는 이러한 샘플 시나리오 중 하나를 구현하는 방법을 보여 주며 이 문서에서는 이러한 샘플 시나리오 중 하나를 구현하는 방법을 보여 주며 이 문서에서는 이러한 샘플 시나리오를 구현합니다.

- 랩에서 VM(가상 머신)에 대한 최상위 요약 제공
- [원격 데스크톱 게이트웨이를 사용하도록 랩 구성](configure-lab-remote-desktop-gateway.md)
- 내부 지원 페이지에서 규정 준수 보고
- 사용자가 구독에서 권한 증가를 필요로 하는 작업을 완료할 수 있도록 합니다.
- [DevTest Labs 이벤트를 기반으로 워크플로 시작](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>개요
[Azure Functions는](../azure-functions/functions-overview.md) Azure의 서버리스 컴퓨팅 플랫폼입니다. DevTest Labs를 사용하여 솔루션에서 Azure 함수를 사용하면 사용자 지정 코드로 기존 기능을 보강할 수 있습니다. Azure 함수에 대한 자세한 내용은 [Azure Functions 설명서를](../azure-functions/functions-overview.md)참조하십시오. Azure Functions가 DevTest Labs에서 요구 사항 또는 전체 시나리오를 충족하는 데 어떻게 도움이 되는지 설명하기 위해 이 문서에서는 다음과 같이 랩에서 VM에 대한 최상위 요약을 제공하는 예제를 사용합니다.

**요구 사항/시나리오 예제:** 사용자는 운영 체제, 소유자 및 적용된 아티팩트를 포함하여 랩의 모든 VM에 대한 세부 정보를 볼 수 있습니다.  또한 **Windows 업데이트 적용** 아티팩트가 최근에 적용되지 않은 경우 쉽게 적용할 수 있습니다.

시나리오를 완료하려면 다음 다이어그램에 설명된 대로 두 가지 함수를 사용합니다.  

![전체 흐름](./media/extend-devtest-labs-azure-functions/flow.png)

이러한 샘플 함수의 소스 코드는 [DevTest Labs GitHub 리포지토리에](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) 있습니다(C# 및 PowerShell 구현을 모두 사용할 수 있음).

- **업데이트내부지원페이지**: 이 함수는 DevTest Labs를 쿼리하고 가상 시스템에 대한 세부 정보로 내부 지원 페이지를 직접 업데이트합니다.
- **ApplyWindowsUpdate 아티팩트**: 랩의 VM의 경우 이 함수는 **Windows 업데이트** 아티팩트를 적용합니다.

## <a name="how-it-works"></a>작동 방법
사용자가 DevTest Labs에서 **내부 지원** 페이지를 선택하면 VM, 랩 소유자 및 지원 연락처에 대한 정보가 있는 미리 채워진 페이지가 있습니다.  

**새로 고치려면 여기 선택** 단추를 선택하면 페이지가 첫 번째 Azure 함수인 **UpdateInternalSupportPage 를 호출합니다.** 이 함수는 DevTest Labs에 정보를 쿼리한 다음 **내부 지원** 페이지를 새 정보로 다시 작성합니다.

Windows 업데이트 아티팩트가 최근에 적용되지 않은 VM에 대해 Windows 업데이트를 적용하기 위한 버튼이 있을 것입니다. VM에 대한 ***Windows 업데이트 단추실행** 단추를 선택하면 페이지는 두 번째 Azure 함수인 **ApplyWindowsUpdate아티팩트**를 호출합니다. 이 함수는 가상 시스템이 실행 되고 있는지 여부를 확인하고 실행 중인 경우 [Windows Update](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) 아티팩트를 직접 적용합니다.

## <a name="step-by-step-walkthrough"></a>단계별 안내
이 섹션에서는 **내부 지원** 페이지를 업데이트하는 데 필요한 Azure 리소스를 설정하기 위한 단계별 지침을 제공합니다. 이 연습에서는 DevTest 랩 을 확장하는 한 가지 예를 제공합니다. 다른 시나리오에 이 패턴을 사용할 수 있습니다.

### <a name="step-1-create-a-service-principal"></a>1단계: 서비스 주체 만들기 
첫 번째 단계는 랩을 포함하는 구독에 대한 권한이 있는 서비스 주체를 얻는 것입니다. 서비스 주체는 암호 기반 인증을 사용해야 합니다. [Azure CLI,](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)또는 Azure [포털을](../active-directory/develop/howto-create-service-principal-portal.md)통해 수행할 수 있습니다. 사용할 서비스 주체가 이미 있는 경우 이 단계를 건너뛸 수 있습니다.

서비스 주체에 대한 **응용 프로그램 ID,** **키**및 **테넌트 ID를** 적어 둡자합니다. 이 연습의 나중에 필요합니다. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>2단계: 샘플을 다운로드하여 Visual Studio 2019에서 열기
[C# Azure Functions 샘플의 복사본을](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) 로컬로 다운로드합니다(리포지토리를 복제하거나 [여기에서](https://github.com/Azure/azure-devtestlab/archive/master.zip)리포지토리를 다운로드하여).  

1. Visual Studio 2019를 사용하여 샘플 솔루션을 엽니다.  
1. 이미 설치되어 있지 않은 경우 Visual Studio용 **Azure 개발** 워크로드를 설치합니다. **그것은 도구를** -> 통해 설치할 수 있습니다**도구 도구 및 기능** 메뉴 항목).

    ![Azure 개발 워크로드](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. 솔루션을 빌드합니다. **빌드를** 선택한 다음 솔루션 메뉴 항목 **빌드를** 선택합니다.

### <a name="step-3-deploy-the-sample-to-azure"></a>3단계: Azure에 샘플 배포
시각적 스튜디오에서 솔루션 **탐색기** 창에서 **AzureFunctions** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **게시를**선택합니다. 마법사에 따라 새 앱 또는 기존 Azure Function 앱에 대한 게시를 완료합니다. Visual Studio를 사용하여 Azure 함수 개발 및 배포에 대한 자세한 내용은 [Visual Studio를 사용하여 Azure 함수 개발을](../azure-functions/functions-develop-vs.md)참조하십시오.

![게시 대화 상자](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>4단계: 애플리케이션 설정 수집
함수가 게시되면 Azure 포털에서 이러한 기능에 대한 URL을 얻어야 합니다. 

1. [Azure 포털로](https://portal.azure.com)이동합니다. 
1. 함수 앱을 찾습니다.
1. 함수 **앱** 페이지에서 함수를 선택합니다. 
1. 다음 이미지와 같이 **기능 URL 받기를** 선택합니다. 

    ![Azure 기능 URL](./media/extend-devtest-labs-azure-functions/function-url.png)
4. URL을 복사하고 저장합니다. 다른 Azure 함수에 대해 다음 단계를 반복합니다. 

또한 응용 프로그램 ID, 키 및 테넌트 ID와 같은 서비스 주체에 대한 추가 정보가 필요합니다.


### <a name="step-5--update-application-settings"></a>5단계: 응용 프로그램 설정 업데이트
Visual Studio에서 Azure 함수를 게시한 후 **작업**에서 **Azure 앱 서비스 설정 편집을** 선택합니다. 다음 응용 프로그램 설정(원격)을 업데이트합니다.

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdate허용일(기본값 7)으로
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![애플리케이션 설정](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>6단계: Azure 함수 테스트
이 연습의 마지막 단계는 Azure 함수를 테스트하는 것입니다.  

1. 3단계에서 만든 함수 앱에서 **UpdateInternalSupportPage** 기능으로 이동합니다. 
1. 페이지 오른쪽에서 **테스트를** 선택합니다. 
1. 경로 속성(LABNAME, 리소스 그룹 이름 및 멤버쉽ID)을 입력합니다.
1. **실행을** 선택하여 함수를 실행합니다.  

    이 함수는 지정된 랩의 내부 지원 페이지를 업데이트합니다. 또한 사용자가 다음 번에 함수를 직접 호출할 수 있는 버튼도 포함되어 있습니다.

    ![테스트 기능](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>다음 단계
Azure Functions는 DevTest Labs의 기능을 이미 기본 제공한 것 이상으로 확장하고 고객이 팀에 대한 고유한 요구 사항을 충족하는 데 도움이 될 수 있습니다. 이 패턴은 더 커버하기 위해 더 확장 & 확장 할 수 있습니다.  DevTest 랩에 대한 자세한 내용은 다음 문서를 참조하세요. 

- [DevTest 랩 엔터프라이즈 참조 아키텍처](devtest-lab-reference-architecture.md)
- [질문과 대답](devtest-lab-faq.md)
- [개발자 테스트 랩 확장](devtest-lab-guidance-scale.md)
- [파워쉘로 데브테스트 랩 자동화](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








