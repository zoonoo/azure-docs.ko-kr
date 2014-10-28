<properties linkid="develop-python-ipython-notebook" urlDisplayName="IPython Notebook" pageTitle="IPython Notebook - Azure tutorial" metaKeywords="" description="A tutorial that shows how to deploy the IPython Notebook on Azure, using Linux or Windows virtual machines (VMs)." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="IPython Notebook on Azure" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Azure의 IPython Notebook

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p><a href="http://ipython.org">IPython 프로젝트</a>(영문)는 강력한 대화형 셸, 고성능의 사용하기 쉬운 병렬 라이브러리 및 웹 기반 환경을 포함하는 과학적 컴퓨팅을 위한 도구 모음인 IPython Notebook을 제공합니다. Notebook은 코드 실행과 라이브 계산 문서를 결합하는 대화형 컴퓨팅 작업 환경을 제공합니다. 이러한 노트북 파일에는 임의 텍스트, 수식, 입력 코드, 결과, 그래픽, 비디오를 비롯하여 최신 웹 브라우저에서 표시할 수 있는 기타 모든 미디어가 포함될 수 있습니다.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="label">자습서 보기</a> <a style="background-image: url('/media/devcenter/python/ipy-youtube2.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="dev-onpage-video"><span class="icon">동영상 재생</span></a> <span class="time">오전 5:22:00</span></div>

</div>

Python을 처음 접하며 재미있는 대화형 환경에서 이를 배우려는 경우든,
병렬/기술 컴퓨팅에 대해 상당한 조예가 있는 경우든 IPython Notebook을
사용하면 많은 이점을 누릴 수 있습니다. 그 기능을 보여 주는 다음 스크린샷은
 IPython Notebook과 SciPy 및 matplotlib 패키지를 함께 사용하여 소리 녹음의
 구조를 분석하는 작업을 보여 줍니다.

![스크린샷][스크린샷]

이 문서에서는 Linux 또는 Windows VM(가상 컴퓨터)을 사용하여 Azure에
 IPython Notebook를 배포하는 방법을 보여 줍니다. Azure에서 IPython Notebook을
 사용하면 Python과 많은 라이브러리를 완벽하게 활용할 수 있는,
 확장 가능한 컴퓨팅 리소스에 대한 웹 기반 인터페이스를 쉽게 제공할 수
있습니다. 모든 설치는 클라우드에서 수행되므로 사용자는 최신 웹 브라우저만
 있다면 로컬에서 어떤 구성도 수행하지 않고 이러한 리소스에 액세스할 수
 있습니다.

[WACOM.INCLUDE [create-account-and-vms-note][create-account-and-vms-note]]

## Azure에서 VM 만들기 및 구성

첫 번째 단계는 Azure를 실행하는 VM(가상 컴퓨터)을 만드는 것입니다.
이 VM은 클라우드에서 작동하는 완벽한 운영 체제이며 IPython Notebook을
실행하는 데 사용됩니다. Azure는 Linux 및 Windows 가상 컴퓨터를 모두 실행할
 수 있으므로, 두 가지 유형의 가상 컴퓨터에서 IPython을 설치하는 과정에 대해 살펴보겠습니다.

### Linux VM

[여기][여기]에 나와 있는 지침을 따라서 *OpenSUSE* 또는 *Ubuntu* 배포의 가상 컴퓨터를 만드십시오. 이 자습서에서는 OpenSUSE 12.3 및 Ubuntu Server 13.04를 사용합니다. 기본 사용자 이름은 *azureuser*라고 가정합니다.

### Windows VM

[여기][1]에 나와 있는 지침을 따라서 *Windows Server 2012 Datacenter* 배포의 가상 컴퓨터를 만드십시오. 이 자습서에서는 사용자 이름이 *azureuser*라고 가정합니다.

## IPython Notebook의 끝점 만들기

