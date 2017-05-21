---
title: "Azure Cloud Shell(미리 보기) 창 사용 | Microsoft Docs"
description: "Azure Cloud Shell 창을 둘러봅니다."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 4e78dadb7fa036dcb4c5706fe774cbd85d57a746
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="using-the-shell-window"></a>셸 창 사용
이 문서에는 Azure Cloud Shell 창을 사용하는 방법에 대해 설명합니다.

## <a name="concurrent-sessions"></a>동시 세션
Cloud Shell을 사용하면 각 세션이 별도의 Bash 프로세스로 존재할 수 있게 하여 브라우저 탭에서 여러 개의 세션을 동시에 수행할 수 있습니다.
세션을 종료하는 경우 동일한 컴퓨터에서 실행하더라도 각 프로세스가 독립적으로 실행되므로 각 세션 창을 개별적으로 종료해야 합니다.

## <a name="restart-cloud-shell"></a>Cloud Shell 다시 시작
![](media/recycle.png)
* Cloud Shell 컴퓨터를 다시 설정하려면 도구 모음에서 다시 시작 아이콘을 클릭합니다.

> ![경고] Cloud Shell을 다시 시작하면 컴퓨터 상태가 다시 설정되고 클라우드 드라이브에서 유지하지 않는 파일은 모두 손실됩니다.

## <a name="minimize--maximize-cloud-shell-window"></a>Cloud Shell 창 최소화 및 최대화
![](media/minmax.png)
* 창을 숨기려면 창의 오른쪽 위에 있는 최소화 아이콘을 클릭합니다. 숨기기를 취소하려면 Cloud Shell 아이콘을 다시 클릭합니다.
* 창을 최대 높이로 설정하려면 최대화 아이콘을 클릭합니다. 창을 이전 크기로 복원하려면 복원 아이콘을 클릭합니다.

## <a name="copy-and-paste"></a>복사 및 붙여넣기
* Windows: `Ctrl-insert`와 `Shift-insert`를 누르거나 마우스 오른쪽 단추로 클릭하여 복사하거나 붙여넣습니다.
  * FireFox/IE에서 클립보드 사용 권한을 제대로 지원하지 않을 수 있습니다.
* Mac OS: `Cmd-c`와 `Cmd-v`를 누르거나 마우스 오른쪽 단추로 클릭하여 복사하거나 붙여넣습니다.

## <a name="resize-cloud-shell-window"></a>Cloud Shell 창 크기 조정
* 도구 모음의 위쪽 가장자리를 클릭한 채 위/아래로 끌어 Cloud Shell 창의 크기를 조정합니다.

## <a name="scrolling-text-display"></a>텍스트 표시 스크롤
* 마우스 또는 터치 패드로 스크롤합니다.

## <a name="exit-command"></a>종료 명령
`exit`를 실행하면 활성 세션이 종료됩니다. 이 동작은 기본적으로 상호 작용 없이 10분 후에 발생합니다.

## <a name="next-steps"></a>다음 단계
[Cloud Shell 빠른 시작](quickstart.md)  
