---
title: 인스턴스 및 인증 설정(스크립팅됨)
titleSuffix: Azure Digital Twins
description: 자동화 된 배포 스크립트를 실행 하 여 Azure Digital Twins 서비스의 인스턴스를 설정 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 10d4d07a61bc4ebec789d53e4271a3bcdc7ba76b
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205588"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Azure Digital Twins 인스턴스 및 인증 (스크립팅된) 설정

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

이 문서에서는 인스턴스를 만들고 인증을 설정 하는 등 **새 Azure Digital Twins 인스턴스를 설정**하는 단계에 대해 설명 합니다. 이 문서를 완료 한 후에는에 대 한 프로그래밍을 시작할 준비가 된 Azure Digital Twins 인스턴스를 사용할 수 있습니다.

이 버전의이 문서에서는 프로세스를 간소화 하는 [ **자동화 된 배포 스크립트** 샘플](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) 을 실행 하 여 이러한 단계를 완료 합니다. 
* 백그라운드에서 스크립트를 실행 하는 수동 CLI 단계를 보려면이 문서의 CLI 버전 [*(방법: 인스턴스 및 인증 (CLI) 설정)*](how-to-set-up-instance-cli.md)을 참조 하세요.
* Azure Portal에 따라 수동 단계를 보려면이 문서의 포털 버전 [*(방법: 인스턴스 및 인증 설정) (포털)*](how-to-set-up-instance-portal.md)을 참조 하세요.

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prerequisites-download-the-script"></a>필수 조건: 스크립트 다운로드

샘플 스크립트는 PowerShell로 작성 됩니다. 이 샘플은 샘플 링크로 이동 하 고 제목 아래에 있는 *ZIP 다운로드* 단추를 선택 하 여 컴퓨터에 다운로드할 수 있는 [**Azure Digital twins 종단 간 샘플**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)의 일부입니다.

이렇게 하면 샘플 프로젝트가 _**Azure_Digital_Twins_end_to_end_samples.zip**_ 으로 컴퓨터에 다운로드 됩니다. 컴퓨터의 폴더로 이동한 후 파일의 압축을 풀어 파일의 압축을 풉니다.

압축을 푼 폴더에서 배포 스크립트는 _Azure_Digital_Twins_end_to_end_samples > 스크립트 > **deploy.ps1** _에 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>배포 스크립트 실행

이 문서에서는 azure digital Twins 코드 샘플을 사용 하 여 Azure Digital Twins 인스턴스 및 필요한 인증을 반 자동으로 배포 합니다. 또한 스크립팅된 상호 작용을 직접 작성 하기 위한 시작 지점으로 사용할 수 있습니다.

