---
title: 내장 컨테이너 실행 FAQ
description: Azure App Service의 기본 제공 Linux 컨테이너에 대해 자주 묻는 질문에 대한 답변을 찾아보십시오.
keywords: Azure App Service, 웹앱, FAQ, Linux, OS, 컨테이너에 대한 웹앱, 다중 컨테이너, 다중 컨테이너
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: f0a8b1758571a9473402d11a4d5141a11f76504d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245823"
---
# <a name="azure-app-service-on-linux-faq"></a>Linux의 Azure App Service에 대한 FAQ

Linux의 App Service를 릴리스하면서 현재 플랫폼에 기능을 추가하고 플랫폼을 더욱 개선하기 위한 작업을 진행하고 있습니다. 이 문서는 고객이 최근에 요청한 질문에 대한 대답을 제공합니다.

질문이 있는 경우 이 문서에 댓글을 달아주세요.

## <a name="built-in-images"></a>기본 제공 이미지

**플랫폼에서 제공하는 기본 제공 Docker 컨테이너를 포크하려고 합니다. 해당 파일은 어디에서 찾을 수 있습니까?**

[GitHub](https://github.com/azure-app-service)에서 모든 Docker 파일을 찾을 수 있습니다. [Docker Hub](https://hub.docker.com/u/appsvc/)에서 모든 Docker 컨테이너를 찾을 수 있습니다.

<a id="#startup-file"></a>

**런타임 스택을 구성할 때 시작 파일 섹션에 대해 예상되는 값은 무엇인가요?**

| 스택           | 예상 값                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | JAR 앱을 시작하는 명령(예: `java -jar /home/site/wwwroot/app.jar --server.port=80`) |
| Tomcat          | 필요한 구성을 수행할 스크립트의 위치(예: `/home/site/deployments/tools/startup_script.sh`)          |
| Node.js         | PM2 구성 파일 또는 스크립트 파일                                |
| .NET Core       | 컴파일된 DLL 이름은`dotnet <myapp>.dll`                                 |
| Ruby            | 앱을 초기화하려는 루비 스크립트를                     |

이러한 명령 또는 스크립트는 기본 제공 Docker 컨테이너가 시작된 후 응용 프로그램 코드가 시작되기 전에 실행됩니다.

## <a name="management"></a>관리

**Azure Portal에서 다시 시작 단추를 누르면 어떻게 되나요?**

이 작업은 Docker 다시 시작과 동일합니다.

**앱 컨테이너 가상 머신(VM)에 연결하기 위해 Secure Shell(SSH)을 사용할 수 있나요?**

예. SCM(원본 제어 관리) 사이트를 통해 수행할 수 있습니다.

> [!NOTE]
> SSH, SFTP 또는 Visual Studio Code를 사용하여 로컬 개발 컴퓨터에서 직접 앱 컨테이너에 연결할 수도 있습니다(Node.js 앱 라이브 디버깅을 위해). 자세한 내용은 [Linux App Service의 원격 디버깅 및 SSH](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html)를 참조하세요.
>

**SDK 또는 Azure Resource Manager 템플릿을 통해 Linux App Service 계획을 어떻게 만들 수 있나요?**

앱 서비스의 **예약된** 필드를 *true로*설정합니다.

## <a name="continuous-integration-and-deployment"></a>지속적인 통합 및 배포

**Docker Hub에서 이미지를 업데이트한 후에도 웹 앱에서 이전 Docker 컨테이너 이미지를 계속 사용합니다. 사용자 지정 컨테이너의 지속적인 통합 및 배포를 지원합니까?**

예, Azure Container Registry 또는 DockerHub에 대한 지속적인 통합/배포를 설정하려면 [Web App for Containers를 사용한 지속적인 배포](./app-service-linux-ci-cd.md) 문서를 확인하세요. 프라이빗 레지스트리의 경우 웹앱을 중지했다가 다시 시작하여 컨테이너를 새로 고칠 수 있습니다. 또는 컨테이너를 강제로 새로 고침하도록 더미 애플리케이션을 변경 또는 추가할 수 있습니다.

**스테이징 환경이 지원되나요?**

예.

***WebDeploy/MSDeploy* 를 사용하여 내 웹앱을 배포할 수 있나요?**

예. `WEBSITE_WEBDEPLOY_USE_SCM`이라는 앱 설정을 *false*로 설정해야 합니다.

**Linux 웹 앱을 사용할 때 응용 프로그램의 Git 배포가 실패합니다. 이 문제를 해결하면 어떻게 해야 합니까?**

Linux 웹앱에 대한 Git 배포가 실패하면 다음 옵션 중 하나를 선택하여 애플리케이션 코드를 배포할 수 있습니다.

- 연속 배달(미리 보기) 기능 사용: Azure DevOps Git 리포지토리 또는 GitHub 리포지토리에 앱의 소스 코드를 저장하여 Azure 연속 배달을 사용할 수 있습니다. 자세한 내용은 [Linux 웹앱에 지속적인 업데이트를 구성하는 방법](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/)을 참조하세요.

- [ZIP 배포 API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file) 사용: 이 API를 사용하려면 [웹앱에 SSH를 실행하고](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support) 코드를 배포할 폴더로 이동합니다. 다음 코드를 실행합니다.

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   `curl` 명령을 찾을 수 없다는 오류가 발생하면 이전 `curl` 명령을 실행하기 전에 `apt-get install curl`을 사용하여 curl을 설치해야 합니다.

## <a name="language-support"></a>언어 지원

**Node.js 애플리케이션에서 웹 소켓을 사용하려고 합니다. 특별한 설정이나 구성이 필요한가요?**

예, 서버 쪽 Node.js 코드에서 `perMessageDeflate`를 비활성화해야 합니다. 예를 들어, socket.io를 사용하고 있다면 다음 코드를 사용합니다.

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**컴파일되지 않은 .NET Core 앱을 지원하나요?**

예.

**작성기를 PHP 앱의 종속성 관리자로 지원하나요?**

예, Git 배포 중에 Kudu는 사용자가 PHP 애플리케이션을 배포하고 있음을 감지해야 합니다(composer.lock 파일의 존재 덕분). 그러면 Kudu가 작성기 설치를 트리거합니다.

## <a name="custom-containers"></a>사용자 지정 컨테이너

**사용자 지정 컨테이너를 사용하고 있습니다. 플랫폼이 `/home/` SMB 공유를 디렉토리에 탑재하기를 원합니다.**

설정이 **지정되지 않았거나** *true로* `/home/` 설정된 경우 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 디렉토리는 스케일 인스턴스 간에 **공유되고** 작성된 파일은 다시 시작될 때까지 **유지됩니다.** 명시적으로 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` *false로* 설정하면 마운트가 비활성화됩니다.

**내 사용자 지정 컨테이너는 시작하는 데 시간이 오래 걸리고 플랫폼이 시작을 마무리하기 전에 컨테이너를 다시 시작합니다.**

컨테이너를 다시 시작하기 전에 플랫폼이 대기할 시간을 구성할 수 있습니다. 이를 수행하려면 `WEBSITES_CONTAINER_START_TIME_LIMIT` 앱 설정을 원하는 값으로 설정합니다. 기본값은 230초이고 최댓값은 1800초입니다.

**프라이빗 레지스트리 서버 URL의 형식은 무엇인가요?**

`http://` 또는 `https://`를 포함하여 전체 레지스트리 URL을 입력합니다.

**프라이빗 레지스트리 옵션에서 이미지 이름의 형식은 무엇인가요?**

프라이빗 레지스트리 URL(예: myacr.azurecr.io/dotnet:latest)을 포함하여 전체 이미지 이름을 추가합니다. 사용자 지정 포트를 사용하는 이미지 이름은 [포털을 통해 입력할 수 없습니다](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). 설정하려면 `docker-custom-image-name` [ `az` 명령줄 도구를](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set)사용합니다.

**사용자 지정 컨테이너 이미지에 포트를 두 개 이상 표시할 수 있나요?**

두 개 이상의 포트 노출을 지원하지 않습니다.

**사용자 고유의 스토리지를 가져올 수 있나요?**

예, [사용자 고유의 스토리지 가져오기](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage)는 미리 보기로 제공됩니다.

**내 사용자 지정 컨테이너의 파일 시스템 또는 실행 중인 프로세스를 SCM 사이트에서 찾을 수 없는 이유는 무엇인가요?**

SCM 사이트는 별도의 컨테이너에서 실행됩니다. 사용자가 앱 컨테이너의 파일 시스템 또는 실행 중인 프로세스를 확인할 수 없습니다.

**사용자 지정 컨테이너는 포트 80이 아닌 다른 포트를 수신합니다. 요청을 해당 포트로 라우팅하도록 앱을 구성하려면 어떻게 해야 합니까?**

자동 포트 검색이 있습니다. *WEBSITES_PORT*라는 앱 설정을 지정하고 예상되는 포트 번호 값을 지정할 수도 있습니다. 이전에 플랫폼은 *포트* 앱 설정을 사용했습니다. 이 앱 설정의 사용을 중단하고 *WEBSITES_PORT*를 단독으로 사용할 계획입니다.

**사용자 지정 컨테이너에서 HTTPS를 구현해야 하나요?**

아니요. 플랫폼에서는 공유 프런트 엔드에서 HTTPS 종료를 처리합니다.

## <a name="multi-container-with-docker-compose"></a>도커 컴포지션이 있는 다중 컨테이너

**다중 컨테이너를 사용하도록 ACR(Azure Container Registry)을 구성하려면 어떻게 할까요?**

다중 컨테이너에서 ACR을 사용하기 위해 **모든 컨테이너 이미지**는 동일한 ACR 레지스트리 서버에 호스트되어야 합니다. 동일한 레지스트리 서버에 있으면 응용 프로그램 설정을 만든 다음 ACR 이미지 이름을 포함하도록 Docker Compose 구성 파일을 업데이트해야 합니다.

다음 애플리케이션 설정을 만듭니다.

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (전체 URL, `https://<server-name>.azurecr.io`예 : ))
- DOCKER_REGISTRY_SERVER_PASSWORD(ACR 설정에서 관리자 액세스 사용)