이 단계는 Linux와 Windows VM 모두에 적용됩니다. 이 문서의 뒷부분에서,
포트 9999에서 노트북 서버를 실행하도록 IPython을 구성합니다. 이 포트를
공개적으로 사용 가능하도록 설정하려면 Azure 관리 포털에 끝점을 만들어야 합니다. 이
끝점은 Azure 방화벽의 포트에서 열리며 공용 포트(HTTPS, 443)를 VM(9999)의 개인
 포트에 매핑합니다.

끝점을 만들려면 VM 대시보드로 이동하고, "끝점"과 "끝점 추가"를 차례로 클릭한
 후 새 끝점(이 예에서는 `ipython_nb`)을 만듭니다. 프로토콜로
 TCP를, 공용 포트로 443을, 개인 포트로 9999를 선택합니다.

![스크린샷][2]

이 단계 이후에는 "끝점" 대시보드 탭이 다음과 같이 표시됩니다.

![스크린샷][3]

## VM에 필수 소프트웨어 설치

VM에서 IPython Notebook을 실행하려면 먼저 IPython 및 종속성을 설치해야
 합니다.

### Linux(OpenSUSE)

IPython 및 종속성을 설치하려면 Linux VM에 SSH를 설정하고 다음과 같은 단계를
 수행합니다.

다음을 수행하여 [NumPy][NumPy](영문), [Matplotlib][Matplotlib](영문), [Tornado][Tornado](영문) 및 기타 IPython의 종속성을 설치합니다.

    sudo zypper install python-matplotlib
    sudo zypper install python-tornado
    sudo zypper install ipython

### Linux(Ubuntu)

IPython 및 종속성을 설치하려면 Linux VM에 SSH를 설정하고 다음과 같은 단계를
 수행합니다.

다음을 수행하여 [NumPy][NumPy](영문), [Matplotlib][Matplotlib](영문), [Tornado][Tornado](영문) 및 기타 IPython의 종속성을 설치합니다.

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

Windows VM에 IPython 및 종속성을 설치하려면 원격 데스크톱을 사용하여 VM에 연결합니다. 그런 다음 Windows PowerShell에서
 명령줄 작업을 실행하여 다음 단계를 수행하십시오.

**참고**: Internet Explorer를 사용하여 다운로드하려면 몇 가지 보안 설정을 변경해야 합니다. **서버 관리자**에서 **로컬 서버**와 **IE 보안 강화 구성**을 차례로 클릭한 후 관리자를 대신하여 해당 설정을 해제합니다. IPython 설치를 완료한 후 다시 사용하도록 설정할 수 있습니다.

1.  [python.org][python.org](영문)에서 Python 2.7.5(32비트)를 설치합니다.
    또한 `C:\Python27` 및 `C:\Python27\Scripts`를 `PATH`
    환경 변수에 추가해야 합니다.

2.  [python-distribute.org][python-distribute.org](영문)에서 **distribute\_setup.py**
     파일을 다운로드한 후 다음 명령을 실행하여 배포를 설치합니다.

        python distribute_setup.py

3.  다음 명령을 실행하여 [Tornado][Tornado](영문) 및 [PyZMQ][PyZMQ](영문)를 설치합니다.

        easy_install tornado
        easy_install pyzmq

4.  웹 사이트에서 사용 가능한 `.exe` 이진 설치 관리자를 사용하여
     [NumPy][NumPy](영문)를 다운로드해서 설치합니다. 이 문서의 작성 시간을 기준으로 최신 버전은 **numpy-1.7.1-win32-superpack-python2.7.exe**입니다.

5.  웹 사이트에서 사용 가능한 `.exe` 이진 설치 관리자를 사용하여
     [Matplotlib][Matplotlib](영문)를 다운로드해서 설치합니다. 이 문서의 작성 시간을 기준으로 최신 버전은 **matplotlib-1.2.1.win32-py2.7.exe**입니다.

