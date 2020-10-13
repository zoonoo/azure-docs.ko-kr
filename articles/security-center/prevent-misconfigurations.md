---
title: Azure Security Center에서 잘못 된 구성을 방지 하는 방법
description: 권장 사항 세부 정보 페이지에서 Security Center의 ' 적용 ' 및 ' 거부 ' 옵션을 사용 하는 방법에 대해 알아봅니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 19e9a33350b6a1a67986dc35a372f737e45ab39a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906395"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>적용/거부 권장 구성으로 잘못 된 구성 방지

보안 구성 오류는 보안 인시던트의 주요 원인입니다. 이제 Security Center에는 특정 추천 사항과 관련하여 새 리소스의 구성 오류를 *방지*하는 데 도움이 되는 기능이 있습니다. 

이 기능을 통해 워크로드를 안전하게 유지하고 보안 점수를 안정화할 수 있습니다.

특정 추천 사항에 따라 보안 구성을 적용하는 방법은 다음 두 가지 모드로 제공됩니다.

- Azure Policy의 **거부** 효과를 사용하여 비정상 리소스가 만들어지는 것을 중지할 수 있습니다.
- **적용** 옵션을 사용하여 Azure Policy의 **DeployIfNotExist** 효과를 활용하고, 비준수 리소스를 만들 때 자동으로 수정할 수 있습니다.

이는 선택한 보안 권장 사항에 대 한 리소스 세부 정보 페이지의 맨 위에 있습니다 ( [거부/적용 옵션을 사용 하는 권장 사항](#recommendations-with-denyenforce-options)참조).

## <a name="prevent-resource-creation"></a>리소스 생성 방지

1. 새 리소스에서 충족 해야 하는 권장 사항을 열고 페이지 맨 위에서 **거부** 단추를 선택 합니다.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="거부 단추가 강조 표시 된 권장 사항 페이지":::

    구성 창이 열리고 범위 옵션이 나열 됩니다. 

1. 관련 구독 또는 관리 그룹을 선택 하 여 범위를 설정 합니다.

    > [!TIP]
    > 행의 끝에 있는 세 개의 점을 사용 하 여 단일 구독을 변경 하거나 확인란을 사용 하 여 여러 구독 또는 그룹을 선택한 후 **거부로 변경**을 선택할 수 있습니다.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="거부 단추가 강조 표시 된 권장 사항 페이지":::


## <a name="enforce-a-secure-configuration"></a>보안 구성 적용

1. 새 리소스가 충족 하지 않는 경우 템플릿 배포를 배포할 권장 사항을 열고 페이지 맨 위에 있는 **적용** 단추를 선택 합니다.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="거부 단추가 강조 표시 된 권장 사항 페이지":::

    구성 창이 열리고 모든 정책 구성 옵션이 나타납니다. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="거부 단추가 강조 표시 된 권장 사항 페이지":::

1. 범위, 할당 이름 및 기타 관련 옵션을 설정 합니다.

1. **검토 + 만들기**를 선택합니다.

## <a name="recommendations-with-denyenforce-options"></a>거부/적용 옵션을 사용한 권장 사항

이러한 권장 사항은 **deny** 옵션과 함께 사용할 수 있습니다.

- 가상 머신을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.
- 스토리지 계정을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.
- RootManageSharedAccessKey를 제외한 모든 권한 부여 규칙을 이벤트 허브 네임스페이스에서 제거해야 함
- Service Bus 네임스페이스에서 RootManageSharedAccessKey를 제외한 모든 인증 규칙을 제거해야 함
- Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.
- Redis Cache에 보안 연결만 사용하도록 설정해야 합니다.
- 자동화 계정 변수를 암호화해야 합니다.
- Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 합니다.
- Service Fabric 클러스터의 ClusterProtectionLevel 속성을 EncryptAndSign으로 설정해야 합니다.
- 스토리지 계정에 대한 무제한 네트워크 액세스 감사


이러한 권장 사항은 **적용** 옵션과 함께 사용할 수 있습니다.

- Logic Apps의 진단 로그를 사용하도록 설정해야 함
- Data Lake Analytics의 진단 로그를 사용하도록 설정해야 합니다.
- IoT Hub의 진단 로그를 사용하도록 설정해야 합니다.
- 배치 계정의 진단 로그를 사용하도록 설정해야 합니다.
- Azure Stream Analytics에서 진단 로그를 사용하도록 설정
- Service Bus의 진단 로그를 사용하도록 설정해야 합니다.
- Search Service의 진단 로그를 사용하도록 설정해야 함
- Event Hub의 진단 로그를 사용하도록 설정해야 합니다.
- Virtual Machine Scale Sets의 진단 로그를 사용하도록 설정해야 합니다.
- Key Vault의 진단 로그를 사용하도록 설정해야 합니다.
- SQL Server에 대한 감사가 사용되도록 설정되어야 함
- SQL Server에서 Advanced Data Security를 사용하도록 설정해야 합니다.



