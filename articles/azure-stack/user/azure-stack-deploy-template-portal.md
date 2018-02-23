---
title: "Azure 스택에서 포털을 사용 하 여 템플릿을 배포 | Microsoft Docs"
description: "Azure Stack 포털을 사용하여 템플릿을 배포하는 방법에 대해 알아봅니다."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 30c077747685ad7617148e9130926120420b098b
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Azure Stack 포털을 사용하여 템플릿 배포

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 개발 키트를 Azure 리소스 관리자 템플릿을 배포 하는 포털을 사용 합니다.

리소스 관리자 템플릿 배포 및 조정를 단일 작업에서 응용 프로그램에 대 한 모든 리소스를 프로 비전 합니다.

1. 포털에 로그인을 클릭 **새로**, 클릭 **사용자 지정**, 클릭 하 고 **템플릿 배포**합니다.
2. 클릭 **템플릿 편집**블레이드를 붙여 JSON 템플릿 코드를 한 다음 클릭 **저장**합니다.
3. 클릭 **매개 변수 편집**, 나열 된 매개 변수에 대 한 값을 입력 하 고 클릭 **확인**합니다.
4. 클릭 **구독**를 사용 하 고 클릭 하려는 구독 선택 **확인**합니다.
5. 클릭 **리소스 그룹**, 기존 리소스 그룹을 선택 하거나 새 대시보드를 만든 하 고 클릭 **확인**합니다.
6. **만들기**를 클릭합니다. 대시보드의 새 타일은 템플릿 배포의 진행 상태를 추적합니다.

## <a name="next-steps"></a>다음 단계
[PowerShell을 사용하여 템플릿 배포](azure-stack-deploy-template-powershell.md)