구성 파일 내에서 다음 예제와 같이 ACR 이미지를 참조합니다.

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**인터넷에 액세스할 수 있는 컨테이너를 알려면 어떻게 할까요?**

- 하나의 컨테이너만이 액세스에 열릴 수 있습니다.
- 포트 80 및 8080만이 액세스될 수 있습니다(노출된 포트)

우선 순위로 액세스할 수 있는 컨테이너를 결정하는 규칙은 다음과 같습니다.

- 컨테이너 이름으로 설정된 애플리케이션 설정 `WEBSITES_WEB_CONTAINER_NAME`
- 포트 80 또는 8080을 정의하는 첫 번째 컨테이너
- 위의 조건이 모두 true가 아닌 경우 파일에 정의된 첫 번째 컨테이너는 액세스될 수 있습니다(노출됨).


## <a name="web-sockets"></a>웹 소켓

웹 소켓은 Linux 앱에서 지원됩니다.

> [!IMPORTANT]
> 웹 소켓은 현재 무료 앱 서비스 계획에서 Linux 앱에 대해 지원되지 않습니다. 우리는이 제한을 제거하기 위해 노력하고 무료 앱 서비스 계획에 최대 5 웹 소켓 연결을 지원할 계획이다.

## <a name="pricing-and-sla"></a>가격 및 SLA

