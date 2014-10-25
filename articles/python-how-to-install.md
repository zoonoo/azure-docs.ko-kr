<properties linkid="develop-python-install-python" urlDisplayName="Install Python" pageTitle="Install Python and the SDK - Azure" metaKeywords="Azure Python SDK" description="Learn how to install Python and the SDK to use with Azure." metaCanonical="" services="" documentationCenter="Python" title="Installing Python and the SDK" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Python 및 SDK 설치

Python은 Windows에서 쉽게 설정할 수 있으며 Mac 및 Linux에서는 사전 설치되어 제공됩니다. 이 가이드에서는 설치 및 Azure와 함께 사용할 수 있도록 컴퓨터를 설정하는 작업을 단계별로 안내합니다. 이 가이드에서는 다음에 대해 설명합니다.

-   Python Azure SDK에 포함된 내용
-   사용할 Python 및 버전
-   Windows에서 설치
-   Mac 및 Linux에서 설치

## Python Azure SDK에 포함된 내용

Python용 Azure SDK에는 Azure용 Python 응용 프로그램을 개발, 배포 및 관리할 수 있는 구성 요소가 포함되어 있습니다. 구체적으로 말해서 Python용 Azure SDK에는 다음이 포함되어 있습니다.

-   **Azure용 Python 클라이언트 라이브러리**. 이러한 클래스 라이브러리는 Azure 기능(예: 데이터 관리 서비스 및 클라우드 서비스)에 액세스하기 위한 인터페이스를 제공합니다.
-   **Azure 에뮬레이터(Windows에만 해당)**. 계산 및 저장소 에뮬레이터는 응용 프로그램을 로컬로 테스트할 수 있는 클라우드 서비스 및 데이터 관리 서비스의 로컬 에뮬레이터입니다. Azure 에뮬레이터는 Windows에서만 실행됩니다.

이 릴리스의 핵심 시나리오는 다음과 같습니다.

-   **Windows**: 클라우드 서비스, 웹 사이트 - 예: 웹 역할을 사용하는 Django 사이트
-   **Mac/Linux**: IaaS -- VM에서 원하는 기능 실행, Python을 통해 Azure 서비스 사용

## 사용할 Python 및 버전

사용할 수 있는 몇 가지의 Python 인터프리터 옵션이 있으며 다음을 예로 들 수 있습니다.

-   CPython - 가장 일반적으로 사용되는 표준 Python 인터프리터
-   IronPython - .Net/CLR에서 실행되는 Python 인터프리터
-   Jython - JVM에서 실행되는 Python 인터프리터

이 릴리스에서는 **CPython**만이 테스트를 거쳤으며 지원됩니다. 버전 2.7 또는 3.4를 사용하는 것이 좋습니다.

## Python을 구하는 위치

CPython을 구하는 몇 가지 방법이 있습니다.

-   www.python.org에서 직접
-   www.enthought.com, www.ActiveState.com 또는 www.continuum.io와 같은 신뢰할 수 있는 배포자
-   원본에서 빌드

구체적인 요구 사항이 없다면 아래 설명대로 처음 두 가지 옵션을 사용하는 것이 좋습니다.

## Windows에서 설치

Windows의 경우 기본 Python 개발자 센터에서 제공되는 [WebPI 설치 프로그램][]을 사용하여 설치를 간소화할 수 있습니다(www.python.org 에서 CPython 사용).

**참고:** Windows Server에서는 WebPI 설치 프로그램을 다운로드하려면 IE ESC 설정을 구성해야 할 수 있습니다(시작/관리 도구/서버 관리자에서 **IE ESC 구성**을 클릭하고 Off로 설정).

WebPI를 사용하여 Python 2.7 및/또는 Python 3.4를 설치하도록 선택할 수 있습니다.

### Python 2.7

WebPI 설치 프로그램은 Python Azure 앱에 필요한 모든 것을 제공합니다.

![how-to-install-python-webpi-27-1][]

마치고 나면 설치 선택을 확인하는 다음 화면이 표시됩니다.

![how-to-install-python-webpi-27-2][]

설치가 완료된 후에는 프롬프트에 python을 입력하여 원활하게 작업이 이루어졌는지 확인합니다. 설치한 방법에 따라, 올바른 버전의 Python을 찾도록 "path" 변수를 설정해야 할 수 있습니다.