Cloud Shell에서 배포 스크립트를 실행 하는 단계는 다음과 같습니다.
1. 브라우저에서 [Azure Cloud Shell](https://shell.azure.com/) 창으로 이동 합니다. 다음 명령을 사용 하 여 로그인 합니다.
    ```azurecli
    az login
    ```
    CLI는 기본 브라우저를 열 수 있으면 기본 브라우저를 열고 Azure 로그인 페이지를 로드합니다. 그렇지 않으면 *https://aka.ms/devicelogin*에서 브라우저 페이지를 열고 터미널에 표시된 권한 부여 코드를 입력합니다.
 
2. Cloud Shell 아이콘 모음에서 Cloud Shell PowerShell 버전을 실행 하도록 설정 되었는지 확인 합니다.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="PowerShell 버전 선택을 보여 주는 Cloud Shell 창&quot;:::

1. &quot;파일 업로드/다운로드" 아이콘을 선택 하 고 "업로드"를 선택 합니다.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="PowerShell 버전 선택을 보여 주는 Cloud Shell 창&quot;:::

1. &quot;파일 업로드/다운로드":::

    컴퓨터의 _**deploy.ps1**_ 파일 ( _Azure_Digital_Twins_end_to_end_samples > 스크립트 > **deploy.ps1** _)로 이동 하 고 "열기"를 누릅니다. 그러면 Cloud Shell 창에서 실행할 수 있도록 Cloud Shell에 파일이 업로드 됩니다.

4. Cloud Shell 창에서 명령을 전송 하 여 스크립트를 실행 `./deploy.ps1` 합니다. 아래 명령을 복사 (Cloud Shell에 붙여 넣을 수 있습니다. Windows 및 Linux에서는 **Ctrl + shift + v** 를 사용 하거나 Macos에서는 **Cmd + shift + v** 를 사용할 수 있습니다. 오른쪽 클릭 메뉴를 사용할 수도 있습니다.

    ```azurecli
    ./deploy.ps1
    ```

    이 스크립트는 azure Digital Twins 인스턴스를 만들고 azure 사용자에 게 인스턴스에 azure *디지털 쌍 소유자 (미리 보기)* 역할을 할당 합니다.

    스크립트가 자동화 된 설치 단계를 실행 하면 다음 값을 전달 하 라는 메시지가 표시 됩니다.
    * 인스턴스: 사용할 Azure 구독의 *구독 ID*
    * 인스턴스의 경우 인스턴스를 배포 하려는 *위치* 입니다. Azure Digital Twins를 지 원하는 지역을 확인 하려면 [*지역별 제공 되는 azure 제품*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)을 방문 하세요.
    * 인스턴스의 경우: *리소스 그룹* 이름입니다. 기존 리소스 그룹을 사용 하거나 새 이름을 입력 하 여 만들 수 있습니다.
    * 인스턴스의 경우: Azure Digital Twins 인스턴스의 *이름* 입니다. 새 인스턴스의 이름은 구독에 대 한 지역 내에서 고유 해야 합니다. 즉, 사용자가 선택한 이름을 이미 사용 하 고 있는 지역에 다른 Azure 디지털 Twins 인스턴스가 있는 경우 다른 이름을 선택 하 라는 메시지가 표시 됩니다.

다음은 스크립트에서 출력 로그를 발췌 한 것입니다.

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="PowerShell 버전 선택을 보여 주는 Cloud Shell 창&quot;:::

1. &quot;파일 업로드/다운로드" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

스크립트가 성공적으로 완료 되 면 최종 인쇄물은로 표시 됩니다 `Deployment completed successfully` . 그렇지 않으면 오류 메시지를 해결 하 고 스크립트를 다시 실행 합니다. 이미 완료 한 단계를 무시 하 고 종료 된 지점에서 입력 요청을 다시 시작 합니다.

> [!NOTE]
> 이 스크립트는 현재 Azure Digital Twins (*Azure Digital Twins 소유자 (미리 보기)*) 내에서 필요한 관리 역할을 Cloud Shell 스크립트를 실행 하는 동일한 사용자에 게 할당 합니다. 인스턴스를 관리 하는 다른 사람에 게이 역할을 할당 해야 하는 경우 Azure Portal ([지침](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) 또는 CLI ([지침](how-to-set-up-instance-cli.md#set-up-user-access-permissions))를 통해이 역할을 수행할 수 있습니다.

>[!NOTE]
>현재 스크립팅된 설치에는 **알려진 문제가** 있습니다. 일부 사용자 (특히 [msas (개인 Microsoft 계정](https://account.microsoft.com/account))의 사용자)가 ** _Azure 디지털 쌍 소유자 (미리 보기)_ 에 대 한 역할 할당**을 찾을 수 없습니다.
>
>이 문서의 뒷부분에 나오는 [*사용자 역할 할당 확인*](#verify-user-role-assignment) 섹션을 사용 하 여 역할 할당을 확인 하 고 필요한 경우 [Azure Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) 또는 [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)를 사용 하 여 역할 할당을 수동으로 설정할 수 있습니다.
>
>이 문제에 대 한 자세한 내용은 [*문제 해결: Azure Digital Twins의 알려진 문제*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup)를 참조 하세요.

## <a name="verify-success-and-collect-important-values"></a>성공 확인 및 중요 한 값 수집

스크립트에 의해 설정 된 리소스 및 사용 권한 생성을 확인 하려면 아래 지침을 사용 하 여 [Azure Portal](https://portal.azure.com) 에서 해당 리소스를 확인할 수 있습니다. 단계의 성공 여부를 확인할 수 없는 경우 단계를 다시 시도 합니다. [Azure Portal](how-to-set-up-instance-portal.md) 또는 [CLI](how-to-set-up-instance-cli.md) 명령을 사용 하 여 단계를 개별적으로 수행할 수 있습니다.

또한이 섹션에서는 Azure Digital Twins 인스턴스 작업을 계속할 때 필요할 수 있는 스크립트에 의해 설정 된 리소스에서 중요 한 값을 찾는 방법을 보여 줍니다. 이러한 값은 안전한 위치에 저장 하거나 나중에 필요할 때 찾을 수 있도록이 섹션으로 돌아옵니다. 다른 사용자가 인스턴스에 대 한 프로그래밍을 수행 하는 경우에도 이러한 값을 공유 해야 합니다.

### <a name="verify-instance"></a>인스턴스 확인

인스턴스가 만들어졌는지 확인 하려면 Azure Portal의 [Azure Digital Twins 페이지로](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) 이동 합니다. 포털 검색 창에서 *Azure Digital Twins* 를 검색 하 여이 페이지로 직접 이동할 수 있습니다.

이 페이지에는 모든 Azure Digital Twins 인스턴스가 나열 됩니다. 목록에서 새로 만든 인스턴스의 이름을 찾습니다.

확인에 실패 한 경우 [포털](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) 또는 [CLI](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)를 사용 하 여 인스턴스 만들기를 다시 시도할 수 있습니다.

### <a name="collect-instance-values"></a>인스턴스 값 수집

[Azure Digital Twins 페이지](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) 에서 인스턴스의 이름을 선택 하 여 인스턴스의 *개요* 페이지를 엽니다. *이름*, *리소스 그룹*및 *호스트 이름을*확인 합니다. 나중에 인스턴스를 식별 하 고 연결 하는 데 필요할 수 있습니다.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="PowerShell 버전 선택을 보여 주는 Cloud Shell 창&quot;:::

1. &quot;파일 업로드/다운로드":::

### <a name="verify-user-role-assignment"></a>사용자 역할 할당 확인

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> 스크립트는 현재이 필수 역할을 Cloud Shell 스크립트를 실행 하는 사용자와 동일한 사용자에 게 할당 합니다. 인스턴스를 관리 하는 다른 사람에 게이 역할을 할당 해야 하는 경우 Azure Portal ([지침](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) 또는 CLI ([지침](how-to-set-up-instance-cli.md#set-up-user-access-permissions))를 통해이 역할을 수행할 수 있습니다.

확인에 실패 한 경우 [포털](how-to-set-up-instance-portal.md#set-up-user-access-permissions) 또는 [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)를 사용 하 여 자체 역할 할당을 다시 실행할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins CLI 명령을 사용 하 여 인스턴스에서 개별 REST API 호출을 테스트 합니다. 
* [az dt reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*방법: Azure Digital Twins CLI 사용*](how-to-use-cli.md)

또는 인증 코드를 사용 하 여 인스턴스에 클라이언트 응용 프로그램을 연결 하는 방법을 참조 하세요.
* [*방법: 앱 인증 코드 작성*](how-to-authenticate-client.md)