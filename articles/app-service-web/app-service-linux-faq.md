---
title: "Linux의 Azure App Service Web App에 대한 FAQ | Microsoft Docs"
description: "Linux의 Azure App Service Web App에 대한 FAQ"
keywords: "Azure App Service, 웹앱, faq, linux, oss"
services: app-service
documentationCenter: 
authors: ahmedelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: ff4f4ecd12bc26fcc44a20a193d73f952ed56f1a
ms.contentlocale: ko-kr
ms.lasthandoff: 08/07/2017

---

# <a name="azure-app-service-web-app-on-linux-faq"></a>Linux의 Azure App Service Web App에 대한 FAQ

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


Linux에 웹앱을 릴리스하면서 현재 플랫폼에 기능을 추가하고 플랫폼을 더욱 개선하기 위한 작업을 진행하고 있습니다. 다음은 최근 몇 달 동안 고객이 가장 자주 물어본 몇 가지 질문입니다.
질문이 있으면 문서에 남겨주세요. 최대한 신속하게 답변을 드리겠습니다.

## <a name="built-in-images"></a>기본 제공 이미지

**Q:** 플랫폼에서 제공하는 기본 제공 Docker 컨테이너를 분기하려고 합니다. 이러한 파일은 어디서 찾을 수 있나요?

