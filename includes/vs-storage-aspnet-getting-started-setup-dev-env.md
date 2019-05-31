---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a7c696870e22e1692ca5ed778e47f8e4cc00615a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160718"
---
## <a name="set-up-the-development-environment"></a>개발 환경 설정

이 섹션에서는 개발 환경을 설정하는 과정을 안내합니다. 여기에는 ASP.NET MVC 앱 만들기, 연결된 서비스 연결 추가, 컨트롤러 추가 및 필수 네임스페이스 지시문 지정이 포함됩니다.

### <a name="create-an-aspnet-mvc-app-project"></a>ASP.NET MVC 앱 프로젝트 만들기

1. Visual Studio를 엽니다.

1. 주 메뉴에서 **파일** > **새로 만들기** > **프로젝트**를 선택합니다.

1. **새 프로젝트** 대화 상자에서 **웹** > **ASP.NET 웹 애플리케이션(.NET Framework)** 을 선택합니다. **이름** 필드에서 **StorageAspNet**을 지정합니다. **확인**을 선택합니다.

    ![새 프로젝트 대화 상자 스크린샷](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. **새 ASP.NET 웹 애플리케이션** 대화 상자에서 **MVC**를 선택한 후 **확인**을 선택합니다.

    ![새 ASP.NET 웹 애플리케이션 대화 상자 스크린샷](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>연결된 서비스를 사용하여 Azure Storage 계정에 연결

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.

1. 상황에 맞는 메뉴에서 **추가** > **연결된 서비스**를 선택합니다.

1. **연결된 서비스** 대화 상자에서 **Azure Storage의 클라우드 스토리지**를 선택합니다.

    ![연결된 서비스 대화 상자 스크린샷](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. **Azure Storage** 대화 상자에서 이 자습서에 사용할 Azure Storage 계정을 선택합니다. 새 Azure Storage 계정을 만들려면 **새 스토리지 계정 만들기**를 선택하고 양식을 완성합니다. 기존 저장소 계정을 선택하거나 새 저장소 계정을 만든 후에 **추가**를 선택합니다. Visual Studio에서는 Azure Storage용 NuGet 패키지 및 스토리지 연결 문자열을 **Web.config**에 설치합니다.

1. **솔루션 탐색기**를 마우스 오른쪽 단추로 클릭 **종속성**, 선택 **NuGet 패키지 관리**, 최신 버전의 NuGet 패키지 참조를 추가 하 고 Microsoft.Azure.ConfigurationManager 합니다.

> [!TIP]
> [Azure Portal](https://portal.azure.com)에서 저장소 계정을 만드는 방법을 알아보려면 [저장소 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)를 참조하세요.
>
> [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure CLI](../articles/storage/common/storage-azure-cli.md) 또는 [Azure Cloud Shell](../articles/cloud-shell/overview.md)을 사용하여 저장소 계정을 만들 수도 있습니다.
