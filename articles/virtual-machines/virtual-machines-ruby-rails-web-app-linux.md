<properties 
	pageTitle="Azure에서 Linux VM을 사용하여 Ruby on Rails 웹 앱 호스트" 
	description="Azure에서 Linux 가상 컴퓨터를 사용하여 Ruby on Rails 기반 웹 사이트를 호스트하는 방법에 대해 알아봅니다." 
	services="virtual-machines" 
	documentationCenter="ruby" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="mwasson"/>





#Azure VM의 Ruby on Rails 웹 응용 프로그램

이 자습서에서는 Azure에서 Linux 가상 컴퓨터를 사용하여 Ruby on Rails 웹 사이트를 호스트하는 방법을 보여줍니다.

이 자습서의 내용은 Ubuntu Server 14.04 LTS를 사용하여 유효성이 검사되었습니다. 다른 Linux 배포를 사용하는 경우, Rails를 설치하는 단계를 수정해야 합니다.

## Azure VM 만들기

Linux 이미지와 Azure VM을 만들어 시작합니다.

VM을 만들려면 Azure 관리 포털 또는 Azure 명령줄 인터페이스(CLI)를 사용할 수 있습니다.

### Azure 관리 포털

1. [Azure 관리 포털](http://manage.windowsazure.com)에 로그인합니다.
2. **새로 만들기** > **계산** > **가상 컴퓨터** > **빨리 만들기**를 클릭합니다. Linux 이미지를 선택합니다.
3. 암호를 입력합니다.

VM을 프로비전하면 VM 이름을 클릭하고 **대시보드**를 클릭합니다. **SSH 세부 정보**에 나열된 SSH 끝점을 찾습니다.

### Azure CLI

[Linux를 실행하는 가상 컴퓨터 만들기][vm-instructions]의 단계를 따릅니다.

VM이 프로비전된 후, 다음 명령을 실행하여 SSH 끝점을 가져올 수 있습니다.

	azure vm endpoint list <vm-name>  

## Rails에 Ruby 설치

1. SSH를 사용하여 VM에 연결합니다. 
	
2. SSH 세션에서 다음 명령을 사용하여 VM에 Ruby를 설치합니다.

		sudo apt-get update -y
		sudo apt-get upgrade -y
		sudo apt-get install ruby ruby-dev build-essential libsqlite3-dev zlib1g-dev nodejs -y

	설치는 몇 분 정도 걸릴 수 있습니다. 완료되면 다음 명령을 사용하여 Ruby가 설치되어 있는지 확인합니다.

		ruby -v

	설치된 Ruby 버전을 반환합니다.

3. 다음 명령을 사용하여 Rails를 설치합니다.

		sudo gem install rails --no-rdoc --no-ri

	\--no-rdoc 및 --no-ri 플래그를 사용하여 설명서 설치를 건너뜁니다. 이 방법이 더 빠릅니다.

## 앱 만들기 및 실행

SSH를 통해 로그인한 경우.다음 명령을 실행합니다.

	rails new myapp
	cd myapp
	rails server -b 0.0.0.0 -p 3000

[새](http://guides.rubyonrails.org/command_line.html#rails-new) 명령은 새 Rails 앱을 만듭니다. [서버](http://guides.rubyonrails.org/command_line.html#rails-server) 명령은 Rails와 함께 제공되는 WEBrick 웹 서버를 시작합니다. (프로덕션에서 사용하기 위해, 유니콘 또는 승객과 같은 다른 서버를 사용할 수 있습니다.)

다음과 유사한 결과가 표시됩니다.

	=> Booting WEBrick
	=> Rails 4.2.1 application starting in development on http://0.0.0.0:3000
	=> Run `rails server -h` for more startup options
	=> Ctrl-C to shutdown server
	[2015-06-09 23:34:23] INFO  WEBrick 1.3.1
	[2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
	[2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000


## 끝점 추가

1. [Azure 관리 포털][management-portal]로 이동한 다음 VM을 선택합니다.

	![가상 컴퓨터 목록][vmlist]

2. 페이지 맨 위에서 **끝점**을 선택한 후 페이지 맨 아래에서 **+ 끝점 추가**를 클릭합니다.

	![끝점 페이지][endpoints]

3. **끝점 추가** 대화 상자에서 "독립형 끝점 추가"를 선택하고 **다음** 화살표를 클릭합니다.

	![새 끝점 대화 상자][new-endpoint1]

3. 다음 대화 페이지에서 다음 정보를 입력합니다.

	* **이름**: HTTP

	* **프로토콜**: TCP

	* **공용 포트**: 80

	* **개인 포트**: 3000

	트래픽을 개인 포트 3000으로 라우팅하는 공용 포트 80이 만들어지며, Rails는 이 포트에서 수신 대기합니다.

	![새 끝점 대화 상자][new-endpoint]

4. 확인 표시를 클릭하여 끝점을 저장합니다.

5. **업데이트하는 중**이라는 메시지가 나타납니다. 이 메시지가 사라지면 끝점이 활성 상태가 됩니다. 이제 가상 컴퓨터의 DNS 이름으로 이동하여 응용 프로그램을 테스트할 수 있습니다. 웹 사이트는 다음과 유사합니다.

	![기본 Rails 페이지][default-rails-cloud]


##<a id="next"></a>다음 단계

이 자습서에서는 수동으로 대부분의 단계를 수행했습니다. 프로덕션 환경의 개발 컴퓨터에 앱을 작성하고 Azure VM에 배포합니다. 또한 대부분의 프로덕션 환경은 Rails 응용 프로그램을 다른 서버 프로세스(예: Apache 또는 NginX)와 함께 호스트하며 이러한 서버 프로세스는 Rails 응용 프로그램의 여러 인스턴스로 라우팅하여 정적 리소스를 제공하는 요청을 처리합니다. 자세한 내용은 http://rubyonrails.org/deploy/을 참조하세요.

Ruby on Rails에 대해 자세히 알아보려면 [Ruby on Rails 가이드][rails-guides](영문)를 방문하세요.

Ruby 응용 프로그램에서 Azure 서비스를 사용하려면 다음을 참조하세요.

* [Blob를 사용하여 구조화되지 않은 데이터 저장][blobs]

* [테이블을 사용하여 키/값 쌍 저장][tables]

* [콘텐츠 배달 네트워크로 높은 대역폭 콘텐츠 제공][cdn-howto]



<!-- WA.com links -->
[blobs]: ../storage-ruby-how-to-use-blob-storage.md

[cdn-howto]: /develop/ruby/app-services/

[management-portal]: https://manage.windowsazure.com/

[tables]: /develop/ruby/how-to-guides/table-service/

[vm-instructions]: virtual-machines-linux-tutorial.md


<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/

[sqlite3]: http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailscloud.png

[vmlist]: ./media/virtual-machines-ruby-rails-web-app-linux/vmlist.png

[endpoints]: ./media/virtual-machines-ruby-rails-web-app-linux/endpoints.png

[new-endpoint]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint.png

[new-endpoint1]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint1.png
 

<!---HONumber=August15_HO6-->