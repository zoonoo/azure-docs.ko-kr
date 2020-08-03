---
author: baanders
description: Azure Digital Twins 설정 개요의 단계에 대 한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b8751685cfd273171cb8a5b500e4bed41ce2f59e
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407525"
---
>[!NOTE]
>이러한 작업은 Azure 구독에 대 한 *소유자* 역할이 있는 사용자가 완료 하기 위한 것입니다. 일부 부분은이 상승 된 권한 없이도 완료할 수 있지만 소유자의 협력은 사용 가능한 인스턴스를 완전히 설정 해야 합니다. 이에 대 한 자세한 내용은 아래의 [*필수 조건: 필수 권한*](#prerequisites-permission-requirements) 섹션을 참조 하세요.

새 Azure Digital Twins 인스턴스에 대 한 전체 설치는 다음 세 부분으로 구성 됩니다.
1. **인스턴스 만들기**
2. **사용자 액세스 권한 설정**: azure 사용자는 Azure digital twins 인스턴스에서 azure *디지털 쌍 소유자 (미리 보기)* 역할을 사용 하 여 it 및 해당 데이터를 관리할 수 있어야 합니다. 이 단계에서 Azure 구독의 소유자는 Azure Digital Twins 인스턴스를 관리 하는 사용자에 게이 역할을 할당 합니다. 사용자 또는 조직에서 다른 사람이 될 수 있습니다.
3. **클라이언트 응용 프로그램에 대 한 액세스 권한 설정**: 인스턴스와 상호 작용 하는 데 사용 되는 클라이언트 응용 프로그램을 작성 하는 것이 일반적입니다. 해당 클라이언트 앱이 Azure Digital Twins에 액세스 하려면 클라이언트 응용 프로그램이 인스턴스에 인증 하는 데 사용할 [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) 에서 *앱 등록* 을 설정 해야 합니다.

계속 하려면 Azure 구독이 필요 합니다. [여기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)에서 무료로 하나를 설정할 수 있습니다.
