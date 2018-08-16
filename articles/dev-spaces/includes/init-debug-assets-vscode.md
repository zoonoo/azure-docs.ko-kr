---
title: 포함 파일
description: 포함 파일
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: b8778067b86d4936ac0cbcfb94d0a812d5ce2bce
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129276"
---
### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>VS Code 확장을 사용하여 디버그 자산 초기화
먼저 VS Code가 Azure에서 개발 환경과 통신하도록 코드 프로젝트를 구성해야 합니다. Azure Dev Spaces에 대한 VS Code 확장은 디버그 구성을 설정하는 도우미 명령을 제공합니다. 

**명령 팔레트**(**보기 | 명령 팔레트** 메뉴를 사용하여)를 열고, 자동 완성을 사용하여 입력하고 이 명령을 선택합니다. `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces` 

이렇게 하면 `.vscode` 폴더 아래에 Azure Dev Spaces에 대한 디버그 구성이 추가됩니다.

![](../media/common/command-palette.png)