**서비스가 일반 공급될 때 가격은 얼마인가요?**

가격은 SKU 및 지역에 따라 다르지만 가격 책정 페이지에서 자세한 내용을 확인할 수 [있습니다.](https://azure.microsoft.com/pricing/details/app-service/linux/)

## <a name="other-questions"></a>기타 질문

**"요청된 기능은 리소스 그룹에서 사용할 수 없음"은 무엇을 의미합니까?**

ARM(Azure 리소스 관리자)을 사용하여 웹 앱을 만들 때 이 메시지가 표시될 수 있습니다. 동일한 리소스 그룹에 대해 현재 제한에 따라 동일한 리전에서 Windows 및 Linux 앱을 혼합할 수 없습니다.

**애플리케이션 설정 이름에 지원되는 문자는 무엇입니까?**

애플리케이션 설정에 문자(A-Z, a-z), 숫자(0-9) 및 밑줄(_)만 사용할 수 있습니다.

**어디서 새 기능을 요청할 수 있나요?**

[Web Apps 사용자 의견 포럼](https://aka.ms/webapps-uservoice)에서 사용자의 아이디어를 제출할 수 있습니다. 아이디어 제목에 "[Linux]"를 붙여 주세요.

## <a name="next-steps"></a>다음 단계

- [Linux의 Azure App Service란?](app-service-linux-intro.md)
- [Azure App Service에서 스테이징 환경 설정](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [컨테이너용 웹 앱으로 연속 배포](./app-service-linux-ci-cd.md)
