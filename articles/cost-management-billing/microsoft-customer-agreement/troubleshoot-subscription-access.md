---
title: Microsoft 고객 계약에 서명한 후에 구독 액세스 문제 해결 - Azure
description: 이 문서는 새 Microsoft 고객 계약에 서명한 후에 구독 액세스 문제를 해결하는 데 도움이 됩니다.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 04/07/2021
ms.author: banders
ms.reviewer: baolcsva
ms.openlocfilehash: 887c3018c7791fe3e827a7cb974a237cdf2358dd
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108129678"
---
# <a name="troubleshoot-subscription-access-after-you-sign-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 서명한 후에 구독 액세스 문제 해결

이 문서는 새 Microsoft 고객 계약에 서명한 후에 구독 액세스 문제를 해결하는 데 도움이 됩니다. 다음 정보를 사용하여 일반적인 문제를 해결합니다.

## <a name="troubleshoot-subscription-access"></a>구독 액세스 문제 해결

청구 계정 유형이 Microsoft 고객 계약인지 확인합니다. Azure Portal의 **Cost Management + Billing** 에서 계정 유형을 확인할 수 있습니다. 자세한 내용은 [MCA에 대한 액세스 권한이 있는지 확인](../understand/mca-understand-your-usage.md#check-access-to-a-microsoft-customer-agreement)을 참조하세요.

## <a name="troubleshoot-viewing-your-billing-account"></a>청구 계정 보기 문제 해결

청구 계정을 볼 때 문제가 발생하고 여러 테넌트가 있는 경우 Azure Portal에서 디렉터리를 전환해 봅니다.

1. Azure Portal 오른쪽 위에서 Azure 계정을 선택합니다.
1. 디렉터리 전환을 선택합니다.  
    :::image type="content" source="./media/troubleshoot-subscription-access/switch-directory.png" alt-text="디렉터리 전환 옵션을 보여주는 스크린샷" lightbox="./media/troubleshoot-subscription-access/switch-directory.png" :::
1. 디렉터리 + 구독 창에서 다른 디렉터리를 선택하여 전환합니다.  
    :::image type="content" source="./media/troubleshoot-subscription-access/select-directory.png" alt-text="다른 디렉터리를 선택할 수 있는 위치를 보여주는 스크린샷" lightbox="./media/troubleshoot-subscription-access/select-directory.png" :::

## <a name="troubleshoot-account-access"></a>계정 액세스 문제 해결

동일한 이메일 주소를 사용하는 Microsoft 계정 여러 개가 있을 수 있습니다. 개인 계정과 회사 계정이 있을 수 있습니다. Microsoft 고객 계약에서는 회사 또는 학교 계정을 사용합니다. 자세한 내용은 [회사 또는 학교 계정으로 로그인했는지 확인](https://support.microsoft.com/office/which-account-do-you-want-to-use-2b5bbd7a-7df6-4283-beff-8015e28eb7b9)을 참조하세요.

- 회사 또는 학교 계정으로 로그인합니다.  
    :::image type="content" source="./media/troubleshoot-subscription-access/two-accounts.png" alt-text="회사 또는 학교 계정 선택을 보여주는 스크린샷" lightbox="./media/troubleshoot-subscription-access/two-accounts.png" :::

## <a name="next-steps"></a>다음 단계

- [Microsoft 고객 계약 문서](./index.yml)를 참조하세요.