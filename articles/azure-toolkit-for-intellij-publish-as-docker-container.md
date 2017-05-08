---
title: "IntelliJ용 Azure 도구 키트를 사용하여 Docker 컨테이너 게시 | Microsoft Docs"
description: "IntelliJ용 Azure 도구 키트를 사용하여 Docker 컨테이너로 Microsoft Azure에 웹앱을 게시하는 방법을 알아봅니다."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: f8ae15f4a5edb3cd84b5bb2302e1a5f709b57f82
ms.lasthandoff: 04/22/2017


---

# <a name="how-to-publish-a-web-app-as-a-docker-container-using-the-azure-toolkit-for-intellij"></a>IntelliJ용 Azure 도구 키트를 사용하여 웹앱을 Docker 컨테이너로 게시하는 방법

Docker 컨테이너는 개발자가 모든 프로젝트 파일과 종속성을 서버에 배포할 단일 패키지로 통합하기 위해 광범위하게 사용되는 웹 애플리케이션 배포 방법입니다. IntelliJ용 Azure 도구 키트에서는 Microsoft Azure에 배치할 *Docker 컨테이너로 게시* 기능을 추가하여 Java 개발자를 위해 이 프로세스를 간소화하고, 이 문서의 단계를 통해 Docker 컨테이너로 Azure에 응용 프로그램을 게시하는 데 필요한 단계를 안내합니다.

> [!NOTE]
>
> Docker에 대한 자세한 내용은 [Docker 웹 사이트]를 참조하세요.
>

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>Docker 컨테이너를 사용하여 Azure에 웹앱 게시

