---
title: 애플리케이션 변경 분석 문제 해결 - Azure Monitor
description: 애플리케이션 변경 분석 문제를 해결하는 방법에 대해 알아봅니다.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 91a7cc4b96dcff7a20afccfe5995574b89a8e87b
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108315708"
---
# <a name="troubleshoot-application-change-analysis-preview"></a>애플리케이션 변경 분석 문제 해결(미리 보기)

## <a name="having-trouble-registering-microsoft-change-analysis-resource-provider-from-change-history-tab"></a>Microsoft에 등록하는 데 문제가 있습니다. 기록 변경 탭의 변경 분석 리소스 공급자

애플리케이션 변경 분석과 통합한 후 기록 변경을 처음 보는 것이라면, 리소스 공급자 **Microsoft.ChangeAnalysis** 를 자동으로 등록하는 것을 볼 수 있습니다. 드문 경우지만 다음과 같은 이유로 실패할 수 있습니다.

- **Microsoft.ChangeAnalysis 리소스 공급자를 등록할 수 있는 권한이 없습니다**. 이 오류 메시지는 현재 구독한 역할에 **Microsoft.Support/register/action** 범위가 연결되어 있지 않음을 의미합니다. 이는 구독의 소유자가 아니고 동료(즉, 리소스 그룹에 대한 액세스 보기)를 통해 공유 액세스 권한을 받은 경우에 발생할 수 있습니다. 이 문제를 해결하려면 구독 소유자에게 문의하여 **Microsoft. ChangeAnalysis** 리소스 공급자를 등록합니다. 이 작업은 **구독 | 리소스 공급자** 를 통해 Azure Portal에서 ```Microsoft.ChangeAnalysis```을 검색하고 UI에 등록하거나 Azure PowerShell 또는 Azure CLI를 통해 수행할 수 있습니다.

    다음처럼 PowerShell을 통해 리소스 공급자를 등록합니다.
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **Microsoft.ChangeAnalysis 리소스 공급자 등록 실패**. 이 메시지는 UI가 리소스 공급자를 등록하라는 요청을 보낸 후 즉시 실패한 것을 의미하며, 권한 문제와는 관련이 없습니다. 일시적인 인터넷 연결 문제일 가능성이 있습니다. 페이지를 새로 고치고 인터넷 연결을 확인하세요. 오류가 지속되면 changeanalysishelp@microsoft.com로 연락하세요.

- **예상보다 오래 걸리고 있습니다**. 이 메시지는 등록에 2분 이상 걸리는 것을 의미합니다. 이것은 비정상이지만 반드시 문제가 발생했다고 볼 수는 없습니다. **구독 | 리소스 공급자** 로 이동하여 **Microsoft.ChangeAnalysis** 리소스 공급자 등록 상태를 확인할 수 있습니다. UI를 사용하여 등록을 취소하거나 다시 등록하거나 새로 고친 후에 도움이 되는지 확인할 수 있습니다. 문제가 지속되면 changeanalysishelp@microsoft.com로 연락하여 도움을 받으세요.
    ![너무 오래 걸리는 RP 등록 문제 해결](./media/change-analysis/troubleshoot-registration-taking-too-long.png)

