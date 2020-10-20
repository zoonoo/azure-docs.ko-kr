---
author: baanders
description: Azure Digital Twins 설정 개요의 단계에 대 한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 5ab6812d144122c61018ad740892db869a7ba43d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205496"
---
>[!NOTE]
>이러한 작업은 사용자가 Azure 구독에 대 한 리소스 및 사용자 액세스를 모두 관리할 수 있는 기능을 제공 하기 위한 것입니다. 일부 단계는 낮은 권한으로 완료할 수 있지만 사용 가능한 인스턴스를 완전히 설정 하려면 이러한 사용 권한을 가진 사람의 협력이 필요 합니다. 이에 대 한 자세한 내용은 아래의 [*필수 조건: 필수 권한*](#prerequisites-permission-requirements) 섹션을 참조 하세요.

새 Azure Digital Twins 인스턴스에 대 한 전체 설치는 다음 두 부분으로 구성 됩니다.
1. **인스턴스 만들기**
2. **사용자 액세스 권한 설정**: azure 사용자는 Azure digital twins 인스턴스에서 azure *디지털 쌍 소유자 (미리 보기)* 역할을 사용 하 여 it 및 해당 데이터를 관리할 수 있어야 합니다. 이 단계에서는 azure 구독의 소유자/관리자가 Azure Digital Twins 인스턴스를 관리 하는 사용자에 게이 역할을 할당 합니다. 사용자 또는 조직에서 다른 사람이 될 수 있습니다.