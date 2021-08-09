---
author: baanders
description: Azure Digital Twins 설정의 단계 개요에 대한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 19d7c55a669a3975cb4bc1dce707e2d1165be0b3
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108759988"
---
새 Azure Digital Twins 인스턴스에 대한 전체 설치는 다음 두 부분으로 구성됩니다.
1. **인스턴스 만들기**
2. **사용자 액세스 권한 설정**: Azure 사용자는 Azure Digital Twins 인스턴스에서 **Azure Digital Twins 데이터 소유자** 역할을 가지고 있어야 해당 인스턴스와 데이터를 관리할 수 있습니다. 이 단계에서는 Azure 구독의 소유자/관리자로서, Azure Digital Twins 인스턴스를 관리하는 사용자에게 이 역할을 할당합니다. 사용자 자신일 수도 있고 조직의 다른 사람일 수도 있습니다.
 
>[!IMPORTANT]
>이 전체 문서를 완료하고 사용 가능한 인스턴스를 완전히 설정하려면 Azure 구독에서 리소스와 사용자 액세스를 모두 관리할 수 있는 권한이 필요합니다. 첫 번째 단계는 구독에서 리소스를 만들 수 있는 사용자면 누구나 완료할 수 있지만 두 번째 단계에는 사용자 액세스 관리 권한(또는 이러한 권한이 있는 사람의 협력)이 필요합니다. 이에 대한 자세한 내용은 사용자 액세스 권한 단계의 [필수 조건: 필수 권한](#prerequisites-permission-requirements) 섹션을 참조하세요.
