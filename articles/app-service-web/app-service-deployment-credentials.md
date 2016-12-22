---
title: "Azure App Service 배포 자격 증명 | Microsoft Docs"
description: "Azure App Service 배포 자격 증명 사용 방법을 알아봅니다."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: wpickett
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/21/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 73753bb96d2c9680ed5d5bdf5eb3780e371c08d3
ms.openlocfilehash: 53f971624c2c7a7f64eed257aeb0c402461cc7ba


---
# <a name="azure-app-service-deployment-credentials"></a>Azure App Service 배포 자격 증명
[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 플랫폼은 콘텐츠 배포를 위해 두 가지 유형의 자격 증명을 지원합니다.
* 배포 자격 증명: 사용자 범위 자격 증명
* 게시 프로필: 앱 범위 자격 증명 

## <a name="a-nameuserscopeauser-scoped-credentials"></a><a name="userscope"></a>사용자 범위 자격 증명
사용자 범위 자격 증명은 Azure 사용자가 만들며 특정 App Service 앱이 아닌 Microsoft 계정에 직접 매핑됩니다. 사용자 범위 배포 자격 증명은 각 App Service 앱에 편집 시작 지점이 있는 [Azure Portal](https://portal.azure.com)의 **앱 배포 > 배포 자격 증명**에서 설정하거나 다시 설정할 수 있습니다. 시작 지점에 관계 없이 이러한 사용자 범위 자격 증명에 대한 편집 내용은 전체 Microsoft 계정에 적용됩니다. 이러한 자격 증명은 FTP 및 Git 배포에 자주 사용됩니다.

![Azure App Service 배포 자격 증명](./media/app-service-deployment-credentials/deployment_credentials.png)
 
RBAC(역할 기반 액세스 제어) 또는 공동 관리자 권한을 통해 Azure 리소스에 대한 액세스 권한을 위임하는 경우 액세스 권한을 수신하는 각 Azure 사용자는 액세스 권한이 취소될 때까지 자신의 사용자 범위 자격 증명에 사용할 수 있습니다. 이러한 배포 자격 증명은 다른 Azure 사용자와 공유하지 말아야 합니다.

## <a name="a-nameappscopeaapp-scoped-credentials"></a><a name="appscope"></a>앱 범위 자격 증명
앱 범위 자격 증명은 App Service 플랫폼에서 자동으로 만들어집니다. 각 App Service 앱에 대한 XML 게시 프로필에 저장됩니다. 게시 프로필은 앱의 **개요** 블레이드에 있는 **게시 프로필 가져오기** 작업을 통해 [Azure Portal](https://portal.azure.com)에서 사용할 수 있습니다. 이러한 자격 증명은 WebDeploy 기반 배포에 자주 사용됩니다. FTP 또는 Git 배포에 사용할 수도 있습니다. WebDeploy 기반 배포에 대한 진입점에 해당하는 Visual Studio는 인증을 위해 게시 프로필을 구문 분석할 수 있습니다.

![Azure App Service 게시 프로필](./media/app-service-deployment-credentials/publish_profile.png)

RBAC(역할 기반 액세스 제어) 또는 공동 관리자 권한을 통해 Azure 리소스에 대한 액세스 권한을 위임하는 경우 액세스 권한을 수신하는 각 Azure 사용자는 동일한 앱 특정 게시 프로필을 다운로드할 수 있습니다. Azure Portal 앱 **개요** 블레이드에서 언제든지 게시 프로필을 다시 설정할 수 있습니다. 위임된 액세스 권한을 취소한 후에 앱 범위 자격 증명을 다시 설정하는 것이 좋습니다.



<!--HONumber=Nov16_HO4-->


