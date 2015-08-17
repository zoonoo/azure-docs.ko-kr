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

Python은 Windows에서 쉽게 설정할 수 있으며 Mac 및 Linux에서는 사전 설치되어 제공됩니다. 이 가이드에서는 설치 및 Azure와 함께 사용할 수 있도록 컴퓨터를 설정하는 작업을 단계별로 안내합니다. 이 가이드에서는 다음에 대해 설명합니다.

* Python Azure SDK에 포함된 내용
* 사용할 Python 및 버전
* Windows에서 설치
* Mac 및 Linux에서 설치

## Python Azure SDK에 포함된 내용

Python용 Azure SDK에는 Azure용 Python 응용 프로그램을 개발, 배포 및 관리할 수 있는 구성 요소가 포함되어 있습니다. 구체적으로 말해서 Python용 Azure SDK에는 다음이 포함되어 있습니다.

* **Azure용 Python 클라이언트 라이브러리**. 이러한 클래스 라이브러리는 저장소 및 서비스 버스 등의 Azure 기능에 액세스하고, 저장소 계정, 가상 컴퓨터 등과 같은 Azure 리소스를 관리하는 인터페이스를 제공합니다.
* **Azure 에뮬레이터(Windows에만 해당)**. 계산 및 저장소 에뮬레이터는 응용 프로그램을 로컬로 테스트할 수 있는 클라우드 서비스 및 데이터 관리 서비스의 로컬 에뮬레이터입니다. Azure 에뮬레이터는 Windows에서만 실행됩니다.

## 사용할 Python 및 버전

사용할 수 있는 몇 가지의 Python 인터프리터 옵션이 있으며 다음을 예로 들 수 있습니다.

* CPython - 가장 일반적으로 사용되는 표준 Python 인터프리터
* IronPython - .Net/CLR에서 실행되는 Python 인터프리터
* Jython - JVM에서 실행되는 Python 인터프리터

웹 사이트 및 클라우드 서비스와 같은Python Azure SDK 및 Azure 서비스에 대해 **CPython**만 테스트되고 지원됩니다. 버전 2.7 또는 3.4를 사용하는 것이 좋습니다.

## Python을 구하는 위치

CPython을 구하는 몇 가지 방법이 있습니다.

* [www.python.org][]에서 직접 구하기
* [www.continuum.io][], [www.enthought.com][] 또는 [www.activestate.com][]과 같은 신뢰할 수 있는 배포자로부터 구하기
* 원본에서 빌드

구체적인 요구 사항이 없다면 아래 설명대로 처음 두 가지 옵션을 사용하는 것이 좋습니다.

## Windows에서 설치

Windows의 경우 웹 플랫폼 설치 관리자를 사용하여 설치를 간소화할 수 있습니다. 여기에는 [www.python.org][]의 CPython이 포함됩니다.

* [Python 2.7용 Microsoft Azure SDK][]
* [Python 3.4용 Microsoft Azure SDK][]

**참고:** Windows Server에서는 WebPI 설치 프로그램을 다운로드하려면 IE ESC 설정을 구성해야 할 수 있습니다(시작/관리 도구/서버 관리자/로컬 서버를 클릭하고 **IE 보안 강화 구성**을 클릭하고 Off로 설정).


### Python 2.7

WebPI 설치 프로그램은 Python Azure 앱에 필요한 모든 것을 제공합니다.

