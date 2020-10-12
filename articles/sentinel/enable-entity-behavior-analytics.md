---
title: Entity behavior analytics를 사용 하 여 고급 위협 검색 | Microsoft Docs
description: Azure 센티널에서 사용자 및 엔터티 동작 분석을 사용 하도록 설정 하 고 데이터 원본 구성
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2020
ms.author: yelevin
ms.openlocfilehash: c55ea0e7753faa6dc21b955d63a57d96e3849f70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90997119"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Azure 센티널에서 사용자 및 엔터티 동작 분석 (UEBA) 사용 



## <a name="prerequisites"></a>필수 구성 요소

- UEBA를 사용 하거나 사용 하지 않도록 설정 하기 위해 사용자에 게 Azure AD의 **전역 관리자** 또는 **보안 관리자** 역할을 할당 해야 합니다.

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>사용자 및 엔터티 동작 분석을 사용 하도록 설정 하는 방법

1. Azure 센티널 탐색 메뉴에서 **엔터티 동작 (미리 보기)** 을 선택 합니다.

1. **제목 아래에서 설정/** 해제 **를 켜기로 전환 합니다.**

1. **데이터 원본 선택** 단추를 클릭 합니다.

1. **데이터 원본 선택** 창에서 UEBA를 사용 하도록 설정할 데이터 원본 옆의 확인란을 선택 하 고 **적용**을 선택 합니다.

    > [!NOTE]
    >
    > **데이터 원본 선택** 창의 아래쪽에는 아직 사용 하도록 설정 하지 않은 UEBA-지원 되는 데이터 원본 목록이 표시 됩니다. 
    >
    > UEBA를 사용 하도록 설정 하면 새 데이터 원본을 연결 하는 옵션을 사용할 수 있습니다 .이 옵션을 선택 하면 데이터 커넥터 창에서 직접 UEBA를 사용할 수 있습니다.

1. **엔터티 검색으로 이동을**선택 합니다. 그러면 기본 Azure 센티널 메뉴에서 **엔터티 동작** 을 선택 하면 현재부터 표시 되는 엔터티 검색 창으로 이동 됩니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 센티널에서 사용자 및 엔터티 동작 분석 (UEBA)을 사용 하도록 설정 하 고 구성 하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
