<properties 
	pageTitle="Python 및 SDK 설치 - Azure" 
	description="Azure에서 사용할 Python 및 SDK를 설치하는 방법에 대해 알아봅니다." 
	services="" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/05/2015" 
	ms.author="huvalo"/>




# Python 및 SDK 설치

Python은 Windows에서 쉽게 설정할 수 있으며 Mac 및 Linux에서는 사전 설치되어 제공됩니다.  이 가이드에서는 설치 및 Azure와 함께 사용할 수 있도록 컴퓨터를 설정하는 작업을 단계별로 안내합니다.  이 가이드에서는 다음에 대해 설명합니다.

* Python Azure SDK에 포함된 내용
* 사용할 Python 및 버전
* Windows에서 설치
* Mac 및 Linux에서 설치

## Python Azure SDK에 포함된 내용

Python용 Azure SDK에는 Azure용 Python 응용 프로그램을 개발, 배포 및 관리할 수 있는 구성 요소가 포함되어 있습니다. 구체적으로 말해서 Python용 Azure SDK에는 다음이 포함되어 있습니다.

* **Azure 용 Python 클라이언트 라이브러리는**. 이러한 클래스 라이브러리는 저장소 및 서비스 버스 등의 Azure 기능에 액세스 하 고 저장소 계정, 가상 컴퓨터 등과 같은 Azure 리소스 관리에 대 한 인터페이스를 제공 합니다.
* **Azure 에뮬레이터 (Windows에만 해당)**. 계산 및 저장소 에뮬레이터는 응용 프로그램을 로컬로 테스트할 수 있는 클라우드 서비스 및 데이터 관리 서비스의 로컬 에뮬레이터입니다. Azure 에뮬레이터는 Windows에서만 실행됩니다.

## 사용할 Python 및 버전

사용할 수 있는 몇 가지의 Python 인터프리터 옵션이 있으며 다음을 예로 들 수 있습니다.

* CPython - 가장 일반적으로 사용되는 표준 Python 인터프리터
* IronPython - .Net/CLR에서 실행되는 Python 인터프리터
* Jython - JVM에서 실행되는 Python 인터프리터

만 **CPython** 테스트를 거쳤으며 웹사이트와 같은 Python Azure SDK 및 Azure 서비스와 클라우드 서비스에 대 한 지원.  버전 2.7 또는 3.4를 사용하는 것이 좋습니다.

## Python을 구하는 위치

CPython을 구하는 몇 가지 방법이 있습니다.

* 직접 [www.python.org][]
* 와 같은 신뢰할 수 있는 distro에서 [www.continuum.io][], [www.enthought.com][] 또는 [www.activestate.com][]
* 원본에서 빌드

구체적인 요구 사항이 없다면 아래 설명대로 처음 두 가지 옵션을 사용하는 것이 좋습니다.

## Windows에서 설치

Windows의 경우 웹 플랫폼 설치 관리자를 사용하여 설치를 간소화할 수 있습니다. 여기에 포함 된 CPython에서 [www.python.org][].

* [Microsoft Azure SDK for Python 2.7][]
* [Microsoft Azure SDK for Python 3.4][]

**참고:** Windows 서버에서 WebPI 설치 프로그램을 다운로드 하기 위해 해야 IE ESC 설정을 구성 하려면 (시작/관리 도구/서버 관리자/로컬 서버를 클릭 한 다음 **IE 보안 강화 구성**설정, 해제로)


### Python 2.7

WebPI 설치 프로그램은 Python Azure 앱에 필요한 모든 것을 제공합니다.