6.  OpenSSL을 다운로드하여 설치합니다. [][]<http://slproweb.com/products/Win32OpenSSL.html></a>(영문)에서 Windows 버전의 OpenSSL을 찾아볼 수 있습니다.

    -   **Light** 버전을 설치하는 경우 **Visual C++ 2008 재배포 가능**(이 페이지에서도 다운로드 가능)을 설치해야 합니다.

    -   `C:\OpenSSL-Win32\bin`을 `PATH` 환경 변수에 추가해야 합니다.

    > [WACOM.NOTE] OpenSSL을 설치할 경우 1.0.1g 이상 버전을 사용하십시오. 이 버전에 하트블리드(Heartbleed) 보안 취약성에 대한 픽스가 포함되어 있습니다.

7.  다음 명령을 사용하여 IPython을 설치합니다.

        easy_install ipython

8.  Windows 방화벽의 포트를 엽니다. Windows Server 2012에서는 방화벽이 들어오는 연결을 기본적으로 차단합니다. 포트 9999를 열려면 다음 단계를 따르십시오.

    -   시작 화면에서 **고급 보안이 포함된 Windows 방화벽**을 시작합니다.

    -   왼쪽 창에서 **인바운드 규칙**을 클릭합니다.

    -   작업 패널에서 **새 규칙...**을 클릭합니다.

    -   새 인바운드 규칙 마법사에서 **포트**를 선택합니다.

    -   다음 화면에서 **TCP**를 선택하고 **특정 로컬 포트**에 **9999**를 입력합니다.

    -   기본값을 그대로 사용하고, 규칙의 이름을 지정하고 마침을 클릭합니다.

### IPython Notebook 구성

이번에는 IPython Notebook을 구성합니다. 첫 번째 단계는 사용자 지정 IPython
 구성 프로필을 만들어 구성 정보를 캡슐화하는 것입니다.

    ipython profile create nbserver

그런 다음 프로필 디렉터리로 변경하여(`cd`) SSL 인증서를 만들고 프로필
 구성 파일을 편집합니다.

Linux(OpenSUSE)에서

    cd ~/.config/ipython/profile_nbserver/

Linux(Ubuntu)에서

    cd ~/.ipython/profile_nbserver/

Windows에서

    cd \users\azureuser\.ipython\profile_nbserver

두 플랫폼 모두에서 SSL 인증서를 다음과 같이 만듭니다.

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

자체 서명된 SSL 인증서를 만들고 있으므로 노트북에 연결할 때 브라우저에서
 보안 경고를 표시합니다. 장기 프로덕션용으로는
 조직과 연결된 적절히 서명된 인증서를 사용하는 것이 좋습니다.
 이 데모에서는 인증서 관리를 다루지 않으므로 지금은
 자체 서명된 인증서만을 설명하겠습니다.

인증서를 사용할 뿐만 아니라, 권한이 없는 사용자로부터 노트북을 보호하기
 위해 암호도 제공해야 합니다. 보안상의 이유로 IPython은 구성
 파일에서 암호화된 암호를 사용하므로, 먼저 암호를 암호화해야 합니다.
 IPython에서 암호화할 수 있는 유틸리티를 제공하므로 명령 프롬프트에서 다음을 실행하십시오.

    python -c "import IPython;print IPython.lib.passwd()"

암호 및 암호 확인을 위한 프롬프트가 표시된 후 다음과 같이 암호가 표시됩니다.

    Enter password: 
    Verify password: 
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

이제 현재 프로필 디렉터리에서
`ipython_notebook_config.py` 파일인 프로필 구성 파일을 편집합니다. 이 파일에는 몇 개의 필드가
 있으며 기본적으로 모두 주석으로 처리되어 있습니다. 원하는 텍스트
 편집기로 이 파일을 열 수 있으며, 다음과 같은 내용이 있는지 확인해야
 합니다.

    c = get_config()

    # This starts plotting support always with matplotlib
    c.IPKernelApp.pylab = 'inline'

    # You must give the path to the certificate file.

    # If using a Linux VM (OpenSUSE):
    c.NotebookApp.certfile = u'/home/azureuser/.config/ipython/profile_nbserver/mycert.pem'

    # If using a Linux VM (Ubuntu):
    c.NotebookApp.certfile = u'/home/azureuser/.ipython/profile_nbserver/mycert.pem'

    # And if using a Windows VM:
    c.NotebookApp.certfile = r'C:\Users\azureuser\.ipython\profile_nbserver\mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port

    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### IPython Notebook에서 실행

