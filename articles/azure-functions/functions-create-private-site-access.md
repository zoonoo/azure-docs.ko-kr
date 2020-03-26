---
title: Azure Functions에 대한 프라이빗 사이트 액세스 사용
description: Azure Functions에 대한 Azure 가상 네트워크 프라이빗 사이트 액세스를 설정하는 방법을 알아봅니다.
author: mcollier
ms.author: mcollier
ms.service: azure-functions
ms.topic: tutorial
ms.date: 02/15/2020
ms.openlocfilehash: ada08de182791c6ecb2b83ef3b924bf40975e1ee
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78852018"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>자습서: Azure Functions 프라이빗 사이트 액세스 설정

이 자습서에서는 Azure Functions를 사용하여 [프라이빗 사이트 액세스](./functions-networking-options.md#private-site-access)를 설정하는 방법을 보여 줍니다. 프라이빗 사이트 액세스를 사용하면 함수 코드가 특정 가상 네트워크에서만 트리거되도록 요구할 수 있습니다.

프라이빗 사이트 액세스는 함수 앱에 대한 액세스를 특정 가상 네트워크로 제한해야 하는 시나리오에서 유용합니다. 예를 들어 함수 앱은 특정 조직의 직원 또는 지정된 가상 네트워크 내에 있는 서비스(예: 다른 Azure Function, Azure Virtual Machine 또는 AKS 클러스터)에만 적용할 수 있습니다.

함수 앱에서 가상 네트워크 내의 Azure 리소스에 액세스하거나 [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 통해 연결해야 하는 경우 [ 가상 네트워크 통합](./functions-create-vnet.md)이 필요합니다.

이 자습서에서는 함수 앱에 대한 프라이빗 사이트 액세스를 구성하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 가상 머신 만들기
> * Azure Bastion 서비스 만들기
> * Azure Functions 앱 만들기
> * 가상 네트워크 서비스 엔드포인트 구성
> * Azure Function 앱 만들기 및 배포
> * 가상 네트워크 외부 및 내부에서 함수 호출

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="topology"></a>토폴로지

다음 다이어그램에서는 만들 솔루션의 아키텍처를 보여 줍니다.

![프라이빗 사이트 액세스 솔루션에 대한 개략적인 아키텍처 다이어그램](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 IP 주소 지정 및 서브넷 구성을 이해하는 것이 중요합니다. [주소 지정 및 서브넷 구성의 기본 사항을 설명하는 이 문서](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)로 시작할 수 있습니다. 온라인에서 더 많은 문서와 비디오를 사용할 수 있습니다.

## <a name="sign-in-to-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

이 자습서의 첫 번째 단계는 새 가상 머신을 가상 네트워크 내에 만드는 것입니다.  가상 네트워크 내에서만 사용할 수 있도록 가상 머신의 액세스를 제한하는 경우 가상 머신은 함수에 액세스하는 데 사용됩니다.

1. **리소스 만들기** 단추를 선택합니다.

2. 검색 필드에서 `Windows Server`를 입력하고, 검색 결과에서 **Windows Server**를 선택합니다.

3. Windows Server 옵션 목록에서 **Windows Server 2019 Datacenter**를 선택하고, **만들기** 단추를 누릅니다.

4. **기본 사항** 탭에서 VM 설정을 이미지 아래의 표에서 지정한 대로 사용합니다.

    >[!div class="mx-imgBorder"]
    >![새 Windows VM에 대한 기본 사항 탭](./media/functions-create-private-site-access/create-vm-3.png)

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. |
    | [**리소스 그룹**](../azure-resource-manager/management/overview.md) | myResourceGroup | 이 자습서의 모든 리소스를 포함할 리소스 그룹을 선택합니다.  동일한 리소스 그룹을 사용하면 이 자습서를 완료할 때 리소스를 더 쉽게 정리할 수 있습니다. |
    | **가상 머신 이름** | myVM | VM 이름은 리소스 그룹에서 고유해야 합니다. |
    | [**지역**](https://azure.microsoft.com/regions/) | (미국) 미국 중북부 | 사용자 또는 액세스할 함수 근처의 지역을 선택합니다. |
    | **퍼블릭 인바운드 포트** | None | 인터넷에서 VM으로의 인바운드 연결을 사용하지 못하도록 하려면 **없음**을 선택합니다. VM에 대한 원격 액세스는 Azure Bastion 서비스를 통해 구성됩니다. |

5. **네트워킹** 탭을 선택하고, **새로 만들기**를 선택하여 새 가상 네트워크를 구성합니다.

    >[!div class="mx-imgBorder"]
    >![새 VM에 대한 새 가상 네트워크 만들기](./media/functions-create-private-site-access/create-vm-networking.png)

6. **가상 네트워크 만들기**에서 이미지 아래의 표에서 설명하는 설정을 사용합니다.

    >[!div class="mx-imgBorder"]
    >![새 VM에 대한 새 가상 네트워크 만들기](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **이름** | myResourceGroup-vnet | 가상 네트워크에 대해 생성된 기본 이름을 사용할 수 있습니다. |
    | **주소 범위** | 10.10.0.0/16 | 가상 네트워크에 단일 주소 범위를 사용합니다. |
    | **서브넷 이름** | 자습서 | 서브넷 이름입니다. |
    | **주소 범위**(서브넷) | 10.10.1.0/24 | 서브넷 크기는 서브넷에 추가할 수 있는 인터페이스 수를 정의합니다. 이 서브넷은 VM에서 사용합니다. `/24` 서브넷은 254개의 호스트 주소를 제공합니다. |

7. **확인**을 선택하여 가상 네트워크를 만듭니다.
8. **네트워킹** 탭으로 돌아가서 **공용 IP**에 대해 **없음**이 선택되어 있는지 확인합니다.
9. **관리** 탭을 선택한 다음, **진단 스토리지 계정**에서 **새로 만들기**를 선택하여 새 스토리지 계정을 만듭니다.
10. **ID**, **자동 종료** 및 **백업** 섹션의 기본값을 그대로 둡니다.
11. **검토 + 만들기**를 선택합니다. 유효성 검사가 완료되면 **만들기**를 선택합니다. VM 만들기 프로세스는 몇 분 정도 걸립니다.

## <a name="configure-azure-bastion"></a>Azure Bastion 구성

[Azure Bastion](https://azure.microsoft.com/services/azure-bastion/)은 Azure Portal에서 직접 가상 머신에 대한 보안 RDP 및 SSH 액세스를 제공하는 완전 관리형 Azure 서비스입니다. Azure Bastion 서비스를 사용하면 RDP 액세스와 관련된 네트워크 설정을 구성할 필요가 없습니다.

1. 포털에서 리소스 그룹 보기의 위쪽에 있는 **추가**를 선택합니다.
2. 검색 필드에서 "Bastion"을 입력합니다.  "Bastion"을 선택합니다.
3. **만들기**를 선택하여 새 Azure Bastion 리소스 만들기 프로세스를 시작합니다. 아직 `AzureBastionSubnet` 서브넷이 없으므로 **가상 네트워크** 섹션에 오류 메시지가 표시됩니다. 서브넷은 다음 단계에서 만들어집니다. 이미지 아래의 표에서 설명하는 설정을 사용합니다.

    >[!div class="mx-imgBorder"]
    >![Azure Bastion 만들기 시작](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **이름** | myBastion | 새 Bastion 리소스의 이름입니다. |
    | **지역** | 미국 중북부 | 사용자 근처 또는 함수가 액세스할 기타 서비스에 가까운 [지역](https://azure.microsoft.com/regions/)을 선택합니다. |
    | **가상 네트워크** | myResourceGroup-vnet | Bastion 리소스를 만들 가상 네트워크입니다. |
    | **서브넷** | AzureBastionSubnet | 새 Bastion 호스트 리소스가 배포될 가상 네트워크의 서브넷입니다. `AzureBastionSubnet` 이름 값을 사용하여 서브넷을 만들어야 합니다. 이 값을 통해 Azure에서 Bastion 리소스를 배포할 서브넷을 인식할 수 있습니다. `/27` 이하의 서브넷(`/27`, `/26` 등)을 사용해야 합니다. |

    > [!NOTE]
    > Azure Bastion 리소스를 만드는 방법에 대한 자세한 단계별 가이드는 [Azure Bastion 호스트 만들기](../bastion/bastion-create-host-portal.md) 자습서를 참조하세요.

4. Azure에서 Azure Bastion 호스트를 프로비저닝할 수 있는 서브넷을 만듭니다. **서브넷 구성 관리**를 선택하면 새 서브넷을 정의할 수 있는 새 창이 열립니다.  **+ 서브넷**을 선택하여 새 서브넷을 만듭니다.
5. 서브넷 이름은 `AzureBastionSubnet`이고, 서브넷 접두사는 `/27` 이상이어야 합니다.  **확인**을 선택하여 서브넷을 만듭니다.

    >[!div class="mx-imgBorder"]
    >![Azure Bastion 호스트에 대한 서브넷 만들기](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

6. **Bastion 만들기** 페이지의 사용 가능한 서브넷 목록에서 새로 만든 `AzureBastionSubnet`을 선택합니다.

    >[!div class="mx-imgBorder"]
    >![특정 서브넷을 사용하여 Azure Bastion 호스트 만들기](./media/functions-create-private-site-access/create-bastion-basics-2.png)

7. **검토 및 만들기**를 선택합니다. 유효성 검사가 완료되면 **만들기**를 선택합니다. Azure Bastion 리소스를 만드는 데 몇 분 정도 걸립니다.

## <a name="create-an-azure-functions-app"></a>Azure Functions 앱 만들기

다음 단계는 [사용 계획](functions-scale.md#consumption-plan)을 사용하여 Azure에서 함수 앱을 만드는 것입니다. 자습서의 뒷부분에서 함수 코드를 이 리소스에 배포합니다.

1. 포털에서 리소스 그룹 보기의 위쪽에 있는 **추가**를 선택합니다.
2. **컴퓨팅 > 함수 앱**을 차례로 선택합니다.
3. **기본 사항** 섹션에서 함수 앱 설정을 아래 표에서 지정한 대로 사용합니다.

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **리소스 그룹** | myResourceGroup | 이 자습서의 모든 리소스를 포함할 리소스 그룹을 선택합니다.  동일한 리소스 그룹을 함수 앱 및 VM에 사용하면 이 자습서를 완료할 때 리소스를 더 쉽게 정리할 수 있습니다. |
    | **함수 앱 이름** | 전역적으로 고유한 이름 | 새 함수 앱을 식별하는 이름입니다. 유효한 문자는 a-z(대/소문자 구분 안 함), 0-9 및 -입니다. |
    | **게시** | 코드 | 코드 파일 또는 Docker 컨테이너를 게시하는 옵션입니다. |
    | **런타임 스택** | 기본 설정 언어 | 즐겨찾는 함수 프로그래밍 언어를 지원하는 런타임을 선택합니다. |
    | **지역** | 미국 중북부 | 사용자 근처 또는 함수가 액세스할 기타 서비스에 가까운 [지역](https://azure.microsoft.com/regions/)을 선택합니다. |

    페이지 맨 아래에서 **다음: 호스팅 >** 단추를 선택합니다.
4. **호스팅** 섹션에서 **스토리지 계정**, **운영 체제**  및 **계획**을 다음 표에서 설명한 대로 적절하게 선택합니다.

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **스토리지 계정** | 전역적으로 고유한 이름 | 함수 앱에서 사용하는 스토리지 계정을 만듭니다. Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다. 기존 계정을 사용할 수도 있습니다. 여기서는 [스토리지 계정 요구 사항](./functions-scale.md#storage-account-requirements)을 충족해야 합니다. |
    | **운영 체제** | 기본 설정 운영 체제 | 운영 체제는 런타임 스택 선택에 따라 미리 선택되지만 필요한 경우 설정을 변경할 수 있습니다. |
    | **계획** | Consumption | [호스팅 계획](./functions-scale.md)은 함수 앱의 크기를 조정하는 방법과 각 인스턴스에서 사용할 수 있는 리소스를 지정합니다. |
5. **검토 + 만들기**를 선택하여 앱 구성 선택을 검토합니다.
6. **만들기**를 선택하여 함수 앱을 프로비전하고 배포합니다.

## <a name="configure-access-restrictions"></a>액세스 제한 구성

다음 단계는 가상 네트워크의 리소스만 이 함수를 호출할 수 있도록 [액세스 제한](../app-service/app-service-ip-restrictions.md)을 구성하는 것입니다.

함수 앱과 지정된 가상 네트워크 간에 Azure Virtual Network [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 만들어 [프라이빗 사이트](functions-networking-options.md#private-site-access) 액세스를 사용하도록 설정합니다. 액세스 제한은 서비스 엔드포인트를 통해 구현됩니다. 서비스 엔드포인트는 지정된 가상 네트워크 내에서 발생하는 트래픽만 지정된 리소스에 액세스할 수 있도록 보장합니다. 이 경우 지정된 리소스는 Azure Function입니다.

1. 함수 앱 내에서 **플랫폼 기능** 탭으로 이동합니다. *네트워킹* 섹션 헤더 아래에서 **네트워킹** 링크를 선택하여 [네트워크 기능 상태] 섹션을 엽니다.
2. **네트워크 기능 상태** 페이지는 Azure Front Door, Azure CDN 및 액세스 제한을 구성하기 위한 시작 지점입니다. **액세스 제한 구성**을 선택하여 프라이빗 사이트 액세스를 구성합니다.
3. **액세스 제한** 페이지에서 기본 제한만 표시됩니다. 기본값은 함수 앱에 대한 액세스를 제한하지 않습니다.  **규칙 추가**를 선택하여 프라이빗 사이트 액세스 제한 구성을 만듭니다.
4. **액세스 제한 추가** 창의 **형식** 드롭다운 상자에서 **Virtual Network**를 선택한 다음, 이전에 만든 가상 네트워크와 서브넷을 선택합니다.
5. **액세스 제한** 페이지에서 이제 새 제한이 있음을 보여 줍니다. **엔드포인트 상태**가 `Disabled`, `Provisioning`, `Enabled`로 차례로 변경되는 데 몇 초 정도 걸릴 수 있습니다.

    >[!IMPORTANT]
    > 각 함수 앱에는 함수 앱 배포를 관리하는 데 사용되는 [고급 도구(Kudu) 사이트](../app-service/app-service-ip-restrictions.md#scm-site)가 있습니다. 이 사이트는 `<FUNCTION_APP_NAME>.scm.azurewebsites.net`과 같은 URL에서 액세스합니다. 이 배포 사이트에서 액세스 제한이 사용되도록 설정되지 않았으므로 가상 네트워크 내에서 에이전트를 프로비저닝할 필요 없이 로컬 개발자 워크스테이션 또는 빌드 서비스에서 프로젝트 코드를 배포할 수 있습니다.

## <a name="access-the-functions-app"></a>함수 앱에 액세스

1. 이전에 만든 함수 앱으로 돌아갑니다.  **개요** 섹션에서 URL을 복사합니다.

    >[!div class="mx-imgBorder"]
    >![함수 앱 URL 가져오기](./media/functions-create-private-site-access/access-function-overview.png)

2. 지금 가상 네트워크 외부의 컴퓨터에서 함수 앱에 액세스하려고 하면 해당 앱이 중지되었음을 나타내는 HTTP 403 페이지가 표시됩니다.  앱이 중지되지 않습니다. 응답은 실제로 HTTP 403 IP 사용 권한 없음 상태입니다.
3. 이제 가상 네트워크에 연결된 이전에 만든 가상 머신에서 함수에 액세스할 수 있습니다. VM에서 사이트에 액세스하려면 Azure Bastion 서비스를 통해 VM에 연결해야 합니다.  먼저 **연결**을 선택한 다음, **Bastion**을 선택합니다.
4. 가상 머신에 로그인하는 데 필요한 사용자 이름과 암호를 제공합니다.  **연결**을 선택합니다. 가상 머신과 상호 작용할 수 있는 새 브라우저 창이 표시됩니다.
5. 이 VM은 가상 네트워크를 통해 함수에 액세스하므로 VM의 웹 브라우저에서 사이트에 액세스할 수 있습니다.  함수 앱은 지정된 가상 네트워크 내에서만 액세스할 수 있지만 공용 DNS 항목은 유지됩니다. 위와 같이 사이트에 액세스하려고 하면 HTTP 403 응답이 발생합니다.

## <a name="create-a-function"></a>함수 만들기

이 자습서의 다음 단계는 HTTP로 트리거되는 Azure Function을 만드는 것입니다. HTTP GET 또는 POST를 통해 함수를 호출하면 "Hello, {name}"이라는 응답이 발생합니다.  

1. 다음 빠른 시작 중 하나를 수행하여 Azure Functions 앱을 만들고 배포합니다.

    * [Visual Studio Code](./functions-create-first-function-vs-code.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [명령줄](./functions-create-first-azure-function-azure-cli.md)
    * [Maven(Java)](./functions-create-first-java-maven.md)

2. Azure Functions 프로젝트를 게시하는 경우 이 자습서의 앞부분에서 만든 함수 앱 리소스를 선택합니다.
3. 함수가 배포되었는지 확인합니다.

    >[!div class="mx-imgBorder"]
    >![함수 목록에 배포된 함수](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>직접 함수 호출

1. 함수에 대한 액세스를 테스트하려면 함수 URL을 복사해야 합니다. 배포된 함수를 선택한 다음, **</> 함수 URL 가져오기**를 선택합니다. 그런 다음, **복사** 단추를 클릭하여 URL을 클립보드에 복사합니다.

    >[!div class="mx-imgBorder"]
    >![함수 URL 복사](./media/functions-create-private-site-access/get-function-url.png)

    > [!NOTE]
    > 함수가 실행되면 포털에서 함수 런타임을 시작할 수 없다는 런타임 오류가 표시됩니다. 메시지 텍스트와 관계없이 함수 앱은 실제로 실행되고 있습니다. 이 오류는 새 액세스 제한으로 인해 포털에서 런타임을 확인하기 위해 쿼리할 수 없도록 합니다.

2. URL을 웹 브라우저에 붙여넣습니다. 지금 가상 네트워크 외부의 컴퓨터에서 함수 앱에 액세스하려고 하면 앱이 중지되었음을 나타내는 HTTP 403 응답을 받습니다.

## <a name="invoke-the-function-from-the-virtual-network"></a>가상 네트워크에서 함수 호출

가상 네트워크의 구성된 VM에서 웹 브라우저를 통해 함수에 액세스하면(Azure Bastion 서비스 사용) 성공합니다!

>[!div class="mx-imgBorder"]
>![Azure Bastion을 통해 Azure Function에 액세스](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>다음 단계


> [!div class="nextstepaction"]
> [Functions 네트워킹 옵션에 대한 자세한 정보](./functions-networking-options.md)
