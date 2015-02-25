<properties pageTitle="Azure에서 Linux용 Docker VM 확장 사용" description="Docker 및 Azure 가상 컴퓨터 확장에 대해 설명하고, azure-cli 명령 인터페이스를 사용하여 명령줄에서 Docker 호스트인 가상 컴퓨터를 Azure에 프로그래밍 방식으로 만드는 방법을 안내합니다." services="virtual-machines" documentationCenter="" authors="squillace" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/21/2014" ms.author="rasquill"/>
<!--The next line, with one pound sign at the beginning, is the page title--> 
# Azure 포털에서 Docker VM 확장 사용

[Docker](https://www.docker.com/)는 공유 리소스의 데이터와 계산을 격리시키는 한 가지 방법으로 가상 컴퓨터 대신 [Linux 컨테이너](http://en.wikipedia.org/wiki/LXC)를 사용하는 가장 많이 사용되는 가상화 방법 중 하나입니다. [Azure Linux 에이전트]에 대한 Docker VM 확장을 사용하여 Azure에 응용 프로그램의 컨테이너를 개수에 제한없이 호스트하는 Docker VM을 만들 수 있습니다. 

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->
섹션 내용

+ [이미지 갤러리에서 새 VM 만들기]
+ [Docker 인증서 만들기]
+ [Docker VM 확장 추가]
+ [Docker 클라이언트 및 Azure Docker 호스트 테스트]
+ [다음 단계]

> [AZURE.NOTE] 이 항목에서는 Azure 포털에서 Docker VM을 만드는 방법을 설명합니다. 명령줄에서 Docker VM을 만드는 방법을 확인하려면 [Azure 플랫폼 간 인터페이스(xplat-cli)에서 Docker VM 확장을 사용하는 방법]을 참조하세요. 컨테이너와 해당 이점에 대한 간략한 설명을 확인하려면 [Docker 요약 화이트보드](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard)를 참조하세요. 

## <a id='createvm'>이미지 갤러리에서 새 VM 만들기</a>
첫 단계를 수행하려면 Docker VM 확장을 지원하는 Linux 이미지의 Azure VM이 필요합니다. 여기서는 이미지 갤러리의 Ubuntu 14.04 LTS 이미지를 예제 서버 이미지로, Ubuntu 14.04 Desktop을 클라이언트로 사용합니다. 포털의 왼쪽 아래에서 **+ 새로 만들기**를 클릭하여 새 VM 인스턴스를 만들고 아래에 나와 있는 것처럼 선택 가능한 항목 또는 전체 이미지 갤러리에서 Ubuntu 14.04 LTS 이미지를 선택합니다. 

> [AZURE.NOTE] 현재는 2014년 7월 이후의 Ubuntu 14.04 LTS 이미지만이 Docker VM 확장을 지원합니다.

![Create a new Ubuntu Image](./media/virtual-machines-docker-with-portal/ChooseUbuntu.png)

## <a id'dockercerts'>Docker 인증서 만들기</a>

VM을 만든 후에는 클라이언트 컴퓨터에 Docker가 설치되어 있는지 확인합니다. 자세한 내용은 [Docker 설치 지침](https://docs.docker.com/installation/#installation)을 참조하세요. 

[https를 사용하여 Docker 실행]의 지침에 따라 Docker 통신용 인증서 및 키 파일을 만든 다음 클라이언트 컴퓨터의 **`~/.docker`** 디렉터리에 저장합니다. 

> [AZURE.NOTE] 현재는 포털의 Docker VM 확장을 사용하려면 base64로 인코딩된 자격 증명이 필요합니다.

명령줄에서 **`base64`** 또는 평소 사용하는 기타 인코딩 도구를 사용하여 base64 인코딩 항목을 만듭니다. 아래에는 간단한 인증서 및 키 파일 집합을 사용하는 이 작업의 예제가 나와 있습니다.

```
 ~/.docker$ l
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ l
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a id'adddockerextension'>Docker VM 확장 추가</a>
Docker VM 확장을 추가하려면 앞에서 만든 VM 인스턴스를 찾은 다음 아래쪽의 확장으로 스크롤하여 **확장**을 클릭합니다. 그러면 아래와 같이 VM 확장이 표시됩니다.

![](./media/virtual-machines-docker-with-portal/ClickExtensions.png)
### 확장 추가
**+ 추가**를 클릭하여 이 VM에 추가할 수 있는 VM 확장을 표시합니다. 

![](./media/virtual-machines-docker-with-portal/ClickAdd.png)
### Docker VM 확장 선택
Docker VM 확장을 선택하면 Docker 설명 및 중요 링크가 표시됩니다. 그러면 아래쪽의 **만들기**를 클릭하여 설치 절차를 시작합니다.

![](./media/virtual-machines-docker-with-portal/ChooseDockerExtension.png)

![](./media/virtual-machines-docker-with-portal/CreateButtonFocus.png)
### 인증서 및 키 파일 추가

다음 그림에 나와 있는 것처럼 양식 필드에서 CA 인증서, 서버 인증서 및 서버 키의 base64 인코딩 버전을 입력합니다.

![](./media/virtual-machines-docker-with-portal/AddExtensionFormFilled.png)

> [AZURE.NOTE] 위의 이미지에 나와 있는 것처럼 포트에는 기본적으로 4243이 입력되어 있습니다. 여기에는 원하는 어떤 끝점이든 입력할 수 있지만 다음 단계에서 일치하는 끝점을 열어야 합니다. 기본값을 변경하는 경우에는 다음 단계에서 일치하는 끝점을 여세요.

## Docker 통신 끝점 추가
직접 만든 리소스 그룹에서 VM을 표시하고 아래쪽으로 스크롤하여 **끝점**을 클릭합니다. 그러면 아래 그림과 같이 VM의 끝점이 표시됩니다.

![](./media/virtual-machines-docker-with-portal/AddingEndpoint.png)

**+ 추가**를 클릭하여 다른 끝점을 추가합니다. 기본 끝점의 경우 끝점 이름을 입력하고(여기서는 **docker**) 공용 포트와 개인 포트에 대해 모두 4243을 입력합니다. 프로토콜 값은 **TCP**로 유지하고 **확인**을 클릭하여 끝점을 만듭니다.

![](./media/virtual-machines-docker-with-portal/AddEndpointFormFilledOut.png)


## <a id='testclientandserver'>Docker 클라이언트 및 Azure Docker 호스트 테스트</a>
VM 도메인의 이름을 찾아서 복사한 다음 클라이언트 컴퓨터의 명령줄에 `docker --tls -H tcp://`*dockerextension*`.cloudapp.net:4243 info`를 입력합니다. 여기서  *dockerextension*은 VM의 하위 도메인으로 바꿉니다. 

결과는 다음과 같이 표시됩니다.

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:4243 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

이제 [Docker 사용자 가이드]로 이동하여 Docker VM을 사용할 수 있습니다. Azure에서 Docker VM을 반복적으로 빠르게 만들려면 [Azure 플랫폼 간 인터페이스(xplat-cli)에서 Docker VM 확장을 사용하는 방법]을 참조하세요.

<!--Anchors-->
[이미지 갤러리에서 새 VM 만들기]: #createvm
[Docker 인증서 만들기]: #dockercerts
[Docker VM 확장 추가]: #adddockerextension
[Docker 클라이언트 및 Azure Docker 호스트 테스트]: #testclientandserver
[다음 단계]: #next-steps

<!--Image references-->
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Azure 플랫폼 간 인터페이스(xplat-cli)에서 Docker VM 확장을 사용하는 방법]: http://azure.microsoft.com/ko-kr/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Azure Linux 에이전트]: ../virtual-machines-linux-agent-user-guide/
[다른 azure.microsoft.com 설명서 항목에 대한 링크 3]: ../storage-whatis-account/

[https를 사용하여 Docker 실행]: http://docs.docker.com/articles/https/
[Docker 사용자 가이드]: https://docs.docker.com/userguide/


<!--HONumber=42-->
