---
title: 빠른 시작 - Azure Communication Services에서 리소스 만들기 및 관리
titleSuffix: An Azure Communication Services quickstart
description: 이 빠른 시작에서는 첫 번째 Azure Communication Services 리소스를 만들고 관리하는 방법에 대해 알아봅니다.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-azp-net
ms.openlocfilehash: 3ba3d8d4b345c9db2e41c647147021eb9616288e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945941"
---
# <a name="quickstart-create-and-manage-communication-services-resources"></a>빠른 시작: Communication Services 리소스 만들기 및 관리

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

첫 번째 Communication Services 리소스를 프로비저닝하여 Azure Communication Services를 시작합니다. Communication Services 리소스는 Azure Portal 또는 .NET 관리 클라이언트 라이브러리를 통해 프로비저닝될 수 있습니다. 관리 클라이언트 라이브러리를 사용하면 리소스를 만들고, 구성, 업데이트 및 삭제하고 Azure의 배포 및 관리 서비스인 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)와 상호 연결할 수 있습니다. 클라이언트 라이브러리에서 사용할 수 있는 모든 기능은 Azure Portal에서 사용할 수 있습니다. 

> [!WARNING]
> 공개 미리 보기 동안에는 Communication Services 사용이 미국 내로 제한됩니다. 또한 공개 미리 보기 동안에는 통신 리소스를 다른 구독으로 이전할 수 없습니다.

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/create-resource-azp.md)]
::: zone-end

::: zone pivot="platform-net"
[!INCLUDE [.NET](./includes/create-resource-net.md)]
::: zone-end

## <a name="access-your-connection-strings-and-service-endpoints"></a>연결 문자열 및 서비스 엔드포인트 액세스

연결 문자열을 통해 Communication Services 클라이언트 라이브러리를 Azure에 연결하고 인증할 수 있습니다. Azure Portal에서 또는 Azure Resource Manager API를 사용하여 프로그래밍 방식으로 Communication Services 연결 문자열 및 서비스 엔드포인트에 액세스할 수 있습니다. 

Communication Services 리소스로 이동한 후 탐색 메뉴에서 **키**를 선택하고 Communication Services 클라이언트 라이브러리의 사용에 대한 **연결 문자열** 또는 **엔드포인트** 값을 복사합니다. 기본 키와 보조 키에 대한 액세스 권한이 있는지 확인합니다. 이는 타사 또는 스테이징 환경에 Communication Services 리소스에 대한 임시 액세스를 제공하려는 시나리오에서 유용할 수 있습니다.

:::image type="content" source="./media/key.png" alt-text="Communication Services 키 페이지의 스크린샷.":::

## <a name="store-your-connection-string"></a>연결 문자열 저장

Communication Services 클라이언트 라이브러리는 연결 문자열을 사용하여 Communication Services에 대한 요청에 권한을 부여합니다. 연결 문자열을 저장하기 위한 여러 가지 옵션이 있습니다.

* 데스크톱 또는 디바이스에서 실행 중인 애플리케이션의 경우 연결 문자열을 **app.config** 또는 **web.config** 파일에 저장할 수 있습니다. 이러한 파일의 **AppSettings** 섹션에 연결 문자열을 추가합니다.
* Azure App Service에서 실행 중인 애플리케이션의 경우, 연결 문자열을 [Azure Service 애플리케이션 설정](https://docs.microsoft.com/azure/app-service/configure-common)에 저장할 수 있습니다. 포털 내 애플리케이션 설정 탭의 **연결 문자열** 섹션에 연결 문자열을 추가합니다.
* [Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)에 연결 문자열을 저장할 수 있습니다.
* 애플리케이션을 로컬로 실행하는 경우 환경 변수에 연결 문자열을 저장하는 것이 좋습니다.

### <a name="store-your-connection-string-in-an-environment-variable"></a>환경 변수에 연결 문자열 저장

환경 변수를 구성하려면 콘솔 창을 열고 아래 탭에서 운영 체제를 선택합니다. `<yourconnectionstring>`을 실제 연결 문자열로 바꿉니다.

#### <a name="windows"></a>[Windows](#tab/windows)

콘솔 창을 열고 다음 명령을 입력합니다.

```console
setx COMMUNICATION_SERVICES_CONNECTION_STRING "<yourconnectionstring>"
```

환경 변수를 추가한 후에는 콘솔 창을 포함하여 실행 중인 프로그램 중에서 환경 변수를 읽어야 하는 프로그램을 다시 시작해야 할 수도 있습니다. 예를 들어 편집기로 Visual Studio를 사용하는 경우 Visual Studio를 다시 시작한 후 예제를 실행합니다.

#### <a name="macos"></a>[macOS](#tab/unix)

**.zshrc**를 편집하고 환경 변수를 추가합니다.

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

환경 변수를 추가한 후에는 콘솔 창에서 `source ~/.zshrc` 명령을 실행하여 변경 내용을 적용합니다. IDE가 열린 상태에서 환경 변수를 만든 경우 해당 변수에 액세스하려면 편집기, IDE 또는 셸을 닫고 다시 열어야 합니다.

#### <a name="linux"></a>[Linux](#tab/linux)

**.bash_profile**을 편집하고, 환경 변수를 추가합니다.

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

환경 변수를 추가한 후에는 콘솔 창에서 `source ~/.bash_profile` 명령을 실행하여 변경 내용을 적용합니다. IDE가 열린 상태에서 환경 변수를 만든 경우 해당 변수에 액세스하려면 편집기, IDE 또는 셸을 닫고 다시 열어야 합니다.

---

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

리소스를 삭제하는 동안 리소스에 할당된 전화 번호가 있으면 리소스에서 전화 번호는 동시에 자동으로 해제됩니다. 

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음을 수행하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * Communication Services 리소스 만들기
> * 리소스 지리 및 태그 구성
> * 해당 리소스에 대한 키 액세스
> * 리소스 삭제

> [!div class="nextstepaction"]
> [첫 번째 사용자 액세스 토큰 만들기](access-tokens.md)
