<properties linkid="develop-python-web-app-with-django-and-mysql" urlDisplayName="Web with Django + MySQL" pageTitle="Python web app with Django and MySQL - Azure tutorial" metaKeywords="Azure django web app, Azure Django MySQL, Azure django Python" description="A tutorial that teaches you how to use MySQL in with Django on an Azure virtual machine. Code samples written in Python." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Django Hello World - MySQL Windows Edition" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Django Hello World - MySQL Windows Edition

이 자습서에서는 단일 Azure 가상 컴퓨터에서 MySQL과 Django를 함께 사용하는 방법을 설명합니다. 이 가이드에서는 이전에 Azure를 사용한 경험이 어느 정도 있다고 가정합니다. Azure 및 Django에 대한 지침을 보려면 [Django Hello World][Django Hello World]를 참조하십시오. 또한 이 가이드에서는 MySQL에 대한 지식도 어느 정도 있다고 가정합니다. MySQL 개요에 대해서는 [MySQL 웹 사이트][MySQL 웹 사이트](영문)를 참조하십시오.

이 자습서에서는 다음 방법에 대해 알아봅니다.

-   MySQL 및 Django를 호스트하도록 Azure 가상 컴퓨터 설정. 이 자습서에서는 Windows Server 2008 R2에서 설정하는 방법을 설명하지만 Azure에서 호스트되는 Linux VM에서도 같은 작업을 수행할 수 있습니다.
-   Python용 [MySQL 드라이버][MySQL 드라이버](영문) 설치
-   MySQL 데이터베이스를 사용하도록 기존 Django 응용 프로그램 구성
-   Python에서 직접 MySQL 사용
-   MySQL Django 응용 프로그램 호스트 및 실행

Azure VM에 호스트되는 MySQL 데이터베이스를 활용하여 [Django Hello World][Django Hello World] 샘플을 확장해서 *World*를 대체하는 흥미로운 문자열을 찾아보겠습니다. 대체 문자열은 MySQL을 기반으로 하는 Django *카운터* 앱을 통해 결정됩니다. Hello World 샘플의 경우와 마찬가지로 이 Django 응용 프로그램도 Azure 가상 컴퓨터에 호스트됩니다.

이 자습서의 프로젝트 파일은 **C:\\django\\helloworld**에 저장되며 완료된 응용 프로그램은 다음과 유사하게 보입니다.

![][0]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## MySQL 및 Django를 호스트하도록 가상 컴퓨터 설정

1.  [여기][여기]에 나와 있는 지침을 따라서 *Windows Server 2008 R2* 배포의 Azure 가상 컴퓨터를 만드십시오.

2.  가상 컴퓨터에서 다음과 같이 MySQL 트랜잭션용 TCP 포트를 엽니다.

-   Azure 포털에서 새로 만든 가상 컴퓨터로 이동하고 *끝점* 탭을 클릭합니다.
-   화면 맨 아래에 있는 *끝점 추가*를 클릭합니다.
-   이름 = **mysql**, 프로토콜 = **TCP**, 공용 포트 = **3306**, 개인 포트 = **3306**을 사용하여 끝점을 추가합니다.

1.  이름 = **web**, 프로토콜 = **TCP**, 공용 포트 = **80**, 개인 포트 = **80**을 사용하여 다른 끝점을 추가합니다. 그러면 외부 인터넷 요청이 Django가 실행되는 포트 즉, *80*으로 리디렉션됩니다.

2.  Windows *원격 데스크톱*을 사용하여 새로 만든 Azure 가상 컴퓨터에 원격으로 로그인합니다.

3.  가상 컴퓨터에서 TCP 포트 **80**을 엽니다.

-   **시작** 메뉴에서 **관리자 도구**를 선택한 후 **고급 보안이 포함된 Windows 방화벽**을 선택합니다.
-   왼쪽 창에서 **인바운드 규칙**을 선택합니다. 오른쪽에 있는 **작업** 창에서 **새 규칙...**을 선택합니다.
-   **새 인바운드 규칙 마법사**에서 **포트**를 선택한 후 **다음**을 클릭합니다.
-   **TCP**를 선택한 후 **특정 로컬 포트**를 선택합니다. "80" 포트(Django가 수신 대기하는 포트)를 지정하고 **다음**을 클릭합니다.
-   **연결 허용**을 선택하고 **다음**을 클릭합니다.
-   다시 **다음**을 클릭합니다.
-   "DjangoPort"와 같은 규칙 이름을 지정하고 마침을 클릭합니다.