![선택한 문제 해결 도구가 있는 가상 머신에 대한 문제 진단 및 해결 도구의 스크린샷.](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![가상 머신에 대한 최근 변경 내용 문제 해결 도구에 대한 타일의 스크린샷.](./media/change-analysis/analyze-recent-changes.png)

## <a name="azure-lighthouse-subscription-is-not-supported"></a>Azure Lighthouse 구독은 지원되지 않습니다.

- *Azure Lighthouse 구독이 지원되지 않으며 변경 내용은 오직 구독의 홈 테넌트에만 제공된다* 는 메시지가 표시되며 **Microsoft ChangeAnalysis 리소스 공급자를 쿼리하지 못했습니다**. 현재 홈 테넌트에 없는 사용자에 대하여 Azure Lighthouse 구독을 통해 변경 분석 리소스 공급자를 등록하는 데 제한 사항이 있습니다. 이 제한 사항을 해결하기 위해 노력하고 있습니다. 이 문제가 차단 문제인 경우 서비스 주체를 만들고 액세스를 허용하도록 역할을 명시적으로 할당하는 해결 방법이 있습니다.  자세한 정보를 알아보려면 changeanalysishelp@microsoft.com로 연락하세요.

## <a name="an-error-occurred-while-getting-changes-please-refresh-this-page-or-come-back-later-to-view-changes"></a>변경 내용을 가져오는 동안 오류가 발생했습니다. 변경 내용을 보려면 이 페이지를 새로 고치거나 나중에 다시 방문하세요.

변경 내용을 로드할 수 없는 경우 애플리케이션 변경 분석 서비스에서 제공하는 일반 오류 메시지입니다. 몇 가지 알려진 원인은 다음과 같습니다.

- 클라이언트 디바이스에서 인터넷 연결 오류가 발생했습니다.
- 변경 분석 서비스가 일시적으로 사용할 수 없게 된 경우 몇 분 후에 페이지를 새로 고치면 일반적으로 이 문제가 해결됩니다. 오류가 지속되면 changeanalysishelp@micorosoft.com로 연락하세요.

## <a name="you-dont-have-enough-permissions-to-view-some-changes-contact-your-azure-subscription-administrator"></a>일부 변경 내용을 볼 수 있는 권한이 없습니다. Azure 구독 관리자에게 문의

현재 사용자에게 변경 내용을 볼 수 있는 충분한 권한이 없다는 것을 설명하는 일반적인 권한 없음 오류 메시지입니다. Azure Resource Graph 및 Azure Resource Manager에서 반환된 인프라 변경 내용을 보려면 리소스에 대한 최소한 읽기 권한자 액세스가 필요합니다. 웹앱 게스트 내 파일 변경 내용 및 구성 변경 내용의 경우, 최소한 참가자 역할이 필요합니다.

## <a name="failed-to-register-microsoftchangeanalysis-resource-provider"></a>Microsoft.ChangeAnalysis 리소스 공급자 등록 실패

이 메시지는 UI가 리소스 공급자를 등록하라는 요청을 보낸 후 즉시 실패한 것을 의미하며, 권한 문제와는 관련이 없습니다. 일시적인 인터넷 연결 문제일 가능성이 있습니다. 페이지를 새로 고치고 인터넷 연결을 확인하세요. 오류가 지속되면 changeanalysishelp@microsoft.com로 연락하세요.

## <a name="you-dont-have-enough-permissions-to-register-microsoftchangeanalysis-resource-provider-contact-your-azure-subscription-administrator"></a>Microsoft.ChangeAnalysis 리소스 공급자를 등록할 수 있는 권한이 없습니다. Azure 구독 관리자에게 문의

이 오류 메시지는 현재 구독한 역할에 **Microsoft.Support/register/action** 범위가 연결되어 있지 않음을 의미합니다. 이는 구독의 소유자가 아니고 동료(즉, 리소스 그룹에 대한 액세스 보기)를 통해 공유 액세스 권한을 받은 경우에 발생할 수 있습니다. 이 문제를 해결하려면 구독 소유자에게 문의하여 **Microsoft. ChangeAnalysis** 리소스 공급자를 등록합니다. 이 작업은 **구독 | 리소스 공급자** 를 통해 Azure Portal에서 ```Microsoft.ChangeAnalysis```을 검색하고 UI에 등록하거나 Azure PowerShell 또는 Azure CLI를 통해 수행할 수 있습니다.

다음처럼 PowerShell을 통해 리소스 공급자를 등록합니다.

```PowerShell
# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
```

## <a name="next-steps"></a>다음 단계

- Power 변경 분석에 사용할 수 있는 [Azure Resource Graph](../../governance/resource-graph/overview.md)에 대한 자세한 정보를 알아봅니다.
