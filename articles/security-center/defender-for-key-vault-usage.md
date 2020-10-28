---
title: Key Vault 경고를 위해 Azure Defender에 응답 하는 방법
description: Key Vault에 대해 Azure Defender에서 경고에 응답 하는 데 필요한 단계에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 6a40ffab652a6ae8671d6cd1c0dd9336764ce2c4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791939"
---
# <a name="respond-to-azure-defender-for-key-vault-alerts"></a>Key Vault용 Azure Defender 경고에 응답
Azure Defender에서 Key Vault에 대 한 경고를 수신 하는 경우 아래에 설명 된 대로 경고를 조사 하 고 응답 하는 것이 좋습니다. Azure Defender for Key Vault는 응용 프로그램 및 자격 증명을 보호 하므로 경고를 트리거한 응용 프로그램이 나 사용자에 대해 잘 알고 있는 경우에도 모든 경고를 둘러싼 상황을 확인 하는 것이 중요 합니다.  

Azure Defender for Key Vault의 모든 경고에는 다음 요소가 포함 됩니다.

- 개체 ID입니다.
- 의심 스러운 리소스의 사용자 계정 이름 또는 IP 주소

> [!TIP]
> 발생 한 액세스 *유형에* 따라 일부 필드는 사용 하지 못할 수 있습니다. 예를 들어 응용 프로그램에서 키 자격 증명 모음에 액세스 한 경우에는 연결 된 사용자 계정 이름이 표시 되지 않습니다. 트래픽이 Azure 외부에서 시작 된 경우에는 개체 ID가 표시 되지 않습니다.

## <a name="step-1-contact"></a>1단계: 연락처

1. 트래픽이 Azure 테 넌 트 내에서 시작 되었는지 여부를 확인 합니다. 키 자격 증명 모음 방화벽을 사용 하는 경우이 경고를 트리거한 사용자 또는 응용 프로그램에 대 한 액세스를 제공 했을 수 있습니다.
1. 트래픽의 원본을 확인할 수 없으면 [2 단계를 계속 합니다. 즉시 완화](#step-2-immediate-mitigation).
1. 테 넌 트에서 트래픽의 원본을 식별할 수 있는 경우 응용 프로그램의 사용자나 소유자에 게 문의 하세요. 

> [!CAUTION]
> Azure Defender for Key Vault은 도난당 한 자격 증명으로 인 한 의심 스러운 활동을 식별 하는 데 도움이 되도록 설계 되었습니다. 사용자 또는 응용 프로그램을 인식 하기 때문에 경고를 해제 **하지 마십시오** . 응용 프로그램 또는 사용자의 소유자에 게 문의 하 여 활동이 합법적인 지 확인 합니다. 필요에 따라 노이즈를 제거 하는 비 표시 규칙을 만들 수 있습니다. 자세한 내용은 [Azure Defender에서 경고 표시 안 함](alerts-suppression-rules.md)을 확인 하세요.


## <a name="step-2-immediate-mitigation"></a>2단계. 즉시 완화 
사용자 또는 응용 프로그램을 인식할 수 없거나 액세스 권한이 부여 되지 않은 것으로 생각 되는 경우:

- 트래픽이 인식할 수 없는 IP 주소에서 발생 한 경우:
    1. [Azure Key Vault 방화벽 및 가상 네트워크 구성](../key-vault/general/network-security.md)에 설명 된 대로 Azure Key Vault 방화벽을 사용 하도록 설정 합니다.
    1. 신뢰할 수 있는 리소스 및 가상 네트워크를 사용 하 여 방화벽을 구성 합니다.

- 경고의 소스가 권한이 없는 응용 프로그램 또는 의심 스러운 사용자 인 경우:
    1. 키 자격 증명 모음의 액세스 정책 설정을 엽니다.
    1. 해당 보안 주체를 제거 하거나 보안 주체에서 수행할 수 있는 작업을 제한 합니다.  

- 경고의 원본에 테 넌 트의 Azure Active Directory 역할이 있는 경우:
    1. 관리자에게 문의하십시오.
    1. Azure Active Directory 사용 권한을 줄이거나 취소 해야 하는지 여부를 확인 합니다.

## <a name="step-3-identify-impact"></a>3단계: 영향 식별 
영향이 완화 된 경우 주요 자격 증명 모음에서 영향을 받은 암호를 조사 합니다.
1. Azure Key Vault에서 "보안" 페이지를 열고 트리거된 경고를 확인 합니다.
1. 트리거된 특정 경고를 선택 합니다.
    액세스 된 암호 및 타임 스탬프의 목록을 검토 합니다.
1. 키 자격 증명 모음 진단 로그가 사용 하도록 설정 된 경우 필요에 따라 해당 호출자 IP, 사용자 계정 또는 개체 ID에 대 한 이전 작업을 검토 합니다.  

## <a name="step-4-take-action"></a>4단계. 작업 수행 
의심 스러운 사용자나 응용 프로그램에서 액세스 한 비밀, 키 및 인증서 목록을 컴파일한 경우 해당 개체를 즉시 회전 해야 합니다.

1. 영향을 받는 암호는 키 자격 증명 모음에서 사용 하지 않도록 설정 하거나 삭제 해야 합니다.
1. 특정 응용 프로그램에 자격 증명이 사용 된 경우:
    1. 응용 프로그램의 관리자에 게 연락 하 여 손상 된 자격 증명이 손상 된 후 해당 자격 증명을 사용 하는 환경에 대 한 감사를 요청 합니다.
    1. 손상 된 자격 증명이 사용 된 경우에는 응용 프로그램 소유자가 액세스 한 정보를 식별 하 여 영향을 완화 해야 합니다.


## <a name="next-steps"></a>다음 단계

이 페이지에서는 Azure Defender에서 Key Vault에 대 한 경고에 응답 하는 프로세스를 설명 했습니다. 관련 정보는 다음 페이지를 참조 하세요.

- [Azure Defender for Key Vault 소개](defender-for-key-vault-introduction.md)
- [Azure Defender의 경고 표시 안 함](alerts-suppression-rules.md)
- [Security Center 데이터를 지속적으로 내보내기](continuous-export.md)