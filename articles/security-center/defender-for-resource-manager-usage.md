---
title: Azure Defender for Resource Manager 경고에 대응하는 방법
description: Azure Defender for Resource Manager에서 경고에 대응하는 데 필요한 단계에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 54790795aab8aac247e17198159130d7139dd38c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754741"
---
# <a name="respond-to-azure-defender-for-resource-manager-alerts"></a>Azure Defender for Resource Manager 경고에 대한 대응

Azure Defender for Resource Manager 경고를 수신하면 다음 설명대로 경고를 조사하고 대응하는 것을 추천합니다. Azure Defender for Resource Manager는 연결된 모든 리소스를 보호하므로 경고를 트리거한 애플리케이션이나 사용자에 대해 잘 알고 있는 경우에도 각 경고와 관련된 상황을 확인하는 것이 중요합니다.  


## <a name="step-1-contact"></a>1단계. 연락처

1. 리소스 소유자에게 문의하여 동작이 예상된 것인지 또는 의도한 것인지를 확인합니다.
1. 예상된 작업인 경우, 경고를 해제합니다.
1. 예상되지 않은 작업인 경우, 다음 단계에 설명된 대로 관련 사용자 계정, 구독, 가상 머신을 손상된 것으로 처리하고 완화합니다.

## <a name="step-2-immediate-mitigation"></a>2단계. 즉각적인 위협 완화 

1. 손상된 사용자 계정을 다음과 같이 재구성합니다.
    - 익숙하지 않은 경우, 위협 행위자가 생성된 것일 수 있으니 삭제합니다.
    - 친숙한 경우 인증 자격 증명을 변경합니다.
    - Azure 활동 로그를 사용하여 사용자가 수행한 모든 활동을 검토하고 의심스러운 모든 활동을 식별합니다.

1. 손상된 구독을 다음과 같이 재구성합니다.
    - 손상된 자동화 계정에서 익숙하지 않은 Runbook 제거
    - 구독에 대한 IAM 권한을 검토하고 익숙하지 않은 사용자 계정은 사용 권한을 제거
    - 구독에서 모든 Azure 리소스를 검토하고 익숙하지 않은 리소스는 삭제
    - Azure Security Center에서 구독에 대한 보안 경고를 검토하고 조사
    - Azure 활동 로그를 사용하여 구독에서 수행된 모든 작업을 검토하고 의심스러운 작업을 식별

1. 손상된 가상 머신 재구성
    - 모든 사용자 암호 변경
    - 머신에서 전체 맬웨어 방지 검사 실행
    - 맬웨어가 없는 원본에서 이미지로 다시 설치


## <a name="next-steps"></a>다음 단계

이 페이지에서는 Azure Defender for Resource Manager에서 경고에 대응하는 프로세스를 설명했습니다. 관련 정보는 다음 페이지를 참조하세요.

- [Azure Defender for Resource Manager 소개](defender-for-resource-manager-introduction.md)
- [Azure Defender의 경고 표시 안 함](alerts-suppression-rules.md)
- [Security Center 데이터 연속 내보내기](continuous-export.md)