1.  가상 컴퓨터에 최신 버전의 Windows용 [MySQL Community Server][MySQL Community Server](영문)를 설치합니다.

    **참고**: OS 설치 파티션과 다른 데이터 파티션에 DB를 설치하는 것이 좋습니다.

-   [여기][MySQL Community Server](영문)에서 *Windows (x86, 64-bit), MSI 설치 관리자*를 실행하고 현재 지역에서 가장 가까운 다운로드 미러에서 적절한 MSI 설치 관리자를 다운로드합니다. 유용한 힌트는 다음과 같습니다.

    -   *Complete* 설정 유형을 선택합니다.
    -   구성 마법사에서 *Detailed Configuration*을 선택합니다.
    -   **포트 번호 3306에서 TCP/IP 네트워킹을 사용하도록 설정하고 해당 포트에 대한 방화벽 예외를 추가합니다.**
    -   루트 암호를 설정하고 원격 컴퓨터의 루트 액세스를 사용하도록 설정합니다.
-   샘플 ["world" 데이터베이스]["world" 데이터베이스](MyISAM 버전)(영문)를 다음과 같이 설치합니다.

    -   Azure 가상 컴퓨터에서 [이][이](영문) zip 파일을 다운로드합니다.
    -   **이 파일의 압축을 *C:\\Users\\Administrator\\Desktop\\world.sql*에 풉니다.**

1.  MySQL을 설치한 후 Windows *시작* 메뉴를 클릭하고 새로 설치한 *MySQL 5.5 Command Line Client*를 실행합니다. 다음 명령을 실행합니다.

        CREATE DATABASE world;
        USE world;
        SOURCE C:\Users\Administrator\Desktop\world.sql
        CREATE USER 'testazureuser'@'%' IDENTIFIED BY 'testazure';
        CREATE DATABASE djangoazure;
        GRANT ALL ON djangoazure.* TO 'testazureuser'@'%';
        GRANT ALL ON world.* TO 'testazureuser'@'%';
        SELECT name from country LIMIT 1;

이제 다음과 유사한 응답이 표시됩니다.

![][1]

1.  Django 응용 프로그램 개발을 시작하려면 먼저 가상 컴퓨터에 Python+Django를 설치해야 합니다. [웹 플랫폼 설치 관리자][웹 플랫폼 설치 관리자](영문)를 사용하여 그렇게 할 수 있습니다. 웹 PI를 설치한 후 이를 사용하여 "Django"를 검색하고 Django(Python) 제품을 설치합니다.

**참고:** 단지 웹 PI에서 *Django* 제품을 설치하면 이 자습서를 진행할 수 있습니다. *Python Tools for Visual Studio* 또는 심지어 이 자습서에서 설치한 Azure Python SDK도 필요하지 **않습니다**.

1.  MySQL Python 클라이언트 패키지를 설치합니다. [이 링크에서][이 링크에서](영문) 직접 설치할 수 있습니다. 완료한 후에는 다음 명령을 실행하여 설치를 확인합니다.

![][2]

## Django Hello World 응용 프로그램 확장

1.  [Django Hello World][Django Hello World] 자습서에 나와 있는 지침을 따라서 Django로 간단한 "Hello World" 웹 응용 프로그램을 만듭니다.

2.  원하는 텍스트 편집기에서 **C:\\django\\helloworld\\helloworld\\settings.py**를 엽니다. 읽을 **DATABASES** 글로벌 사전을 다음과 같이 수정합니다.

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': 'djangoazure',               
                'USER': 'testazureuser',  
                'PASSWORD': 'testazure',
                'HOST': '127.0.0.1', 
                'PORT': '3306',
                },
            'world': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': 'world',               
                'USER': 'testazureuser',  
                'PASSWORD': 'testazure',
                'HOST': '127.0.0.1', 
                'PORT': '3306',
                }
            }

