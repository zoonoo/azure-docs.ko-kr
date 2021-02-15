---
title: 응용 프로그램 변경 분석 문제 해결-Azure Monitor
description: 응용 프로그램 변경 분석의 문제를 해결 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 7200978ce31a47f7bd0d023cecf359b10a1c96de
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100521085"
---
# <a name="troubleshoot-application-change-analysis-preview"></a>응용 프로그램 변경 분석 (미리 보기) 문제 해결

## <a name="having-trouble-registering-microsoft-change-analysis-resource-provider-from-change-history-tab"></a>Microsoft를 등록 하는 데 문제가 있습니다. 변경 기록 탭에서 분석 리소스 공급자 변경

응용 프로그램 변경 분석과 통합 한 후 변경 기록을 처음 볼 때 리소스 공급자를 자동으로 등록 하는 것을 볼 수 있습니다. **ChangeAnalysis**. 드문 경우 지만 다음과 같은 이유로 실패할 수 있습니다.

- **Microsoft. ChangeAnalysis 리소스 공급자를 등록할 수 있는 권한이** 없습니다. 이 오류 메시지는 현재 구독의 역할에 **Microsoft. 지원/등록/동작** 범위가 연결 되어 있지 않음을 의미 합니다. 이는 구독의 소유자가 아니고 동료 (즉, 리소스 그룹에 대 한 액세스 보기)를 통해 공유 액세스 권한을 받은 경우에 발생할 수 있습니다. 이 문제를 해결 하려면 구독의 소유자에 게 문의 하 여 **Microsoft. ChangeAnalysis** 리소스 공급자를 등록 합니다. 이 작업은 구독을 통해 Azure Portal에서 수행할 수 있습니다. **| 리소스 공급자** 를 검색 하 고 ```Microsoft.ChangeAnalysis``` UI를 검색 하 고 등록 하거나 Azure PowerShell 또는 Azure CLI를 통해 등록 합니다.

    PowerShell을 통해 리소스 공급자를 등록 합니다.
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **Microsoft. ChangeAnalysis 리소스 공급자를 등록 하지 못했습니다**. 이 메시지는 UI가 리소스 공급자를 등록 하 라는 요청을 보낸 후 즉시 실패 한 것을 의미 하며, 권한 문제와는 관련이 없습니다. 일시적인 인터넷 연결 문제일 가능성이 있습니다. 페이지를 새로 고치고 인터넷 연결을 확인 하세요. 오류가 계속 발생 하면 다음으로 문의 하세요. changeanalysishelp@microsoft.com

- **이 작업이 예상 보다 오래 걸리고** 있습니다. 이 메시지는 등록이 2 분 이상 걸리는 것을 의미 합니다. 이것은 비정상 이지만 반드시 문제가 발생 한 것은 아닙니다. 구독으로 이동할 수 있습니다. **|** **Microsoft. changeanalysis** 리소스 공급자 등록 상태를 확인 하는 리소스 공급자입니다. UI를 사용 하 여 등록을 취소 하 고 다시 등록 하거나 새로 고쳐 도움이 되는지 확인할 수 있습니다. 문제가 계속 되 면 지원 담당자에 게 문의 하세요 changeanalysishelp@microsoft.com .
    ![너무 오래 걸리는 RP 등록 문제 해결](./media/change-analysis/troubleshoot-registration-taking-too-long.png)

