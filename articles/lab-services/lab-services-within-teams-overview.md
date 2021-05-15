---
title: Microsoft Teams 내 Azure Lab Services
description: Microsoft Teams 내에서 Azure Lab Services 사용에 대한 개요를 제공합니다.
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: f39613427ed174dfca80d4a48be9473ab7025e79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433922"
---
# <a name="azure-lab-services-within-microsoft-teams"></a>Microsoft Teams 내 Azure Lab Services

Azure Lab Services은 **Azure Lab Services** Teams 앱을 사용하여 Microsoft Teams에서 활용할 수 있습니다. 랩 계정에 대한 소유자/참가자/작성자 액세스 권한이 있는 팀 소유자는 누구나 랩을 생성하고 팀의 모든 사용자에게 가상 머신을 프로비저닝할 수 있습니다.

이 문서에서는 Teams 내에서 Azure Lab Services를 사용하는 이점에 대해 간략하게 설명하고 Teams 내에서 랩을 만들고 관리하는 방법에 대한 지침을 제공합니다. 

> [!NOTE]
>**Azure Lab Services** Teams 앱은 팀에만 추가할 수 있으며 개별 채팅 또는 그룹 채팅에 추가할 수 없습니다.

## <a name="benefits"></a>이점

Azure Lab Services와 Microsoft Teams의 통합을 통해 교육자는 강의실 환경을 설정하고 팀(클래스) 내에서 가상 실험실 환경을 제공할 수 있습니다. 

* 교육자는 학생들이 Teams을 떠나 [Azure Lab Services 웹 사이트](https://labs.azure.com)를 방문하지 않고도 Teams 내에서 VM에 액세스할 수 있도록 랩을 설정할 수 있습니다.
* Teams에서 Azure Lab Services에 이르는 SSO(Single Sign-On).
* 팀 소유자와 랩 소유자는 두 가지 시스템에 클래스 명단을 유지할 필요가 없습니다. 랩 사용자 목록은 팀 멤버 자격에서 자동으로 채워지고 24시간마다 동기화가 자동으로 수행됩니다. 
* 템플릿 VM의 초기 게시 후 팀 멤버 자격에서 사용자의 추가/삭제 여부에 따라 랩 용량(랩 내 VM 수)이 자동으로 조정됩니다. 
* 팀 및 랩 소유자는 팀과 관련된 랩만 볼 수 있으며, 학생은 특정 팀에 대해 프로비전된 VM만 볼 수 있습니다. 
* 사용자가 랩에 자동으로 등록되고 랩이 게시된 후 첫 번째 로그인 시 VM이 자동으로 할당됩니다. 교육자는 초대를 보낼 필요가 없으며 학생들이 개별적으로 랩을 등록할 필요가 없습니다.  

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하세요.

- [Teams 내에서 랩 시작 및 만들기](how-to-get-started-create-lab-within-teams.md)
- [Teams 내에서 랩 사용자 목록 관리](how-to-manage-user-lists-within-teams.md)
- [Teams 내에서 랩의 VM 풀 관리](how-to-manage-vm-pool-within-teams.md)
- [Teams 내에서 랩 일정 만들기 및 관리](how-to-create-schedules-within-teams.md)
- [Teams 내에서 VM에 액세스 – 학생 보기](how-to-access-vm-for-students-within-teams.md)