위와 같이 MySQL 데이터베이스를 찾을 수 있는 위치에 대한 Django 지침을 제공했습니다.

**참고:** Azure(MySQL) VM의 **영구** IP 주소와 일치하도록 *HOST* 키를 **반드시** 변경해야 합니다. 여기서 *HOST*는 *ipconfig* Windows 명령에서 실제로 보고하는 항목으로 설정해야 합니다.

MySQL VM의 IP 주소와 일치하도록 *HOST*를 수정한 후에는 이 파일을 저장하고 닫으십시오.

1.  *djangoazure* 데이터베이스에 대해 살펴봤으므로, 이제 이 데이터베이스를 사용하여 유용한 작업을 수행해 보겠습니다! 이를 위해 간단한 *카운터* 앱 모델을 만들어 보겠습니다. Django에서 이 앱을 만들려면 다음 명령을 실행하십시오.

        cd C:\django\helloworld
        C:\Python27\python.exe manage.py startapp counter

Django에서 위의 마지막 명령에 대한 출력을 보고하지 않으면 성공한 것입니다.

1.  **C:\\django\\helloworld\\counter\\models.py**에 다음 텍스트를 추가합니다.

        class Counter(models.Model):
            count = models.IntegerField()
            def __unicode__(self):
                return u'%s' % (self.count)

여기서 수행한 작업은 단일 정수 필드인 *count*를 포함하는 *Counter*라는 이름의 Django *Model* 클래스에 속한 하위 클래스를 정의한 것입니다. 이 간단한 카운터 모델은 Django 응용 프로그램의 적중 횟수를 기록합니다.

1.  이제 Django에 *Counter*의 존재를 인식시킵니다.

-   **C:\\django\\helloworld\\helloworld\\settings.py**를 다시 편집합니다. *INSTALLED\_APPS* 튜플에 *'counter'*를 추가합니다.
-   명령 프롬프트에서 다음을 실행합니다.

            cd C:\django\helloworld
            C:\Python27\python manage.py sql counter
            C:\Python27\python manage.py syncdb

    이러한 명령은 라이브 Django 데이터베이스에 *Counter* 모델을 저장하며 출력의 결과는 다음과 유사합니다.

        C:\django\helloworld> C:\Python27\python manage.py sql counter
        BEGIN;
        CREATE TABLE `counter_counter` (
            `id` integer AUTO_INCREMENT NOT NULL PRIMARY KEY,
            `count` integer NOT NULL
        )
        ;
        COMMIT;

        C:\django\helloworld> C:\Python27\python manage.py syncdb
        Creating tables ...
        Creating table auth_permission
        Creating table auth_group_permissions
        Creating table auth_group
        Creating table auth_user_user_permissions
        Creating table auth_user_groups
        Creating table auth_user
        Creating table django_content_type
        Creating table django_session
        Creating table django_site
        Creating table counter_counter

        You just installed Django's auth system, which means you don't have any superusers defined.
        Would you like to create one now? (yes/no): no
        Installing custom SQL ...
        Installing indexes ...
        Installed 0 object(s) from 0 fixture(s)

1.  **C:\\django\\helloworld\\helloworld\\views.py**의 내용을 바꿉니다. 아래에서 새로 구현한 *hello* 함수는 *Counter* 모델과 앞서 설치한 별도의 샘플 데이터베이스인 *world*를 함께 사용하여 "*World*" 문자열의 적절한 대체 문자열을 생성합니다.

        from django.http import HttpResponse
        import django.db
        from counter.models import Counter

        def getCountry(intId):
            #Connect to the MySQL sample database 'world'
            cur = django.db.connections['world'].cursor()
            #Execute a trivial SQL query which returns the name of 
            #all countries contained in 'world'
            cur.execute("SELECT name from country")
            tmp = cur.fetchall()
            #Clean-up after ourselves
            cur.close()
            if intId >= len(tmp):
                return "countries exhausted"
            return tmp[intId][0]

        def hello(request):
            if len(Counter.objects.all())==0:
                #when the database corresponding to 'helloworld.counter' is 
                #initially empty...
                c = Counter(count=0)
            else:
                c = Counter.objects.all()[0]
                c.count += 1
            c.save()       
            world = getCountry(int(c.count))
            return HttpResponse("<html><body>Hello <em>" + world + "</em></body></html>")

