---
title: 빠른 시작 - 서버용 Azure Arc를 사용하여 Azure에 머신 연결 - 포털
description: 이 빠른 시작에서는 포털에서 서버용 Azure Arc를 사용하여 Azure에 연결하는 방법에 대해 알아봅니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Automation, DSC, PowerShell, Desired State Configuration, 업데이트 관리, 변경 내용 추적, 인벤토리, Runbook, Python, 그래픽, 하이브리드, 온보딩
ms.date: 08/25/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 26c79db956b2703bf037fc6f7790d4ee13874410
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834240"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---portal"></a>빠른 시작: 서버용 Azure Arc를 사용하여 Azure에 머신 연결 - 포털

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

[서버용 Azure Arc 개요](overview.md)에서 지원되는 클라이언트와 필수 네트워크 구성을 검토합니다.

## <a name="generate-the-agent-install-script-using-the-azure-portal"></a>Azure Portal을 사용하여 에이전트 설치 스크립트 생성

1. 시작 [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal)
1. **+추가**를 클릭합니다.
1. 마법사에 따라 완료합니다.
1. 마지막 페이지에 복사하거나 다운로드할 수 있는 스크립트가 생성됩니다.

스크립트는 연결할 대상 머신에서 실행해야 합니다. 그러면 에이전트를 다운로드 및 설치하고, 단일 작업으로 머신을 연결합니다.

관리하려는 비 Azure 서버에서 다음을 수행하세요.

1. 서버에 로그온합니다(SSH, RDP 또는 PowerShell Remoting 사용).
1. 셸을 시작합니다(Linux에서는 bash, Windows에서는 관리자 권한으로 PowerShell 실행).
1. 포털의 스크립트에 붙여넣고 서버에서 실행하여 Azure에 연결합니다.
1. 개별 서버를 온보딩하기 위한 기본 인증은 Azure '디바이스 로그인'을 사용하는 *대화형* 방식입니다. 스크립트를 실행하면 다음과 비슷한 메시지가 표시됩니다.

  ```none
  To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B3V3NLWRF to authenticate.
  ```
  
   브라우저를 열고 인증할 코드를 입력하세요. 브라우저는 온보딩하는 서버에서 실행할 필요가 없으며, 노트북 등과 같은 다른 컴퓨터에서 실행할 수 있습니다.

1. 비대화형 방식으로 인증하려면 [서비스 주체 만들기](quickstart-onboard-powershell.md#create-a-service-principal-for-onboarding-at-scale) 단계를 수행하고 포털에서 생성된 스크립트를 수정합니다.

> [!NOTE]
> 서버에서 Internet Explorer를 처음으로 사용하여 로그인하면 오류가 발생합니다. 이 경우 브라우저를 다시 열고 다시 시도하세요.

## <a name="execute-the-script-on-target-nodes"></a>대상 노드에서 스크립트 실행

각 노드에 로그인하고 포털에서 생성 한 스크립트를 실행하세요. 스크립트가 완료되면 Azure Portal로 이동하여 서버가 연결되었는지 확인해야 합니다.

![온보딩 성공](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>정리

서버용 Azure Arc에서 머신을 연결 해제하려면 다음의 두 단계를 수행해야 합니다.

1. [포털](https://aka.ms/hybridmachineportal)에서 머신을 선택하고, 줄임표(`...`)를 클릭한 다음, **삭제**를 선택합니다.
1. 머신에서 에이전트를 제거합니다.

   Windows에서는 "앱 및 기능" 제어판을 사용하여 에이전트를 제거할 수 있습니다.
  
  ![앱 및 기능](./media/quickstart-onboard/apps-and-features.png)

   제거를 스크립팅하려면 **PackageId**를 검색하고 `msiexec /X`를 사용하여 에이전트를 제거하는 다음 예제를 사용할 수 있습니다.

   레지스트리 키 `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` 아래에서 **PackageId**를 찾습니다. 그런 다음, `msiexec`를 사용하여 에이전트를 제거할 수 있습니다.

   아래 예제에서는 에이전트를 제거하는 방법을 보여줍니다.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /Quiet /X "$($_.PsChildName)"}
   ```

   Linux에서는 다음 명령을 실행하여 에이전트를 제거합니다.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [연결된 머신에 정책 할당](../../governance/policy/assign-policy-portal.md)