이제 IPython Notebook을 시작할 준비가 되었습니다. 그렇게 하려면
 노트북을 저장할 디렉터리로 이동하여 다음과 같이 IPython
Notebook 서버를 시작합니다.

    ipython notebook --profile=nbserver

이제
`https://[Your Chosen Name Here].cloudapp.net` 주소에서 IPython Notebook에 액세스할 수 있어야 합니다.

처음으로 노트북에 액세스할 때 로그인 페이지에서 암호를 묻습니다.

![스크린샷][4]

로그인한 후에는 모든 노트북 작업에 대한 제어 센터인 "IPython Notebook
 대시보드"가 표시됩니다. 이 페이지에서 새 노트북을 만들거나,
 기존 노트북을 여는 등의 작업을 수행할 수 있습니다.

![스크린샷][5]

"New Notebook" 단추를 클릭한 경우 다음과 같이 열린 페이지가 표시됩니다.

![스크린샷][6]

`In []:` 프롬프트로 표시되는 이 영역은 입력 영역이며, 여기서 유효한 Python
 코드를 입력할 수 있고 `Shift-Enter`를 누르거나 "Play" 아이콘(도구 모음의
 오른쪽을 가리키는 삼각형)을 클릭하면 코드가 실행됩니다.

NumPy 및 matplotlib 지원을 자동으로 시작하도록 노트북을 구성했으므로 다음과
 같은 그림도 생성할 수 있습니다.

![스크린샷][7]

## 강력한 패러다임: 다양한 미디어를 지원하는 라이브 컴퓨팅 문서

노트북은 Python과 워드 프로세서가 결합된 방식이므로 노트북 자체는 Python 및
 워드 프로세서를 사용해 본 적이 있는 사람들에게는 매우 자연스럽게 느껴집니다. Python 코드 블록을
 실행할 수 있지만 도구 모음의 드롭다운 메뉴를 사용하여 "Code"에서
 "Markdown"으로 셀 스타일을 변경해서 메모 및 기타 텍스트를 유지할 수도
있습니다.

![스크린샷][8]

그러나 IPython 노트북은 컴퓨팅 및 다양한 미디어(텍스트, 그래픽, 비디오 및 최신 웹
 브라우저에서 표시할 수 있는 모든 것)의 결합을 허용하므로 워드 프로세서보다
 훨씬 많은 기능을 제공합니다. 예를 들어 교육을 위한 컴퓨팅
 기능과 설명 비디오를 결합할 수 있습니다.

![스크린샷][9]

또는 노트북 파일 안에 사용 가능한 라이브 상태의 외부 웹 사이트를 포함할 수
 있습니다.

![스크린샷][10]

또한 Python에서 지원하는 여러 가지 탁월한 과학 및 기술 컴퓨팅용
 라이브러리 덕분에 단일 환경에서 단순한 계산뿐만 아니라 복잡한 네트워크 분석도
 간편하게 수행할 수 있습니다.

![스크린샷][11]

최신 웹 기능과 라이브 컴퓨팅을 결합한 이 패러다임은 많은 가능성을 제공하며,
 클라우드에 이상적으로 적합하므로 Notebook을 다음과 같이
 활용할 수 있습니다.

-   컴퓨팅 스크래치패드로 사용하여 문제에 대한 예비 작업을 기록합니다.

-   '라이브' 컴퓨팅 형식이나 하드카피 형식(HTML, PDF)으로 결과를 동료와
     공유하는 데 사용할 수 있습니다.

