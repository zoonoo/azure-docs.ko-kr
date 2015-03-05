<properties 
	pageTitle="Azure에서 Linux VM을 사용하여 Ruby on Rails 웹 앱 호스트" 
	description="Azure에서 Linux 가상 컴퓨터를 사용하여 Ruby on Rails 기반 웹 사이트를 호스트하는 방법에 대해 알아봅니다." 
	services="virtual-machines" 
	documentationCenter="ruby" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>





#Azure VM의 Ruby on Rails 웹 응용 프로그램

이 자습서에서는 Azure에서 Linux 가상 컴퓨터를 사용하여 Ruby on Rails 기반 웹 사이트를 호스트하는 방법을 설명합니다. 이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 이 자습서를 완료하면 클라우드에서 Ruby on Rails 기반 응용 프로그램을 실행할 수 있게 됩니다.

다음 방법에 대해 알아봅니다.

* 개발 환경 설정

* Ruby on Rails를 호스트하도록 Azure 가상 컴퓨터 설정

* 새 Rails 응용 프로그램 만들기

* scp를 사용하여 가상 컴퓨터로 파일 복사 

다음은 완료된 응용 프로그램의 스크린샷입니다.

![a browser displaying Listing Posts][blog-rails-cloud]

##이 문서의 내용

* [개발 환경 설정](#setup)

* [Rails 응용 프로그램 만들기](#create)

* [응용 프로그램 테스트](#test)

* [Azure 가상 컴퓨터 만들기](#createvm)

* [VM으로 응용 프로그램 복사](#copy)

* [gem 설치 및 응용 프로그램 시작](#start)

* [다음 단계](#next)

##<a id="setup"></a>개발 환경 설정

1. 개발 환경에 Ruby를 설치합니다. 운영 체제에 따라 아래 단계는 달라질 수 있습니다.

	* **Apple OS X** - OS X용 Ruby 배포가 몇 가지 있습니다. OS X에 대한 이 자습서 내용은 [Homebrew](http://brew.sh/)를 사용하여 **rbenv** 및 **ruby-build**를 설치함으로써 유효성이 검사되었습니다. 설치 정보는 [https://github.com/sstephenson/rbenv/](https://github.com/sstephenson/rbenv/)에서 볼 수 있습니다.

	* **Linux** - 배포 패키지 관리 시스템을 사용합니다. Ubuntu 12.10에 대한 이 자습서 내용은 ruby1.9.1 및 ruby1.9.1-dev 패키지를 사용하여 유효성이 검사되었습니다.

	* **Windows** - Windows용 Ruby 배포가 몇 가지 있습니다. 이 자습서의 내용은 [RailsInstaller](http://railsinstaller.org/) 1.9.3-p392를 사용하여 유효성이 검사되었습니다.

2. 새 명령줄 또는 터미널 세션을 열고 다음 명령을 입력하여 Ruby on Rails를 설치합니다.

		gem install rails --no-rdoc --no-ri

	> [AZURE.NOTE] 일부 운영 체제에서 이 명령을 사용하려면 관리자 또는 루트 권한이 필요할 수 있습니다. 이 명령을 실행하는 동안 오류가 발생한 경우 다음과 같이  'sudo'를 사용해 보세요.
	>
	>````` 
	sudo gem install rails
	`````

	> [AZURE.NOTE] 이 자습서에서는 Rails gem의 버전 3.2.12를 사용했습니다.

3. 또한 JavaScript 인터프리터를 설치해야 합니다. Rails에서 Rails 응용 프로그램에 사용되는 CoffeeScript 자산을 컴파일하는 데 이 인터프리터를 사용합니다. 지원되는 인터프리터의 목록은 [https://github.com/sstephenson/execjs#readme](https://github.com/sstephenson/execjs#readme)에서 확인할 수 있습니다.
	
	[Node.js](http://nodejs.org/)는 OS X, Linux 및 Windows 운영 체제에 사용할 수 있으므로, 이 자습서 내용의 유효성 검사 중 사용되었습니다.

##<a id="create"></a>Rails 응용 프로그램 만들기

1. 명령줄 또는 터미널 세션에서 다음 명령을 사용하여 "blog_app"이라는 새 Rails 응용 프로그램을 만듭니다.

		rails new blog_app

	이 명령은 **blog_app**이라는 새 디렉터리를 만들고 Rails 응용 프로그램에 필요한 파일 및 하위 디렉터리로 채웁니다.

	> [AZURE.NOTE] 이 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 이 명령은 기본 응용 프로그램에 필요한 gem을 자동으로 설치하며, 자동 설치 중에는 화면이 중단된 것처럼 보일 수 있습니다.

2. **blog_app** 디렉터리로 변경한 후 다음 명령을 사용하여 기본 블로그 스캐폴딩을 만듭니다.

		rails generate scaffold Post name:string title:string content:text

	이 명령은 블로그에 게시물을 유지하는 데 사용되는 컨트롤러, 보기, 모델 및 데이터베이스 마이그레이션을 만듭니다. 각 게시물에는 만든 이 이름, 게시물의 제목 및 텍스트 내용이 포함됩니다.

3. 블로그 게시물을 저장할 데이터베이스를 만들려면 다음 명령을 사용합니다.

		rake db:migrate

	이 명령은 Rails의 기본 데이터베이스 공급자인 [SQLite3 Database][sqlite3]를 사용합니다. 프로덕션 응용 프로그램에 다른 데이터베이스를 사용할 수도 있지만 이 자습서에서는 SQLite로 충분합니다.

##<a id="test"></a>응용 프로그램 테스트

개발 환경에서 Rails 서버를 시작하려면 다음 단계를 수행하세요.

1. 명령줄 또는 터미널 세션에서 다음 명령을 사용하여 Rails 서버를 시작합니다.

		rails s

	다음과 유사한 결과가 표시됩니다. 웹 서버가 수신 대기 중인 포트를 기록하세요. 아래 예에서는 포트 3000에서 수신 대기 중입니다.

		=> Booting WEBrick
		=> Rails 3.2.12 application starting in development on http://0.0.0.0:3000
		=> Call with -d to detach
		=> Ctrl-C to shutdown server
		[2013-03-12 19:11:31] INFO  WEBrick 1.3.1
		[2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
		[2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2. 브라우저를 열고 http://localhost:3000/으로 이동합니다. 다음과 유사한 결과가 표시됩니다.

	![default rails page][default-rails]

	이 페이지는 정적인 시작 페이지입니다. 스캐폴딩 명령으로 생성되는 양식을 보려면 http://localhost:3000/posts로 이동합니다. 다음과 유사한 결과가 표시됩니다.

	![a page listing posts][blog-rails]

	서버 프로세스를 중지하려면 명령줄에서 Ctrl+C를 누릅니다.

##<a id="createvm"></a>Azure 가상 컴퓨터 만들기

[여기][vm-instructions]에 나와 있는 지침을 따라 Linux를 호스트하는 Azure 가상 컴퓨터를 만드세요.

> [AZURE.NOTE] 이 자습서의 단계는 Ubuntu 12.10을 호스트하는 Azure 가상 컴퓨터에서 수행되었습니다. 다른 Linux 배포를 사용하는 경우 동일한 작업을 수행하는 데 다른 단계가 필요할 수 있습니다.

> [AZURE.IMPORTANT] 가상 컴퓨터를 만들기**만** 하면 됩니다. SSH를 사용하여 가상 컴퓨터에 연결하는 방법을 알아본 후 중지하세요.

Azure 가상 컴퓨터를 만든 후 다음 단계를 수행하여 가상 컴퓨터에 Ruby 및 Rails를 설치하세요.

1. 명령줄 또는 터미널 세션에서 다음 명령으로 SSH를 사용하여 가상 컴퓨터에 연결합니다.

		ssh username@vmdns -p port

	VM, VM의 DNS 주소 및 SSH 끝점의 포트를 만드는 동안 지정한 사용자 이름으로 바꾸세요. 예를 들면 다음과 같습니다.

		ssh railsdev@railsvm.cloudapp.net -p 61830

	> [AZURE.NOTE] Windows를 개발 환경으로 사용하는 경우 SSH용 **PuTTY** 기능과 같은 유틸리티를 사용할 수 있습니다. PuTTY는 [PuTTY 다운로드 페이지](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 다운로드할 수 있습니다.

2. SSH 세션에서 다음 명령을 사용하여 VM에 Ruby를 설치합니다.

		sudo apt-get update -y
		sudo apt-get upgrade -y
		sudo apt-get install ruby1.9.1 ruby1.9.1-dev build-essential libsqlite3-dev nodejs -y

	설치를 완료한 후 다음 명령을 사용하여 Ruby가 제대로 설치되었는지 확인합니다.

		ruby -v

	이 명령은 가상 컴퓨터에 설치된 Ruby 버전을 반환합니다. 반환되는 버전은 1.9.1과 다를 수 있습니다. 예를 들어 **ruby 1.9.3p194(2012-04-20 revision 35410) [x86_64-linux]**일 수 있습니다.

2. 다음 명령을 사용하여 번들러를 설치합니다.

		sudo gem install bundler --no-rdoc --no-ri

	서버로 복사한 번들러는 Rails 응용 프로그램에 필요한 gem을 설치하는 데 사용됩니다.

##<a id="copy"></a>VM으로 응용 프로그램 복사

개발 환경에서 새 명령줄 또는 터미널 세션을 열고 **scp** 명령을 사용하여 **blog_app** 디렉터리를 가상 컴퓨터로 복사합니다. 이 명령의 형식은 다음과 같습니다.

	scp -r -P 54822 -C directory-to-copy user@vmdns:

예를 들면 다음과 같습니다.

	scp -r -P 54822 -C ~/blog_app railsdev@railsvm.cloudapp.net:

> [AZURE.NOTE] Windows를 개발 환경으로 사용하는 경우 scp용 **pscp** 기능과 같은 유틸리티를 사용할 수 있습니다. Pscp는 [PuTTY 다운로드 페이지](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 다운로드할 수 있습니다.

이 명령에 사용되는 매개 변수의 결과는 다음과 같습니다.

* **-r**: 지정된 디렉터리 및 모든 하위 디렉터리의 내용을 반복적으로 복사합니다.

* **-P**: SSH 통신에 지정된 포트를 사용합니다.

* **-C**: 압축을 사용합니다.

* **directory-to-copy**: 복사할 로컬 디렉터리입니다.

* **user@vmdns**: 파일을 복사할 컴퓨터의 주소와 로그인하는 데 사용하는 사용자 계정입니다.

복사 작업 후 **blog_app** 디렉터리는 사용자 홈 디렉터리에 있습니다. 가상 컴퓨터의 SSH 세션에서 다음 명령을 사용하여 복사된 파일을 봅니다.

	cd ~/blog_app
	ls

반환되는 파일 목록은 개발 환경의 **blog_app** 디렉터리에 포함된 파일과 일치합니다.

##<a id="start"></a>gem 설치 및 Rails 시작

1. 가상 컴퓨터에서 **blog_app** 디렉터리로 변경하고 다음 명령을 사용하여 **Gemfile**에 지정된 gem을 설치합니다.

		sudo bundle install

2. 데이터베이스를 만들려면 다음 명령을 사용합니다.

		rake db:migrate

3. 다음 명령을 사용하여 서버를 시작합니다.
	
		rails s

	다음과 유사한 결과가 표시됩니다. 웹 서버가 수신 대기 중인 포트를 기록하세요. 아래 예에서는 포트 3000에서 수신 대기 중입니다.

		=> Booting WEBrick
		=> Rails 3.2.12 application starting in development on http://0.0.0.0:3000
		=> Call with -d to detach
		=> Ctrl-C to shutdown server
		[2013-03-12 19:11:31] INFO  WEBrick 1.3.1
		[2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
		[2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2. 브라우저에서 [Azure 관리 포털][management-portal]로 이동하고 가상 컴퓨터를 선택합니다.

	![virtual machine list][vmlist]

3. 페이지 맨 위에서 **끝점**을 선택한 후 페이지 맨 아래에서 **+ 끝점 추가**를 클릭합니다.

	![endpoints page][endpoints]

4. **끝점 추가** 대화 상자 아래 왼쪽에서 화살표를 클릭하여 두 번째 페이지로 진행하고 양식에 다음 정보를 입력합니다.

	* **이름**: HTTP

	* **프로토콜**: TCP

	* **공용 포트**: 80

	* **개인 포트**: &lt;위 3단계의 포트 정보&gt;

	트래픽을 개인 포트 3000으로 라우팅하는 공용 포트 80이 만들어지며, Rails는 이 포트에서 수신 대기합니다.

	![new endpoint dialog][new-endpoint]

5. 확인 표시를 클릭하여 끝점을 저장합니다.

6. **업데이트 진행 중**이라는 메시지가 나타납니다. 이 메시지가 사라지면 끝점이 활성 상태가 됩니다. 이제 가상 컴퓨터의 DNS 이름으로 이동하여 응용 프로그램을 테스트할 수 있습니다. 웹 사이트는 다음과 유사합니다.

	![default rails page][default-rails-cloud]

	URL에 **/posts**를 추가하면 스캐폴딩 명령으로 생성된 페이지가 표시됩니다.

	![posts page][blog-rails-cloud]

##<a id="next"></a>다음 단계

이 문서에서는 기본 양식 기반 Rails 응용 프로그램을 만들어 Azure 가상 컴퓨터에 게시하는 방법을 알아보았습니다. 지금까지 대부분의 작업을 수동으로 처리했지만 프로덕션 환경에서는 자동화하는 것이 좋습니다. 또한 대부분의 프로덕션 환경은 Rails 응용 프로그램을 다른 서버 프로세스(예: Apache 또는 NginX)와 함께 호스트하며 이러한 서버 프로세스는 Rails 응용 프로그램의 여러 인스턴스로 라우팅하여 정적 리소스를 제공하는 요청을 처리합니다.

Rails 응용 프로그램의 자동 배포와 Unicorn 웹 서버 및 NginX 사용에 대한 자세한 내용은 [Unicorn+NginX+Capistrano 및 Azure 가상 컴퓨터][unicorn-nginx-capistrano]를 참조하세요.

Ruby on Rails에 대해 자세히 알아보려면 [Ruby on Rails 가이드][rails-guides]를 참조하세요.

Azure SDK for Ruby를 사용하여 Ruby 응용 프로그램에서 Azure 서비스에 액세스하려면 다음을 참조하세요.

* [Blob를 사용하여 데이터 저장][blobs]

* [테이블을 사용하여 키/값 쌍 저장][tables]

* [콘텐츠 배달 네트워크로 높은 대역폭 콘텐츠 제공][cdn-howto]



<!-- WA.com links -->
[blobs]: /ko-kr/documentation/articles/storage-ruby-how-to-use-blob-storage

[cdn-howto]: /ko-kr/develop/ruby/app-services/

[management-portal]: https://manage.windowsazure.com/

[tables]: /ko-kr/develop/ruby/how-to-guides/table-service/

[unicorn-nginx-capistrano]: /ko-kr/documentation/articles/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/

[vm-instructions]: /ko-kr/documentation/articles/virtual-machines-linux-tutorial


<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/

[sqlite3]: http://www.sqlite.org/

<!-- Images -->
[blog-rails]: ./media/virtual-machines-ruby-rails-web-app-linux/blograilslocal.png

[blog-rails-cloud]: ./media/virtual-machines-ruby-rails-web-app-linux/blograilscloud.png 

[default-rails]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailslocal.png

[default-rails-cloud]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailscloud.png

[vmlist]: ./media/virtual-machines-ruby-rails-web-app-linux/vmlist.png

[endpoints]: ./media/virtual-machines-ruby-rails-web-app-linux/endpoints.png

[new-endpoint]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint.png


<!--HONumber=45--> 
