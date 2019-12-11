---
title: Azure Functions 바인딩 확장을 수동으로 설치 또는 업데이트
description: 배포 된 함수 앱에 대 한 Azure Functions 바인딩 확장을 설치 하거나 업데이트 하는 방법에 대해 알아봅니다.
ms.topic: reference
ms.date: 09/26/2018
ms.openlocfilehash: 83ae2cdd16bce0b0a5b11b8c24e996306453a7a9
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977491"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>포털에서 Azure Functions 바인딩 확장을 수동으로 설치 또는 업데이트

버전 2.x부터 Azure Functions 런타임은 바인딩 확장을 사용 하 여 트리거 및 바인딩에 대 한 코드를 구현 합니다. 바인딩 확장은 NuGet 패키지에 제공 됩니다. 확장을 등록 하려면 기본적으로 패키지를 설치 합니다. 함수를 개발할 때 바인딩 확장을 설치 하는 방법은 개발 환경에 따라 달라 집니다. 자세한 내용은 트리거 및 바인딩 문서에서 [바인딩 확장 등록](./functions-bindings-register.md) 을 참조 하세요.

Azure Portal에서 바인딩 확장을 수동으로 설치 하거나 업데이트 해야 하는 경우도 있습니다. 예를 들어 등록 된 바인딩을 최신 버전으로 업데이트 해야 할 수 있습니다. 포털의 **통합** 탭에서 설치할 수 없는 지원 되는 바인딩을 등록 해야 할 수도 있습니다.

## <a name="install-a-binding-extension"></a>바인딩 확장 설치

포털에서 수동으로 확장을 설치 하거나 업데이트 하려면 다음 단계를 따르십시오.

1. [Azure Portal](https://portal.azure.com)에서 함수 앱을 찾아 선택 합니다. **개요** 탭을 선택 하 고 **중지**를 선택 합니다.  함수 앱을 중지 하면 변경 내용이 적용 될 수 있도록 파일의 잠금이 해제 됩니다.

1. **플랫폼 기능** 탭을 선택 하 고 **개발 도구** 에서 **고급 도구 (Kudu)** 를 선택 합니다. Kudu 끝점 (`https://<APP_NAME>.scm.azurewebsites.net/`)이 새 창에서 열립니다.

1. Kudu 창에서 **디버그 콘솔** > **CMD**를 선택 합니다.  

1. 명령 창에서 `D:\home\site\wwwroot`로 이동 하 고 `bin` 옆에 있는 삭제 아이콘을 선택 하 여 폴더를 삭제 합니다. **확인** 을 선택 하 여 삭제를 확인 합니다.

1. 함수 앱에 대 한 바인딩 확장을 정의 하는 `extensions.csproj` 파일 옆의 편집 아이콘을 선택 합니다. 프로젝트 파일이 온라인 편집기에서 열립니다.

1. **ItemGroup**에서 필요한 **PackageReference** 항목을 추가 하 고 업데이트 한 다음 **저장**을 선택 합니다. 지원 되는 패키지 버전의 현재 목록은 [필요한 패키지는 무엇 인가요?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) wiki 문서에서 찾을 수 있습니다. 세 가지 Azure Storage 바인딩 모두 WebJobs 패키지가 필요 합니다.

1. `wwwroot` 폴더에서 다음 명령을 실행 하 여 `bin` 폴더에서 참조 된 어셈블리를 다시 빌드합니다.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. 포털의 **개요** 탭으로 돌아가서 **시작** 을 선택 하 여 함수 앱을 다시 시작 합니다.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure 함수 트리거 및 바인딩에 대 한 자세한 정보](functions-triggers-bindings.md)
