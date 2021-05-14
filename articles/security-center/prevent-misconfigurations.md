---
title: Azure Security Center를 사용하여 잘못된 구성을 방지하는 방법
description: 권장 사항 세부 정보 페이지에서 Security Center의 ‘적용’ 및 ‘거부’ 옵션을 사용하는 방법에 대한 정보
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: fabbd255f64e5614ae052c9d7c8d65fc35d45856
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600499"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>적용/거부 권장 사항을 사용하여 구성 오류 방지

보안 구성 오류는 보안 인시던트의 주요 원인입니다. 이제 Security Center에는 특정 추천 사항과 관련하여 새 리소스의 구성 오류를 *방지* 하는 데 도움이 되는 기능이 있습니다. 

이 기능을 통해 워크로드를 안전하게 유지하고 보안 점수를 안정화할 수 있습니다.

특정 추천 사항에 따라 보안 구성을 적용하는 방법은 다음 두 가지 모드로 제공됩니다.

- Azure Policy의 **거부** 효과를 사용하여 비정상 리소스가 만들어지는 것을 중지할 수 있습니다.
- **적용** 옵션을 사용하여 Azure Policy의 **DeployIfNotExist** 효과를 활용하고, 비준수 리소스를 만들 때 자동으로 수정할 수 있습니다.

이는 선택한 보안 권장 사항에 대한 리소스 세부 정보 페이지의 맨 위에 있습니다([거부/적용 옵션 관련 권장 사항](#recommendations-with-denyenforce-options)참조).

## <a name="prevent-resource-creation"></a>리소스 생성 방지

1. 새 리소스가 충족해야 하는 권장 사항을 열고 페이지 상단에서 **거부** 단추를 선택합니다.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="거부 단추가 강조 표시된 권장 사항 페이지":::

    구성 창이 열리고 범위 옵션이 나열됩니다. 

1. 관련 구독 또는 관리 그룹을 선택하여 범위를 설정합니다.

    > [!TIP]
    > 행의 끝에 있는 세 개의 점을 사용하여 단일 구독을 변경하거나 확인란을 사용하여 여러 구독 또는 그룹을 선택한 후 **거부로 변경** 을 선택할 수 있습니다.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Azure Policy 거부에 대한 범위 설정":::


## <a name="enforce-a-secure-configuration"></a>보안 구성 적용

1. 새 리소스가 충족하지 않는 경우 템플릿 배포를 배포할 권장 사항을 열고 페이지 상단에 있는 **적용** 단추를 선택합니다.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="적용 단추가 강조 표시된 권장 사항 페이지":::

    구성 창이 열리고 모든 정책 구성 옵션이 나타납니다. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="구성 옵션 적용":::

1. 범위, 할당 이름, 기타 관련 옵션을 설정합니다.

1. **검토 + 만들기** 를 선택합니다.

## <a name="recommendations-with-denyenforce-options"></a>거부/적용 옵션 권장 사항

해당 권장 사항은 **deny** 옵션과 함께 사용할 수 있습니다.

[!INCLUDE [azure-security-center-recommendations-deny](../../includes/asc/recommendations-with-deny.md)]

해당 권장 사항은 **적용** 옵션과 함께 사용할 수 있습니다.

- SQL Server에 대한 감사가 사용되도록 설정되어야 함
- Azure Backup을 가상 머신에 사용하도록 설정해야 합니다.
- SQL 서버에서 SQL용 Azure Defender를 사용하도록 설정해야 함
- 클러스터에 Kubernetes용 Azure Policy 추가 기능을 설치하고 사용하도록 설정해야 함
- Azure Stream Analytics에서 진단 로그를 사용하도록 설정
- 배치 계정의 진단 로그를 사용하도록 설정해야 합니다.
- Data Lake Analytics의 진단 로그를 사용하도록 설정해야 합니다.
- Event Hub의 진단 로그를 사용하도록 설정해야 합니다.
- Key Vault의 진단 로그를 사용하도록 설정해야 합니다.
- Logic Apps의 진단 로그를 사용하도록 설정해야 함
- Search Service의 진단 로그를 사용하도록 설정해야 함
- Service Bus의 진단 로그를 사용하도록 설정해야 합니다.