-   학생들이 실제 코드로 즉시 실험해 보고 수정하고 대화형으로 다시 실행할
     수 있도록 계산이 포함된 라이브 교육 교재를 배포하고 표시하는 데 사용할 수
     있습니다.

-   다른 사람이 즉시 재현하고 유효성을 검사하고 확장할 수 있는 방식으로,
     연구 결과를 나타내는 "실행 가능한 백서"를 제공하는 데 사용할 수 있습니다.

-   협업 컴퓨팅을 위한 플랫폼으로 사용할 수 있습니다. 즉, 많은 사용자가 동일한 노트북
     서버에 로그인하여 라이브 컴퓨팅 세션을 공유할 수 있습니다.

-   그 외에 많은 작업을 수행할 수 있습니다.

IPython 소스 코드 리포지토리로 이동하면
[노트북
예제][노트북
예제](영문)가 있는 전체 디렉터리를 볼 수 있습니다.
여기서 예제를 다운로드하여 Azure IPython VM에서 실험해 볼 수 있습니다.
사이트에서 `.ipynb` 파일을 다운로드하고 노트북
 Azure VM의 대시보드에 업로드(또는 VM으로 직접 다운로드)하면 됩니다.

## 결론

IPython Notebook은 Azure에서 Python 에코시스템의 기능에 대화형으로
 액세스할 수 있는 강력한 인터페이스를 제공합니다. 이 인터페이스는 간단한
 Python 탐색 및 학습, 데이터 분석 및 시각화, 시뮬레이션, 병렬 컴퓨팅 등 다양한
 사용 사례를 처리합니다. 결과적으로 생성된 Notebook
 문서에는 작업을 수행한 후 다른 IPython 사용자와 공유할 수 있는 전체 컴퓨팅
 레코드가 포함됩니다. IPython Notebook은 로컬 응용 프로그램으로
 사용할 수 있지만 Azure의 클라우드 배포에 이상적으로 적합합니다.

또한 IPython의 핵심 기능은
[Python Tools for Visual Studio][Python Tools for Visual Studio](PTVS)(영문)를 통해 Visual Studio 내에서도 사용할 수 있습니다. PTVS는 Microsoft의 무료 오픈 소스
플러그 인으로, IntelliSense, 디버깅, 프로파일링, 병렬 컴퓨팅 통합 등 고급 편집기
 기능을 포함하는 고급 Python 개발 환경으로 Visual Studio를 전환해 줍니다.

  [IPython 프로젝트]: http://ipython.org
  [자습서 보기]: http://go.microsoft.com/fwlink/?LinkID=254535&clcid=0x409
  [스크린샷]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png
  [create-account-and-vms-note]: ../includes/create-account-and-vms-note.md
  [여기]: /ko-kr/manage/linux/tutorials/virtual-machine-from-gallery/
  [1]: /ko-kr/manage/windows/tutorials/virtual-machine-from-gallery/
  [2]: ./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-005.png
  [3]: ./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-006.png
  [NumPy]: http://www.numpy.org/ "NumPy"
  [Matplotlib]: http://matplotlib.sourceforge.net/ "Matplotlib"
  [Tornado]: http://www.tornadoweb.org/ "Tornado"
  [python.org]: http://www.python.org/download
  [python-distribute.org]: http://python-distribute.org/
  [PyZMQ]: https://github.com/zeromq/pyzmq "PyZMQ"
  []: http://slproweb.com/products/Win32OpenSSL.html
  [4]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png
  [5]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png
  [6]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png
  [7]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-004.png
  [8]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-005.png
  [9]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-006.png
  [10]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-007.png
  [11]: ./media/virtual-machines-python-ipython-notebook/ipy-notebook-008.png
  [노트북
  예제]: https://github.com/ipython/ipython/tree/master/examples/notebooks
  [Python Tools for Visual Studio]: http://pytools.codeplex.com