![how-to-install-python-win-run-27][]

설치한 후에는 기본 위치에서 Python 및 클라이언트 라이브러리를 사용할 수 있습니다.

        C:\Python27\Lib\site-packages\azure

### Python 3.4

WebPI 설치 프로그램은 Python Azure 앱에 필요한 모든 것을 제공합니다.

![how-to-install-python-webpi-34-1][]

마치고 나면 설치 선택을 확인하는 다음 화면이 표시됩니다.

![how-to-install-python-webpi-34-2][]

설치가 완료된 후에는 프롬프트에 python을 입력하여 원활하게 작업이 이루어졌는지 확인합니다. 설치한 방법에 따라, 올바른 버전의 Python을 찾도록 "path" 변수를 설정해야 할 수 있습니다.

![how-to-install-python-win-run-34][]

설치한 후에는 기본 위치에서 Python 및 클라이언트 라이브러리를 사용할 수 있습니다.

        C:\Python34\Lib\site-packages\azure

### 추가 패키지 가져오기

이 릴리스는 웹 앱에 주로 초점을 두지만, [PyPI(Python 패키지 인덱스)][]를 찾아보고 다양한 다른 소프트웨어를 선택할 수도 있습니다. Distro를 설치하도록 선택했다면 웹 개발 및 기술 컴퓨팅을 포함한 다양한 시나리오에 맞는 거의 모든 흥미로운 기능을 이미 갖추었습니다.

### Python Tools for Visual Studio

PTVS(Python Tools for Visual Studio)는 VS를 완전한 Python IDE로 전환하는 Microsoft의 무료/OSS 플러그 인입니다.

![how-to-install-python-ptvs][]

PTVS는 선택 사항이지만 Python 및 Django 프로젝트/솔루션 지원, 디버깅, 프로파일링, 대화형 창, 템플릿 편집 및 Intellisense를 제공합니다.

또한 PTVS를 사용하면 [클라우드 서비스 및 웹 사이트 배포][] 지원과 함께 Microsoft Azure에 쉽게 배포할 수 있습니다.

PTVS는 기존 Visual Studio 2010, 2012 또는 2013 설치와 함께 작동합니다. 자세한 내용 및 다운로드는 [CodePlex의 Python Tools for Visual Studio][]를 참조하세요.

## Windows 제거

**Python용 Azure SDK** WebPI 제품은 일반적인 의미의 응용 프로그램이 아니라 실제로 32비트 Python 2.7/3.4, 번들로 제공되는 Python용 Azure 클라이언트 라이브러리 등과 같은 개별 제품의 모음입니다. 이에 따라, 전통적인 개념의 고유 제거 프로그램이 없으므로 설치된 프로그램을 Windows 제어판에서 개별적으로 제거해야 합니다.

**Python용 Azure SDK**를 다시 설치하려는 경우 관리자 권한으로 PowerShell 명령 프롬프트를 열어 다음 명령을 실행하면 됩니다.

    rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

그런 다음, WebPI를 다시 실행합니다.

## Linux 및 MacOS에서 설치

Python은 개발자 컴퓨터에 이미 설치되어 있는 경우가 많습니다. 다음을 입력하여 이를 확인할 수 있습니다.

![how-to-install-python-linux-run][]

여기에서는 Azure에서 실행되는 이 Ubuntu Server 14.04 LTS VM에 CPython 2.7.6이 설치된 것을 볼 수 있습니다. 업그레이드해야 하는 경우 사용 중인 OS의 권장 패키지 업그레이드 지침을 따르십시오.

Python Azure 클라이언트 라이브러리를 설치하려면 **pip**를 사용하여 **PyPI**에서 가져옵니다.

    curl https://bootstrap.pypa.io/get-pip.py | sudo python

위의 명령은 루트 암호를 요구하는 메시지를 자동으로 표시합니다. 입력한 후 Enter 키를 누릅니다. 다음 명령입니다.

    sudo pip install azure

이제 **site-packages** 아래에 설치된 클라이언트 라이브러리가 표시됩니다. MacOS의 경우 다음과 같습니다.

![how-to-install-python-mac-site][]

