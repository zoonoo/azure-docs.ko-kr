---
title: "FTP/S를 사용하여 Azure App Service에 앱 배포 | Microsoft Docs"
description: "FTP 또는 FTPS를 사용하여 Azure App Service에 앱을 배포하는 방법을 알아봅니다."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2016
ms.author: cephalin;dariac
ms.openlocfilehash: e3ac2f2156719ad975049b0c2b4cbca81d88e779
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>FTP/S를 사용하여 앱에 Azure App Service에 배포

이 문서는 FTP 또는 FTPS를 사용하여 웹앱, 모바일 앱 백 엔드 또는 API 앱을 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)에 배포하는 방법을 보여 줍니다.

앱에 대한 FTP/S 끝점은 이미 활성화되어 있습니다. FTP/S 배포를 사용하도록 설정하는 데 필요한 구성은 없습니다.

> [!IMPORTANT]
> Microsoft Azure 플랫폼 보안을 개선하기 위해 계속해서 작업하고 있습니다. 이러한 지속적인 노력의 일환으로 독일 중부 및 독일 북동부 하위 지역에 대해 웹 응용 프로그램 업그레이드가 예정되어 있습니다. 이 프로세스 동안 웹앱은 배포에 일반 텍스트 FTP 프로토콜이 사용되지 않도록 설정하게 됩니다. 고객은 배포를 위해 FTPS로 전환하는 것이 좋습니다. 9월 5일로 계획된 이 업그레이드 동안 서비스를 중단하지 않을 예정입니다. 여러분의 지원에 감사 드립니다.

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>1단계: 배포 자격 증명 설정

앱에 대한 FTP 서버에 액세스하려면 먼저 배포 자격 증명이 필요합니다. 

배포 자격 증명을 설정하거나 다시 설정하려면 [Azure App Service 배포 자격 증명](app-service-deployment-credentials.md)을 참조하세요. 이 자습서는 사용자 수준의 자격 증명 사용 방법을 보여 줍니다.

## <a name="step-2-get-ftp-connection-information"></a>2단계: FTP 연결 정보 가져오기

1. [Azure Portal](https://portal.azure.com)에서 앱의 [리소스 블레이드](../azure-resource-manager/resource-group-portal.md#manage-resources)를 엽니다.
2. 왼쪽 메뉴에서 **개요**를 선택하고 **P/배포 사용자**, **FTP 호스트 이름** 및 **FTPS 호스트 이름** 값을 적어둡니다. 

    ![FTP 연결 정보](./media/app-service-deploy-ftp/FTP-Connection-Info.PNG)

    > [!NOTE]
    > FTP 서버에 올바른 컨텍스트를 제공하기 위해 Azure 포털에 표시된 **FTP/배포 사용자** 사용자 값(앱 이름 포함)을 복사합니다.
    > 왼쪽 메뉴에서 **속성**을 선택하면 같은 정보를 찾을 수 있습니다. 
    >
    > 또한 배포 암호는 표시되지 않습니다. 배포 암호를 잊은 경우 [1단계](#step1)로 이동한 후 배포 암호를 다시 설정합니다.
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>3단계: Azure에 파일 배포

1. FTP 클라이언트([Visual Studio](https://www.visualstudio.com/vs/community/), [FileZilla](https://filezilla-project.org/download.php?type=client) 등)에서 수집한 연결 정보를 사용하여 앱에 연결합니다.
3. 파일 및 해당 디렉터리 구조를 Azure의 [**/site/wwwroot** 디렉터리](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)(또는 WebJobs의 경우 **/site/wwwroot/App_Data/Jobs/** 디렉터리)에 복사합니다.
4. 앱의 URL을 찾아 앱이 제대로 실행하는지 확인합니다. 

> [!NOTE] 
> [Git 기반 배포](app-service-deploy-local-git.md)와 달리, FTP 배포에서는 다음과 같은 배포 자동화를 지원하지 않습니다. 
>
> - 종속성 복원(예: NuGet, NPM, PIP 및 Composer Automation)
> - .NET 이진 파일 컴파일
> - web.config 생성([Node.js 예제](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps) 참조)
> 
> 로컬 컴퓨터에서 이러한 필요한 파일을 수동으로 복원, 빌드 및 생성한 후 앱과 함께 배포해야 합니다.
>
>

## <a name="next-steps"></a>다음 단계

고급 배포 시나리오에 대해서는 [Git를 사용하여 Azure에 배포](app-service-deploy-local-git.md)를 시도하세요. Azure로의 Git 기반 배포를 수행하면 버전 제어, 패키지 복원, MSBuild 등을 수행할 수 있습니다.

## <a name="more-resources"></a>추가 리소스

* [Azure App Service 배포 자격 증명](app-service-deploy-ftp.md)