![문제 해결 도구가 선택 된 가상 컴퓨터에 대 한 문제 진단 및 해결 도구의 스크린샷](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![가상 컴퓨터에 대 한 최근 변경 내용 분석 문제 해결 도구에 대 한 타일의 스크린샷](./media/change-analysis/analyze-recent-changes.png)

## <a name="azure-lighthouse-subscription-is-not-supported"></a>Azure Lighthouse 구독은 지원 되지 않습니다.

- Azure lighthouse 구독 메시지를 사용 하 여 **Microsoft ChangeAnalysis 리소스 공급자를 쿼리하지 못했습니다** . *변경 내용은 구독의 홈 테 넌 트에서만 제공* 됩니다. 현재는 홈 테 넌 트에 없는 사용자를 위해 Azure Lighthouse 구독을 통해 변경 분석 리소스 공급자를 등록 하는 제한 사항이 있습니다. 이 제한 사항을 해결 하기 위해 노력 하 고 있습니다. 이 문제가 차단 문제인 경우 서비스 주체를 만들고 액세스를 허용 하도록 역할을 명시적으로 할당 하는 해결 방법이 있습니다.  자세한 내용은에 문의 하세요 changeanalysishelp@microsoft.com .

## <a name="an-error-occurred-while-getting-changes-please-refresh-this-page-or-come-back-later-to-view-changes"></a>변경 내용을 가져오는 동안 오류가 발생 했습니다. 변경 내용을 보려면이 페이지를 새로 고치거 나 나중에 다시 방문해 주세요.

변경 내용을 로드할 수 없는 경우 응용 프로그램 변경 분석 서비스에서 제공 하는 일반 오류 메시지입니다. 몇 가지 알려진 원인은 다음과 같습니다.

- 클라이언트 장치에서 인터넷 연결 오류가 발생 했습니다.
- 몇 분 후에 페이지 새로 고침을 일시적으로 사용할 수 없게 되는 변경 분석 서비스는 일반적으로이 문제를 해결 합니다. 오류가 계속 발생 하면 다음으로 문의 하세요. changeanalysishelp@micorosoft.com

## <a name="you-dont-have-enough-permissions-to-view-some-changes-contact-your-azure-subscription-administrator"></a>일부 변경 내용을 볼 수 있는 권한이 없습니다. Azure 구독 관리자에 게 문의

현재 사용자에 게 변경 내용을 볼 수 있는 권한이 없다는 것을 설명 하는 일반 무단 오류 메시지입니다. Azure 리소스 그래프 및 Azure Resource Manager에서 반환 된 인프라 변경 내용을 보려면 리소스에 대해 최소한의 판독기 액세스가 필요 합니다. 웹 앱 게스트 파일 변경 및 구성 변경의 경우 참가자 역할이 최소한 필요 합니다.

## <a name="failed-to-register-microsoftchangeanalysis-resource-provider"></a>Microsoft. ChangeAnalysis 리소스 공급자를 등록 하지 못했습니다.

이 메시지는 UI가 리소스 공급자를 등록 하 라는 요청을 보낸 후 즉시 실패 한 것을 의미 하며, 권한 문제와는 관련이 없습니다. 일시적인 인터넷 연결 문제일 가능성이 있습니다. 페이지를 새로 고치고 인터넷 연결을 확인 하세요. 오류가 계속 발생 하면 다음으로 문의 하세요. changeanalysishelp@microsoft.com

## <a name="you-dont-have-enough-permissions-to-register-microsoftchangeanalysis-resource-provider-contact-your-azure-subscription-administrator"></a>Microsoft. ChangeAnalysis 리소스 공급자를 등록할 수 있는 권한이 없습니다. Azure 구독 관리자에 게 문의

이 오류 메시지는 현재 구독의 역할에 **Microsoft. 지원/등록/동작** 범위가 연결 되어 있지 않음을 의미 합니다. 이는 구독의 소유자가 아니고 동료 (즉, 리소스 그룹에 대 한 액세스 보기)를 통해 공유 액세스 권한을 받은 경우에 발생할 수 있습니다. 이 문제를 해결 하려면 구독의 소유자에 게 문의 하 여 **Microsoft. ChangeAnalysis** 리소스 공급자를 등록 합니다. 이 작업은 구독을 통해 Azure Portal에서 수행할 수 있습니다. **| 리소스 공급자** 를 검색 하 고 ```Microsoft.ChangeAnalysis``` UI를 검색 하 고 등록 하거나 Azure PowerShell 또는 Azure CLI를 통해 등록 합니다.

PowerShell을 통해 리소스 공급자를 등록 합니다.

```PowerShell
# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
```

## <a name="next-steps"></a>다음 단계

- Power Change 분석에 도움이 되는 [Azure 리소스 그래프](../../governance/resource-graph/overview.md)에 대해 자세히 알아보세요.