![how-to-install-python-webpi-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

마치고 나면 설치 선택을 확인하는 다음 화면이 표시됩니다.

![how-to-install-python-webpi-27-2](./media/python-how-to-install/how-to-install-python-webpi-27-2.png)

설치가 완료 되 면 입력 `python` 확인 하는 메시지가 나타나면 라인 원활 하 게 합니다.  설치한 방법에 따라, 올바른 버전의 Python을 찾도록 "path" 변수를 설정해야 할 수 있습니다.

![how-to-install-python-win-run-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

설치한 후에는 기본 위치에서 Python 및 클라이언트 라이브러리를 사용할 수 있습니다.

		C:\Python27\Lib\site-packages\azure


### Python 3.4

WebPI 설치 프로그램은 Python Azure 앱에 필요한 모든 것을 제공합니다.

![how-to-install-python-webpi-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)
 
마치고 나면 설치 선택을 확인하는 다음 화면이 표시됩니다.

![how-to-install-python-webpi-34-2](./media/python-how-to-install/how-to-install-python-webpi-34-2.png)

설치가 완료된 후에는 프롬프트에 python을 입력하여 원활하게 작업이 이루어졌는지 확인합니다.  설치한 방법에 따라, 올바른 버전의 Python을 찾도록 "path" 변수를 설정해야 할 수 있습니다.

![how-to-install-python-win-run-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

설치한 후에는 기본 위치에서 Python 및 클라이언트 라이브러리를 사용할 수 있습니다.

		C:\Python34\Lib\site-packages\azure


### 추가 패키지 가져오기

[Python 패키지 인덱스][] (PyPI) Python 라이브러리의 풍부한 선택 했습니다.  Distro를 설치하도록 선택했다면 웹 개발 및 기술 컴퓨팅을 포함한 다양한 시나리오에 맞는 거의 모든 흥미로운 기능을 이미 갖추었습니다.


### Python Tools for Visual Studio

[Python Tools for Visual Studio][] (PTVS)은 자유 OSS 플러그인 VS Python IDE 단추로 바뀝니다는 Microsoft에서:

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

PTVS를 사용 하는 선택적 이지만 Python 및 웹 프로젝트/솔루션 지원, 디버깅, 프로 파일링, 대화형 창, 템플릿 편집 및 Intellisense 제공 하므로 권장 됩니다.

PTVS도 쉽게 배포 하기 위한 지원으로 Microsoft Azure에 배포 하려면 [클라우드 서비스][] 및 [웹사이트][].

PTVS는 기존 Visual Studio 2010, 2012 또는 2013 설치와 함께 작동합니다.  참조 설명서, 다운로드 및 토론을 [Python Tools for Visual Studio codeplex][].  

## Windows 제거

**Python 용 Azure SDK** WebPI 제품은 일반적인 의미 하지만 실제로 개별 제품 같은 32 비트 Python 2.7/3.4, 함께 묶어서 제공 되는 등 Python 용 Azure 클라이언트 라이브러리의 컬렉션에서 응용 프로그램 되지 않습니다.  이에 따라, 전통적인 개념의 고유 제거 프로그램이 없으므로 설치된 프로그램을 Windows 제어판에서 개별적으로 제거해야 합니다.  

다시 설치 하려는 경우 **Python 용 Azure SDK**, 단순히 관리자 권한으로 PowerShell 명령 프롬프트를 열고 다음 명령을 실행 합니다.

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

그런 다음, WebPI를 다시 실행합니다.

## Linux 및 MacOS에서 설치

Python 개발 컴퓨터에 이미 설치 된 것 같습니다.  다음을 입력하여 이를 확인할 수 있습니다.

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

여기에서는 Azure에서 실행되는 이 Ubuntu Server 14.04 LTS VM에 CPython 2.7.6이 설치된 것을 볼 수 있습니다. 업그레이드해야 하는 경우 사용 중인 OS의 권장 패키지 업그레이드 지침을 따르십시오.

Python Azure 클라이언트 라이브러리를 설치 하려면 사용 **pip** 에서 잡으면 **PyPI**.

없는 경우 **pip** 설치 하려면이 명령을 사용 하 여 설치 하십시오.

	curl https://bootstrap.pypa.io/get-pip.py | sudo python
	
위의 명령은 루트 암호를 요구하는 메시지를 자동으로 표시합니다. 입력한 후 Enter 키를 누릅니다.  

다음을 사용 하 여 `pip` Python Azure 클라이언트 라이브러리를 설치 하는 명령:
	
	sudo pip install azure

이제 아래에서 설치 된 클라이언트 라이브러리가 표시 **사이트 패키지**.  MacOS의 경우 다음과 같습니다.

![how-to-install-python-mac-site](./media/python-how-to-install/how-to-install-python-mac-site.png)

## Python Azure Linux와 MacOS 시나리오

Linux 또는 MacOS, 다음은 지원 되는 주요 Azure 시나리오입니다.

1. Python용 클라이언트 라이브러리를 사용하여 Azure 서비스 사용

2. Linux VM에서 앱 실행

3. 개발 하 고 Git를 사용 하 여 Azure 웹사이트에 게시 합니다.

첫번째 시나리오를 사용 하면 Azure PaaS 기능을 같은 활용 하는 풍부한 웹 응용 프로그램을 작성할 수 있습니다 [blob 저장소][], [큐 저장소][], [테이블 저장소][] Azure REST API에 대 한 Pythonic 래퍼를 통해 등입니다.  이러한 Windows, Mac 및 Linux에서 동일 하 게 작동 합니다.  또한 로컬 개발 컴퓨터 또는 Azure에서 실행 되는 Linux VM에서 이러한 클라이언트 라이브러리를 사용할 수 있습니다.

VM 시나리오의 경우, 원하는 Linux VM(Ubuntu, CentOS, SUSE)을 시작한 후 원하는 대로 실행하고 관리하면 됩니다.  한 예로, 실행할 수 있습니다 [IPython][] REPL/notebook 프로그램 Windows/Mac/linux 컴퓨터 및 Linux 또는 Windows 다중 프로세싱 VM이 Azure에서 IPython Engine을 실행 하려면 브라우저를 가리킵니다. 참조는 [Azure에서 IPython Notebook][] 자세한 정보에 대 한 자습서입니다.

Linux VM을 설치 하는 방법에 대 한 정보를 참조 하십시오는 [만들 가상 컴퓨터 실행 Linux][] 자습서입니다.

Git 배포를 사용 하 여 Python 웹 응용 프로그램을 개발 하 고 모든 운영 체제에서 Azure 웹사이트에 게시할 수 있습니다.  Azure에 저장소를 푸시할 때 가상 환경에 자동으로 만들어집니다 및 pip 필요한 패키지를 설치 합니다.

개발 하 고 Azure 웹사이트를 게시에 대 한 자세한 내용은 대 한 자습서를 참조 하십시오. [과 Django 웹사이트를 만드는][], [병으로 웹사이트를 만드는][] 및 [플라스도 웹사이트를 만드는][]. WSGI 규격 프레임 워크 사용에 대 한 일반적인 정보를 참조 하십시오. [구성 Python Azure 웹사이트와][].


## 추가 소프트웨어 및 리소스

* [연속선 분석 Python 배포][]
* [Enthought Python][]
* [ActiveState Python 배포][]
* [SciPy-Scientific Python 라이브러리의 모음][]
* [NumPy-Python 숫자 라이브러리][]
* [Django 프로젝트-는 완성도 높은 웹 프레임 워크 CMS][]
* [IPython-는 고급 REPL/Notebook Python 용][]
* [Azure에서 IPython Notebook][]
* [CodePlex의 Visual Studio 용 Python 도구][]


[연속선 분석 Python 배포]: http://continuum.io
[Enthought Python]: http://www.enthought.com
[ActiveState Python 배포]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy-Scientific Python 라이브러리의 모음]: http://www.scipy.org
[NumPy-Python 숫자 라이브러리]: http://www.numpy.org
[Django 프로젝트-는 완성도 높은 웹 프레임 워크 CMS]: http://www.djangoproject.com 
[IPython-는 고급 REPL/Notebook Python 용]: http://ipython.org
[IPython]: http://ipython.org
[Azure에서 IPython Notebook]: ../virtual-machines-python-ipython-notebook/
[클라우드 서비스]: ../cloud-services-python-ptvs/
[웹 사이트]: ../web-sites-python-ptvs-django-mysql
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[CodePlex의 Visual Studio 용 Python 도구]: http://pytools.codeplex.com 
[Python 패키지 인덱스]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409
[Azure 포털을 통해 Linux VM을 설정합니다.]: ../../../shared/chunks/create-and-configure-opensuse-vm-in-portal
[Mac 및 Linux 용 Azure 명령줄 도구를 사용 하는 방법]: ../../shared/chunks/crossplat-cmd-tools
[Linux를 실행 하는 가상 컴퓨터 만들기]: ../virtual-machines-linux-tutorial/
[Django를 사용하여 웹 사이트 만들기]: ../web-sites-python-create-deploy-django-app/
[Bottle을 사용하여 웹 사이트 만들기]: ../web-sites-python-create-deploy-bottle-app/
[Flask를 사용하여 웹 사이트 만들기]: ../web-sites-python-create-deploy-flask-app/
[Azure 웹사이트를 사용 하 여 Python 구성]: ../web-sites-python-configure/
[테이블 저장소]: ../storage-python-how-to-use-table-storage/
[큐 저장소]: ../storage-python-how-to-use-queue-storage/
[blob 저장소]: ../storage-python-how-to-use-blob-storage/

<!--HONumber=47-->
