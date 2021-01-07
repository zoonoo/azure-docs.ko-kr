---
title: DNS 경고에 대해 Azure Defender에 응답 하는 방법
description: DNS 용 Azure Defender에서 경고에 응답 하는 데 필요한 단계에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 375a81127a000741ca5e0397642800794610bcda
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754776"
---
# <a name="respond-to-azure-defender-for-dns-alerts"></a>DNS 경고에 대해 Azure Defender에 응답

DNS 용 Azure Defender에서 경고를 수신 하는 경우 아래에 설명 된 대로 경고를 조사 하 고 응답 하는 것이 좋습니다. DNS 용 Azure Defender는 연결 된 모든 리소스를 보호 하므로 경고를 트리거한 응용 프로그램이 나 사용자에 게 친숙 한 경우에도 모든 경고를 둘러싼 상황을 확인 하는 것이 중요 합니다.  


## <a name="step-1-contact"></a>1단계: 연락처

1. 리소스 소유자에 게 문의 하 여 동작이 예상 또는 의도적인 지 여부를 확인 합니다.
1. 작업이 필요한 경우 경고를 해제 합니다.
1. 작업이 예기치 않은 경우 다음 단계에 설명 된 대로 리소스를 잠재적으로 손상 및 완화할 수 있는 것으로 처리 합니다.

## <a name="step-2-immediate-mitigation"></a>2단계. 즉시 완화 

1. 측면 이동을 방지 하기 위해 네트워크에서 리소스를 격리 합니다.
1. 모든 결과 재구성 조언을 따라 리소스에서 전체 맬웨어 방지 검사를 실행 합니다.
1. 리소스에 설치 되 고 실행 중인 소프트웨어를 검토 하 여 알 수 없거나 원치 않는 패키지를 제거 합니다.
1. 컴퓨터를 알려진 양호한 상태로 되돌리고, 필요한 경우 운영 체제를 다시 설치 하 고, 확인 된 맬웨어 없는 원본에서 소프트웨어를 복원 합니다.
1. 컴퓨터에 대 한 Azure Security Center 권장 사항을 해결 하 고 강조 표시 된 보안 문제를 수정 하 여 이후 위반을 방지 합니다.


## <a name="next-steps"></a>다음 단계

이 페이지에서는 DNS 용 Azure Defender에서 경고에 응답 하는 프로세스에 대해 설명 했습니다. 관련 정보는 다음 페이지를 참조 하세요.

- [DNS 용 Azure Defender 소개](defender-for-dns-introduction.md)
- [Azure Defender의 경고 표시 안 함](alerts-suppression-rules.md)
- [Security Center 데이터 연속 내보내기](continuous-export.md)