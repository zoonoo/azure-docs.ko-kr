---
title: UI 정의 파일 테스트
description: 포털을 통해 Azure Managed Application을 만들기 위해 사용자 환경을 테스트하는 방법을 설명합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: tomfitz
ms.openlocfilehash: 55a966c17f3bcd51f354198e36e03071efd4834d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111951480"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Azure Managed Applications에 대한 포털 인터페이스 테스트

관리 애플리케이션에 대해 [createUiDefinition.json 파일을 만든](create-uidefinition-overview.md) 후에는 사용자 환경을 테스트해야 합니다. 테스트를 간소화하려면 포털에서 파일을 로드하는 샌드박스 환경을 사용합니다. 실제로 관리되는 애플리케이션을 배포할 필요는 없습니다. 샌드박스는 현재 전체 화면 포털 환경에서 사용자 인터페이스를 제공합니다. 샌드박스는 인터페이스 미리 보기에 권장되는 방법입니다.

## <a name="prerequisites"></a>사전 요구 사항

* **createUiDefinition.json** 파일. 이 파일이 없으면 [샘플 파일](https://github.com/Azure/azure-quickstart-templates/blob/master/demos/100-marketplace-sample/createUiDefinition.json)을 복사합니다.

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="use-sandbox"></a>샌드박스 사용

1. [UI 정의 샌드박스 만들기](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)를 엽니다.

   ![샌드박스 표시](./media/test-createuidefinition/show-sandbox.png)

1. 빈 정의를 파일에 createUiDefinition.json의 콘텐츠로 바꿉니다. **미리 보기** 를 선택합니다.

   ![미리 보기 선택](./media/test-createuidefinition/select-preview.png)

1. 만든 양식이 표시됩니다. 사용자 환경에 따라 값을 입력할 수 있습니다.

   ![양식 표시](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>문제 해결

**미리 보기** 를 선택하여도 양식이 표시되지 않으면 구문 오류가 있을 수 있습니다. 오른쪽 스크롤 막대에서 빨간색 표시기를 찾아 이동합니다.

![구문 오류 표시](./media/test-createuidefinition/show-syntax-error.png)

양식이 표시되지 않고 눈물 방울이 있는 구름 아이콘이 표시되면 양식에 속성 누락과 같은 오류가 있는 것입니다. 브라우저에서 웹 개발자 도구를 엽니다. **콘솔** 은 인터페이스에 관한 중요 메시지를 표시합니다.

![오류 표시](./media/test-createuidefinition/show-error.png)

## <a name="test-your-solution-files"></a>솔루션 파일 테스트

이제 포털 인터페이스가 예상대로 작동하는 것을 확인했으므로 createUiDefinition 파일이 mainTemplate.json 파일과 제대로 통합되었는지 유효성을 검사할 시점입니다. 유효성 검사 스크립트 테스트를 실행하여 createUiDefinition 파일 등, 솔루션 파일의 콘텐츠를 테스트할 수 있습니다. 이 스크립트는 JSON 구문 유효성을 검사하고 텍스트 필드에서 Regex 식을 검사하며 포털 인터페이스의 출력 값이 템플릿 매개 변수와 ㅇ리치하는지 확인합니다. 이 스크립트 실행에 대한 자세한 내용은 [템플릿에 대해 정적 유효성 검사 실행](https://aka.ms/arm-ttk)을 참조하세요.

## <a name="next-steps"></a>다음 단계

포털 인터페이스 유효성 검사 후 [Marketplace에서 Azure Managed Application](../../marketplace/azure-app-offer-setup.md)을 제공하는 방법을 알아보세요.