**A:** [GitHub](https://github.com/azure-app-service)에서 모든 Docker 파일을 찾을 수 있습니다. [Docker Hub](https://hub.docker.com/u/appsvc/)에서 모든 Docker 컨테이너를 찾을 수 있습니다.

**Q:** 런타임 스택을 구성할 때 시작 파일 섹션에 대해 예상되는 값은 무엇인가요?

**A:** Node.Js의 경우 PM2 구성 파일 또는 스크립트 파일을 지정합니다. .Net Core의 경우 컴파일된 DLL 이름을 지정해야 합니다. Ruby의 경우 앱을 초기화하려면 Ruby 스크립트를 지정할 수 있습니다.

## <a name="management"></a>관리

**Q:** Azure Portal에서 다시 시작 단추를 누르면 어떻게 되나요?

**A:** Docker를 다시 시작하는 것과 같습니다.

**Q:** 앱 컨테이너 가상 컴퓨터(VM)에 연결하기 위해 Secure Shell(SSH)을 사용할 수 있나요?

**A:** 예, SCM 사이트를 통해 이 작업을 수행할 수 있습니다. 자세한 내용은 [Linux의 웹앱에 대한 SSH 지원](./app-service-linux-ssh-support.md) 문서를 참조하세요.

## <a name="continuous-integrationdeployment"></a>지속적인 통합/배포

**Q:** Docker Hub에서 이미지를 업데이트한 후에도 웹앱에서 여전히 기존 Docker 컨테이너 이미지를 사용합니다. 사용자 지정 컨테이너의 지속적인 통합/배포를 지원하나요?

**A:** Azure Container Registry 또는 DockerHub 이미지에 대한 지속적인 통합/배포를 설정하려면 [Linux에서 Azure 웹앱을 사용하여 연속 배포](./app-service-linux-ci-cd.md) 문서를 확인하세요. 개인 레지스트리의 경우 웹앱을 중지했다가 다시 시작하여 컨테이너를 새로 고칠 수 있습니다. 또는 컨테이너를 강제로 새로 고침하도록 더미 응용 프로그램을 변경 또는 추가할 수 있습니다.

**Q:** 스테이징 환경이 지원되나요?

**A:** 예.

**Q:** **웹 배포**를 사용하여 내 웹앱을 설정할 수 있나요?

**A:** 예. `WEBSITE_WEBDEPLOY_USE_SCM`이라는 앱 설정을 `false`로 설정해야 합니다.

## <a name="language-support"></a>언어 지원

**Q:** 컴파일되지 않은 .NET Core 앱을 지원하나요?

**A:** 예.

**Q:** 작성기를 PHP 앱의 종속성 관리자로 지원하나요?

**A:** 예. Git 배포 중에 Kudu는 사용자가 PHP 응용 프로그램을 배포하고 있음을 감지하고(composer.json 파일의 존재 덕분) 자동으로 작성기 설치를 트리거합니다.

## <a name="custom-containers"></a>사용자 지정 컨테이너

**Q:** 나만의 사용자 지정 컨테이너를 사용하고 있습니다. 앱이 `\home\` 디렉터리에 상주하는데 [SCM 사이트](https://github.com/projectkudu/kudu) 또는 FTP 클라이언트를 사용하여 콘텐츠를 찾아볼 때 파일을 찾을 수 없습니다. 내 파일은 어디에 있나요?

**A:** `\home\` 디렉터리에 SMB 공유를 탑재합니다. 포함된 모든 콘텐츠를 재정의합니다.

**Q:** 개인 레지스트리 서버 URL의 형식은 무엇인가요?

**A:** `http://` 또는 `https://`를 포함하여 전체 레지스트리 URL을 입력해야 합니다.

**Q:** 개인 레지스트리 옵션에서 이미지 이름의 형식은 무엇인가요?

**A:** 개인 레지스트리 URL을 포함하여 전체 이미지 이름을 추가해야 합니다(예: myacr.azurecr.io/dotnet:latest).

**Q:** 사용자 지정 컨테이너 이미지에 포트를 두 개 이상 표시하려 합니다. 가능할까요?

**A:** 현재는 지원되지 않습니다.

**Q:** 사용자 고유의 저장소를 가져올 수 있나요?

**A:** 현재는 지원되지 않습니다.

**Q:** 내 사용자 지정 컨테이너의 파일 시스템 또는 실행 중인 프로세스를 SCM 사이트에서 찾을 수 없습니다. 그 이유는 무엇입니까?

**A:** SCM 사이트는 별도의 컨테이너에서 실행되기 때문에 사용자가 앱 컨테이너의 파일 시스템 또는 실행 중인 프로세스를 확인할 수 없습니다.

**Q:** 내 사용자 지정 컨테이너가 포트 80 이외의 포트를 수신합니다. 해당 포트로 요청을 라우팅하도록 내 앱을 구성하려면 어떻게 합니까?

**A:** 자동 포트 검색 기능이 있으며 **WEBSITES_PORT**라는 응용 프로그램 설정을 지정하고 예상되는 포트 번호 값을 지정할 수 있습니다. 이전에 플랫폼은 `PORT` 앱 설정을 사용했으며 이 앱 설정 사용을 사용되지 않도록 하고 `WEBSITES_PORT`를 단독으로 사용하도록 이동할 예정입니다.

**Q:** 사용자 지정 컨테이너에서 HTTPS를 구현해야 하나요?

**A:** 아니요. 플랫폼에서는 공유 프런트 엔드에서 HTTPS 종료를 처리합니다.

## <a name="pricing-and-sla"></a>가격 및 SLA

**Q:** 공개 미리 보기를 사용 하는 동안 가격은 얼마입니까?

**A:** 일반 Azure App Service 가격으로 앱이 실행되는 시간의 절반이 청구됩니다. 즉, 이 일반 Azure App Service 가격에서 50% 할인이 적용됩니다.

## <a name="other"></a>기타

**Q:** 응용 프로그램 설정 이름에 지원되는 문자는 무엇입니까?

**A:** 응용 프로그램 설정에는 A-Z, a-z, 0-9 및 밑줄만 사용할 수 있습니다.

**Q:** 어디서 새 기능을 요청할 수 있나요?

**A:** [Web Apps 사용자 의견 포럼](https://aka.ms/webapps-uservoice)에서 사용자의 아이디어를 제출할 수 있습니다. 아이디어 제목에 "[Linux]"를 붙여 주세요.

## <a name="next-steps"></a>다음 단계
* [Linux에서 Azure Web App이란?](app-service-linux-intro.md)
* [Linux의 Azure Web App에서 웹앱 만들기](app-service-linux-how-to-create-web-app.md)
* [Linux의 Azure Web App에 대한 SSH 지원](./app-service-linux-ssh-support.md)
* [Azure App Service에서 스테이징 환경 설정](./web-sites-staged-publishing.md)
* [Linux에서 Azure 웹앱을 사용한 연속 배포](./app-service-linux-ci-cd.md)

