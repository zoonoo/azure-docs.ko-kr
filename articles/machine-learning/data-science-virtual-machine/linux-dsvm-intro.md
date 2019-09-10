---
title: '빠른 시작: CentOS Linux 만들기'
titleSuffix: Azure Data Science Virtual Machine
description: 분석 및 기계 학습을 위해 Azure에서 Linux Data Science Virtual Machine을 만들고 구성합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: quickstart
ms.date: 03/16/2018
ms.openlocfilehash: 1ab3a527643bc5a567644a937fdd1eb9e31bf567
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195343"
---
# <a name="quickstart-set-up-a-linux-centos-data-science-virtual-machine-in-azure"></a>빠른 시작: Azure에서 Linux CentOS Data Science Virtual Machine 설정

Linux DSVM(Data Science Virtual Machine)은 CentOS 기반 Azure 가상 머신입니다. Linux DSVM에는 데이터 분석 및 기계 학습에 사용할 수 있는 미리 설치된 도구 모음이 제공됩니다. 

Linux DSVM에 포함된 주요 소프트웨어 구성 요소는 다음과 같습니다.

* Linux CentOS 배포 운영 체제
* Microsoft Machine Learning Server
* Anaconda Python 배포(3.5 및 2.7 버전) - 인기 있는 데이터 분석 라이브러리 포함
* JuliaPro(큐레이팅된 Julia 언어 배포) 및 인기 있는 과학 및 데이터 분석 라이브러리
* Spark 독립 실행형 인스턴스 및 단일 노드 Hadoop(HDFS, YARN)
* JupyterHub - R, Python, PySpark 및 Julia 커널을 지원하는 다중 사용자 Jupyter Notebook 서버
* Azure Storage Explorer.
* Azure CLI - Azure 리소스 관리용 Azure 명령줄 인터페이스
* PostgresSQL 데이터베이스
* 기계 학습 도구:
  * [CNTK](https://github.com/Microsoft/CNTK)(Microsoft Cognitive Toolkit) - Microsoft Research의 딥 러닝 소프트웨어 도구 키트
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) - 온라인, 해시, allreduce, 감소, learning2search, 활성 및 대화형 학습과 같은 기술을 지원하는 고속 기계 학습 시스템
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/) - 빠르고 정확하고 향상된 트리 구현을 제공하는 도구
  * [Rattle](https://togaware.com/rattle/) - R에서 데이터 분석 및 기계 학습을 쉽게 시작할 수 있게 하는 그래픽 도구. Rattle은 자동 R 코드 생성을 사용하여 GUI 기반 데이터 검색 및 모델링을 모두 제공합니다.
* Java, Python, Node.js, Ruby 및 PHP의 Azure SDK
* R 및 Python의 라이브러리 - Azure Machine Learning 및 기타 Azure 서비스에서 사용
* 개발 도구 및 편집기(RStudio, PyCharm, IntelliJ, Emacs, gedit, vi)

데이터 과학은 다음과 같은 일련의 작업을 반복합니다.

1. 데이터 찾기, 로드 및 전처리
1. 모델 빌드 및 테스트
1. 지능형 애플리케이션에서 사용하기 위한 모델 배포

데이터 과학자는 다양한 도구를 사용하여 이러한 태스크를 완료합니다. 올바른 버전의 소프트웨어를 찾은 다음, 해당 소프트웨어를 다운로드, 컴파일 및 설치하는 데 시간이 오래 걸릴 수 있습니다.

Linux DSVM은 이 부담을 크게 줄일 수 있습니다. Linux DSVM을 사용하여 분석 프로젝트를 바로 시작합니다. Linux DSVM은 R, Python, SQL, Java 및 C++를 포함하여 다양한 언어로 작업을 수행하는 데 도움이 됩니다. Eclipse는 코드를 개발하고 테스트하는 데 사용하기 쉬운 IDE를 제공합니다. DSVM에 포함된 Azure SDK는 Linux에서 Microsoft 클라우드 플랫폼에 다양한 서비스를 사용하여 애플리케이션을 빌드하는 데 도움이 됩니다. Ruby, Perl, PHP 및 Node.js를 포함한 다른 언어는 미리 설치되어 있습니다.

DSVM 이미지에는 소프트웨어 요금이 부과되지 않습니다. DSVM 이미지를 사용하여 프로비저닝하는 가상 머신의 크기에 따라 평가되는 Azure 하드웨어 사용 요금만 지불합니다. 컴퓨팅 요금에 대한 자세한 내용은 Azure Marketplace의 [Linux(CentOS)용 Data Science Virtual Machine 목록](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

Linux Data Science Virtual Machine을 만들려면 먼저 다음 필수 구성 요소가 있어야 합니다.

* **Azure 구독**: Azure 구독을 얻으려면 [Azure 체험 계정 만들기](https://azure.microsoft.com/free/)를 참조하세요.
* **Azure Storage 계정**: Azure 스토리지 계정을 얻으려면 [스토리지 계정 만들기](../../storage/common/storage-quickstart-create-account.md)를 참조하세요. 기존 Azure 스토리지 계정을 사용하지 않으려면 DSVM을 만들 때 스토리지 계정을 만들 수 있습니다.

## <a name="other-versions-of-the-data-science-virtual-machine"></a>다른 버전의 Data Science Virtual Machine

Data Science Virtual Machine은 다음 버전에서도 사용할 수 있습니다.

* [Ubuntu](dsvm-ubuntu-intro.md): Ubuntu 이미지에는 딥 러닝 프레임워크를 포함하여 CentOS 이미지와 동일한 도구가 많이 있습니다. 
* [Windows](provision-vm.md)

## <a name="create-a-linux-data-science-virtual-machine"></a>Linux Data Science Virtual Machine 만들기

Linux DSVM 인스턴스를 만들려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm)에서 가상 머신 목록으로 이동합니다.
1. **만들기**를 선택하여 마법사를 엽니다.

   ![Data Science Virtual Machine을 구성하는 마법사](./media/linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
1. 마법사의 각 단계에 대해 다음 정보를 입력하거나 선택합니다.
   
   **1** **기본**:

      * **이름**: 만들려는 데이터 과학 서버 이름입니다.
      * **사용자 이름**: 첫 번째 계정의 로그인 ID입니다.
      * **암호**: 첫 번째 계정의 암호입니다. (암호 대신 SSH 공개 키를 사용할 수 있습니다.)
      * **구독**: 둘 이상의 구독이 있으면 머신을 만들고 요금을 청구할 구독을 선택합니다. 구독에 대한 리소스를 만들 수 있는 권한이 있어야 합니다.
      * **리소스 그룹**: 새 리소스 그룹을 만들거나 기존 그룹을 사용할 수 있습니다.
      * **위치**: DSVM에 사용할 데이터 센터를 선택합니다. 대부분의 경우 대부분의 데이터를 보유하고 있거나 네트워크에 가장 빠르게 액세스할 수 있도록 실제 위치와 가장 가까운 데이터 센터를 선택합니다.
   
   **2** **크기**: 기능 요구 사항과 비용 제약 조건에 맞는 서버 유형을 선택합니다. **모두 보기** 를 선택하면 더 많은 VM 크기 선택 항목을 확인할 수 있습니다.
   
   
   **3** **설정**:
      * **디스크 유형**: SSD(반도체 드라이브)를 선호하는 경우 **프리미엄**을 선택합니다. 그렇지 않은 경우 **표준**을 선택합니다.
      * **스토리지 계정**: 새 Azure 스토리지 계정을 구독에 만들거나, 마법사의 **기본** 단계에서 선택한 것과 동일한 위치에 있는 기존 계정을 사용할 수 있습니다.
      * **기타 매개 변수**: 대부분의 경우 기본값을 사용하여 다른 매개 변수를 구성 합니다. 기본값이 아닌 값을 보려면 마우스로 매개 변수에 대한 정보 링크 위를 가리킵니다.
   
   **4** **요약**: 입력한 정보가 올바른지 확인합니다.
   
   **5** **구입**: 프로비저닝 프로세스를 시작하려면 **구입**을 선택합니다. 거래 조건에 대한 링크가 제공됩니다. **크기**에서 선택한 서버 크기에 대한 컴퓨팅 외에는 DSVM에 대한 추가 요금이 없습니다.

프로비저닝하는 데 10-20 분 정도 걸립니다. 프로비저닝 상태가 Azure Portal에 표시됩니다.

## <a name="how-to-access-the-linux-data-science-virtual-machine"></a>Linux 데이터 과학 Virtual Machine에 액세스하는 방법

DSVM이 만들어지면 SSH를 사용하여 로그인할 수 있습니다. 마법사의 **기본** 섹션에서 텍스트 셸 인터페이스에 대해 만든 계정 자격 증명을 사용합니다. Windows에서는 [PuTTY](https://www.putty.org)와 같은 SSH 클라이언트 도구를 다운로드할 수 있습니다. 그래픽 데스크톱(X Windows 시스템)을 선호하는 경우 PuTTY에서 X11 전달을 사용하거나 X2Go 클라이언트를 설치할 수 있습니다.

> [!NOTE]
> 테스트 결과 X2Go 클라이언트의 성능이 X11 전달보다 더 우수했습니다. 그래픽 데스크톱 인터페이스에서는 X2Go 클라이언트를 사용하는 것이 좋습니다.

## <a name="install-and-configure-the-x2go-client"></a>X2Go 클라이언트 설치 및 구성

Linux DSVM은 X2Go 서버를 통해 프로비저닝되었고 클라이언트 연결을 허용할 준비가 되었습니다. Linux DSVM 그래픽 데스크톱에 연결하려면 클라이언트에서 다음 절차를 수행합니다.

1. 사용 중인 클라이언트 플랫폼용 X2Go 클라이언트를 [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient)에서 다운로드하여 설치합니다.
1. X2Go 클라이언트를 실행합니다. **새 세션**을 선택합니다. 여러 탭이 있는 구성 창이 열립니다. 다음 구성 매개 변수를 입력합니다.
   * **세션 탭**:
     * **호스트**: Linux DSVM의 호스트 이름 또는 IP 주소를 입력합니다.
     * **로그인**: Linux DSVM에서 사용자 이름을 입력합니다.
     * **SSH 포트**: 기본값(**22**)을 그대로 둡니다.
     * **세션 유형**: 값을 **XFCE**로 변경합니다. 현재 Linux DSVM은 XFCE 데스크톱만 지원합니다.
   * **미디어 탭**: 사운드 지원 및 클라이언트 인쇄를 사용하지 않으려면 해제할 수 있습니다.
   * **공유 폴더**: 클라이언트 머신의 디렉터리를 Linux DSVM에 탑재하려면 DSVM과 공유하려는 클라이언트 머신 디렉터리를 추가합니다.

SSH 클라이언트를 사용하거나 X2Go 클라이언트를 통해 XFCE 그래픽 데스크톱을 사용하여 DSVM에 로그인하면 DSVM에 설치되고 구성된 도구를 사용할 준비가 됩니다. XFCE에는 대부분의 도구에 대한 애플리케이션 메뉴 바로 가기와 데스크톱 아이콘이 표시됩니다.

## <a name="tools-installed-on-the-linux-data-science-virtual-machine"></a>Linux 데이터 과학 Virtual Machine에 설치된 도구

### <a name="machine-learning-server"></a>Machine Learning Server

R은 가장 많이 사용되는 데이터 분석 및 Machine Learning 언어 중 하나입니다. R을 분석에 사용하려는 경우 DSVM에서 Microsoft R Open 및 Math Kernel Library가 있는 Machine Learning Server를 사용합니다. Math Kernel Library는 분석 알고리즘에서 일반적인 수학 연산을 최적화합니다. R Open은 CRAN R과 완벽하게 호환됩니다. CRAN R에 게시된 모든 R 라이브러리는 R Open에 설치할 수 있습니다. 

Machine Learning Server를 사용하여 R 모델을 웹 서비스로 확장하고 운영할 수 있습니다. RStudio, vi 또는 Emacs 등의 기본 편집기 중 하나에서 R 프로그램을 편집할 수 있습니다. Emacs 편집기는 DSVM에 미리 설치되어 있습니다. Emacs ESS(Emacs Speaks Statistics) 패키지는 Emacs 편집기에서 R 파일 작업을 간소화합니다.

R 콘솔을 열려면 셸에서 **R**을 입력합니다. 이 명령을 사용하면 대화형 환경으로 이동합니다. R 프로그램을 개발하려면 일반적으로 Emacs 또는 vi와 같은 편집기를 사용한 다음, R에서 스크립트를 실행합니다. RStudio는 R 프로그램을 개발할 수 있는 완벽한 그래픽 IDE를 제공합니다.

[상위 20개 R 패키지](https://www.kdnuggets.com/2015/06/top-20-r-packages.html)를 설치하는 데 사용할 수 있는 R 스크립트는 DSVM에 포함되어 있습니다. R 대화형 인터페이스에 있으면 이 스크립트를 실행할 수 있습니다. 앞에서 설명한 대로 해당 인터페이스를 열려면 셸에서 **R**을 입력합니다.  

### <a name="python"></a>Python

Anaconda Python은 Python 3.5 및 2.7 환경과 함께 설치됩니다. 2\.7 환경은 _root_라고 하며, 3.5 환경은 _py35_라고 합니다. 이 배포 버전에는 약 300개의 가장 인기 있는 수학, 엔지니어링 및 데이터 분석 패키지와 함께 기본 Python이 포함되어 있습니다.

py35 환경은 기본값입니다. root(2.7) 환경을 활성화하려면 다음 명령을 사용합니다.

```bash
source activate root
```

py35 환경을 다시 활성화하려면 다음 명령을 사용합니다.

```bash
source activate py35
```

Python 대화형 세션을 호출하려면 셸에서 **python**을 입력합니다. 

Conda 또는 pip를 사용하여 추가 Python 라이브러리를 설치합니다. pip의 경우 기본값을 원하지 않는 경우 올바른 환경을 먼저 활성화합니다.

```bash
source activate root
pip install <package>
```

또는 전체 경로를 pip로 지정합니다.

```bash
/anaconda/bin/pip install <package>
```

Conda의 경우 항상 환경 이름(py35 또는 root)을 지정해야 합니다.

```bash
conda install <package> -n py35
```

그래픽 인터페이스를 사용하거나 X11 전달을 설정한 경우 **pycharm**을 입력하여 PyCharm Python IDE를 열 수 있습니다. 기본 텍스트 편집기를 사용할 수 있습니다. 또한 Anaconda Python 배포에 번들로 포함된 Python IDE인 Spyder를 사용할 수도 있습니다. Spyder를 사용하려면 그래픽 데스크톱 또는 X11 전달이 필요합니다. 그래픽 데스크톱에는 Spyder 바로 가기가 있습니다.

### <a name="jupyter-notebook"></a>Jupyter 노트북

Anaconda 배포에는 코드 및 분석을 공유하는 환경인 Jupyter Notebook도 제공됩니다. JupyterHub를 통해 Jupyter Notebook에 액세스합니다. 로컬 Linux 사용자 이름과 암호를 사용하여 로그인합니다.

Jupyter Notebook 서버는 Python 2, Python 3 및 R 커널을 사용하여 미리 구성되어 있습니다. **Jupyter Notebook** 데스크톱 아이콘을 사용하여 브라우저를 열고 Jupyter Notebook 서버에 액세스합니다. SSH 또는 X2Go 클라이언트를 통해 DSVM에 액세스하는 경우 https:\//localhost:8000/에서 Jupyter Notebook 서버에 액세스할 수도 있습니다.

> [!NOTE]
> 인증서 경고가 나타나는 경우 계속 진행하세요.

모든 호스트에서 Jupyter Notebook 서버에 액세스할 수 있습니다. **https:\//\<DSVM DNS 이름 또는 IP 주소\>:8000/** 을 입력합니다.

> [!NOTE]
> DSVM이 프로비저닝될 때 기본적으로 8000 포트가 방화벽에서 열립니다. 

Microsoft는 Notebook 샘플을 Python과 R에서 각각 하나씩 패키지했습니다. 로컬 Linux 사용자 이름과 암호를 사용하여 Jupyter Notebook에 인증하면 샘플에 대한 링크가 Notebook 홈페이지에 표시됩니다. 새 Notebook을 만들려면 **새로 만들기**를 선택한 다음, 사용하려는 언어 커널을 선택합니다. **새로 만들기** 단추가 표시되지 않으면 왼쪽 위에서 **Jupyter** 아이콘을 선택하여 Notebook 서버의 홈페이지로 이동합니다.

### <a name="spark-standalone"></a>Spark 독립 실행형 

Spark 독립 실행형 모드의 인스턴스는 Linux DSVM에 미리 설치되어 Spark 애플리케이션을 먼저 로컬로 개발한 후에 대형 클러스터에서 테스트 및 배포하는 데 도움이 됩니다. 

Jupyter 커널을 통해 PySpark 프로그램을 실행할 수 있습니다. Jupyter를 열어 **새로 만들기** 단추를 선택하면 사용 가능한 커널의 목록이 표시됩니다. **Spark - Python**은 Python 언어를 사용하여 Spark 애플리케이션을 빌드할 수 있는 PySpark 커널입니다. 또한 PyCharm 또는 Spyder와 같은 Python IDE를 사용하여 Spark 프로그램을 빌드할 수도 있습니다. 

이 독립 실행형 인스턴스에서 Spark 스택은 호출 클라이언트 프로그램에서 실행됩니다. 이 기능을 사용하면 Spark 클러스터에서 개발하는 것에 비해 문제를 더 빠르고 쉽게 해결할 수 있습니다.

Jupyter는 PySpark Notebook 샘플을 제공합니다. 이 샘플은 Jupyter의 홈 디렉터리($HOME/notebooks/SparkML/pySpark) 아래에 있는 SparkML 디렉터리에서 찾을 수 있습니다. 

Spark용 R에서 프로그래밍하는 경우 Machine Learning Server, SparkR 또는 sparklyr을 사용할 수 있습니다. 

Machine Learning Server의 Spark 컨텍스트에서 실행하기 전에 일회성 설정 단계를 수행하여 로컬 단일 노드 Hadoop HDFS 및 YARN 인스턴스를 사용하도록 설정해야 합니다. 기본적으로 Hadoop 서비스는 설치되지만 DSVM에서 사용하지 않도록 설정됩니다. Hadoop 서비스를 사용하도록 설정하려면 다음 명령을 루트로 처음 실행해야 합니다.

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Hadoop 관련 서비스가 필요하지 않은 경우 `systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn`을 실행하여 이를 중지할 수 있습니다.

/dsvm/samples/MRS 디렉터리는 원격 Spark 컨텍스트(DSVM의 독립 실행형 Spark 인스턴스)에서 Machine Learning Server를 개발하고 테스트하는 방법을 보여 주는 샘플을 제공합니다.

### <a name="ides-and-editors"></a>IDE 및 편집기

vi/VIM, Emacs, gedit, PyCharm, RStudio, Eclipse, LaTeX 및 IntelliJ를 포함한 몇 가지 코드 편집기에서 선택할 수 있습니다. 

* gedit, Eclipse, IntelliJ, R Studio 및 PyCharm은 그래픽 편집기입니다. 이를 사용하려면 그래픽 데스크톱에 로그인해야 합니다. 데스크톱 및 애플리케이션 메뉴 바로 가기를 사용하여 엽니다.

* Vim 및 Emacs는 텍스트 기반 편집기입니다. Emacs의 경우 ESS 추가 기능 패키지를 통해 Emacs 편집기에서 R을 더 쉽게 사용할 수 있습니다. 자세한 내용은 [ESS 웹 사이트](https://ess.r-project.org/)에서 확인할 수 있습니다.

* Eclipse는 여러 언어를 지원하는 확장 가능한 오픈 소스 IDE입니다. Java 개발자용 Eclipse IDE는 DSVM에 설치된 버전입니다. 인기 있는 여러 언어용 플러그 인을 설치하여 환경을 확장할 수 있습니다. 

  Azure Toolkit for Eclipse 플러그 인은 Eclipse와 함께 DSVM에 설치됩니다. Java와 같은 언어를 지원하는 Eclipse 개발 환경을 통해 Azure Toolkit for Eclipse를 사용하여 Azure 애플리케이션을 생성 개발, 테스트 및 배포할 수 있습니다.

  Java용 Azure SDK는 Azure Toolkit for Eclipse와 함께 DSVM에 설치됩니다. Java용 Azure SDK를 사용하면 Java 환경 내에서 다양한 Azure 서비스에 액세스할 수 있습니다. 
  
  자세한 내용은 [Eclipse용 Azure 도구 키트 설치](/java/azure/eclipse/azure-toolkit-for-eclipse)를 참조하세요.

* LaTeX는 texlive 패키지를 통해 [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html)라는 Emacs 추가 기능 패키지와 함께 설치됩니다. 이 패키지는 Emacs에서 LaTeX 문서 작성을 간소화합니다. 

### <a name="databases"></a>데이터베이스

Linux DSVM을 사용하면 여러 데이터베이스 및 명령줄 도구에 액세스할 수 있습니다.

#### <a name="postgressql"></a>PostgresSQL

PostgresSQL 오픈 소스 데이터베이스는 서비스가 실행되고 initdb가 완료된 DSVM에서 사용할 수 있습니다. 데이터베이스와 사용자를 만들어야 합니다. 자세한 내용은 [PostgresSQL 설명서](https://www.postgresql.org/docs/)를 참조하세요.  

#### <a name="squirrel-sql"></a>SQuirreL SQL

SQuirreL SQL은 다양한 데이터베이스(SQL Server, PostgresSQL 및 MySQL 포함)에 연결하고 SQL 쿼리를 실행할 수 있는 그래픽 SQL 클라이언트입니다. 예를 들어 데스크톱 아이콘을 사용하여 그래픽 데스크톱 세션에서(예: X2Go 클라이언트를 통해) SQuirreL SQL을 실행할 수 있습니다. 또는 셸에서 다음 명령을 사용하여 클라이언트를 실행할 수 있습니다.

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

SQuirrel SQL을 처음 사용하기 전에 드라이버 및 데이터베이스 별칭을 설정합니다. JDBC 드라이버는 /usr/share/java/jdbcdrivers에 있습니다.

자세한 내용은 [SQuirreL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots)을 참조하세요.

#### <a name="command-line-tools-for-accessing-sql-server"></a>SQL Server에 액세스하기 위한 명령줄 도구

SQL Server용 ODBC 드라이버 패키지에서는 다음의 두 가지 명령줄 도구도 제공됩니다.

* **bcp**: bcp 도구는 SQL Server 인스턴스와 사용자 지정 형식의 데이터 파일 간에 데이터를 대량 복사합니다. bcp 도구를 사용하여 많은 수의 새 행을 SQL Server 테이블로 가져오거나 데이터를 테이블에서 데이터 파일로 내보낼 수 있습니다. 데이터를 테이블로 가져오려면 해당 테이블에 대해 만든 서식 파일을 사용해야 합니다. 또는 테이블의 구조와 해당 열에 유효한 데이터 형식을 이해해야 합니다.

  자세한 내용은 [bcp를 사용하여 연결](https://msdn.microsoft.com/library/hh568446.aspx)을 참조하세요.

* **sqlcmd**: sqlcmd 유틸리티를 사용하여 명령 프롬프트에서 Transact-SQL 문, 시스템 프로시저 및 스크립트 파일을 입력할 수 있습니다. sqlcmd 유틸리티는 ODBC를 사용하여 Transact-SQL 일괄 처리를 실행합니다.

  자세한 내용은 [sqlcmd를 사용하여 연결](https://msdn.microsoft.com/library/hh568447.aspx)을 참조하세요.

  > [!NOTE]
  > 이 도구는 Linux 및 Windows 플랫폼 간에 약간의 차이가 있습니다. 자세한 내용은 (영문) 설명서를 참조하세요.

#### <a name="database-access-libraries"></a>데이터베이스 액세스 라이브러리

데이터베이스 액세스용 라이브러리는 R 및 Python에서 사용할 수 있습니다.

* R에서는 RODBC 패키지 또는 dplyr 패키지를 사용하여 데이터베이스 서버에서 SQL 문을 쿼리하거나 실행할 수 있습니다.
* Python에서 pyodbc 라이브러리는 ODBC를 기본 계층으로 사용하여 데이터베이스 액세스를 제공합니다.

### <a name="azure-tools"></a>Azure 도구

DSVM에 설치되는 Azure 도구는 다음과 같습니다.

* **Azure CLI**: Azure에서 명령줄 인터페이스를 사용하여 셸 명령을 통해 Azure 리소스를 만들고 관리할 수 있습니다. Azure 도구를 열려면 **azure help**를 입력합니다. 자세한 내용은 [Azure CLI 설명서 페이지](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)를 참조하세요.
* **Azure Storage Explorer**: Azure Storage Explorer는 Azure 스토리지 계정에 저장한 개체를 검색하고 Azure Blob 간에 데이터를 업로드 및 다운로드하는 데 사용할 수 있는 그래픽 도구입니다. 바탕 화면 바로 가기 아이콘을 사용하여 Storage Explorer에 액세스할 수 있습니다. 셸 프롬프트에서 **StorageExplorer**를 입력하여 열 수도 있습니다. X2Go 클라이언트에서 로그인하거나 X11 전달을 설정해야 합니다.
* **Azure 라이브러리**: DSVM에 미리 설치되는 라이브러리는 다음과 같습니다.
  
  * **Python**: Python의 Azure 관련 라이브러리는 *azure*, *azureml*, *pydocumentdb* 및 *pyodbc*입니다. 처음 세 개의 라이브러리를 사용하면 Azure Storage 서비스, Azure Machine Learning 및 Azure Cosmos DB(Azure의 NoSQL 데이터베이스)에 액세스할 수 있습니다. 네 번째 라이브러리인 pyodbc를 SQL Serve용 Microsoft ODBC 드라이버와 함께 사용하면 ODBC 인터페이스를 사용하여 Python에서 SQL Server, Azure SQL Database 및 Azure SQL Data Warehouse에 액세스할 수 있습니다. **pip list** 를 입력하면 나열된 라이브러리를 모두 확인할 수 있습니다. Python 2.7 및 3.5 환경 모두에서 이 명령을 실행해야 합니다.
  * **R**: R의 Azure 관련 라이브러리는 AzureML 및 RODBC입니다.
  * **Java**: Azure Java 라이브러리 목록은 DSVM의 /dsvm/sdk/AzureSDKJava 디렉터리에서 찾을 수 있습니다. 핵심 라이브러리는 Azure Storage 및 관리 API, Azure Cosmos DB, SQL Server용 JDBC 드라이버입니다.  

미리 설치된 Firefox 브라우저에서 [Azure Portal](https://portal.azure.com)에 액세스할 수 있습니다. Azure Portal에서 Azure 리소스를 만들고, 관리하고, 모니터링할 수 있습니다.

### <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning은 예측 분석 솔루션을 빌드, 배포 및 공유하는 데 사용할 수 있는 완전 관리형 클라우드 서비스입니다. Azure Machine Learning Studio에서 실험 및 모델을 빌드합니다. DSVM의 웹 브라우저에서 Azure Machine Learning에 액세스하려면 [Microsoft Azure Machine Learning](https://studio.azureml.net)으로 이동합니다.

Azure Machine Learning Studio에 로그인하면 실험 캔버스를 사용하여 기계 학습 알고리즘에 대한 논리적 흐름을 작성할 수 있습니다. 또한 Azure Machine Learning에서 호스팅되는 Jupyter Notebook에도 액세스할 수 있습니다. 이 Notebook은 Machine Learning Studio의 실험에서 원활하게 작동할 수 있습니다. 

웹 서비스 인터페이스에서 래핑하여 빌드한 기계 학습 모델을 운영합니다. 기계 학습 모델을 운용하면 모든 언어로 작성된 클라이언트가 해당 모델에서 예측을 호출할 수 있습니다. 자세한 내용은 [Machine Learning 설명서](https://azure.microsoft.com/documentation/services/machine-learning/)를 참조하세요.

DSVM에서 모델을 R 또는 Python으로 빌드한 다음, Azure Machine Learning에서 프로덕션 환경에 배포할 수도 있습니다. Microsoft는 이 기능을 지원하기 위해 R(**AzureML**) 및 Python(**azureml**)에 라이브러리를 설치했습니다.

R 및 Python의 모델을 Azure Machine Learning에 배포하는 방법에 대한 자세한 내용은 [Data Science Virtual Machine에서 수행할 수 있는 10가지 작업](vm-do-ten-things.md)을 참조하세요.

> [!NOTE]
> [Data Science Virtual Machine에서 수행할 수 있는 10가지 작업](vm-do-ten-things.md)의 지침은 Windows 버전의 DSVM용으로 작성되었습니다. 그러나 모델을 Azure Machine Learning에 배포하는 방법에 대한 내용은 Linux DSVM에도 적용됩니다.

### <a name="machine-learning-tools"></a>기계 학습 도구

DSVM에는 로컬로 미리 컴파일되고 미리 설치된 몇 가지 기계 학습 도구 및 알고리즘이 제공됩니다. 내용은 다음과 같습니다.

* **Microsoft Cognitive Toolkit**: 딥러닝 도구 키트
* **Vowpal Wabbit**: 속성 온라인 학습 알고리즘
* **XGBoost**: 최적화되고 향상된 트리 알고리즘을 제공하는 도구
* **Python**: Anaconda Python에서는 Scikit-learn 등의 라이브러리가 포함된 기계 학습 알고리즘이 번들로 제공됩니다. `pip install` 하여 다른 라이브러리를 설치할 수 있습니다.
* **R**: R에서는 다양한 기계 학습 기능 라이브러리를 사용할 수 있습니다. 미리 설치된 라이브러리에는 lm, glm, randomForest 및 rpart가 포함됩니다. 다른 라이브러리는 `install.packages(<lib name>)`를 실행하여 설치할 수 있습니다.

Microsoft Cognitive Toolkit, Vowpal Wabbit 및 XGBoost에 대해서는 다음 섹션에서 자세히 설명합니다.

#### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive 도구 키트

Microsoft Cognitive Toolkit는 오픈 소스 딥 러닝 도구 키트입니다. 명령줄 도구(CNTK)이며 이미 PATH에 있습니다.

기본 샘플을 실행하려면 셸에서 다음 명령을 실행합니다.

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

자세한 내용은 [GitHub CNTK 리포지토리](https://github.com/Microsoft/CNTK) 및 [CNTK wiki](https://github.com/Microsoft/CNTK/wiki)를 참조하세요.

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit은 온라인, 해시, allreduce, 감소, learning2search, 활성 및 대화형 학습과 같은 기술을 사용하는 기계 학습 시스템입니다.

기본 예제에서 도구를 실행하려면 다음 명령을 실행합니다.

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Vowpal Wabbit 데모 디렉터리에는 더 큰 다른 데모가 포함되어 있습니다. Vowpal Wabbit에 대한 자세한 내용은 [GitHub Vowpal Wabbit 리포지토리](https://github.com/JohnLangford/vowpal_wabbit) 및 [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki)를 참조하세요.

#### <a name="xgboost"></a>XGBoost

XGBoost 라이브러리는 향상된(트리) 알고리즘에 맞게 설계 및 최적화되었습니다. XGBoost 라이브러리는 확장 가능하고, 이식 가능하며, 정확한 대규모 트리 부스팅을 제공하는 데 필요한 머신의 계산 제한을 극단적으로 높이기 위한 것입니다.

XGBoost는 명령줄 및 R 라이브러리로 제공됩니다.

R에서 XGBoost 라이브러리를 사용하려면 셸에서 **R**을 입력하여 대화형 R 세션을 시작한 다음, 라이브러리를 로드합니다.

R 프롬프트에서 실행할 수 있는 간단한 예제는 다음과 같습니다.

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

XGBoost 명령줄을 실행하려면 셸에서 다음 명령을 실행합니다.

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

.model 파일이 지정된 디렉터리에 기록됩니다. GitHub의 이 데모 예제에 대한 자세한 내용은 [이진 분류](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification)를 참조하세요.

XGBoost에 대한 자세한 내용은 [XGBoost 설명서](https://xgboost.readthedocs.org/en/latest/) 및 [XGBoost GitHub 리포지토리](https://github.com/dmlc/xgboost)를 참조하세요.

#### <a name="rattle"></a>Rattle

Rattle(*R* *A*nalytical *T*ool *T*o *L*earn *E*asily)은 GUI 기반 데이터 검색 및 모델링을 사용합니다. Rattle에서 수행하는 작업은 다음과 같습니다.
- 데이터의 통계 및 시각적 요약을 표시합니다.
- 쉽게 모델링할 수 있는 데이터를 변환합니다.
- 데이터에서 감독되지 않는 모델 및 감독되는 모델을 모두 빌드합니다.
- 모델의 성능을 그래픽으로 표시합니다.
- 새 데이터 세트의 점수를 매깁니다.
- R 코드를 생성합니다.
- 추가 분석을 위해 R에서 직접 실행하거나 시작 지점으로 사용할 수 있는 UI의 작업을 복제합니다.

Rattle을 실행하려면 그래픽 데스크톱 세션에 로그인해야 합니다. 터미널에서 **R**을 입력하여 R 환경을 엽니다. R 프롬프트에서 다음 명령을 입력합니다.

```R
library(rattle)
rattle()
```

탭 세트가 있는 그래픽 인터페이스가 열립니다. Rattle에서 다음 빠른 시작 단계를 사용하여 날씨 데이터 세트 샘플을 사용하고 모델을 빌드합니다. 일부 단계에서는 아직 시스템에 없는 몇 가지 필수 R 패키지를 자동으로 설치하고 로드하라는 메시지가 표시됩니다.

> [!NOTE]
> 패키지를 시스템 디렉터리(기본값)에 설치할 수 있는 권한이 없는 경우 R 콘솔 창에 패키지를 개인 라이브러리에 설치하라는 메시지가 표시될 수 있습니다. 이러한 메시지가 표시되면 **y**를 입력합니다.

1. **실행**을 선택합니다.
1. 날씨 데이터 세트 예제를 로드하라는 대화 상자가 표시됩니다. **예**를 선택하여 예제를 로드합니다.
1. **모델** 탭을 선택합니다.
1. **실행**을 선택하여 의사 결정 트리를 작성합니다.
1. **그리기**를 선택하여 의사 결정 트리를 표시합니다.
1. **포리스트** 옵션을 선택한 다음, **실행**을 선택하여 임의 포리스트를 작성합니다.
1. **평가** 탭을 선택합니다.
1. **위험** 옵션을 선택한 다음, **실행**을 선택하여 두 개의 **위험(누적)** 성능 도표를 표시합니다.
1. **로그** 탭을 선택하여 이전 작업에서 생성된 R 코드를 표시합니다. (현재 Rattle 릴리스의 버그로 인해 로그 텍스트의 **Export this log**(이 로그 내보내기) 앞에 **#** 문자를 삽입해야 합니다.)
1. **내보내기** 단추를 선택하여 *weather_script.R*이라는 R 스크립트 파일을 홈 폴더에 저장합니다.

Rattle 및 R을 종료할 수 있습니다. 이제 생성된 R 스크립트를 수정할 수 있습니다. 또는 스크립트를 있는 그대로 사용하고, 언제든지 이를 실행하여 Rattle UI에서 수행된 모든 작업을 반복합니다. 특히 이는 R 초보자를 위해 간단한 그래픽 인터페이스에서 분석 및 기계 학습을 빠르게 수행하는 동시에 R에서 코드를 자동으로 생성하여 수정하거나 학습할 수 있는 방법입니다.

## <a name="next-steps"></a>다음 단계

학습과 탐색을 계속하는 방법은 다음과 같습니다.

* [Linux용 Data Science Virtual Machine의 데이터 과학](linux-dsvm-walkthrough.md) 연습에서는 여기서 프로비저닝된 Linux DSVM을 사용하여 몇 가지 일반적인 데이터 과학 작업을 수행하는 방법을 보여 줍니다. 
* 이 문서에서 설명하는 도구를 사용하여 DSVM에서 다양한 데이터 과학 도구를 살펴봅니다. 가상 머신 내의 셸에서 `dsvm-more-info`를 실행하여 DSVM에 설치된 도구에 대한 기본 소개 및 자세한 정보에 대한 포인터를 확인할 수도 있습니다.  
* [팀 데이터 과학 프로세스](https://aka.ms/tdsp)를 사용하여 엔드투엔드 분석 솔루션을 체계적으로 빌드하는 방법을 알아봅니다.
* Azure AI 서비스를 사용하는 기계 학습 및 데이터 분석 샘플을 보려면 [Azure AI Gallery](https://gallery.azure.ai/)를 방문합니다.