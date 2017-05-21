---
title: "Azure Cloud Shell(미리 보기) 제한 사항 | Microsoft Docs"
description: "Azure Cloud Shell의 제한 사항 개요"
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
ms.openlocfilehash: a48b32d3e2383497f68ab4eba81af81dd9f8449d
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="limitations"></a>제한 사항
Azure Cloud Shell에는 다음과 같이 알려진 제한 사항이 있습니다.

## <a name="system-state-and-persistence"></a>시스템 상태 및 지속성
Cloud Shell 세션을 제공하는 컴퓨터는 일시적이며 세션이 10분 동안 비활성화된 후 재순환됩니다. Cloud Shell에 파일 공유를 탑재해야 합니다.
* 탑재된 저장소에서 `$Home` 디렉터리 또는 `clouddrive` 디렉터리 내 수정 사항만 유지됩니다.
  * 파일 공유는 [할당된 지역](persisting-shell-storage.md#pre-requisites-for-manual-mounting) 내에서만 탑재될 수 있습니다.
  * Azure Files는 LRS 및 GRS 저장소 계정만 지원합니다.

## <a name="user-permissions"></a>사용자 권한
권한은 sudo 액세스 권한이 없는 일반 사용자로 설정됩니다. 사용자 Home이 아닌 곳에서의 설치는 유지되지 않습니다.
`clouddrive` 디렉터리 내 `git clone`과 같은 특정 명령에는 적절한 사용 권한이 없지만 사용자의 Home 디렉터리에는 있습니다.

## <a name="browser-support"></a>브라우저 지원
Cloud Shell은 Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox 및 Apple Safari의 최신 버전을 지원합니다. Safari는 개인 모드에서 지원되지 않습니다.

## <a name="copy-and-paste"></a>복사 및 붙여넣기
Ctrl + V 및 Ctrl + C는 Windows 컴퓨터에서 복사/붙여넣기로 작동하지 않습니다. Ctrl + Insert 및 Shift + Insert로 복사/붙여넣기하세요.
복사 붙여넣기 옵션을 마우스 오른쪽 단추로 클릭할 수도 있지만 브라우저 전용 클립보드 액세스의 적용을 받습니다.

## <a name="usage-limits"></a>사용 제한
Cloud Shell은 대화형 사용 사례를 위해 고안되었으므로 비대화형 세션을 오래 실행하면 경고 없이 종료됩니다.

## <a name="network-connectivity"></a>네트워크 연결
Cloud Shell의 대기 시간은 로컬 인터넷 연결의 영향을 받으며, Cloud Shell은 전송된 지침과 함께 작동을 계속 시도하게 됩니다.

## <a name="next-steps"></a>다음 단계
[Cloud Shell 빠른 시작](quickstart.md)
