---
title: Azure Defender for DNS 경고에 대응하는 방법
description: Azure Defender for DNS의 경고에 대응하기 위해 필요한 단계를 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 375a81127a000741ca5e0397642800794610bcda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754776"
---
# <a name="respond-to-azure-defender-for-dns-alerts"></a>Azure Defender for DNS 경고에 대한 대응

Azure Defender for DNS 경고를 수신하면 다음 설명대로 경고를 조사하고 대응하는 것을 추천합니다. Azure Defender for DNS는 모든 관련 리소스를 보호하므로 경고를 트리거한 애플리케이션이나 사용자에 대해 잘 알고 있는 경우에도 각 경고와 관련된 상황을 확인하는 것이 중요합니다.  


## <a name="step-1-contact"></a>1단계. 연락처

1. 리소스 소유자에게 문의하여 동작이 예상된 것인지 또는 의도한 것인지를 확인합니다.
1. 예상된 작업인 경우, 경고를 해제합니다.
1. 작업이 예상되지 않은 경우 다음 단계에 설명된 대로 리소스를 잠재적으로 손상 및 완화될 수 있는 것으로 처리합니다.

## <a name="step-2-immediate-mitigation"></a>2단계. 즉각적인 위협 완화 

1. 측면 이동을 방지하기 위해 네트워크에서 리소스를 격리합니다.
1. 수정 조언에 따라 리소스에서 전체 맬웨어 방지 검사를 실행합니다.
1. 리소스에 설치 및 실행 중인 소프트웨어를 검토하여 알 수 없거나 원치 않는 패키지를 제거합니다.
1. 머신을 알려진 정상 상태로 되돌리고 필요한 경우 운영 체제를 다시 설치하며 맬웨어가 없는 확인된 소스에서 소프트웨어를 복원합니다.
1. 머신에 대한 Azure Security Center 권장 사항을 해결하고 강조 표시된 보안 문제를 수정하여 향후 발생하는 위반을 방지합니다.


## <a name="next-steps"></a>다음 단계

이 페이지에서는 Azure Defender for DNS의 경고에 대응하는 프로세스를 설명했습니다. 관련 정보는 다음 페이지를 참조하세요.

- [Azure Defender for DNS 소개](defender-for-dns-introduction.md)
- [Azure Defender의 경고 표시 안 함](alerts-suppression-rules.md)
- [Security Center 데이터 연속 내보내기](continuous-export.md)