> [!NOTE]
>
> 웹앱을 게시하려면 배포 준비된 아티팩트를 만들어야 합니다. 자세한 내용은 이 문서의 뒷부분에 있는 [아티팩트 만들기에 대한 추가 정보](#artifacts)를 참조하세요.
>
> 또한 배포 마법사를 한 번 이상 완료한 후 마법사를 다시 실행할 때 이 연습에 지정된 대부분의 설정을 기본값으로 사용합니다.
>

1. IntelliJ의 웹앱 프로젝트를 엽니다.

1. 다음 방법 중 하나를 사용하여 Docker 컨테이너로 게시 마법사를 시작합니다.

   * **프로젝트** 도구 창에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Azure**를 클릭한 다음 **Docker 컨테이너로 게시**:  ![Docker 컨테이너로 게시][PUB01]를 클릭합니다.

   * IntelliJ 도구 모음에서 **그룹 게시** 메뉴를 클릭한 다음 **Docker 컨테이너로 게시**:  ![Docker 컨테이너로 게시][PUB02]를 클릭합니다.

1. **Azure에 Docker 컨테이너 배포** 마법사가 표시되면 다음 그림과 비슷한 대화 상자가 표시됩니다. ![Azure에 Docker 컨테이너 배포 마법사][PUB03]

   a. **Docker 이미지 이름** 텍스트 상자에 있는 Docker 호스트의 텍스트 상자에 고유한 이름을 입력합니다. (이 마법사가 자동으로 이름을 만들지만, 원하는 경우 해당 이름을 수정할 수 있습니다.)

   b. **호스트** 창에 사용자가 만든 Docker 호스트가 표시됩니다.
      * Docker 호스트를 만들지 않은 경우 대화 상자의 이 섹션이 비어 있습니다.
      * Docker 호스트를 이미 만든 경우 기존 호스트에 웹앱을 배포하도록 선택할 수 있습니다. 그렇지 않으면 아래 나열된 단계를 따라 새 Docker 호스트를 만듭니다.

1. 새 Docker 호스트를 만들려면 녹색 더하기("**+**") 기호를 클릭합니다. 그러면 **Docker 호스트 만들기** 대화 상자가 시작됩니다.
      ![Azure에 Docker 컨테이너 배포 마법사][PUB04a]

   a. Docker 호스트에 대한 다음 옵션을 지정합니다. (마법사가 대부분의 옵션을 자동으로 생성하지만, 사용자 지정하려는 옵션은 직접 수정할 수 있습니다.)

      * **이름**: Docker 호스트의 고유 이름입니다. (이전에 지정한 Docker 이미지 이름과 같지 않습니다.)

      * **구독**: 호스트에 사용할 Azure 구독을 지정합니다.
      
      * **지역**: 호스트가 배치될 지역을 지정합니다.
      
      * **OS 및 크기** 탭에서 다음을 수행합니다.
         * **호스트 OS**: 호스트를 포함할 가상 컴퓨터의 운영 체제를 지정합니다.
         * **크기**: 호스트의 가상 컴퓨터 크기를 지정합니다.

      * **리소스 그룹** 탭에서 다음을 수행합니다.
         * **새 리소스 그룹**: 호스트의 새 리소스 그룹을 만들 수 있습니다.
         * **기존 리소스 그룹**: Azure 계정에서 기존 리소스 그룹을 지정할 수 있습니다.

      * **네트워크** 탭에서 다음을 수행합니다.
         * **새 가상 네트워크**: 호스트의 새 가상 네트워크를 만들 수 있습니다.
         * **기존 가상 네트워크**: Azure 계정에서 기존 가상 네트워크를 지정할 수 있습니다.

      * **저장소** 탭에서 다음을 수행합니다.
         * **새 저장소 계정**: 호스트의 새 저장소 계정을 만들 수 있습니다.
         * **기존 저장소 계정**: Azure 계정에서 기존 저장소 계정을 지정할 수 있습니다.

   b. 위의 옵션을 모두 지정한 경우 **다음**을 클릭합니다.

   c. Docker 호스트의 가상 컴퓨터 로그인을 위한 다음 옵션 중 하나를 선택합니다.    ![Docker 호스트 만들기][PUB05]

      * **Azure Key Vault에서 자격 증명 가져오기**: Azure 구독에 저장되어 있는 이전에 저장된 자격 증명 집합을 지정할 수 있습니다.

      > [!NOTE]
      > 특정 계정 또는 서비스 주체로 작성된 Azure Key Vault는 동일한 구독을 공유하는 다른 계정 또는 서비스 주체를 통해 자동으로 액세스할 수 있습니다. 다른 계정 또는 서비스 주체에서 Key Vault를 사용할 수 있으려면 Azure Portal을 사용하여 계정 또는 서비스 주체를 추가해야 합니다.

      * **새 로그인 자격 증명**: 새 로그인 자격 증명 집합을 만들 수 있습니다. 이 경우 **VM 자격 증명** 탭에서 다음 옵션을 지정해야 합니다.
         * **사용자 이름**: 가상 컴퓨터 로그인의 사용자 이름을 지정합니다.
         * **암호** 및 **확인**: 가상 컴퓨터 로그인의 암호를 지정합니다.
         * **SSH**: Docker 호스트의 SSH(보안 셸) 설정을 지정합니다. 다음 옵션 중에서 선택할 수 있습니다.
            * **없음**: 가상 컴퓨터에서 SSH 연결을 허용하지 않게 지정합니다.
            * **자동 생성**: 이 옵션은 SSH를 통해 연결하는 데 필요한 설정을 자동으로 만듭니다.
            * **디렉터리에서 가져오기**: 이전에 저장된 SSH 설정 집합을 포함하는 디렉터리를 지정할 수 있습니다. 구체적으로 말하면 디렉터리에 다음 두 파일이 포함되어야 합니다.
               * *id_rsa*: 이 파일에는 사용자의 RSA ID가 포함됩니다.
               * *id_rsa.pub*: 이 파일에는 인증에 사용할 RSA 공개 키가 포함되어 있습니다.
        
      * **Docker 디먼 액세스** 탭에서 다음 옵션을 지정합니다. ![Docker 호스트 만들기][PUB06]

         * **Docker 디먼 포트**: Docker 호스트의 고유한 TCP 포트를 지정합니다.
         * **TLS 보안**: Docker 호스트의 TLS(전송 계층 보안) 설정을 지정하고 다음 옵션 중에서 하나를 선택할 수 있습니다.
            * **없음**: 가상 컴퓨터에서 TLS 연결을 허용하지 않게 지정합니다.
            * **자동 생성**: 이 옵션은 TLS를 통해 연결하는 데 필요한 설정을 자동으로 만듭니다.
            * **디렉터리에서 가져오기**: 이전에 저장된 TLS 설정 집합을 포함하는 디렉터리를 지정할 수 있습니다. 구체적으로 말하면 디렉터리에 다음 6개의 파일이 포함되어야 합니다.
               * *ca.pem* 및 *ca-key.pem*: 이러한 파일에는 TLS 인증 기관의 인증서와 공개 키가 포함되어 있습니다.
               * *cert.pem* 및 *key.pem*: 이러한 파일에는 TLS 인증에 사용할 클라이언트 인증서와 공개 키가 포함되어 있습니다.
               * *server.pem* 및 *server-key.pem*: 이러한 파일에는 호스트의 서버 인증서와 공개 키가 포함되어 있습니다.

   ㄹ. 위의 옵션을 모두 입력한 경우 **마침**을 클릭합니다.

1. **Azure에 Docker 컨테이너 배포** 마법사가 다시 표시되면 **다음**을 클릭합니다.
   ![Azure에 Docker 컨테이너 배포 마법사][PUB07]

1. 마법사의 마지막 페이지에서 다음 옵션을 지정합니다.

   * **Docker 컨테이너 이름**: Docker 컨테이너의 고유한 이름입니다.

   * 다음 Docker 이미지 중 하나를 선택합니다.

      * **미리 정의된 Docker 이미지**: Azure에서 기존 Docker 이미지를 지정할 수 있습니다.

      > [!NOTE]
      > 이 드롭다운 메뉴의 Docker 이미지 목록은 아티팩트를 자동으로 배치하기 위해 Azure 도구 키트가 패치하도록 구성된 여러 이미지로 구성됩니다.

      * **사용자 지정 Dockerfile**: 로컬 컴퓨터에서 이전에 저장한 Dockerfile을 지정할 수 있습니다.

      > [!NOTE]
      > 고유 Dockerfile을 배포하려는 개발자를 위한 고급 기능입니다. 그러나 이 기능은 Dockerfile을 올바르게 빌드하기 위해 이 옵션을 사용하는 개발자에 따라 달라집니다. Azure 도구 키트는 사용자 지정 Dockerfile에 있는 콘텐츠의 유효성을 검사하지 않으므로, Dockerfile에 문제가 있으면 배포에 실패할 수 있습니다. 또한 Azure 도구 키트에서는 사용자 지정 Dockerfile에 웹앱 아티팩트가 포함되어야 하며, HTTP 연결을 열려고 시도합니다. 개발자가 다른 유형의 아티팩트를 게시하면 배포 후 무해한 오류가 발생할 수 있습니다.

   * **포트 설정**: Docker 컨테이너의 고유한 TCP 포트 바인딩을 지정합니다.
   ![Azure에 Docker 컨테이너 배포 마법사][PUB08]

1. 위의 단계를 모두 완료하고 나면 **마침**을 클릭합니다.

Azure 도구 키트에서 Docker 컨테이너의 Azure에 웹앱 배포를 시작하고, 백그라운드로 배포하도록 IntelliJ를 구성하지 않으면 배포 진행률을 표시하는 대화 상자가 나타납니다. 
![배포 진행률][PUB09]

<a name="artifacts"></a>
## <a name="additional-information-about-creating-artifacts"></a>아티팩트 가져오기에 대한 추가 정보

배포 준비된 아티팩트를 만들려면 다음 단계를 사용합니다.

1. IntelliJ의 웹앱 프로젝트를 엽니다.

1. **파일**을 클릭한 다음 **프로젝트 구조**를 클릭합니다.
   ![프로젝트 구조 메뉴][ART01]

1. 녹색 더하기("**+**") 기호를 클릭하여 아티팩트를 추가한 다음 **웹 응용 프로그램: 아티팩트**를 클릭합니다.
   ![아티팩트 추가][ART02]

1. ".war" 확장자를 추가하지 않고 아티팩트의 이름을 지정한 다음 **확인**을 클릭합니다.
   ![아티팩트 속성][ART03]

IntelliJ에서 아티팩트를 만드는 데 관한 자세한 내용은 JetBrains 웹 사이트의 [아티팩트 구성]을 참조하세요.

## <a name="see-also"></a>참고 항목
Java IDE용 Azure 도구 키트에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Eclipse용 Azure 도구 키트]
  * [Eclipse용 Azure 도구 키트의 새로운 기능]
  * [Eclipse용 Azure 도구 키트 설치]
  * [Eclipse용 Azure 도구 키트에 대한 로그인 지침]
  * [Eclipse에서 Azure용 Hello World 웹앱 만들기]