mac/linux에서 개발할 때 다음 두 가지 기본 시나리오가 지원됩니다.

1.  Python용 클라이언트 라이브러리를 사용하여 Azure 서비스 사용

2.  Linux VM에서 앱 실행

첫 번째 시나리오에서는 Azure REST API용 Python 래퍼를 통해 Blob 저장소, 큐 등의 Azure PaaS 기능을 활용하는 풍부한 기능의 웹 앱을 작성할 수 있습니다. 이 기능은 Windows, Mac 및 Linux에서 동일하게 작동합니다. 예를 보려면 자습서 및 방법 가이드를 참조하십시오. 이 클라이언트를 Linux VM 내에서도 사용할 수 있습니다.

VM 시나리오의 경우, 원하는 Linux VM(Ubuntu, CentOS, SUSE)을 시작한 후 원하는 대로 실행하고 관리하면 됩니다. 예를 들어 Windows/Mac/Linux 컴퓨터에서 [IPython][] REPL/Notebook을 실행하고 Azure에서 IPython Engine을 실행하는 Linux 또는 Windows 다중 프로세싱 VM으로 브라우저를 가리키면 됩니다. IPython 설치에 대한 자세한 내용은 해당 자습서를 참조하십시오.

Linux VM을 설정하는 방법에 대한 자세한 내용은 [Linux 관리][] 섹션을 참조하십시오.

## 추가 소프트웨어 및 리소스

-   [Enthought Python 분산(영문)][]
-   [ActiveState Python 분산(영문)][]
-   [SciPy - Scientific Python 라이브러리 제품군(영문)][]
-   [NumPy - Python의 숫자 라이브러리(영문)][]
-   [Django 프로젝트 - 완성도 높은 웹 프레임워크/CMS(영문)][]
-   [IPython - Python용 고급 REPL/Notebook(영문)][IPython]
-   [Azure의 IPython Notebook][]
-   [CodePlex의 Python Tools for Visual Studio(영문)][CodePlex의 Python Tools for Visual Studio]
-   [Virtualenv(영문)][]

  [WebPI 설치 프로그램]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
  [how-to-install-python-webpi-27-1]: ./media/python-how-to-install/how-to-install-python-webpi-27-1.png
  [how-to-install-python-webpi-27-2]: ./media/python-how-to-install/how-to-install-python-webpi-27-2.png
  [how-to-install-python-win-run-27]: ./media/python-how-to-install/how-to-install-python-win-run-27.png
  [how-to-install-python-webpi-34-1]: ./media/python-how-to-install/how-to-install-python-webpi-34-1.png
  [how-to-install-python-webpi-34-2]: ./media/python-how-to-install/how-to-install-python-webpi-34-2.png
  [how-to-install-python-win-run-34]: ./media/python-how-to-install/how-to-install-python-win-run-34.png
  [PyPI(Python 패키지 인덱스)]: http://pypi.python.org/pypi
  [how-to-install-python-ptvs]: ./media/python-how-to-install/how-to-install-python-ptvs.png
  [클라우드 서비스 및 웹 사이트 배포]: /ko-KR/documentation/articles/cloud-services-web-sites-python-django-app-with-ptvs/
  [CodePlex의 Python Tools for Visual Studio]: http://pytools.codeplex.com
  [how-to-install-python-linux-run]: ./media/python-how-to-install/how-to-install-python-linux-run.png
  [how-to-install-python-mac-site]: ./media/python-how-to-install/how-to-install-python-mac-site.png
  [IPython]: http://ipython.org
  [Linux 관리]: /ko-KR/manage/linux/
  [Enthought Python 분산(영문)]: http://www.enthought.com
  [ActiveState Python 분산(영문)]: http://www.activestate.com
  [SciPy - Scientific Python 라이브러리 제품군(영문)]: http://www.scipy.org
  [NumPy - Python의 숫자 라이브러리(영문)]: http://www.numpy.org
  [Django 프로젝트 - 완성도 높은 웹 프레임워크/CMS(영문)]: http://www.djangoproject.com
  [Azure의 IPython Notebook]: /ko-KR/documentation/articles/virtual-machines-python-ipython-notebook
  [Virtualenv(영문)]: http://pypi.python.org/pypi/virtualenv
