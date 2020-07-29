---
title: ARM 템플릿 faq (질문과 대답)
description: Azure Resource Manager 템플릿에 대 한 FAQ (질문과 대답)입니다.
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: e23334daf0f4f6d5a6917af49c786d47c4557375
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327262"
---
# <a name="frequently-asked-questions-about-arm-templates"></a>ARM 템플릿에 대 한 질문과 대답

이 문서에서는 ARM (Azure Resource Manager) 템플릿에 대 한 자주 묻는 질문에 답변 합니다.

## <a name="getting-started"></a>시작

* **ARM 템플릿 이란 무엇 이며 사용 해야 하는 이유는 무엇 인가요?**

  ARM 템플릿은 Azure에 배포 하려는 항목을 정의 하는 JSON 파일입니다. 템플릿을 통해 Azure에 대 한 코드 인프라 솔루션을 구현할 수 있습니다. 조직은 필요한 인프라를 반복적이 고 안정적으로 다양 한 환경에 배포할 수 있습니다.
  
  ARM 템플릿을 통해 Azure 인프라를 관리 하는 방법에 대 한 자세한 내용은 [arm 템플릿 이란?](overview.md) 을 참조 하세요.

* **템플릿을 시작 어떻게 할까요??**

  ARM 템플릿 제작을 간소화 하려면 올바른 도구가 필요 합니다. [Visual Studio Code](https://code.visualstudio.com/) 및 [Azure Resource Manager 도구 확장](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)을 설치 하는 것이 좋습니다. 이러한 도구에 대 한 간략 한 소개는 [퀵 스타트: Visual Studio Code를 사용 하 여 템플릿 만들기 Azure Resource Manager](quickstart-create-templates-use-visual-studio-code.md)를 참조 하세요.

  ARM 템플릿을 만드는 방법에 대 한 자세한 내용은 [arm 템플릿에서 초보자를 위한 자습서 시리즈](template-tutorial-create-first-template.md)를 시작 하세요. 이러한 자습서에서는 ARM 템플릿을 생성 하는 과정을 단계별로 안내 합니다. 템플릿의 다양 한 섹션과이를 함께 사용 하는 방법에 대해 알아봅니다. 이 콘텐츠는 [Microsoft Learn 모듈로](/learn/modules/authoring-arm-templates/)도 제공 됩니다.

* **ARM 템플릿 또는 Terraform을 사용 하 여 Azure에 배포 해야 하나요?**

  가장 적합 한 옵션을 사용 하세요. 두 서비스 모두 Azure에 대 한 배포를 자동화 하는 데 도움이 됩니다.
  
  다른 인프라 코드 서비스에서 ARM 템플릿을 사용할 경우의 이점이 있다고 생각 합니다. 이러한 이점에 대 한 자세한 내용은 [ARM 템플릿 선택 이유](overview.md#why-choose-arm-templates) 를 참조 하세요.

## <a name="build-2020"></a>빌드 2020

* **Microsoft Build 2020에서 프레젠테이션을 누락 했습니다. 프레젠테이션을 볼 수 있나요?**

  예, [언제 든 지 시청](https://mybuild.microsoft.com/sessions/82984db4-37a4-4ed3-bf8b-13298841ed18?source=sessions)하세요.

* **빌드 시 발표 한 새로운 기능에 대 한 자세한 정보는 어디서 얻을 수 있나요?**

  작업 중인 기능에 대 한 일반적인 내용은 [Azure Advisor 배포 Yammer 그룹](https://aka.ms/ARMMeet)을 참여 하세요.

  새 템플릿 언어에 대해 알아보려면 [알림에 등록](https://aka.ms/armLangUpdates)합니다.

  템플릿 사양에 대 한 자세한 내용은 [Azure Resource Manager 템플릿 사양 (미리 보기)](template-specs.md)을 참조 하세요.

## <a name="creating-and-testing-templates"></a>템플릿 만들기 및 테스트

* **ARM 템플릿에 대 한 모범 사례에 대 한 자세한 내용은 어디서 확인할 수 있나요?**

  템플릿을 구현 하는 방법에 대 한 권장 사항은 [ARM 템플릿 모범 사례](template-best-practices.md)를 참조 하세요. 템플릿을 만든 후 [ARM 테스트 도구 키트](https://github.com/azure/arm-ttk)를 실행 합니다. 템플릿이 권장 사례와 일치 하는지 여부를 확인 합니다.

* **포털을 통해 내 환경을 설정 했습니다. 기존 리소스 그룹에서 템플릿을 가져오는 방법이 있나요?**

  예, 리소스 그룹에서 [템플릿을 내보낼](export-template-portal.md) 수 있습니다. 내보낸 템플릿은 템플릿에 대 한 학습을 위한 좋은 출발점 이지만 프로덕션 환경에서 사용 하기 전에 수정 해야 할 수도 있습니다.
  
  템플릿을 내보낼 때 템플릿에 포함할 리소스를 선택할 수 있습니다.

* **ARM 템플릿에서 리소스 그룹을 만들고 여기에 리소스를 배포할 수 있나요?**

  예, Azure 구독 수준에서 템플릿을 배포할 때 템플릿에서 리소스 그룹을 만들 수 있습니다. 리소스 그룹을 만들고 리소스를 배포 하는 방법에 대 한 예제는 [리소스 그룹 및 리소스](deploy-to-subscription.md#resource-groups)를 참조 하세요.

* **ARM 템플릿에서 구독을 만들 수 있나요?**

  아직 하지는 않지만 작업 하는 중입니다.

* **템플릿을 배포 하기 전에 테스트 하려면 어떻게 해야 하나요?**

  [ARM 테스트 도구 키트](https://github.com/azure/arm-ttk) 와 템플릿에서 [가상 작업](template-deploy-what-if.md) 을 배포 하기 전에 실행 하는 것이 좋습니다. 테스트 도구 키트는 템플릿이 모범 사례를 사용 하는지 여부를 확인 합니다. 템플릿을 구현 하는 방법을 향상 시킬 수 있는 변경 내용을 식별 하는 경우 경고를 제공 합니다.

  가상 작업에서는 템플릿이 사용자 환경에 적용 하는 변경 내용을 보여 줍니다. 배포 되기 전에 의도 하지 않은 변경 내용을 볼 수 있습니다. 는 실행 전 유효성 검사 중에 검색할 수 있는 오류도 반환 합니다. 예를 들어 템플릿에 구문 오류가 있는 경우 해당 오류를 반환 합니다. 또한 배포 된 리소스의 최종 상태에 대해 확인할 수 있는 모든 오류를 반환 합니다. 예를 들어 템플릿에서 이미 사용 중인 이름으로 저장소 계정을 배포 하는 경우에는에서 해당 오류를 반환 합니다.

* **각 리소스 유형에 사용할 수 있는 속성에 대 한 정보는 어디서 찾을 수 있나요?**

  VS Code는 리소스 속성을 사용 하기 위한 intellisense를 제공 합니다. 속성 및 설명에 대 한 [템플릿 참조](/azure/templates/) 를 볼 수도 있습니다.

* **리소스 유형의 인스턴스를 여러 개 만들어야 합니다. 내 템플릿에서 반복기를 만들 어떻게 할까요? 있나요?**

  Copy 요소를 사용 하 여 둘 이상의 인스턴스를 지정 합니다. [리소스](copy-resources.md), [속성](copy-properties.md), [변수](copy-variables.md)및 [출력](copy-outputs.md)에 대 한 복사를 사용할 수 있습니다.

## <a name="template-language"></a>템플릿 언어

* **새 템플릿 언어로 작업 하 고 있습니다. 이에 대 한 자세한 내용은 어디서 확인할 수 있나요?**

  새 템플릿 언어에 대해 알아보려면 [알림에 등록](https://aka.ms/armLangUpdates)합니다.

* **YAML에서 템플릿 만들기를 지원 하기 위한 계획이 있나요?**

  현재는 YAML을 지원할 계획이 없습니다. 새 템플릿 언어가 YAML 또는 JSON 보다 사용 하기 쉬운 솔루션을 제공 하는 것으로 생각 합니다.

* **새 템플릿 언어가 출시 된 후에도 여전히 JSON에서 템플릿을 작성할 수 있나요?**

  예, JSON 템플릿을 계속 사용할 수 있습니다.

* **JSON 템플릿을 새 템플릿 언어로 변환 하는 도구를 제공 하나요?**

  예.

## <a name="template-specs"></a>템플릿 사양

* **템플릿 사양의 preview 릴리스에는 어떻게 참여할 수 있나요?**

  템플릿 사양의 [대기 목록에 조인](https://aka.ms/templateSpecsWaitlist) 합니다.

* **템플릿 사양 및 Azure 청사진은 어떻게 관련 되나요?**

  Azure 청사진은 리소스를 리소스로 바꿔 구현에서 템플릿 사양을 사용 `blueprint definition` `template spec` 합니다. 청사진 정의를 템플릿 사양으로 변환 하는 마이그레이션 경로를 제공 하지만 청사진 정의 Api는 계속 지원 됩니다. 리소스에 대 한 변경 내용이 없습니다 `blueprint assignment` . 청사진은 Azure에서 관리 되는 환경을 구성 하기 위한 사용자 환경으로 유지 됩니다.

* **템플릿 사양이 연결 된 템플릿을 바꾸시겠습니까?**

  아니요. 하지만 템플릿 사양은 연결 된 템플릿에서 잘 작동 하도록 설계 되었습니다. 부모 템플릿을 배포 하기 전에 연결 된 템플릿을 공개적으로 액세스할 수 있는 끝점으로 이동할 필요가 없습니다. 대신 템플릿 사양을 만들 때 부모 템플릿과 해당 아티팩트를 함께 패키지할 수 있습니다.

* **구독에서 템플릿 사양을 공유할 수 있나요?**

  예, 사용자가 템플릿 사양에 대 한 읽기 권한이 있는 한 구독에서 사용할 수 있습니다. 템플릿 사양은 테 넌 트 간에 사용할 수 없습니다.

## <a name="scripts-in-templates"></a>템플릿의 스크립트

* **템플릿에서 불가능 한 작업을 수행 하기 위해 템플릿에 스크립트를 포함할 수 있나요?**

  예, [배포 스크립트](deployment-script-template.md)를 사용 합니다. 템플릿에 Azure PowerShell 또는 Azure CLI 스크립트를 포함할 수 있습니다. 이 기능은 미리 보기 상태입니다.

* **사용자 지정 스크립트 확장 및 DSC (필요한 상태 구성)를 계속 사용할 수 있나요?**

  이러한 옵션은 계속 사용할 수 있으며 변경 되지 않습니다. 배포 스크립트는 VM 게스트와 관련 되지 않은 작업을 수행 하도록 설계 되었습니다. VM의 호스트 운영 체제에서 스크립트를 실행 해야 하는 경우 사용자 지정 스크립트 확장 및/또는 DSC를 선택 하는 것이 더 좋습니다. 그러나 배포 스크립트에는 제한 시간을 설정 하는 것과 같은 장점이 있습니다.

* **Azure Government에서 배포 스크립트가 지원 되나요?**

  예, US Gov 애리조나 및 US Gov 버지니아의 배포 스크립트를 사용할 수 있습니다.

## <a name="preview-changes-before-deployment"></a>배포 전 변경 내용 미리 보기

* **템플릿을 배포 하기 전에 수행 되는 변경 내용을 미리 볼 수 있나요?**

  예, 가상의 [기능](template-deploy-what-if.md)을 사용 합니다. 환경의 현재 상태를 평가 하 고 배포 후 존재 하는 상태와 비교 합니다. 요약 된 변경 내용을 검토 하 여 템플릿에 예기치 않은 결과가 포함 되지 않도록 할 수 있습니다.

* **모든 기능을 증분 모드와 전체 모드로 사용할 수 있나요?**

  예, 두 [배포 모드가](deployment-modes.md) 지원 됩니다. 증분 모드 사용에 대 한 예제는 [what-if 작업 실행](template-deploy-what-if.md#run-what-if-operation)을 참조 하세요. 전체 모드를 사용 하는 예제는 [삭제 확인](template-deploy-what-if.md#confirm-deletion)을 참조 하세요.

* **연결 된 템플릿에 대 한 작업을 수행 하나요?**

  예, 가상에서 부모 템플릿 및 연결 된 템플릿의 상태를 평가 합니다.

* **Azure 파이프라인에서 무엇을 사용할 수 있나요?**

  예, 가상의 경우 파이프라인을 계속 해야 하는지 확인 하는 데 사용할 수 있습니다.

* **-If를 사용 하는 경우 내 템플릿에 없는 속성의 변경 내용이 표시 됩니다. 이 "노이즈"가 필요 한가요?**

  What은 미리 보기 상태입니다. 노이즈를 줄이기 위해 노력 하 고 있습니다. GitHub 리포지토리에서 문제를 제출 하 여 개선 하는 데 도움을 주세요.https://aka.ms/WhatIfIssues

## <a name="template-visualizer"></a>템플릿 시각화 도우미

* **내 ARM 템플릿 및 해당 리소스를 시각화 하는 방법이 있나요?**

  ARM 템플릿을 시각화 하는 뛰어난 작업을 수행 하는 [커뮤니티 제공 VS Code 확장이](https://aka.ms/ARMVisualizer) 있습니다. 배포 하는 리소스와 둘 간의 관계를 보여 줍니다.

* **VS Code 외부에서 템플릿 시각화 도우미를 사용할 수 있나요?**

  포털에서 템플릿 시각화를 미리 보는 중인 경우 자세한 내용은 [빌드에서이 짧은 세션](https://mybuild.microsoft.com/sessions/0525094b-1fd2-4f69-bfd6-6d2fff6ffe5f?source=sessions)을 시청 하세요.

## <a name="deployment-limits"></a>배포 제한

* **단일 배포 작업에서에 배포할 수 있는 리소스 그룹은 몇 개입니까?**

  이전에는이 한도는 5 개의 리소스 그룹 이었습니다. 최근 800 리소스 그룹으로 늘어났습니다. 자세한 내용은 [구독 수준에서 리소스 그룹 및 리소스 만들기](deploy-to-subscription.md)를 참조 하세요.

* **배포 기록에서 800 배포로 제한 되는 것에 대 한 오류가 발생 했습니다. 제가 뭘 해야 하나요?**

  리소스 그룹에 대 한 배포 기록이 유지 되는 방식을 변경 하는 중입니다. 이전에이 오류를 방지 하려면이 기록에서 배포를 수동으로 삭제 해야 했습니다. 6 월 2020부터 제한에 도달 하면 자동으로 기록에서 배포를 삭제 합니다. 자세한 내용은 [배포 기록에서 자동 삭제](deployment-history-deletions.md)를 참조 하세요.

  기록에서 배포를 삭제 해도 배포 된 리소스에는 영향을 주지 않습니다.

## <a name="templates-and-devops"></a>템플릿 및 DevOps

* **ARM 템플릿을 Azure Pipelines에 통합할 수 있나요?**

  예. 템플릿 및 파이프라인을 사용 하는 방법에 대 한 설명은 [자습서: Azure Pipelines를 사용 하 여 Azure Resource Manager 템플릿 연속 통합](deployment-tutorial-pipeline.md) 및 [ARM 템플릿을 Azure Pipelines와 통합](add-template-to-azure-pipelines.md)을 참조 하세요.

* **GitHub 작업을 사용 하 여 템플릿을 배포할 수 있나요?**

  예, [GitHub 작업을 사용 하 여 Azure Resource Manager 템플릿 배포](deploy-github-actions.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

ARM 템플릿에 대 한 소개는 [arm 템플릿 이란?](overview.md)을 참조 하세요.
