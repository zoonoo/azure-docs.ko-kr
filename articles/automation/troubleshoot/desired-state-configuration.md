---
title: Azure Automation DSC(Desired State Configuration)에 관한 문제 해결
description: 이 문서에는 DSC(Desired State Configuration) 문제 해결에 대한 정보가 제공됩니다.
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a2ec36a99b2940fa662b0d9bd16b06777684db2f
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448065"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>DSC(필요한 상태 구성) 문제 해결

이 문서에는 DSC(Desired State Configuration)에 관한 문제를 해결하는 방법에 대한 정보가 제공됩니다.

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>DSC(필요한 상태 구성)으로 작업하는 경우 일반적인 오류 문제

### <a name="unsupported-characters"></a>시나리오: 특수 문자를 사용 하 여 구성 포털에서 삭제할 수 없습니다.

#### <a name="issue"></a>문제

포털에서 DSC 구성을 삭제 하려고 시도할 때, 다음 오류가 표시:

```
An error occured while deleteing the DSC configuration '<name>'.  Error-details: The arguement configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>원인

확인할 계획 된 임시 문제입니다.

#### <a name="resolution"></a>해결 방법

* 구성을 삭제 하려면 "제거-AzAutomationDscConfiguration" Az Cmdlet을 사용 합니다.
* 이 cmdlet에 대 한 설명서가 아직 업데이트 되지 않았습니다.  그때 까지는 AzureRM 모듈에 대 한 설명서를 참조 하십시오.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-not-found"></a>시나리오: 노드가 "찾을 수 없음" 오류로 실패한 상태임

#### <a name="issue"></a>문제

노드에 **실패** 상태의 보고서가 있으며 오류가 포함되어 있습니다.

```
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>원인

이 오류는 일반적으로 노드가 노드 구성 이름(예: ABC.WebServer) 대신 구성 이름(예: ABC)에 할당된 경우에 발생합니다.

#### <a name="resolution"></a>해결 방법

* "구성 이름"이 아니라 "노드 구성 이름"으로 노드를 할당해야 합니다.
* Azure 포털 또는 PowerShell cmdlet을 사용하여 노드 구성을 노드에 할당할 수 있습니다.

  * Azure Portal을 사용하여 노드 구성을 노드에 할당하려면 **DSC 노드** 페이지를 열고, 노드를 선택한 다음, **노드 구성 할당** 단추를 클릭합니다.  
  * PowerShell cmdlet을 사용하여 노드 구성을 노드에 할당하려면 **Set-AzureRmAutomationDscNode** cmdlet을 사용합니다.

### <a name="no-mof-files"></a>시나리오: 구성이 컴파일될 때 생성된 노드 구성(mof 파일)이 없음

#### <a name="issue"></a>문제

DSC 컴파일 작업이 오류로 인해 중단됩니다.

```
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>원인

DSC 구성에서 **Node** 키워드 다음에 오는 식이 `$null`로 평가되면 노드 구성이 생성되지 않습니다.

#### <a name="resolution"></a>해결 방법

다음 해결 방법 중 하나를 사용하여 문제를 해결합니다.

* 구성 정의에서 **Node** 키워드 옆의 식이 $null로 계산되지 않는지 확인합니다.
* 구성을 컴파일할 때 ConfigurationData를 전달하는 경우 [ConfigurationData](../automation-dsc-compile.md#configurationdata)에서 구성에 필요한 값을 전달해야 합니다.

### <a name="dsc-in-progress"></a>시나리오: DSC 노드 보고서가 "진행 중" 상태로 중단됨

#### <a name="issue"></a>문제

DSC 에이전트 출력:

```
No instance found with given property values
```

#### <a name="cause"></a>원인

WMF 버전을 업그레이드했고 WMI가 손상되었습니다.

#### <a name="resolution"></a>해결 방법

문제를 해결하려면 [DSC 알려진 문제 및 제한 사항](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) 문서의 지침을 따릅니다.

### <a name="issue-using-credential"></a>시나리오: DSC 구성에서 자격 증명을 사용할 수 없습니다.

#### <a name="issue"></a>문제

DSC 컴파일 작업이 오류로 인해 중단되었습니다.

```
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>원인

구성에서 자격 증명을 사용하지만 각 노드 구성에 대해 **PSDscAllowPlainTextPassword**를 true로 설정하는 적절한 **ConfigurationData**를 제공하지 않았습니다.

#### <a name="resolution"></a>해결 방법

* 적절한 **ConfigurationData**에 전달하여 구성에서 언급한 각 노드의 구성에 대해 **PSDscAllowPlainTextPassword**를 true로 설정하도록 합니다. 자세한 내용은 [Azure Automation DSC의 자산](../automation-dsc-compile.md#assets)을 참조하세요.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우, 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.
