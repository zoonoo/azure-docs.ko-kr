<properties pageTitle="Node.js 모듈 작업" description="Azure 웹 사이트 또는 클라우드 서비스를 사용할 때 Node.js 모듈로 작업하는 방법을 알아봅니다." services="" documentationCenter="nodejs" authors="MikeWasson" manager="wpickett" editor="mollybos"/>

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="02/19/2015" ms.author="mwasson"/>





# Azure 응용 프로그램에 Node.js 모듈 사용

이 문서에서는 Azure에서 호스트되는 응용 프로그램에 Node.js 모듈을 사용하는 방법에 대해 안내합니다. 응용 프로그램에서 특정 버전의 모듈을 사용하고 Azure 네이티브 모듈을 사용하도록 하는 방법을 설명합니다.

이미 Node.js 모듈, **package.json** 및 **npm-shrinkwrap.json** 파일을 사용하는 데 익숙한 개발자를 위해 이 문서에서 다루는 내용을 다음과 같이 빠른 개요로 제공합니다.

* Azure 웹 사이트는 **package.json** 및 **npm-shrinkwrap.json** 파일을 인식하며 이 파일의 항목을 기반으로 모듈을 설치할 수 있습니다.
* Azure 클라우드 서비스는 모든 모듈이 개발 환경에 설치되며 **node_modules** 디렉터리가 배포 패키지의 일부로 포함될 것을 예상합니다.

> [AZURE.NOTE]Azure 가상 컴퓨터에 대해서는 이 문서에서 다루지 않습니다. VM의 배포 환경은 가상 컴퓨터에서 호스트되는 운영 체제에 의존하기 때문입니다.

