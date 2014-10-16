<properties linkid="dev-ruby-web-app-with-linux-vm-capistrano" urlDisplayName="Ruby on Rails Azure VM Capistrano" pageTitle="Deploying a Ruby on Rails Web application to an Azure Virtual Machine using Capistrano - tutorial" metaKeywords="ruby on rails, ruby on rails azure, rails azure, rails vm, capistrano azure vm, capistrano azure rails, unicorn azure vm, unicorn azure rails, unicorn nginx capistrano, unicorn nginx capistrano azure, nginx azure" description="Learn how to deploy a Ruby on Rails application to an Azure Virtual Machine using Capistrano, Unicorn and Nginx." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Deploy a Ruby on Rails Web application to an Azure VM using Capistrano" authors="larryfr" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-linux" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

# Capistrano를 사용하여 Azure VM에 Ruby on Rails 웹 응용 프로그램 배포

이 자습서에서는 [Capistrano 3][](영문)을 사용하여 Azure 가상 컴퓨터에 Ruby on Rails 웹 사이트를 배포하는 방법에 대해 설명합니다. 배포한 후에는 [Nginx][] 및 [Unicorn][]을 사용하여 웹 사이트를 호스트합니다. [PostgreSQL][]은 배포된 응용 프로그램의 응용 프로그램 데이터를 저장합니다.

이 자습서에서는 사용자가 전에 Azure를 사용한 적이 없지만 Ruby, Rails, Git 및 Linux에는 친숙하다고 가정합니다. 이 자습서를 완료하면 클라우드에서 Ruby on Rails 기반 응용 프로그램을 실행할 수 있게 됩니다.

다음 방법에 대해 알아봅니다.

-   개발 및 프로덕션 환경 설정

-   Ruby 및 Ruby on Rails 설치

-   Unicorn, Nginx 및 PostgreSQL 설치

-   새 Rails 응용 프로그램 만들기

-   Capistrano 배포 만들기 및 Capistrano 배포를 사용하여 응용 프로그램 배포

다음은 완료된 응용 프로그램의 스크린샷입니다.

![게시물 목록을 표시하는 브라우저][]

> [WACOM.NOTE] 이 자습서에서 사용한 응용 프로그램에는 원시 이진 구성 요소가 포함되어 있습니다. 배포 환경이 Linux 기반이 아닌 경우 VM에 배포할 때 오류가 발생할 수 있습니다. 배포 중 사용된 Gemfile.lock 파일은 플랫폼 관련 gem을 포함하므로, VM에 필요한 gem의 기본 Linux 버전에 대한 항목을 포함하지 않을 수 있습니다.
>
> Windows 배포 환경 사용에 대한 구체적인 단계는 설명하지 않습니다. 그러나 이 문서에서 다루지 않은 오류가 배포 중 또는 배포 후 발생한 경우 Linux 기반 개발 환경에서 이 문서의 단계를 다시 시도해 볼 수 있습니다.

## 이 문서에서는 다음을 수행합니다.

-   [개발 환경 설정][]

-   [Rails 응용 프로그램 만들기][]

-   [응용 프로그램 테스트][]

-   [소스 리포지토리 만들기][]

-   [Azure 가상 컴퓨터 만들기][]

-   [Nginx 테스트][]

-   [배포 준비][]

-   [배포][]

-   [다음 단계][]

## <span id="setup"></span></a>개발 환경 설정

1.  개발 환경에 Ruby를 설치합니다. 운영 체제에 따라 아래 단계는 달라질 수 있습니다.

    -   **Apple OS X** - OS X용 Ruby 배포가 몇 가지 있습니다. OS X에 대한 이 자습서 내용은 [Homebrew][]를 사용하여 **rbenv**, **ruby-build** 및 **Ruby 2.0.0-p451**을 설치함으로써 유효성이 검사되었습니다. 설치 정보는 [][]<https://github.com/sstephenson/rbenv/></a>(영문)에서 볼 수 있습니다.

    -   **Linux** - 배포 패키지 관리 시스템을 사용합니다. 이 자습서 내용은 **rbenv**, **ruby-build** 및 **Ruby 2.0.0-p451**을 사용하여 Ubuntu 12.10에서 유효성이 검사되었습니다.

    -   **Windows** - Windows용 Ruby 배포가 몇 가지 있습니다. 이 자습서의 내용은 [RubyInstaller][]를 사용하여 **Ruby 2.0.0-p451**을 설치함으로써 유효성이 검사되었습니다. [Windows용 Git][]에서 사용 가능한 **GitBash** 명령줄을 사용하여 명령을 실행했습니다.

