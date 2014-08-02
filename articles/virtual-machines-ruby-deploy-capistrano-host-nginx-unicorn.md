<properties linkid="dev-ruby-web-app-with-linux-vm-capistrano" urlDisplayName="Ruby on Rails Azure VM Capistrano" pageTitle="Deploying a Ruby on Rails Web application to an Azure Virtual Machine using Capistrano - tutorial" metaKeywords="ruby on rails, ruby on rails azure, rails azure, rails vm, capistrano azure vm, capistrano azure rails, unicorn azure vm, unicorn azure rails, unicorn nginx capistrano, unicorn nginx capistrano azure, nginx azure" description="Learn how to deploy a Ruby on Rails application to an Azure Virtual Machine using Capistrano, Unicorn and Nginx." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Deploy a Ruby on Rails Web application to an Azure VM using Capistrano" authors="" />

Capistrano를 사용하여 Azure VM에 Ruby on Rails 웹 응용 프로그램 배포
====================================================================

이 자습서에서는 [Capistrano](https://github.com/capistrano/capistrano/)(영문)를 사용하여 Azure 가상 컴퓨터에 Ruby on Rails 기반 웹 사이트를 배포하는 방법에 대해 설명합니다. 또한 이 자습서에서는 [Nginx](http://nginx.org/)(영문) 및 [Unicorn](https://github.com/blog/517-unicorn)(영문)을 사용하여 가상 컴퓨터에서 응용 프로그램을 호스트하는 방법에 대해서도 설명합니다.

이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 이 자습서를 완료하면 클라우드에서 Ruby on Rails 기반 응용 프로그램을 실행할 수 있게 됩니다.

다음 방법에 대해 알아봅니다.

-   개발 환경 설정

-   Ruby 및 Ruby on Rails 설치

-   Nginx 및 Unicorn 설치 및 구성

-   새 Rails 응용 프로그램 만들기

-   Capistrano를 사용하여 Azure 가상 컴퓨터에 Rails 응용 프로그램 배포

다음은 완료된 응용 프로그램의 스크린샷입니다.

![게시물 목록을 표시하는 브라우저](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilscloud.png)

**참고**

이 자습서에서 사용한 응용 프로그램에는 원시 이진 구성 요소가 포함되어 있습니다. 이런 이유로 개발 환경이 Linux 기반이 아닌 경우 개발 컴퓨터에서 생성한 Gemfile.lock에 Linux 호환 버전의 필수 gem에 대한 항목이 포함되지 않을 수 있으므로 문제가 발생할 수 있습니다.

특정 단계에서는 Windows 개발 환경을 사용할 경우에 대해 설명했습니다. Windows 환경은 대상 배포 환경과 가장 중요한 차이를 나타내기 때문입니다. 그러나 이 문서의 단계에서 다루지 않은 오류가 배포 중 또는 후 발생한 경우 Linux 기반 개발 환경에서 이 문서의 단계를 다시 시도해 볼 수 있습니다.

이 문서에서는 다음을 수행합니다.
--------------------------------

-   [개발 환경 설정](#setup)

-   [Rails 응용 프로그램 만들기](#create)

-   [응용 프로그램 테스트](#test)

-   [소스 리포지토리 만들기](#repository)

-   [Azure 가상 컴퓨터 만들기](#createvm)

-   [Nginx 테스트](#nginx)

-   [배포 준비](#capify)

-   [배포](#deploy)

-   [다음 단계](#next)

개발 환경 설정
--------------

1.  개발 환경에 Ruby를 설치합니다. 운영 체제에 따라 아래 단계는 달라질 수 있습니다.

    -   **Apple OS X** - OS X용 Ruby 배포가 몇 가지 있습니다. OS X에 대한 이 자습서 내용은 [Homebrew](http://brew.sh/)를 사용하여 **rbenv** 및 **ruby-build**를 설치함으로써 유효성이 검사되었습니다. 설치 정보는 <https://github.com/sstephenson/rbenv/>(영문)에서 볼 수 있습니다.

    -   **Linux** - 배포 패키지 관리 시스템을 사용합니다. Ubuntu 12.10에 대한 이 자습서 내용은 ruby1.9.1 및 ruby1.9.1-dev 패키지를 사용하여 유효성이 검사되었습니다.

    -   **Windows** - Windows용 Ruby 배포가 몇 가지 있습니다. 이 자습서의 내용은 [RailsInstaller](http://railsinstaller.org/)(영문) 1.9.3-p392를 사용하여 유효성이 검사되었습니다.

2.  새 명령줄 또는 터미널 세션을 열고 다음 명령을 입력하여 Ruby on Rails를 설치합니다.

         gem install rails --no-rdoc --no-ri

    **참고**

    일부 운영 체제에서 이 명령을 사용하려면 관리자 또는 루트 권한이 필요할 수 있습니다. 이 명령을 실행하는 동안 오류가 발생한 경우 다음과 같이 'sudo'를 사용해 보십시오.

    ``` {.prettyprint}
    sudo gem install rails
    ```

    **참고**

    이 자습서에서는 Rails gem의 버전 3.2.12를 사용했습니다.

3.  또한 JavaScript 인터프리터를 설치해야 합니다. Rails에서 Rails 응용 프로그램에 사용되는 CoffeeScript 자산을 컴파일하는 데 이 인터프리터를 사용합니다. 지원되는 인터프리터의 목록은 <https://github.com/sstephenson/execjs#readme>(영문)에서 확인할 수 있습니다.

    [Node.js](http://nodejs.org/)(영문)는 OS X, Linux 및 Windows 운영 체제에 사용할 수 있으므로, 이 자습서 내용의 유효성 검사 중 사용되었습니다.

Rails 응용 프로그램 만들기
--------------------------

1.  명령줄 또는 터미널 세션에서 다음 명령을 사용하여 "blog\_app"이라는 새 Rails 응용 프로그램을 만듭니다.

         rails new blog_app

    이 명령은 **blog\_app**이라는 새 디렉터리를 만들고 Rails 응용 프로그램에 필요한 파일 및 하위 디렉터리로 채웁니다.

    **참고**

    이 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 이 명령은 기본 응용 프로그램에 필요한 gem을 자동으로 설치하며, 자동 설치 중에는 화면이 중단된 것처럼 보일 수 있습니다.

2.  **blog\_app** 디렉터리로 변경한 후 다음 명령을 사용하여 기본 블로그 스캐폴딩을 만듭니다.

         rails generate scaffold Post name:string title:string content:text

    이 명령은 블로그에 게시물을 유지하는 데 사용되는 컨트롤러, 보기, 모델 및 데이터베이스 마이그레이션을 만듭니다. 각 게시물에는 만든 이 이름, 게시물의 제목 및 텍스트 내용이 포함됩니다.

3.  블로그 게시물을 저장할 데이터베이스를 만들려면 다음 명령을 사용합니다.

         rake db:migrate

    이 명령은 Rails의 기본 데이터베이스 공급자인 [SQLite3 Database](http://www.sqlite.org/)(영문)를 사용합니다. 프로덕션 응용 프로그램에 다른 데이터베이스를 사용할 수도 있지만 이 자습서에서는 SQLite로 충분합니다.

응용 프로그램 테스트
--------------------

개발 환경에서 Rails 서버를 시작하려면 다음 단계를 수행하십시오.

1.  디렉터리를 아직 변경하지 않은 경우 **blog\_app** 디렉터리로 변경하고 다음 명령을 사용하여 Rails 서버를 시작합니다.

         rails s

    다음과 유사한 결과가 표시됩니다. 웹 서버가 수신 대기 중인 포트를 기록하십시오. 아래 예에서는 포트 3000에서 수신 대기 중입니다.

         => Booting WEBrick
         => Rails 3.2.12 application starting in development on http://0.0.0.0:3000
         => Call with -d to detach
         => Ctrl-C to shutdown server
         [2013-03-12 19:11:31] INFO  WEBrick 1.3.1
         [2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
         [2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2.  브라우저를 열고 http://localhost:3000/으로 이동합니다. 다음과 유사한 결과가 표시됩니다.

    ![기본 Rails 페이지](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailslocal.png)

    이 페이지는 정적인 시작 페이지입니다. 스캐폴딩 명령으로 생성되는 양식을 보려면 http://localhost:3000/posts로 이동합니다. 다음과 유사한 결과가 표시됩니다.

    ![게시물을 나열하는 페이지](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilslocal.png)

    서버 프로세스를 중지하려면 명령줄에서 Ctrl+C를 누릅니다.

소스 리포지토리 만들기
----------------------

이 자습서의 경우 버전 제어에 그리고 코드의 중앙 위치로 [Git](http://git-scm.com/)(영문) 및 [GitHub](https://github.com/)(영문)를 사용합니다.

1.  [GitHub](https://github.com/)에서 새 리포지토리를 만듭니다. 현재 GitHub 계정이 없는 경우 무료 계정을 등록할 수 있습니다. 이 자습서의 단계에서는 리포지토리 이름이 **blog\_app**인 것으로 가정합니다.

    **참고**

    이 문서 뒷부분의 섹션에서 만드는 스크립트에는 SSH를 통해 응용 프로그램을 배포하는 데 사용하는 가상 컴퓨터의 주소 및 사용자 이름이 포함됩니다. 이런 이유로 가능한 경우 개인 GitHub 리포지토리를 사용하는 것이 좋습니다.

2.  명령 프롬프트에서 **blog\_app** 디렉터리로 변경하고 다음 명령을 실행하여 GitHub 리포지토리에 응용 프로그램의 초기 버전을 업로드합니다.

         git init
        git add .
        git commit -m "initial commit on azure"
        git remote add origin https://github.com/YOUR_GITHUB_ACCOUNT/blog-azure.git
        git push -u origin master

Azure 가상 컴퓨터 만들기
------------------------

[여기](/en-us/manage/linux/tutorials/virtual-machine-from-gallery/)에 나와 있는 지침을 따라 Linux를 호스트하는 Azure 가상 컴퓨터를 만드십시오.

**참고**

이 자습서의 단계는 Ubuntu 12.10을 호스트하는 Azure 가상 컴퓨터에서 수행되었습니다. 다른 Linux 배포를 사용하는 경우 동일한 작업을 수행하는 데 다른 단계가 필요할 수 있습니다.

**참고**

**단지** 가상 컴퓨터만 만들면 됩니다. SSH를 사용하여 가상 컴퓨터에 연결하는 방법을 알아본 후 중지하십시오.

SSH를 사용하여 Azure 가상 컴퓨터를 만든 후 다음 단계를 수행하여 가상 컴퓨터에 Ruby 및 Rails를 설치합니다.

1.  SSH를 사용하여 가상 컴퓨터에 연결한 후 다음 명령을 사용하여 기존 패키지를 업데이트하고 Ruby 환경을 설치합니다.

         sudo apt-get -y update
         sudo apt-get -y upgrade
         sudo apt-get -y install ruby1.9.1 ruby1.9.l-dev build-essential libsqlite3-dev nodejs curl git-core nginx

    설치를 완료한 후 다음 명령을 사용하여 Ruby가 제대로 설치되었는지 확인합니다.

         ruby -v

    이 명령은 가상 컴퓨터에 설치된 Ruby 버전을 반환합니다. 반환되는 버전은 **ruby 1.9.3p194 (2012-04-20 revision 35410) [x86\_64-linux]**와 같이 1.9.1과 다를 수 있습니다.

2.  다음 명령을 사용하여 번들러를 설치합니다.

         sudo gem install bundler

    서버로 복사한 번들러는 Rails 응용 프로그램에 필요한 gem을 설치하는 데 사용됩니다.

포트 80 열기 및 Nginx 테스트
----------------------------

Nginx에서는 가상 컴퓨터가 웹 트래픽을 허용하고 있는지 확인하는 데 사용할 수 있는 기본 웹 사이트를 제공합니다. 포트 80을 통한 트래픽을 사용하도록 설정하고 기본 Nginx 웹 사이트를 테스트하려면 다음 단계를 수행합니다.

1.  VM을 사용하여 SSH 세션에서 다음과 같이 Nginx 서비스를 시작합니다.

        sudo service nginx start

    이 명령은 Nginx 서비스를 시작하여 포트 80에서 들어오는 트래픽을 수신합니다.

2.  브라우저에서 [Azure 관리 포털](https://manage.windowsazure.com/)로 이동하고 가상 컴퓨터를 선택합니다.

    ![가상 컴퓨터 목록](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/vmlist.png)

3.  페이지 맨 위에서 **끝점**을 선택한 후 페이지 맨 아래에서 **+ 끝점 추가**를 클릭합니다.

    ![끝점 페이지](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/endpoints.png)

4.  **끝점 추가** 대화 상자 아래 왼쪽에서 화살표를 클릭하여 두 번째 페이지로 진행하고 양식에 다음 정보를 입력합니다.

    -   **이름**: HTTP

    -   **프로토콜**: TCP

    -   **공용 포트**: 80

    -   **개인 포트**: 80

    트래픽을 개인 포트 80으로 라우팅하는 공용 포트 80이 만들어지며, Nginx는 이 포트에서 수신 대기합니다.

    ![새 끝점 대화 상자](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/newendpoint80.png)

5.  확인 표시를 클릭하여 끝점을 저장합니다.

6.  **업데이트 진행 중**이라는 메시지가 나타납니다. 이 메시지가 사라지면 끝점이 활성 상태가 됩니다. 이제 가상 컴퓨터의 DNS 이름으로 이동하여 응용 프로그램을 테스트할 수 있습니다. 다음과 유사한 웹 사이트가 나타납니다.

    ![nginx 시작 페이지](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/welcomenginx.png)

7.  다음 명령을 사용하여 기본 Nginx 웹 사이트를 중지하고 제거합니다.

        sudo service nginx stop
        sudo rm /etc/nginx/sites-enabled/default

    이 자습서의 뒷부분에서 실행되는 배포 스크립트는 blog\_app을 Nginx에서 제공하는 기본 웹 사이트로 만듭니다.

배포 준비
---------

이 섹션에서는 응용 프로그램을 수정하여 Unicorn 웹 서버를 사용하고, 배포에 Capistrano를 사용하고, 가상 컴퓨터에서 GitHub 액세스를 사용하도록 설정하며, 응용 프로그램을 배포하고 시작하는 데 사용하는 스크립트를 만듭니다.

1.  [SSH 키 생성](https://help.github.com/articles/generating-ssh-keys#platform-all)(영문) 페이지에서 설명한 단계를 수행함으로써 인증서를 사용하여 GitHub 계정을 인증하도록 가상 컴퓨터에 권한을 부여합니다. 이는 배포 스크립트에서 GitHub 리포지토리에 액세스하는 데 사용됩니다.

    **참고**

    가상 컴퓨터에서 SSH 키를 생성해야 하지만 개발 환경에서는 브라우저를 사용하여 해당 키를 GitHub 계정에 추가할 수 있습니다.

    GitHub로 복사하여 붙여넣을 수 있도록 SSH 인증서 내용을 보려면 다음 명령을 사용합니다.

    ``` {}
    cat ~/.ssh/id_rsa.pub
    ```

2.  개발 컴퓨터에서 **Gemfile**을 수정하여 다음과 같이 **Capistrano** 및 **Unicorn** 줄의 시작 부분에서 '\#' 문자를 제거하여 주석 처리를 제거합니다.

         # gem 'unicorn'
         # gem 'capistrano'

    **참고**

    Unicorn은 Windows에서 사용할 수 없습니다. 개발 환경으로 Windows를 사용하는 경우 VM에 배포할 경우에만 Unicorn을 설치하도록 **Gemfile**을 수정하십시오.

    ``` {.prettyprint}
    platforms :ruby do  gem 'unicorn'end
    ```

3.  다음 명령을 실행하여 새 gem을 설치하고 프로젝트에 대한 Capistrano를 설정합니다.

        bundle
        capify .

4.  다음 파일을 **blog\_app/config** 디렉터리에 추가하고 아래 링크에 있는 콘텐츠로 각 파일을 채웁니다.

    -   [nginx.conf](https://gist.github.com/cff582f4f970a95991e9)(영문) - Unicorn과 함께 작동하고 Rails 응용 프로그램에 포함된 정적 파일을 제공하도록 Nginx를 구성합니다.
    -   [unicorn\_init.sh](https://gist.github.com/3272994)(영문) - Unicorn 서버 프로세스를 시작하는 데 사용하는 셸 스크립트입니다.
    -   [unicorn.rb](https://gist.github.com/3273014)(영문) - Unicorn의 구성 파일입니다.

    각 파일에서 **blogger** 단어를 가상 컴퓨터에 로그인하는 데 사용하는 사용자 이름으로 바꿉니다. 이 사용자는 응용 프로그램을 배포하는 데 사용할 사용자입니다.

5.  **blog\_app/config** 디렉터리에서 기존 내용을 다음으로 바꾸도록 **deploy.rb** 파일을 편집합니다.

        require "bundler/capistrano"

        set :application, "blog_app"
        set :user, "blogger"

        set :scm, :git
        set :repository, "git@github.com:YourGitHubAccount/blog_app.git"
        set :branch, "master"
        set :use_sudo, true

        server "VMDNSName", :web, :app, :db, primary: true

        set :deploy_to, "/home/#{user}/apps/#{application}"
        default_run_options[:pty] = true
        ssh_options[:forward_agent] = true
        ssh_options[:port] = SSHPort

        namespace :deploy do
          desc "Fix permissions"
          task :fix_permissions, :roles => [ :app, :db, :web ] do
            run "chmod +x #{release_path}/config/unicorn_init.sh"
          end

          %w[start stop restart].each do |command|
            desc "#{command} unicorn server"
            task command, roles: :app, except: {no_release: true} do
              run "service unicorn_#{application} #{command}"
            end
          end

          task :setup_config, roles: :app do
            sudo "ln -nfs #{current_path}/config/nginx.conf /etc/nginx/sites-enabled/#{application}"
            sudo "ln -nfs #{current_path}/config/unicorn_init.sh /etc/init.d/unicorn_#{application}"
            sudo "mkdir -p #{shared_path}/config"
          end
          after "deploy:setup", "deploy:setup_config"

          task :symlink_config, roles: :app do
            # Add database config here
          end
          after "deploy:finalize_update", "deploy:fix_permissions"
          after "deploy:finalize_update", "deploy:symlink_config"
        end

    위 텍스트에서 다음과 같이 바꿉니다.

    -   **YourGitHubAccount**를 GitHub 계정 이름으로 바꿉니다.
    -   **VMDNSName**을 Azure 가상 컴퓨터의 DNS로 바꿉니다.
    -   **blogger**를 가상 컴퓨터에 로그인하는 데 사용하는 사용자 이름으로 바꿉니다.
    -   **SSHPort**를 가상 컴퓨터의 외부 SSH 포트로 바꿉니다.

    **참고**

    개발 환경이 Windows 시스템인 경우 **deploy.rb** 파일에 다음 줄을 추가해야 합니다. 이 줄은 파일의 시작 부분에 있는 다른 **set** 문과 함께 있어야 합니다.

    ``` {}
    set :bundle_flags, "--no-deployment --quiet"
    ```

    이렇게 추가하면 배포 중 Gemfile.lock 대신 Gemfile에서 gem이 로드되므로 모범 사례는 아니지만 대상 시스템(Linux)이 개발 시스템(Windows)과 다르기 때문에 추가해야 합니다.

6.  **blog\_app** 디렉터리에 있는 **Capfile**에서 assets 줄의 주석 처리를 제거합니다.

        load 'deploy/assets'

7.  다음 명령을 실행하여 프로젝트에 대한 변경 내용을 커밋하고 GitHub로 업로드합니다.

        git add .
        git commit -m "adding config files"
        git push

배포
----

1.  로컬 개발 컴퓨터에서 다음 명령을 사용하여 배포용 원격 Azure VM을 설정합니다.

        cap deploy:setup

    메시지가 표시되면 가상 컴퓨터 사용자 계정의 암호를 입력합니다. Capistrano에서 VM에 연결하여 사용자 계정의 홈 디렉터리에 **apps** 디렉터리를 만듭니다.

2.  Azure VM에 대한 SSH 연결에서 다음 명령을 사용하여 **app** 디렉터리의 사용 권한을 수정합니다.

        sudo chmod -R 777 apps

    **참고**

    이 과정은 프로덕션 환경에서는 추천되지 않으며 현재는 데모 용도로만 사용되고 있습니다.

3.  개발 환경에서 다음 명령을 사용하여 콜드 배포를 수행합니다. 이 명령은 가상 컴퓨터에 응용 프로그램을 배포하고 Unicorn 서비스를 시작합니다.

        cap deploy:cold

4.  Nginx 서비스를 시작하여 트래픽을 Unicorn으로 라우팅하고 정적 콘텐츠를 제공합니다.

        sudo service nginx start

이때 Ruby on Rails 응용 프로그램이 Azure 가상 컴퓨터에서 실행되고 있어야 합니다. 이를 확인하려면 웹 브라우저에서 가상 컴퓨터의 DNS 이름을 입력합니다. 예를 들어 http://railsvm.cloudapp.net을 입력합니다. 다음과 같이 'Welcome aboard' 화면이 나타납니다.

![welcome aboard 페이지](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailscloud.png)

URL에 '/posts'를 추가하는 경우 게시물 색인이 표시되고 게시물을 생성, 편집 및 삭제할 수 있습니다.

다음 단계
---------

이 문서에서는 기본 양식 기반 Rails 응용 프로그램을 만들고 Capistrano를 사용하여 Azure 가상 컴퓨터에 게시하는 방법을 알아보았습니다. 가상 컴퓨터는 Unicorn 및 Nginx를 사용하여 응용 프로그램에 대한 웹 요청을 처리했습니다.

SSH만 사용하여 Azure VM에 Rails 응용 프로그램을 만들어 배포하는 추가적인 기본 예제에 대해서는 [Linux 가상 컴퓨터를 사용하여 Ruby on Rails 웹 응용 프로그램 호스트](/en-us/develop/ruby/tutorials/web-app-with-linux-vm/)(영문)를 참조하십시오.

Ruby on Rails에 대해 자세히 알아보려면 [Ruby on Rails 가이드](http://guides.rubyonrails.org/)(영문)를 참조하십시오.

Azure SDK for Ruby를 사용하여 Ruby 응용 프로그램에서 Azure 서비스에 액세스하려면 다음을 참조하십시오.

-   [Blob을 사용하여 데이터 저장](/en-us/develop/ruby/how-to-guides/blob-storage/)

-   [테이블을 사용하여 키/값 쌍 저장(영문)](/en-us/develop/ruby/how-to-guides/table-service/)

-   [콘텐츠 배달 네트워크로 높은 대역폭 콘텐츠 제공](/en-us/develop/ruby/app-services/)