> [AZURE.NOTE]Azure에서 **package.json** 또는 **npm-shrinkwrap.json** 파일을 사용하여 모듈을 설치하도록 지원할 수 있습니다. 하지만 이렇게 하려면 클라우드 서비스 프로젝트에서 사용되는 기본 스크립트를 사용자 지정해야 합니다. 이를 수행하는 방법의 예를 보려면 [npm install을 실행하여 노드 모듈 배포를 방지하는 Azure 시작 작업](http://nodeblog.azurewebsites.net/startup-task-to-run-npm-in-azure)을 참조하세요.

##Node.js 모듈

모듈은 응용 프로그램의 특정 기능을 제공하는 로드 가능한 JavaScript 패키지입니다. 모듈은 일반적으로 **npm** 명령줄 도구를 사용하여 설치되지만, http 모듈과 같은 일부 모듈은 핵심 Node.js 패키지의 일부로 제공됩니다.

모듈이 설치되면 **node_modules** 디렉터리에서 응용 프로그램 디렉터리 구조 루트에 저장됩니다. **node_modules** 디렉터리 내의 각 모듈은 종속되는 모든 모듈이 포함된 고유한 **node_modules** 디렉터리를 유지하며, 이 구조는 종속성 체인 전체에서 모든 모듈에 대해 반복됩니다. 이에 따라, 설치된 각 모듈이 종속되는 모듈에 대해 고유한 버전 요구 사항을 가지지만 상당히 큰 규모의 디렉터리 구조가 생길 수 있습니다.

**node_modules** 디렉터리를 응용 프로그램의 일부로 배포하면 **package.json** 또는 **npm-shrinkwrap.json** 파일을 사용하는 경우와 비교하여 배포의 크기가 커지지만 프로덕션에 사용되는 모듈 버전과 개발에 사용되는 모듈 버전이 같을 수 있습니다.

###네이티브 모듈

대부분의 모듈이 단순히 일반 텍스트 JavaScript 파일인 반면, 일부 모듈은 플랫폼별 이진 이미지입니다. 이 모듈은 설치 시간에 대개 Python 및 node-gyp를 사용하여 컴파일됩니다. Azure 클라우드 서비스에서는 **node_modules** 폴더가 응용 프로그램의 일부로 배포되므로, 설치되는 모듈의 일부로 포함된 네이티브 모듈은 Windows 개발 시스템에서 설치 및 컴파일된 경우 클라우드 서비스에서 작동합니다.

Azure 웹 사이트는 일부 네이티브 모듈을 지원하지 않으며 매우 구체적인 필수 구성 요소를 가진 네이티브 모듈의 컴파일에 실패할 수 있습니다. MongoDB와 같은 일부 일반적인 모듈에는 선택적 네이티브 종속성이 있으며 이러한 종속성 없이도 정상적으로 작동하지만 다음 두 가지 해결 방법은 현재 사용 가능한 거의 모든 네이티브 모듈에 효과적인 것으로 입증되었습니다.

* 모든 네이티브 모듈의 필수 구성 요소가 설치되어 있는 Windows 컴퓨터에서 **npm install**을 실행합니다. 그런 다음 만든 **node_modules** 폴더를 응용 프로그램의 일부로 Azure 웹 사이트에 배포합니다.
* 배포하는 동안 사용자 지정 bash 또는 셸 스크립트를 실행하도록 Azure 웹 사이트를 구성하여 사용자 지정 명령을 실행하고 **npm install** 실행 방식을 정확히 구성할 수 있습니다. 이 작업 방법을 보여 주는 비디오는 [Kudu를 사용하는 사용자 지정 웹 사이트 배포 스크립트]를 참조하세요.

###package.json 파일 사용

**package.json** 파일을 사용하여 응용 프로그램에서 요구하는 최상위 종속성을 지정할 수 있습니다. 그러면 호스팅 플랫폼이 배포의 일부로 **node_packages** 폴더를 포함하도록 요구하는 대신 종속성을 설치할 수 있게 됩니다. 응용 프로그램이 배포된 후에는 **npm install** 명령을 사용하여 **package.json** 파일을 구문 분석하고 나열된 모든 종속성을 설치합니다.

개발하는 동안 모듈 설치 시 **package.json** 파일에 모듈의 항목이 자동으로 추가되도록 **--save**, **--save-dev** 또는 **--save-optional** 매개 변수를 사용할 수 있습니다. 자세한 내용은 [npm-install](https://npmjs.org/doc/install.html)(영문)을 참조하십시오.

**package.json** 파일의 한 가지 잠재적인 문제점은 이 파일이 최상위 종속성에 대한 버전만 지정한다는 것입니다. 설치된 각 모듈은 종속되는 모듈의 버전을 지정하거나 지정하지 않을 수 있으므로, 개발에서 사용된 것과 다른 종속성 체인을 얻게 될 수 있습니다.

> [AZURE.NOTE]Azure 웹 사이트에 배포할 때는 <b>package.json</b> 파일이 네이티브 모듈을 참조하는 경우 Git를 사용하는 응용 프로그램을 게시하면 다음과 유사한 오류가 발생합니다.

>		npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>		npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


###npm-shrinkwrap.json 파일 사용

**npm-shrinkwrap.json** 파일은 **package.json** 파일의 모듈 버전 관리 제한 사항을 해결하기 위한 것입니다. **package.json** 파일은 최상위 모듈에 대한 버전만 포함하는 반면, **npm-shrinkwrap.json** 파일은 전체 모듈 종속성 체인에 대한 버전 요구 사항을 포함합니다.

응용 프로그램을 프로덕션에 적용할 준비가 되면 버전 요구 사항을 잠그고 **npm shrinkwrap** 명령을 사용하여 **npm-shrinkwrap.json** 파일을 만들 수 있습니다. 이렇게 하면 현재 **node_modules** 폴더에 설치되어 있는 버전이 사용되며 이 버전이 **npm-shrinkwrap.json** 파일에 기록됩니다. 응용 프로그램이 호스팅 환경에 배포된 후에는 **npm install** 명령을 사용하여 **npm-shrinkwrap.json** 파일을 구문 분석하고 나열된 모든 종속성을 설치합니다. 자세한 내용은 [npm-install](https://npmjs.org/doc/install.html)(영문)을 참조하십시오.

> [AZURE.NOTE]Azure 웹 사이트에 배포할 때는 <b>npm-shrinkwrap.json</b> 파일이 네이티브 모듈을 참조하는 경우 Git를 사용하는 응용 프로그램을 게시하면 다음과 유사한 오류가 발생합니다.

>		npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>		npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


##다음 단계

이제 Azure에서 Node.js 모듈을 사용하는 방법을 익혔습니다. [Node.js 버전 지정], [Node.js 웹 사이트 빌드 및 배포] 방법 및 [Mac 및 Linux에서 Azure 명령줄 인터페이스를 사용하는 방법]을 알아보겠습니다.

[Node.js 버전 지정]: nodejs-specify-node-version-azure-apps.md
[Mac 및 Linux에서 Azure 명령줄 인터페이스를 사용하는 방법]: xplat-cli.md
[Node.js 웹 사이트 빌드 및 배포]: web-sites-nodejs-develop-deploy-mac.md
[Node.js Web Application with Storage on MongoDB (MongoLab)]: store-mongolab-web-sites-nodejs-store-data-mongodb.md
[Publishing with Git]: web-sites-publish-source-control.md
[Build and deploy a Node.js application to an Azure Cloud Service]: cloud-services-nodejs-develop-deploy-app.md
[Kudu를 사용하는 사용자 지정 웹 사이트 배포 스크립트]: /documentation/videos/custom-web-site-deployment-scripts-with-kudu/

<!---HONumber=July15_HO4-->