2.  새 명령줄 또는 터미널 세션을 열고 다음 명령을 입력하여 Ruby on Rails를 설치합니다.

        gem install rails --no-rdoc --no-ri

    > [WACOM.NOTE] 일부 운영 체제에서 이 명령을 사용하려면 관리자 또는 루트 권한이 필요할 수 있습니다. 이 명령을 실행하는 동안 오류가 발생한 경우 다음과 같이 'sudo'를 사용해 보세요.
    >
    > `sudo gem install rails`

    > [WACOM.NOTE] 이 자습서에서는 Rails gem의 버전 4.0.4를 사용했습니다.

3.  또한 JavaScript 인터프리터를 설치해야 합니다. Rails에서 Rails 응용 프로그램에 사용되는 CoffeeScript 자산을 컴파일하는 데 이 인터프리터를 사용합니다. 지원되는 인터프리터의 목록은 [][1]<https://github.com/sstephenson/execjs#readme></a>(영문)에서 확인할 수 있습니다.

    > [WACOM.NOTE] [Node.js](<http://nodejs.org/>)가 OS X, Linux 및 Windows 운영 체제에서 사용 가능하므로, 이 자습서에서는 Node.js를 사용했습니다.

## <span id="create"></span></a>Rails 응용 프로그램 만들기

1.  명령줄 또는 터미널 세션에서 다음 명령을 사용하여 "blog\_app"이라는 새 Rails 응용 프로그램을 만듭니다.

        rails new blog_app

    이 명령은 **blog\_app**이라는 새 디렉터리를 만들고 Rails 응용 프로그램에 필요한 파일 및 하위 디렉터리로 채웁니다.

    > [WACOM.NOTE] 이 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 이 명령은 기본 응용 프로그램에 필요한 gem을 자동으로 설치하며 이 기간 중 응답하지 않습니다.

2.  **blog\_app** 디렉터리로 변경한 후 다음 명령을 사용하여 기본 블로그 스캐폴딩을 만듭니다.

        rails generate scaffold Post name:string title:string content:text

    이 명령은 블로그에 게시물을 유지하는 데 사용되는 컨트롤러, 보기, 모델 및 데이터베이스 마이그레이션을 만듭니다. 각 게시물에는 만든 이 이름, 게시물의 제목 및 텍스트 내용이 포함됩니다.

3.  블로그 게시물을 저장할 데이터베이스를 만들려면 다음 명령을 사용합니다.

        rake db:migrate

    이제 Rails용 기본 데이터베이스 공급자를 사용하여 게시물을 저장하는 데이터베이스 스키마인 [SQLite3 Database][]가 생성됩니다.

4.  게시물의 인덱스를 홈페이지로 표시하려면 **config/routes.rb** 파일을 수정하고 `resources :posts` 줄 뒤에 다음을 추가하세요.

        root 'posts#index'

    이제 사용자가 웹 사이트를 방문할 때 게시물의 목록이 표시됩니다.

## <span id="test"></span></a>응용 프로그램 테스트

1.  디렉터리를 아직 변경하지 않은 경우 **blog\_app** 디렉터리로 변경하고 다음 명령을 사용하여 Rails 서버를 시작합니다.

        rails s

    다음과 유사한 결과가 표시됩니다. 웹 서버가 수신 대기 중인 포트를 기록하세요. 아래 예에서는 포트 3000에서 수신 대기 중입니다.

        => Booting WEBrick
        => Rails 4.0.4 application starting in development on http://0.0.0.0:3000
        => Call with -d to detach
        => Ctrl-C to shutdown server
        [2013-03-12 19:11:31] INFO  WEBrick 1.3.1
        [2013-03-12 19:11:31] INFO  ruby 2.0.0 (2014-02-24) [x86_64-linux]
        [2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2.  브라우저를 열고 http://localhost:3000/으로 이동합니다. 다음과 유사한 페이지가 표시됩니다.

    ![게시물을 나열하는 페이지][]

    서버 프로세스를 중지하려면 명령줄에서 Ctrl+C를 누릅니다.

## <span id="repository"></span></a>소스 리포지토리 만들기

Capistrano를 사용하여 응용 프로그램을 배포할 때 리포지토리에서 파일을 가져옵니다. 이 자습서에서는 버전 제어에 [Git][]을, 리포지토리에 [GitHub][]를 사용합니다.

1.  [GitHub][]에서 새 리포지토리를 만듭니다. 현재 GitHub 계정이 없는 경우 무료 계정을 등록할 수 있습니다. 다음 단계에서는 리포지토리 이름이 **blog\_app**인 것으로 가정합니다.

    > [WACOM.NOTE] 응용 프로그램의 자동화된 배포를 지원하려면 SSH 키를 사용하여 GitHub에 인증해야 합니다. 자세한 내용은 [SSH 키 생성][](영문)의 GitHub 문서를 참조하세요.

2.  명령 프롬프트에서 **blog\_app** 디렉터리로 변경하고 다음 명령을 실행하여 GitHub 리포지토리에 응용 프로그램을 업로드합니다. **YourGitHubName**을 GitHub 계정의 이름으로 바꿉니다.

        git init
        git add .
        git commit -m "initial commit on azure"
        git remote add origin git@github.com:YourGitHubName/blog-azure.git
        git push -u origin master

다음 섹션에서는 이 응용 프로그램을 배포할 가상 컴퓨터를 만듭니다.

## <span id="createvm"></span></a>Azure 가상 컴퓨터 만들기

[여기][]에 나와 있는 지침을 따라 Linux를 호스트하는 Azure 가상 컴퓨터를 만드세요.

1.  [Azure 관리 포털][]에 로그인합니다. 명령 모음에서 **새로 만들기**를 선택합니다.

2.  **가상 컴퓨터**를 클릭한 다음 **갤러리에서**를 클릭합니다.

3.  **이미지 선택**에서 **Ubuntu**를 선택하고 버전 **12.04 LTS**를 선택합니다. 화살표를 선택하여 계속합니다.

    > [WACOM.NOTE] 이 자습서의 단계는 Ubuntu 12.04 LTS를 호스트하는 Azure 가상 컴퓨터에서 수행되었습니다. 다른 Linux 배포를 사용하는 경우 동일한 작업을 수행하는 데 다른 단계가 필요할 수 있습니다.

4.  **가상 컴퓨터 이름**에서 사용할 이름을 입력합니다. 이 이름은 이 가상 컴퓨터의 도메인 이름을 만드는 데 사용됩니다.

5.  **새 사용자 이름**에서, 이 컴퓨터의 관리자 계정 이름을 입력합니다.

    > [WACOM.NOTE] 이 자습서에서는 관리자 계정이 응용 프로그램을 배포하는 데에도 사용됩니다. 배포용으로 별도 계정 만들기에 대한 자세한 내용은 [Capistrano][](영문) 문서를 참조하세요.

6.  **인증**에서, **인증을 위한 호환 가능한 SSH 키 업로드**를 선택하고 인증서를 포함하는 **.pem** 파일을 찾아서 선택합니다. 마지막으로 화살표를 선택하여 계속합니다.

    > [WACOM.NOTE] SSH 키 생성이나 사용에 친숙한 경우 SSH 키 만들기에 대한 자세한 내용은 [Azure에서 Linux와 SSH를 사용하는 방법][](영문)을 참조하세요.
    >
    > 또한 암호 인증을 사용하도록 설정할 수 있지만 SSH 키가 배포를 자동화하는 데 사용되므로 SSH 키도 제공해야 합니다.

7.  **끝점**에서 **값을 입력하거나 선택** 드롭다운을 사용하여 **HTTP**를 선택합니다. 이 페이지의 다른 필드는 기본값으로 둘 수 있습니다. **클라우드 서비스 DNS 이름**은 이후 단계에 사용되므로, 이 값을 기록해 둡니다. 마지막으로 화살표를 선택하여 계속합니다.

8.  마지막 페이지에서 확인 표시를 선택하여 가상 컴퓨터를 만듭니다.

### Git, Ruby 및 Nginx 설치

가상 컴퓨터를 만든 후 SSH를 사용하여 가상 컴퓨터에 연결하고 다음 명령을 사용하여 Ruby 응용 프로그램의 호스팅 환경을 준비합니다.

    sudo apt-get update
    sudo apt-get -y upgrade
    sudo apt-get -y install git build-essential libssl-dev curl nodejs nginx
    git clone git://github.com/sstephenson/rbenv.git ~/.rbenv
    echo 'export RBENV_ROOT=~/.rbenv' >> ~/.bash_profile
    echo 'export PATH="$RBENV_ROOT/bin:$PATH"' >> ~/.bash_profile
    echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
    source ~/.bash_profile
    eval "$(rbenv init -)"
    git clone git://github.com/sstephenson/ruby-build.git
    cd ruby-build
    sudo ./install.sh
    ~/.rbenv/bin/rbenv install 2.0.0-p451
    ~/.rbenv/bin/rbenv global 2.0.0-p451
    gem install bundler
    ~/.rbenv/bin/rbenv rehash

> [WACOM.NOTE]명령을 실행할 때 오타를 방지하기 위해 위의 명령을 스크립트(.sh file,)에 저장할 수 있습니다.

> [WACOM.NOTE] **~/.rbenv/bin/rbenv install 2.0.0-p451** 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

**rbenv-install.sh** 스크립트는 다음 작업을 수행합니다.

-   현재 설치된 패키지 업데이트 및 업그레이드
-   빌드 도구 설치
-   Git 설치
-   Node.js 설치
-   Nginx 설치
-   빌드 도구와 Ruby 및 Rails에 필요한 기타 유틸리티 설치
-   [rbenv]의 로컬 (사용자) 배포 설정
-   Ruby 2.0.0-p451 설치
-   [Bundler] gem 설치

설치를 완료한 후 다음 명령을 사용하여 Ruby가 제대로 설치되었는지 확인합니다.

    ruby -v

그러면 `ruby 2.0.0p451`이 버전으로 반환됩니다.

### PostgreSQL 설치

개발을 위해 Rails에 사용되는 기본 데이터베이스는 SQLite입니다. 일반적으로 프로덕션 환경에서는 다른 데이터베이스를 사용합니다. 다음 단계에서는 가상 컴퓨터에 PostgreSQL을 설치한 다음 사용자 및 데이터베이스를 만듭니다. 뒷부분의 단계에서는 배포 중 PostgreSQL을 사용하도록 Rails 응용 프로그램을 구성합니다.

1.  다음 명령을 사용하여 PostgreSQL 및 배포 비트를 설치합니다.

        sudo apt-get -y install postgresql postgresql-contrib libpq-dev

2.  다음 명령을 사용하여 로그인을 위한 사용자 및 데이터베이스를 만듭니다. **my\_username** 및 **my\_database**를 로그인 이름으로 바꿉니다. 예를 들어 VM의 로그인이 **deploy**인 경우 **my\_username** 및 **my\_database**를 **deploy**로 바꿉니다.

        sudo -u postgres createuser -D -A -P my_username
        sudo -u postgres createdb -O my_username my_database

    > [WACOM.NOTE] 데이터베이스 이름의 사용자 이름도 사용합니다. 이 사용자 이름은 이 응용 프로그램에 사용되는 capistrano-postgresql gem에 필요합니다.

    메시지가 표시되면 사용자의 암호를 입력합니다. 사용자에게 새 역할을 만들도록 허용할 것인지 묻는 메시지가 표시되면 이 사용자가 배포 중 Rails 응용 프로그램에 사용되는 데이터베이스 및 로그인을 만드는 데 사용되므로 **y**를 선택합니다.

3.  사용자 계정을 사용하여 데이터베이스에 연결할 수 있는지 확인하려면 다음 명령을 사용합니다. **my\_username** 및 **my\_database**를 앞서 사용한 값으로 바꿉니다.

        psql -U my_username -W my_database

    `database=>` 프롬프트가 표시됩니다. psql 유틸리티를 종료하려면 프롬프트에서 `\q`를 입력합니다.

### <span id="nginx"></span></a>Nginx 테스트

가상 컴퓨터를 만드는 동안 추가된 HTTP 끝점에서 포트 80을 통한 HTTP 요청을 허용하도록 합니다. 이를 확인하려면 다음 단계에 따라 Nginx에서 만든 기본 사이트에 액세스할 수 있는지 확인합니다.

1.  VM을 사용하여 SSH 세션에서 다음과 같이 Nginx 서비스를 시작합니다.

        sudo service nginx start

    이 명령은 Nginx 서비스를 시작하여 포트 80에서 들어오는 트래픽을 수신합니다.

2.  가상 컴퓨터의 DNS 이름으로 이동하여 응용 프로그램을 테스트합니다. 웹 사이트는 다음과 유사합니다.

    ![nginx 시작 페이지][]

    > [WACOM.NOTE] 이 자습서의 뒷부분에서 사용되는 배포 스크립트는 blog\_app을 Nginx에서 제공하는 기본 웹 사이트로 만듭니다.

이제 배포를 위해 Ruby, Nginx 및 PostgreSQL이 설치된 Azure 가상 컴퓨터가 준비되었습니다. 다음 섹션에서는 배포를 수행하는 스크립트 및 정보를 추가하도록 Rails 응용 프로그램을 수정합니다.

## <span id="capify"></span></a>배포 준비

배포 환경에서 Unicorn 웹 서버인 PostgreSQL을 사용하도록 응용 프로그램을 수정하고, 배포에 Capistrano를 사용하도록 설정하고, 응용 프로그램을 배포하고 시작하는 데 사용되는 스크립트를 만듭니다.

1.  배포 컴퓨터에서 **Gemfile**을 수정하고 다음 줄을 추가하여 Unicorn, PostgreSQL 및 Capistrano용 gem과 관련 gem을 Rails 응용 프로그램에 추가합니다.

        # Use Unicorn
        gem 'unicorn'
        # Use PostgreSQL
        gem 'pg', group: :production

        group :development do
          # Use Capistrano for deployment
          gem 'capistrano', '~> 3.1'
          gem 'capistrano-rails', '~> 1.1.1'
          gem 'capistrano-bundler'
          gem 'capistrano-rbenv', '~> 2.0'
          gem 'capistrano-unicorn-nginx', '~> 2.0'
          gem 'capistrano-postgresql', '~> 3.0'
        end

    > [WACOM.NOTE] Unicorn은 Windows에서 사용할 수 없습니다. 개발 환경으로 Windows를 사용하는 경우 Unicorn gem을 지정할 때 다음을 사용하여 VM에 배포할 때에만 Unicorn을 설치하도록 **Gemfile**을 수정하세요.
    >
    > `platforms :ruby do`
    > `gem 'unicorn'`
    > `end`

    대부분의 capistraon-\* gem은 프로덕션 서버(rbenv) 또는 프레임워크(rails)에서 사용할 특정 항목에서 작동하는 도우미입니다.

    capistrano-unicorn-nginx gem은 배포 중 Unicorn 및 Nginx에서 사용되는 스크립트를 자동으로 생성하므로 이러한 스크립트를 수동으로 생성할 필요가 없습니다. capistrano-postgresql은 PostgreSQL에서 응용 프로그램의 데이터베이스, 사용자 및 암호를 자동으로 생성합니다. 이러한 항목을 사용할 필요가 없더라도 배포 프로세스가 훨씬 간소해집니다.

2.  다음 명령을 사용하여 새로운 gem을 설치합니다.

        bundle

3.  다음 명령을 사용하여 Capistrano에 사용되는 기본 배포 파일을 만듭니다.

        cap install

    `cap install` 명령이 완료된 후 다음 파일 및 디렉터리가 응용 프로그램에 추가됩니다.

        ├── Capfile
        ├── config
        │   ├── deploy
        │   │   ├── production.rb
        │   │   └── staging.rb
        │   └── deploy.rb
        └── lib
            └── capistrano
                    └── tasks

    **deploy.rb** 파일은 배포의 모든 유형에 대한 일반 구성 및 작업을 제공하고, **production.rb** 및 **staging.rb**는 프로덕션 및 스테이징 배포를 위한 추가 구성을 포함합니다.

    **capistrano** 폴더에는 배포 프로세스의 일부로 사용되는 작업 및 기타 파일이 포함됩니다.

4.  응용 프로그램의 루트에 있는 **Capfile**을 편집해 다음 줄의 시작 부분에서 **\#** 문자를 제거하여 주석 처리를 제거합니다.

        require 'capistrano/rbenv'
        require 'capistrano/bundler'
        require 'capistrano/rails/assets'
        require 'capistrano/rails/migrations'

    위의 줄에 대한 주석 처리를 제거한 후 다음 줄을 추가합니다.

        require 'capistrano/unicorn_nginx'
        require 'capistrano/postgresql'

    이러한 줄은 앞서 Gemfile에 추가한 gem을 사용하도록 Capistrano에 지시합니다.

    위와 같이 변경한 후 파일을 저장합니다.

5.  **config/deploy.rb** 파일을 편집하여 파일 콘텐츠를 다음으로 바꿉니다. **YourApplicationName**을 응용 프로그램의 이름으로 바꾸고 **<https://github.com/YourGitHubName/YourRepoName.git>**을 이 프로젝트의 GitHub 리포지토리 URL로 바꿉니다.

        lock '3.1.0'
        # application name and the github repository
        set :application, 'YourApplicationName'
        set :repo_url, 'git@github.com:YourGitHubName/YourRepoName.git'

        # describe the rbenv environment we are deploying into
        set :rbenv_type, :user
        set :rbenv_ruby, '2.0.0-p451'
        set :rbenv_prefix, "RBENV_ROOT=#{fetch(:rbenv_path)} RBENV_VERSION=#{fetch(:rbenv_ruby)} #{fetch(:rbenv_path)}/bin/rbenv exec"
        set :rbenv_map_bins, %w{rake gem bundle ruby rails}

        # dirs we want symlinked to the shared folder
        # during deployment
        set :linked_dirs, %w{bin log tmp/pids tmp/cache tmp/sockets vendor/bundle public/system}

        namespace :deploy do

          desc 'Restart application'
          task :restart do
            on roles(:app), in: :sequence, wait: 5 do
              # Your restart mechanism here, for example:
              # execute :touch, release_path.join('tmp/restart.txt')
              #
              # The capistrano-unicorn-nginx gem handles all this
              # for this example
            end
          end

          after :publishing, :restart

          after :restart, :clear_cache do
            on roles(:web), in: :groups, limit: 3, wait: 10 do
              # Here we can do anything such as:
              # within release_path do
              #   execute :rake, 'cache:clear'
              # end
            end
          end

        end

    이 파일은 모든 배포 유형에 공통된 일반 정보 및 작업을 제공합니다.

6.  **config/deploy/production.rb** 파일을 편집하고 기존 콘텐츠를 다음으로 바꿉니다. **YourVm.cloudapp.net**을 VM의 도메인 이름으로 바꿉니다. **YourAzureVMUserName**을 Azure VM에 대해 앞서 생성한 로그인 계정으로 바꿉니다.

        # production deployment
        set :stage, :production
        # use the master branch of the repository
        set :branch, "master"
        # the user login on the remote server
        # used to connect and deploy
        set :deploy_user, "YourAzureVMUserName"
        # the 'full name' of the application
        set :full_app_name, "#{fetch(:application)}_#{fetch(:stage)}"
        # the server(s) to deploy to
        server 'YourVM.cloudapp.net', user: 'YourAzureVMUserName', roles: %w{web app db}, primary: true
        # the path to deploy to
        set :deploy_to, "/home/#{fetch(:deploy_user)}/apps/#{fetch(:full_app_name)}"
        # set to production for Rails
        set :rails_env, :production

    이 파일은 프로덕션 배포와 관련된 정보를 제공합니다.

7.  다음 명령을 실행하여 앞의 단계에서 수정한 파일의 변경 내용을 커밋한 다음 GitHub에 변경 내용을 업로드합니다.

        git add .
        git commit -m "adding config files"
        git push

이제 응용 프로그램을 배포할 수 있습니다.

> [WACOM.NOTE] 더 복잡한 응용 프로그램이나 다른 데이터베이스 또는 응용 프로그램 서버의 경우 추가 구성이나 배포 스크립트가 필요할 수 있습니다.

## <span id="deploy"></span></a>배포

1.  로컬 개발 컴퓨터에서 다음 명령을 사용하여 응용 프로그램에 사용되는 구성 파일을 VM에 배포합니다.

        bundle exec cap production setup

    Capistrano는 SSH를 사용하여 VM에 연결한 다음 응용 프로그램을 배포할 디렉터리(~/apps)를 만듭니다. 처음 배포하는 경우 capistrano-postgresql gem에서 서버의 PostgreSQL에 역할 및 데이터베이스도 만듭니다. 또한 Rails에서 데이터베이스에 연결하는 데 사용하는 database.yml 구성 파일도 만듭니다.

    > [WACOM.NOTE] 배포할 때 **Error reading response length from authentication socket** 오류가 발생하면 개발 환경에서 `ssh-agent` 명령을 사용하여 SSH 에이전트를 시작해야 할 수 있습니다. 예를 들어 ~/.bash\_profile 파일에 `eval $(ssh-agent)`를 추가합니다.
    >
    > 또한 `ssh-add` 명령을 사용하여 에이전트 캐시에 SSH 키를 추가해야 할 수도 있습니다.

2.  다음 명령을 사용하여 프로덕션 배포를 수행합니다. 그러면 가상 컴퓨터에 응용 프로그램이 배포되고, Unicorn 서비스가 시작되고, Unicorn으로 트래픽을 라우팅하도록 Nginx가 구성됩니다.

        bundle exec cap production deploy

    이 명령은 VM에 응용 프로그램을 배포하고, 필요한 gem을 설치하고, Unicorn 및 Nginx를 시작/다시 시작합니다.

    > [WACOM.NOTE] 처리 중 이 프로세스는 몇 분 정도 일시 중지될 수 있습니다.

    > [WACOM.NOTE] 배포 중 일부에서는 '종료 상태 1(실패)'을 반환할 수 있습니다. 배포가 완료된다면 이러한 오류는 일반적으로 무시할 수 있습니다.

    > [WACOM.NOTE] 일부 시스템에서는 GitHub에 인증할 때 SSH 에이전트가 원격 VM에 자격 증명을 전달할 수 없는 상황이 발생할 수 있습니다. 이런 경우 **config/deploy.rb** 파일을 수정하여 오류를 해결하고 Github에 액세스할 때 HTTPS를 사용하도록 `set :repo_url` 줄을 변경할 수 있습니다. HTTPS를 사용할 경우 GitHub 사용자 이름 및 암호(또는 인증 토큰)를 URL의 일부로 지정해야 합니다. 예를 들면 다음과 같습니다.
    >
    > \`set :repo\_url, '<https://you:yourpassword@github.com/You/yourrepository.git>'
    >
    > 이렇게 하면 오류를 무시하고 이 자습서를 완료할 수 있지만 인증 자격 증명이 응용 프로그램의 일부로 일반 텍스트에 저장되므로 프로덕션 배포의 권장 솔루션은 아닙니다. SSH 에이전트에서 전달 사용에 대해서는 운영 체제에 대한 설명서를 참조하세요.

이때 Ruby on Rails 응용 프로그램이 Azure 가상 컴퓨터에서 실행되고 있어야 합니다. 이를 확인하려면 웹 브라우저에서 가상 컴퓨터의 DNS 이름을 입력합니다. 예를 들어 <http://railsvm.cloudapp.net>을 입력합니다. 게시물 인덱스가 나타나며 게시물을 만들고 편집하고 삭제할 수 있습니다.

## <span id="next"></span></a>다음 단계

이 문서에서는 기본 Rails 응용 프로그램을 만들고 Capistrano를 사용하여 Azure 가상 컴퓨터에 게시하는 방법을 알아보았습니다. 이 문서에 언급된 응용 프로그램과 같은 기본 응용 프로그램을 사용한 작업은 배포에 Capistrano를 사용하여 수행할 수 있는 작업과 비교했을 때 빙산의 일각에 지나지 않습니다. Capistrano 사용에 대한 자세한 내용은 다음을 참조하세요.

-   [Capistranorb.com][Capistrano] - Capistrano 사이트입니다.
-   [Azure, Ruby on Rails, Capistrano 3 및 PostgreSQL][](영문) - Azure에 배포할 수 있는 다른 방법으로, 사용자 지정 배포 스크립트를 사용합니다.
-   [Capistrano 3 자습서][](영문) - Capistrano 3 사용에 대한 자습서입니다.

SSH만 사용하여 Azure VM에 Rails 응용 프로그램을 만들어 배포하는 추가적인 기본 예제에 대해서는 [Linux 가상 컴퓨터를 사용하여 Ruby on Rails 웹 응용 프로그램 호스트][](영문)를 참조하세요.

Ruby on Rails에 대해 자세히 알아보려면 [Ruby on Rails 가이드][](영문)를 참조하세요.

Azure SDK for Ruby를 사용하여 Ruby 응용 프로그램에서 Azure 서비스에 액세스하려면 다음을 참조하세요.

-   [Blob을 사용하여 데이터 저장][]

-   [테이블을 사용하여 키/값 쌍 저장(영문)][]

-   [콘텐츠 배달 네트워크로 높은 대역폭 콘텐츠 제공][]

  [Capistrano 3]: https://github.com/capistrano/capistrano/
  [Nginx]: http://nginx.org/
  [Unicorn]: https://github.com/blog/517-unicorn
  [PostgreSQL]: https://www.postgresql.org
  [게시물 목록을 표시하는 브라우저]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilscloud.png
  [개발 환경 설정]: #setup
  [Rails 응용 프로그램 만들기]: #create
  [응용 프로그램 테스트]: #test
  [소스 리포지토리 만들기]: #repository
  [Azure 가상 컴퓨터 만들기]: #createvm
  [Nginx 테스트]: #nginx
  [배포 준비]: #capify
  [배포]: #deploy
  [다음 단계]: #next
  [Homebrew]: http://brew.sh/
  []: https://github.com/sstephenson/rbenv/
  [RubyInstaller]: http://RubyInstaller.org/
  [Windows용 Git]: http://git-scm.com/download/win
  [1]: https://github.com/sstephenson/execjs#readme
  [SQLite3 Database]: http://www.sqlite.org/
  [게시물을 나열하는 페이지]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilslocal.png
  [Git]: http://git-scm.com/
  [GitHub]: https://github.com/
  [SSH 키 생성]: https://help.github.com/articles/generating-ssh-keys
  [여기]: /en-us/manage/linux/tutorials/virtual-machine-from-gallery/
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [Capistrano]: http://capistranorb.com
  [Azure에서 Linux와 SSH를 사용하는 방법]: http://azure.microsoft.com/ko-kr/documentation/articles/linux-use-ssh-key/
  [nginx 시작 페이지]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/welcomenginx.png
  [Azure, Ruby on Rails, Capistrano 3 및 PostgreSQL]: http://wootstudio.ca/articles/tutorial-windows-azure-ruby-on-rails-capistrano-3-postgresql
  [Capistrano 3 자습서]: http://www.talkingquickly.co.uk/2014/01/deploying-rails-apps-to-a-vps-with-capistrano-v3/
  [Linux 가상 컴퓨터를 사용하여 Ruby on Rails 웹 응용 프로그램 호스트]: /en-us/develop/ruby/tutorials/web-app-with-linux-vm/
  [Ruby on Rails 가이드]: http://guides.rubyonrails.org/
  [Blob을 사용하여 데이터 저장]: /en-us/develop/ruby/how-to-guides/blob-storage/
  [테이블을 사용하여 키/값 쌍 저장(영문)]: /en-us/develop/ruby/how-to-guides/table-service/
  [콘텐츠 배달 네트워크로 높은 대역폭 콘텐츠 제공]: /en-us/develop/ruby/app-services/
