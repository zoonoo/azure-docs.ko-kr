---
title: "Azure API Management 인스턴스 만들기 | Microsoft Docs"
description: "이 자습서의 단계에 따라 새 Azure API Management 인스턴스를 만듭니다."
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 6433ea1f0eb6ad375402b998b4dfa80bded35c4b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-new-azure-api-management-service-instance"></a>새 Azure API Management 서비스 인스턴스 만들기

이 자습서에서는 [Azure Portal](https://portal.azure.com/)을 사용하여 새 API Management 인스턴스를 만드는 단계를 설명합니다.

## <a name="prerequisites"></a>필수 조건

+ 활성 Azure 구독.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-new-service"></a>새 서비스 만들기

1. [Azure Portal](https://portal.azure.com/)에서 **새로 만들기** > **엔터프라이즈 통합** > **API 관리**를 선택합니다.

    또는 **새로 만들기**를 선택하고 검색 상자에 `API management`를 입력하고 Enter 키를 누릅니다. **만들기**를 클릭합니다.

2. **API Management 서비스** 창에서 API Management 서비스에 대한 고유한 **이름**을 입력합니다. 이 이름은 나중에 변경할 수 없습니다.

    > [!TIP]
    > 서비스 이름은 *{name}.azure-api.net* 형식의 기본 도메인 이름을 생성하는 데 사용됩니다. 사용자 지정 도메인 이름을 사용하려는 경우 [사용자 지정 도메인 구성](configure-custom-domain.md)을 참조하세요. <br/>
    > 서비스 이름은 서비스 및 해당하는 Azure 리소스를 참조하는 데 사용됩니다.

5. 사용자가 액세스할 수 있는 다양한 Azure 구독 중에서 **구독**을 선택합니다.
6. **리소스 그룹**에서 새 또는 기존 리소스를 선택합니다.  리소스 그룹은 수명 주기, 권한 및 정책을 공유하는 리소스의 컬렉션입니다. [여기](../azure-resource-manager/resource-group-overview.md#resource-groups)를 참조하세요.
7. **위치**에서 API Management 서비스가 생성된 지리적 지역을 선택합니다. 사용 가능한 API Management 서비스 지역만 드롭다운 목록 상자에 표시됩니다. 
9. **조직 이름**을 입력합니다. 이 이름은 여러 곳에 사용됩니다. 예를 들어 알림 전자 메일의 개발자 포털 및 보낸 사람의 제목입니다.
10. **관리자 메일**에 **API Management**에서 모든 알림이 전송될 이메일 주소를 설정합니다.
11. **가격 책정 계층**에서 서비스를 평가할 **개발자** 계층을 설정합니다. 이 계층은 프로덕션 환경에서 사용할 수 없습니다. API Management 계층 크기 조정에 대한 자세한 내용은 [업그레이드 및 크기 조정](upgrade-and-scale.md)을 참조하세요.
12. **만들기**를 선택합니다.

    > [!TIP]
    > 일반적으로 API Management 서비스를 만드는 20~30분이 소요됩니다. **대시보드에 고정**을 선택하면 새로 생성된 서비스를 보다 쉽게 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure API Management를 사용하여 API 게시](#api-management-getstarted-publish-api.md)