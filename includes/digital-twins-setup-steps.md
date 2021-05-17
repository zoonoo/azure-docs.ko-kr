---
author: baanders
description: Azure Digital Twins 설정의 단계 개요에 대한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: c1cfab8c9e68d9e6b0c3b84e8848645a50c24710
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100560793"
---
새 Azure Digital Twins 인스턴스에 대한 전체 설치는 다음 두 부분으로 구성됩니다.
1. **인스턴스 만들기**
2. **사용자 액세스 권한 설정**: Azure 사용자는 Azure Digital Twins 인스턴스에서 *Azure Digital Twins 데이터 소유자* 역할을 가지고 있어야 해당 인스턴스와 데이터를 관리할 수 있습니다. 이 단계에서는 Azure 구독의 소유자/관리자로서, Azure Digital Twins 인스턴스를 관리하는 사용자에게 이 역할을 할당합니다. 사용자 자신일 수도 있고 조직의 다른 사람일 수도 있습니다.
 
>[!NOTE]
>이러한 작업은 사용자가 Azure 구독의 리소스와 사용자 액세스를 모두 관리할 수 있는 권한을 가진 사용자가 완료해야 합니다. 일부 단계는 낮은 권한으로 완료할 수 있지만, 사용 가능한 인스턴스를 완전히 설정하려면 이러한 권한을 가진 사람의 협력이 필요합니다. 이에 대한 자세한 내용은 아래의 [*필수 구성 요소: 필수 권한*](#prerequisites-permission-requirements) 섹션에서 확인하세요.