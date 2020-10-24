---
author: baanders
description: Azure Digital Twins 설정 개요의 단계에 대 한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: bf442da43fd7945bf69fbb889ef0c517b8832809
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478877"
---
>[!NOTE]
>이러한 작업은 사용자가 Azure 구독에 대 한 리소스 및 사용자 액세스를 모두 관리할 수 있는 기능을 제공 하기 위한 것입니다. 일부 단계는 낮은 권한으로 완료할 수 있지만 사용 가능한 인스턴스를 완전히 설정 하려면 이러한 사용 권한을 가진 사람의 협력이 필요 합니다. 이에 대 한 자세한 내용은 아래의 [*필수 조건: 필수 권한*](#prerequisites-permission-requirements) 섹션을 참조 하세요.

새 Azure Digital Twins 인스턴스에 대 한 전체 설치는 다음 두 부분으로 구성 됩니다.
1. **인스턴스 만들기**
2. **사용자 액세스 권한 설정**: azure 사용자는 Azure digital twins 인스턴스에 Azure *Digital Twins 데이터 소유자* 역할을 보유 하 여 it 및 해당 데이터를 관리할 수 있어야 합니다. 이 단계에서는 azure 구독의 소유자/관리자가 Azure Digital Twins 인스턴스를 관리 하는 사용자에 게이 역할을 할당 합니다. 사용자 또는 조직에서 다른 사람이 될 수 있습니다.

[!INCLUDE [digital-twins-role-rename-note.md](digital-twins-role-rename-note.md)]