---
title: "Visual Studio를 사용하여 Azure Stack에 템플릿 배포 | Microsoft Docs"
description: "Azure Stack에서 Visual Studio를 사용하여 템플릿을 배포하는 방법에 대해 알아봅니다."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: ecefa8c3695aad0593516cb9ad8812216e163192
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Visual Studio를 사용하여 Azure Stack의 템플릿 배포

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Visual Studio를 사용 하 여 Azure 리소스 관리자 템플릿을 Azure 스택 개발 키트를 배포 합니다.

1. Visual Studio를 사용하여 Azure Stack에 [설치하고 연결합니다](azure-stack-install-visual-studio.md).
2. Visual Studio를 엽니다.
3. **파일**, **새로 만들기**를 차례로 클릭하고 **새 프로젝트** 대화 상자에서 **Azure 리소스 그룹**을 클릭합니다.
4. 새 프로젝트에 대한 **이름**을 입력한 다음 **확인**을 클릭합니다.
5. **Azure 템플릿 선택** 대화 상자에서 *이 위치에서 템플릿 표시* 드롭다운을 **Azure Stack 빠른 시작**으로 변경합니다.
6. 클릭 **101--저장소-계정 만들기**, 클릭 하 고 **확인**합니다.  
7. 새 프로젝트에서 **솔루션 탐색기** 창에서 **템플릿** 노드를 확장하여 사용할 수 있는 템플릿 목록을 볼 수 있습니다.
8. **솔루션 탐색기** 창에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **배포**를 클릭한 다음 **새 배포**를 클릭합니다.
9. **리소스 그룹에 배포** 대화 상자의 **구독** 드롭다운에서 Microsoft Azure Stack 구독을 선택합니다.
10. **리소스 그룹** 목록에서 기존 리소스 그룹을 선택하거나 새 항목을 만듭니다.
11. **리소스 그룹 위치** 목록에서 위치를 선택한 다음 **배포**를 클릭합니다.
12. **매개 변수 편집** 대화 상자에서 매개 변수의 값(템플릿에 따라 다름)을 입력한 다음 **저장**을 클릭합니다.

## <a name="next-steps"></a>다음 단계
[명령줄을 사용하여 템플릿 배포](azure-stack-deploy-template-command-line.md)

[Azure Stack용 템플릿 개발](azure-stack-develop-templates.md)

