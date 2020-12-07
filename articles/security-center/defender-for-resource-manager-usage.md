---
title: 리소스 관리자 경고를 위해 Azure Defender에 응답 하는 방법
description: 리소스 관리자 Azure Defender에서 경고에 응답 하는 데 필요한 단계에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 54790795aab8aac247e17198159130d7139dd38c
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754741"
---
# <a name="respond-to-azure-defender-for-resource-manager-alerts"></a>리소스 관리자 경고를 위해 Azure Defender에 응답

Azure Defender에서 리소스 관리자에 대 한 경고를 수신 하는 경우 아래에 설명 된 대로 경고를 조사 하 고 응답 하는 것이 좋습니다. Azure Defender for 리소스 관리자는 연결 된 모든 리소스를 보호 하므로 경고를 트리거한 응용 프로그램이 나 사용자에 게 친숙 한 경우에도 모든 경고를 둘러싼 상황을 확인 하는 것이 중요 합니다.  


## <a name="step-1-contact"></a>1단계: 연락처

1. 리소스 소유자에 게 문의 하 여 동작이 예상 또는 의도적인 지 여부를 확인 합니다.
1. 작업이 필요한 경우 경고를 해제 합니다.
1. 작업이 예기치 않은 경우 다음 단계에 설명 된 대로 관련 사용자 계정, 구독 및 가상 컴퓨터를 손상 된 것으로 처리 하 고 완화 합니다.

## <a name="step-2-immediate-mitigation"></a>2단계. 즉시 완화 

1. 손상 된 사용자 계정 재구성:
    - 익숙하지 않은 경우 위협 행위자가 만든 것 처럼 삭제 합니다.
    - 친숙 한 경우 인증 자격 증명을 변경 합니다.
    - Azure 활동 로그를 사용 하 여 사용자가 수행한 모든 활동을 검토 하 고 의심 스러운 모든 활동을 식별 합니다.

1. 손상 된 구독 재구성:
    - 손상 된 자동화 계정에서 익숙하지 않은 Runbook 제거
    - 구독에 대 한 IAM 권한을 검토 하 고 익숙하지 않은 사용자 계정에 대 한 사용 권한을 제거 합니다.
    - 구독에서 모든 Azure 리소스를 검토 하 고 익숙하지 않은 모든 리소스를 삭제 합니다.
    - 에서 구독에 대 한 보안 경고를 검토 하 고 조사 Azure Security Center
    - Azure 활동 로그를 사용 하 여 구독에서 수행 된 모든 활동을 검토 하 고 의심 스러운 활동을 식별 합니다.

1. 손상 된 가상 컴퓨터 재구성
    - 모든 사용자에 대 한 암호 변경
    - 컴퓨터에서 전체 맬웨어 방지 검사 실행
    - 맬웨어 없는 원본에서 컴퓨터 이미지로 다시 설치


## <a name="next-steps"></a>다음 단계

이 페이지에서는 Azure Defender에서 리소스 관리자에 대 한 경고에 응답 하는 프로세스를 설명 했습니다. 관련 정보는 다음 페이지를 참조 하세요.

- [리소스 관리자에 대 한 Azure Defender 소개](defender-for-resource-manager-introduction.md)
- [Azure Defender의 경고 표시 안 함](alerts-suppression-rules.md)
- [Security Center 데이터 연속 내보내기](continuous-export.md)