## Django 웹 사이트 배포 및 실행

참고: 다음은 테스트 환경에서 Django를 실행하는 방법을 보여 줍니다. 프로덕션에서 실행하려면 "Django Hello World 자습서"의 "FastCGI로 IIS 설정" 섹션을 따르십시오. FastCGI를 사용하면 Windows Server 2K8 R2 가상 컴퓨터에서 Windows 방화벽 클라이언트를 사용하여 인터넷 트래픽에 대한 포트 80을 열지 않아도 됩니다.

1.  Windows PowerShell 창으로 다시 전환하고 다음 명령을 입력하여 Django 웹 사이트를 공개적으로 배포합니다.

        PS C:\django\helloworld> $ipPort = 
        PS C:\django\helloworld> $ipPort = [string]$ipPort.AddressList[1]
        PS C:\django\helloworld> $ipPort += ":80"
        PS C:\django\helloworld> C:\Python27\python.exe .\manage.py runserver $ipPort

    **runserver** 매개 변수가 TCP 포트 *80*에서 *helloworld* 웹 사이트를 실행하도록 Django에 지시합니다. 이 명령의 결과는 다음과 유사합니다.

        PS C:\django\helloworld> C:\Python27\python.exe .\manage.py runserver $ipPort
        Validating models...

        0 errors found
        Django version 1.4, using settings 'helloworld.settings'
        Development server is running at http://123.34.56.78:80
        Quit the server with CTRL-BREAK.

2.  로컬 웹 브라우저에서 **http://_Vm이름_.cloudapp.net**을 엽니다. 여기서 *Vm이름*은 가상 컴퓨터 만들기 단계에서 사용한 이름입니다. 아래 스크린샷과 같이 "Hello ... !" 가 표시됩니다. 이 메시지가 표시되면 Django가 가상 컴퓨터에서 실행 중이며 제대로 작동하고 있는 것입니다.

    ![][0]

웹 브라우저를 몇 번 새로 고치면 *"Hello **\<국가 abc\>**"*가 *"Hello **\<다른 국가 이름\>**"*으로 변경됩니다.

1.  Django의 웹 사이트 호스팅을 중지하려면 PowerShell 창으로 전환해서 **Ctrl-C**를 누르면 됩니다.

## Azure 가상 컴퓨터 종료

이 자습서를 완료했으므로 새로 만든 Azure 가상 컴퓨터를 종료하거나 종료한 후 제거하여 다른 자습서에 사용할 리소스를 늘리고 Azure 사용량에 따른 요금 부과를 방지하십시오.

  [Django Hello World]: http://windowsazure.com/ko-kr/documentation/articles/virtual-machines-python-django-web-app-windows-server
  [MySQL 웹 사이트]: http://dev.mysql.com/doc/
  [MySQL 드라이버]: http://pypi.python.org/pypi/MySQL-python/1.2.3
  [0]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01.png
  [create-account-and-vms-note]: ../includes/create-account-and-vms-note.md
  [여기]: /ko-kr/manage/windows/tutorials/virtual-machine-from-gallery/
  [MySQL Community Server]: http://dev.mysql.com/downloads/mysql/
  ["world" 데이터베이스]: http://dev.mysql.com/doc/index-other.html
  [이]: http://downloads.mysql.com/docs/world.sql.zip
  [1]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-2.png
  [웹 플랫폼 설치 관리자]: http://www.microsoft.com/web/downloads/platform.aspx
  [이 링크에서]: http://code.google.com/p/soemin/downloads/detail?name=MySQL-python-1.2.3.win32-py2.7.exe&can=2&q=
  [2]: ./media/virtual-machines-python-use-mysql-django/mysql_tutorial01-1.png
  [http://\*Vm이름]: http://*yourVmName