* [IntelliJ용 Azure 도구 키트]
  * [IntelliJ용 Azure 도구 키트의 새로운 기능]
  * [IntelliJ용 Azure 도구 키트 설치]
  * [IntelliJ용 Azure 도구 키트에 대한 로그인 지침]
  * [IntelliJ에서 Azure용 Hello World 웹앱 만들기]

Java와 함께 Azure를 사용하는 방법에 대한 자세한 내용은 [Azure Java 개발자 센터] 및 [Visual Studio Team Services용 Java 도구]를 참조하세요.

Docker의 추가 리소스는 공식 [Docker 웹 사이트]를 참조하세요.

<!-- URL List -->

[Eclipse용 Azure 도구 키트]: ./azure-toolkit-for-eclipse.md
[IntelliJ용 Azure 도구 키트]: ./azure-toolkit-for-intellij.md
[Eclipse에서 Azure용 Hello World 웹앱 만들기]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[IntelliJ에서 Azure용 Hello World 웹앱 만들기]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Eclipse용 Azure 도구 키트 설치]: ./azure-toolkit-for-eclipse-installation.md
[IntelliJ용 Azure 도구 키트 설치]: ./azure-toolkit-for-intellij-installation.md
[Eclipse용 Azure 도구 키트에 대한 로그인 지침]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[IntelliJ용 Azure 도구 키트에 대한 로그인 지침]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Eclipse용 Azure 도구 키트의 새로운 기능]: ./azure-toolkit-for-eclipse-whats-new.md
[IntelliJ용 Azure 도구 키트의 새로운 기능]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java 개발자 센터]: https://azure.microsoft.com/develop/java/
[Visual Studio Team Services용 Java 도구]: https://java.visualstudio.com/

[Docker 웹 사이트]: https://www.docker.com/
[아티팩트 구성]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html

<!-- IMG List -->

[PUB01]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB01.png
[PUB02]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB02.png
[PUB03]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB03.png
[PUB04a]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04a.png
[PUB04b]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04b.png
[PUB04c]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04c.png
[PUB04d]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04d.png
[PUB05]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB05.png
[PUB06]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB06.png
[PUB07]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB07.png
[PUB08]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB08.png
[PUB09]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB09.png

[ART01]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/ART01.png
[ART02]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/ART02.png
[ART03]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/ART03.png

