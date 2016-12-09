---
title: "Azure 앱 서비스 웹앱에서 io.js를 사용하는 방법"
description: "io.js와 함께 Azure 앱 서비스의 웹앱을 사용하는 방법에 대해 알아봅니다."
services: app-service\web
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: d6320725-ffcb-4ad7-ba63-fc72fa2f2808
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4b82981b69628cfd91136810bc801aaf8054c782


---
# <a name="how-to-use-iojs-with-azure-app-service-web-apps"></a>Azure 앱 서비스 웹앱에서 io.js를 사용하는 방법
일반적인 Node 포크인 [io.js] 는 보다 개방적인 거버넌스 모델, 보다 빠른 릴리스 주기, 새롭고 실험적인 JavaScript 기능의 보다 빠른 도입 등 Joyent의 Node.js 프로젝트에 다양한 차이점을 제공합니다.

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps에는 많은 Node.js 버전이 미리 설치되어 있지만 사용자가 제공한 Node.js 이진 파일도 허용합니다. 이 문서에서는 앱 서비스 웹앱에서 io.js를 사용하도록 설정하는 두 가지 방법을 설명합니다. 한 가지 방법은 최신 io.js 버전을 사용하도록 Azure를 자동으로 구성하는 확장 배포 스크립트를 사용하는 방법이고, 또 한 가지 방법은 io.js 이진 파일을 수동으로 업로드하는 방법입니다. 

<a id="deploymentscript"></a>

## <a name="using-a-deployment-script"></a>배포 스크립트 사용
Node.js 앱을 배포하면 앱 서비스 웹앱에서 몇 가지 간단한 명령을 실행하여 환경이 제대로 구성되었는지 확인합니다. 배포 스크립트를 사용하는 경우에는 io.js의 다운로드 및 구성을 포함하도록 이 프로세스를 사용자 지정할 수 있습니다.

[io.js 배포 스크립트](https://github.com/felixrieseberg/iojs-azure) 는 GitHub에서 사용할 수 있습니다. 웹앱에서 io.js를 사용하도록 설정하려면 **.deployment**, **deploy.cmd** 및 **IISNode.yml**을 응용 프로그램 폴더의 루트에 복사하고 Web Apps에 배포하기만 하면 됩니다.  

첫 번째 파일인 **.deployment**는 배포 시 **deploy.cmd**를 실행하도록 Web Apps에 지시합니다. 이 스크립트는 Node.js 응용 프로그램에 대한 모든 일반적인 단계를 실행할 뿐만 아니라 최신 버전의 io.js를 다운로드합니다. 마지막으로 **IISNode.yml** 은 미리 설치된 Node.js 이진 파일 대신 방금 다운로드한 io.js 이진 파일을 사용하도록 웹앱을 구성합니다.

> [!NOTE]
> 사용한 io.js 이진 파일을 업데이트하려면 응용 프로그램을 다시 배포하기만 하면 됩니다. 응용 프로그램이 배포될 때마다 스크립트가 새 버전의 io.js를 다운로드합니다.
> 
> 

<a id="manualinstallation"></a>

## <a name="using-manual-installation"></a>수동 설치 사용
사용자 지정 io.js 버전의 수동 설치는 두 단계만으로 구성됩니다. 첫째, **io.js distribution** 에서 직접 [win-x64]이진 파일을 다운로드합니다. **iojs.exe** 및 **iojs.lib** 파일이 필요합니다. 두 파일 모두 웹앱 내의 폴더(예: **bin/iojs**)에 저장합니다.

미리 설치된 Node 버전 대신 **iojs.exe**를 사용하도록 Web Apps를 구성하려면 응용 프로그램의 루트에 **IISNode.yml** 파일을 만들고 다음 줄을 추가합니다.

    nodeProcessCommandLine: "D:\home\site\wwwroot\bin\iojs\iojs.exe"

<a id="nextsteps"></a>

## <a name="next-steps"></a>다음 단계
이 문서에서는 제공된 배포 스크립트와 수동 설치를 모두 사용하여 앱 서비스 웹앱에서 io.js를 사용하는 방법에 대해 알아보았습니다. 

> [!NOTE]
> io.js는 집중적으로 개발되고 있으며, Node.js보다 자주 업데이트됩니다. 여러 Node.js 모듈이 io.js와 함께 작동하지 않을 수 있으므로 문제 해결은 [GitHub의 io.js] 를 참조하세요.
> 
> 

## <a name="whats-changed"></a>변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure App Service를 시작하려면 [App Service 체험](http://go.microsoft.com/fwlink/?LinkId=523751)으로 이동합니다. App Service에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 

[io.js]: https://iojs.org
[win-x64]: https://iojs.org/dist/
[GitHub의 io.js]: https://github.com/iojs/io.js
[io.js 배포 스크립트]: https://github.com/felixrieseberg/iojs-azure



<!--HONumber=Nov16_HO3-->


