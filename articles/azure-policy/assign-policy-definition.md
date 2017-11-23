---
title: "Azure 환경에서 규정 비준수 리소스를 식별하는 정책 할당 만들기 | Microsoft Docs"
description: "이 문서에서는 규정 비준수 리소스를 식별하는 정책 정의를 만드는 단계를 안내합니다."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/02/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: a1b2f72cf33c740a52cf817766e9950a04886141
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>Azure 환경에서 규정 비준수 리소스를 식별하는 정책 할당 만들기
Azure의 규정 준수를 이해하기 위한 첫 번째 단계는 보유한 리소스의 현재 위치를 파악하는 것입니다. 이 빠른 시작은 Managed Disks를 사용하지 않는 가상 컴퓨터를 식별하는 정책 할당 만들기 과정을 단계별로 안내합니다.

이 프로세스가 끝나면 Managed Disks를 사용하지 않는 *비규격* 가상 컴퓨터가 식별됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="opt-in-to-azure-policy"></a>Azure Policy에 옵트인

Azure Policy는 공개 미리 보기에서 사용할 수 있으며 액세스를 요청하려면 등록해야 합니다.

1. https://aka.ms/getpolicy에 있는 Azure Policy로 이동하고 왼쪽 창에서 **등록**을 선택합니다.

   ![정책 검색](media/assign-policy-definition/sign-up.png)

2. **구독** 목록에서 사용하려는 구독을 선택하여 Azure Policy에 옵트인합니다. 그런 다음 **등록**을 선택합니다.

   ![Azure Policy를 사용하기 위한 옵트인](media/assign-policy-definition/preview-opt-in.png)

   요청은 미리 보기에 대해 자동으로 승인됩니다. 시스템에서 등록을 처리하는 데 최대 30분이 걸릴 수 있습니다.

## <a name="create-a-policy-assignment"></a>정책 할당 만들기

이 빠른 시작에서 정책 할당을 만들고 *Managed Disks가 없는 가상 컴퓨터 감사* 정책 정의를 할당합니다.

1. Azure Policy 페이지의 왼쪽 창에서 **할당**을 선택합니다.
2. **할당** 창의 위쪽에서 **정책 할당**를 선택합니다.

   ![정책 정의 할당](media/assign-policy-definition/select-assign-policy.png)

3. **정책 할당** 페이지에서 **정책** 필드 옆에 있는 ![정책 정의 단추](media/assign-policy-definition/definitions-button.png)를 클릭하여 사용 가능한 정의 목록을 엽니다.

   ![사용 가능한 정책 정의 열기](media/assign-policy-definition/open-policy-definitions.png)

   Azure Policy는 사용 가능한 기본 정책 정의를 제공합니다. 다음과 같은 기본 정책 정의가 표시됩니다.

   - 태그 및 태그 값 강제 적용
   - 태그 및 해당 값 적용
   - SQL Server 버전 12.0 필요

4. 정책 정의를 검색하여 *Managed Disks를 사용하지 않는 VM 감사* 정의를 찾습니다. 해당 정책을 클릭하고 **할당**을 클릭합니다.

   ![올바른 정책 정의 찾기](media/assign-policy-definition/select-available-definition.png)

5. 정책 할당에 대한 표시 **이름**을 제공합니다. 이 경우 *Managed Disks를 사용하지 않는 VM 감사*를 사용합니다. 선택적인 **설명**을 추가할 수도 있습니다. 이 설명은 이 정책 할당이 Managed Disks를 사용하지 않는 이 환경에서 작성된 모든 가상 컴퓨터를 식별하는 방법에 대한 세부 정보를 제공합니다.
6. 정책이 기존 리소스에 적용되도록 하려면 가격 책정 계층을 **표준**으로 변경합니다.

   Azure Policy 내에는 *체험* 및 *표준*의 두 가지 가격 책정 계층이 있습니다. 체험 계층을 사용하면 미래의 리소스에만 정책을 적용할 수 있지만, 표준 계층을 사용하면 기존 리소스에도 정책을 적용하여 규정 준수 상태를 보다 더 잘 파악할 수 있습니다. 제한된 미리 보기로 인해 아직 가격 책정 모델이 공개되지 않아 *표준*을 선택해도 요금이 청구되지 않습니다. 가격 책정에 대한 자세한 내용은 [Azure Policy 가격](https://acom-milestone-ignite.azurewebsites.net/pricing/details/azure-policy/)을 참조하세요.

7. 정책을 적용할 **범위**를 선택합니다.  범위는 정책 할당이 적용되는 리소스 또는 리소스 그룹을 결정합니다. 구독에서 리소스 그룹까지 범위에 포함될 수 있습니다.
8. 이전에 Azure Policy에 옵트인할 때 등록한 구독(또는 리소스 그룹)을 선택합니다. 이 예제에서는 **Azure Analytics 용량 개발** 구독을 사용하겠습니다. 하지만 옵션은 달라질 수 있습니다.

   ![올바른 정책 정의 찾기](media/assign-policy-definition/assign-policy.png)

9. **할당**을 선택합니다.

이제 규정 비준수 리소스를 식별하여 환경의 준수 상태를 파악할 준비가 되었습니다.

## <a name="identify-non-compliant-resources"></a>규정 비준수 리소스 식별

왼쪽 창에서 **준수**를 선택하고 앞에서 만든 정책 할당을 검색합니다.

![정책 준수](media/assign-policy-definition/policy-compliance.png)

이 새 할당을 준수하지 않는 기존 리소스가 있는 경우 **규정 비준수 리소스** 탭에 표시됩니다.

기존 리소스에 대해 조건을 평가한 결과 일부 리소스에서 사실로 확인된 경우 해당 리소스는 정책을 준수하지 않는 것으로 표시됩니다. 다음 표에는 현재 사용 가능한 여러 작업이 조건 평가 결과와 연동하는 방식 및 리소스의 규정 준수 상태가 정리되어 있습니다.

|리소스  |정책의 조건 평가 결과가 다음과 같은 경우  |정책의 작업   |규정 준수 상태  |
|-----------|---------|---------|---------|
|exists     |True     |거부     |비준수 |
|exists     |False    |거부     |준수     |
|exists     |True     |추가   |비준수 |
|exists     |False    |추가   |준수     |
|exists     |True     |감사    |비준수 |
|exists     |False    |감사    |비준수 |

## <a name="clean-up-resources"></a>리소스 정리

이 컬렉션의 다른 가이드는 이 빠른 시작에 기반하여 작성되었습니다. 후속 자습서를 계속 사용하려면 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 단계에 따라 이 빠른 시작에서 만든 모든 리소스를 Azure Portal에서 삭제합니다.
1. 왼쪽 창에서 **할당**을 선택합니다.
2. 방금 만든 할당을 검색합니다.

   ![할당 삭제](media/assign-policy-definition/delete-assignment.png)

3.  **할당 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 범위 내 모든 리소스가 규정을 준수하도록 보장하고 준수하지 않는 리소스를 식별할 수 있도록 특정 범위에 정책 정의를 할당했습니다.

정책 할당에 대해 자세히 알아보고 **앞으로** 만들 리소스에서 정책을 준수하도록 하려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [정책 만들기 및 관리](./create-manage-policy.md)