![how-to-install-python-webpi-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

마치고 나면 설치 선택을 확인하는 다음 화면이 표시됩니다.

![how-to-install-python-webpi-27-2](./media/python-how-to-install/how-to-install-python-webpi-27-2.png)

설치가 완료된 후에는 프롬프트에 `python`을 입력하여 작업이 원활하게 이루어졌는지 확인합니다. 설치한 방법에 따라, 올바른 버전의 Python을 찾도록 "path" 변수를 설정해야 할 수 있습니다.

![how-to-install-python-win-run-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

설치한 후에는 기본 위치에서 Python 및 클라이언트 라이브러리를 사용할 수 있습니다.

		C:\Python27\Lib\site-packages\azure


### Python 3.4

WebPI 설치 프로그램은 Python Azure 앱에 필요한 모든 것을 제공합니다.

![how-to-install-python-webpi-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)

마치고 나면 설치 선택을 확인하는 다음 화면이 표시됩니다.

![how-to-install-python-webpi-34-2](./media/python-how-to-install/how-to-install-python-webpi-34-2.png)

설치가 완료된 후에는 프롬프트에 python을 입력하여 원활하게 작업이 이루어졌는지 확인합니다. 설치한 방법에 따라, 올바른 버전의 Python을 찾도록 "path" 변수를 설정해야 할 수 있습니다.

![how-to-install-python-win-run-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

설치한 후에는 기본 위치에서 Python 및 클라이언트 라이브러리를 사용할 수 있습니다.

		C:\Python34\Lib\site-packages\azure


### 추가 패키지 가져오기

[Python 패키지 인덱스][](PyPI)에서 다양한 Python 라이브러리를 선택할 수 있습니다. Distro를 설치하도록 선택했다면 웹 개발 및 기술 컴퓨팅을 포함한 다양한 시나리오에 맞는 거의 모든 흥미로운 기능을 이미 갖추었습니다.


### Python Tools for Visual Studio

[Visual Studio용 Python Tools][](PTVS)는 VS를 완전한 Python IDE로 전환하는 Microsoft의 무료/OSS 플러그 인입니다.

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

PTVS는 선택 사항이지만 Python 및 웹 프로젝트/솔루션 지원, 디버깅, 프로파일링, 대화형 창, 템플릿 편집 및 Intellisense를 제공합니다.

또한 PTVS를 사용하면 [클라우드 서비스][] 및 [웹 사이트][] 배포 지원과 함께 Microsoft Azure에 쉽게 배포할 수 있습니다.

PTVS는 기존 Visual Studio 2010, 2012 또는 2013 설치와 함께 작동합니다. 설명서, 다운로드 및 토론에 대한 자세한 내용은 [CodePlex의 Python Tools for Visual Studio][]를 참조하십시오.

## Windows 제거

**Python용 Azure SDK** WebPI 제품은 일반적인 의미의 응용 프로그램이 아니라 실제로 32비트 Python 2.7/3.4, 번들로 제공되는 Python용 Azure 클라이언트 라이브러리 등과 같은 개별 제품의 모음입니다. 이에 따라, 전통적인 개념의 고유 제거 프로그램이 없으므로 설치된 프로그램을 Windows 제어판에서 개별적으로 제거해야 합니다.

**Python용 Azure SDK**를 다시 설치하려는 경우 관리자 권한으로 PowerShell 명령 프롬프트를 열어 다음 명령을 실행하면 됩니다.

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

그런 다음, WebPI를 다시 실행합니다.

## Linux 및 MacOS에서 설치

Python은 개발자 컴퓨터에 이미 설치되어 있는 경우가 많습니다. 다음을 입력하여 이를 확인할 수 있습니다.

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

여기에서는 Azure에서 실행되는 이 Ubuntu Server 14.04 LTS VM에 CPython 2.7.6이 설치된 것을 볼 수 있습니다. 업그레이드해야 하는 경우 사용 중인 OS의 권장 패키지 업그레이드 지침을 따르십시오.

Python Azure 클라이언트 라이브러리를 설치하려면 **PIP**를 사용하여 **PyPI**에서 가져옵니다.

**PIP**가 설치되어 있지 않으면 이 명령을 사용하여 설치하십시오.

	curl https://bootstrap.pypa.io/get-pip.py | sudo python

위의 명령은 루트 암호를 요구하는 메시지를 자동으로 표시합니다. 입력한 후 Enter 키를 누릅니다.

그런 다음 `pip` 명령을 사용하여 Python Azure 클라이언트 라이브러리를 설치합니다.

	sudo pip install azure

이제 **site-packages** 아래에 설치된 클라이언트 라이브러리가 표시됩니다. MacOS의 경우 다음과 같습니다.

![how-to-install-python-mac-site](./media/python-how-to-install/how-to-install-python-mac-site.png)

## Linux 및 MacOS용 Python Azure 시나리오

Linux 또는 MacOS의 경우 지원되는 주요 Azure 시나리오는 다음과 같습니다.

1. Python용 클라이언트 라이브러리를 사용하여 Azure 서비스 사용

2. Linux VM에서 앱 실행

3. Git를 사용하여 개발 및 Azure 웹 사이트에 게시

첫 번째 시나리오에서는 Azure REST API용 Python 래퍼를 통해 [Blob 저장소][], [큐 저장소][], [테이블 저장소][] 등의 Azure PaaS 기능을 활용하는 풍부한 기능의 웹 앱을 작성할 수 있습니다. 이 기능은 Windows, Mac 및 Linux에서 동일하게 작동합니다. 또한 로컬 개발 컴퓨터 또는 Azure에서 실행되는 Linux VM에서 이러한 클라이언트 라이브러리를 사용할 수 있습니다.

VM 시나리오의 경우, 원하는 Linux VM(Ubuntu, CentOS, SUSE)을 시작한 후 원하는 대로 실행하고 관리하면 됩니다. 예를 들어 Windows/Mac/Linux 컴퓨터에서 [IPython][] REPL/Notebook을 실행하고 Azure에서 IPython Engine을 실행하는 Linux 또는 Windows 다중 프로세싱 VM으로 브라우저를 가리키면 됩니다. 자세한 내용은 [Azure의 IPython Notebook][] 자습서를 참조하십시오.

Linux VM을 설정하는 방법에 대한 자세한 내용은 [Linux를 실행하는 가상 컴퓨터 만들기][]를 참조하십시오.

Git 배포를 사용하여 Python 웹 응용 프로그램을 개발하고 모든 운영 체제에서 Azure 웹 사이트에 게시할 수 있습니다. Azure에 리포지토리를 푸시할 때 가상 환경이 자동으로 만들어지고 PIP가 필요한 패키지를 설치합니다.

Azure 웹 사이트 개발 및 게시에 대한 자세한 내용은 [Django를 사용하여 웹 사이트 만들기][], [Bottle을 사용하여 웹 사이트 만들기][] 및 [Flask를 사용하여 웹 사이트 만들기][]에 대한 자습서를 참조하십시오. WSGI 규격 프레임워크 사용에 대한 일반적인 정보는 [Azure 웹 사이트를 사용하여 Python 구성][]을 참조하십시오.


## 추가 소프트웨어 및 리소스

* [지속성 분석 Python 배포][]
* [Enthought Python 배포][]
* [ActiveState Python 배포][]
* [SciPy - Scientific Python 라이브러리 제품군][]
* [NumPy - Python의 숫자 라이브러리][]
* [Django 프로젝트 - 완성도 높은 웹 프레임워크/CMS][]
* [IPython - Python용 고급 REPL/Notebook][]
* [Azure의 IPython Notebook][]
* [CodePlex의 Visual Studio용 Python Tools][]


[지속성 분석 Python 배포]: http://continuum.io
[Enthought Python 배포]: http://www.enthought.com
[ActiveState Python 배포]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy - Scientific Python 라이브러리 제품군]: http://www.scipy.org
[NumPy - Python의 숫자 라이브러리]: http://www.numpy.org
[Django 프로젝트 - 완성도 높은 웹 프레임워크/CMS]: http://www.djangoproject.com
[IPython - Python용 고급 REPL/Notebook]: http://ipython.org
[IPython]: http://ipython.org
[Azure의 IPython Notebook]: virtual-machines-python-ipython-notebook.md
[클라우드 서비스]: cloud-services-python-ptvs.md
[웹 사이트]: web-sites-python-ptvs-django-mysql.md
[Visual Studio용 Python Tools]: http://aka.ms/ptvs
[CodePlex의 Python Tools for Visual Studio]: http://pytools.codeplex.com
[CodePlex의 Visual Studio용 Python Tools]: http://pytools.codeplex.com
[Python 패키지 인덱스]: http://pypi.python.org/pypi
[Python 2.7용 Microsoft Azure SDK]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
[Python 3.4용 Microsoft Azure SDK]: http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Linux를 실행하는 가상 컴퓨터 만들기]: virtual-machines-linux-tutorial.md
[Django를 사용하여 웹 사이트 만들기]: web-sites-python-create-deploy-django-app.md
[Bottle을 사용하여 웹 사이트 만들기]: web-sites-python-create-deploy-bottle-app.md
[Flask를 사용하여 웹 사이트 만들기]: web-sites-python-create-deploy-flask-app.md
[Azure 웹 사이트를 사용하여 Python 구성]: web-sites-python-configure.md
[테이블 저장소]: storage-python-how-to-use-table-storage.md
[큐 저장소]: storage-python-how-to-use-queue-storage.md
[Blob 저장소]: storage-python-how-to-use-blob-storage.md

<!---HONumber=August15_HO6-->