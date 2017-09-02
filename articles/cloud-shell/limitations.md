---
title: "Azure Cloud Shell(미리 보기) 제한 사항 | Microsoft Docs"
description: "Azure Cloud Shell의 제한 사항 개요"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: e42841b126a9df9240bec3f489589d5ce4a6db80
ms.contentlocale: ko-kr
ms.lasthandoff: 08/21/2017

---

# <a name="limitations-of-azure-cloud-shell"></a>Azure Cloud Shell의 제한 사항
Azure Cloud Shell에는 다음과 같이 알려진 제한 사항이 있습니다.

## <a name="system-state-and-persistence"></a>시스템 상태 및 지속성
Cloud Shell 세션을 제공하는 컴퓨터는 일시적이며 세션이 20분 동안 비활성화된 후 재순환됩니다. Cloud Shell에 파일 공유를 탑재해야 합니다. 따라서 Cloud Shell에 액세스하도록 구독에서 저장소 리소스를 설정할 수 있어야 합니다. 기타 고려 사항은 다음과 같습니다.
* 탑재된 저장소에서 `$Home` 디렉터리 또는 `clouddrive` 디렉터리 내 수정 사항만 유지됩니다.
* 파일 공유는 [할당된 지역](persisting-shell-storage.md#mount-a-new-clouddrive) 내에서만 탑재될 수 있습니다.
* Azure 파일은 로컬 중복 저장소 및 지역 중복 저장소 계정만 지원합니다.

## <a name="user-permissions"></a>사용자 권한
권한은 sudo 액세스 권한이 없는 일반 사용자로 설정됩니다. 사용자 `$Home` 디렉터리 외부에서의 설치는 유지되지 않습니다.
`git clone`와 같이 `clouddrive` 디렉터리 내 특정 명령에 적절한 권한이 없어도 사용자의 `$Home` 디렉터리에는 권한이 있습니다.

## <a name="browser-support"></a>브라우저 지원
Cloud Shell은 Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox 및 Apple Safari의 최신 버전을 지원합니다. Safari는 개인 모드에서 지원되지 않습니다.

## <a name="copy-and-paste"></a>복사 및 붙여넣기
Ctrl+C 및 Ctrl+V가 Windows 컴퓨터에서 Cloud Shell의 복사/붙여넣기 바로 가기처럼 작동하지 않으므로 복사하여 붙여넣으려면 각각 Ctrl+Insert 및 Shift+Insert를 사용합니다.

복사 및 붙여넣기 옵션을 마우스 오른쪽 단추로 클릭하여 사용할 수도 있지만 마우스 오른쪽 단추 클릭 기능은 브라우저 전용 클립보드 액세스의 적용을 받습니다.

## <a name="editing-bashrc"></a>.bashrc 편집
.bashrc를 편집할 때는 Cloud Shell에 예기치 않은 오류가 발생할 수 있으니 주의하세요.

## <a name="bashhistory"></a>.bash_history
Cloud Shell 세션 중단 또는 동시 세션으로 인해 bash 명령의 기록이 일관되지 않을 수 있습니다.

## <a name="usage-limits"></a>사용 제한
Cloud Shell은 대화형 사용 사례를 위한 것입니다. 따라서 비대화형 세션을 오래 실행하면 경고 없이 종료됩니다.

## <a name="network-connectivity"></a>네트워크 연결
Cloud Shell의 대기 시간은 로컬 인터넷 연결의 영향을 받으며, Cloud Shell은 전송된 지침과 함께 작동을 계속 시도하게 됩니다.

## <a name="next-steps"></a>다음 단계
[Cloud Shell 빠른 시작](quickstart.md)

