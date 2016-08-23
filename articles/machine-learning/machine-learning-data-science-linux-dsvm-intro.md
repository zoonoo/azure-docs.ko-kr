<properties
	pageTitle="Linux 데이터 과학 가상 컴퓨터 프로비전 | Microsoft Azure"
	description="분석 및 기계 학습을 수행하기 위해 Azure에서 Linux 데이터 과학 가상 컴퓨터를 구성하고 만듭니다."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="gokuma;bradsev" />

# Linux 데이터 과학 가상 컴퓨터 프로비전 

Linux 데이터 과학 가상 컴퓨터는 데이터 분석 및 기계 학습을 하는 데 흔히 사용되는 도구 모음으로 사전 설치 및 구성된 Azure VM(가상 컴퓨터) 이미지입니다. 포함된 주요 소프트웨어 구성 요소는 다음과 같습니다.

- Microsoft R Open
- Anaconda Python 배포(v2.7 및 v3.5)(널리 사용되는 데이터 분석 라이브러리 포함)
- Jupyter Notebook(R, Python)
- Azure 저장소 탐색기
- Azure 리소스 관리를 위한 Azure 명령줄
- PostgresSQL 데이터베이스
- 기계 학습 도구
    - [CNTK(Computational Network Toolkit)](https://github.com/Microsoft/CNTK): Microsoft Research의 심화 학습 소프트웨어
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): 온라인, 해시, allreduce, 축소, learning2search, 활성 및 대화형 학습 등의 기술을 지원하는 속성 기계 학습 시스템
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): 빠르고 정확하게 향상된 트리 구현을 제공하는 도구
    - [Rattle](http://rattle.togaware.com/)(R Analytical Tool To Learn Easily): GUI 기반의 데이터 탐색 및 모델링을 자동 R 코드 생성과 함께 사용하여 R에서의 데이터 분석 및 기계 학습을 쉽게 시작할 수 있도록 돕는 도구
- Java, Python, node.js, Ruby, PHP의 Azure SDK
- Azure 기계 학습 및 기타 Azure 서비스에서 사용하기 위한 R 및 Python의 라이브러리
- 개발 도구 및 편집기(Eclipse, Emacs, gedit, vi)

데이터 과학을 수행하려면 일련의 작업에 대해 다음 작업을 반복합니다.

- 데이터 찾기, 로드 및 전처리
- 모델 빌드 및 테스트
- 지능형 응용 프로그램에서 사용하기 위해 모델 배포

이러한 작업을 완료하기 위해 다양한 도구를 사용하는 데이터 과학자에게는 흔한 일입니다. 소프트웨어의 적합한 버전을 찾은 다음 다운로드하여 컴파일하고 설치하는 데 소비되는 시간이 상당히 걸릴 수 있습니다.

Linux 데이터 과학 가상 컴퓨터를 사용하면 이러한 부담을 상당히 줄일 수 있습니다. Linux 데이터 과학 가상 컴퓨터를 사용하여 분석 프로젝트를 시작합니다. R, Python, SQL, Java 및 C++ 등의 다양한 언어로 작업을 수행할 수 있습니다. Eclipse는 사용하기 쉬운 코드를 개발하고 테스트하기 위해 IDE를 제공합니다. VM에 포함된 Azure SDK를 통해 Microsoft의 클라우드 플랫폼을 위한 Linux에서 다양한 서비스를 사용하여 응용 프로그램을 빌드할 수 있습니다. 또한 Ruby, Perl, PHP 및 node.js와 같이 미리 설치된 다른 언어에 액세스해야 합니다.

이 데이터 과학 VM 이미지에 대한 소프트웨어 요금은 부과되지 않습니다. VM 이미지로 프로비전하는 가상 컴퓨터의 크기를 기반으로 평가되는 Azure 하드웨어 사용 요금에 대해서만 지불합니다. 계산 요금에 대한 자세한 내용은 [여기](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linuxdsvm/)에 나와 있습니다.


## 필수 조건

Linux 데이터 과학 가상 컴퓨터를 만들려면 먼저 다음이 있어야 합니다.

- **Azure 구독**: 다운로드하려면 [Azure 무료 평가판 받기](https://azure.microsoft.com/free/)를 참조하세요.
- **Azure 저장소 계정**: 계정을 만들려면 [Azure 저장소 계정 만들기](storage-create-storage-account.md#create-a-storage-account)를 참조하세요. 또는 기존 계정을 사용하지 않을 경우 VM을 만드는 프로세스의 일부로서 저장소 계정을 만들 수 있습니다.


## Linux 데이터 과학 가상 컴퓨터 만들기

Linux 데이터 과학 가상 컴퓨터의 인스턴스를 만드는 단계는 다음과 같습니다.

1.	[Azure 포털](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm)에서 가상 컴퓨터 목록으로 이동합니다.
2.	 마법사로 이동하려면 아래에 있는 **만들기** 단추를 클릭합니다.![configure-data-science-vm](./media/machine-learning-data-science-linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
3.	 다음 섹션에서는 Microsoft 데이터 과학 가상 컴퓨터를 만드는 데 사용되는 마법사의 각 **5단계**(위의 그림 오른쪽에 열거됨)에 **입력**을 제공합니다. 다음은 이러한 각 단계를 구성하는 데 필요한 입력입니다.


  **a. 기본 사항**:

   - **이름**: 만들려는 데이터 과학 서버 이름
   - **사용자 이름**: 첫 번째 계정 로그인 ID
   - **암호**: 첫 번째 계정 암호(암호 대신 SSH 공개 키 사용 가능)
   - **구독**: 둘 이상의 구독을 보유한 경우, 컴퓨터를 만들고 요금을 청구할 구독 하나를 선택합니다. 참고: 이 구독에서 리소스 만들기 권한이 있어야 합니다.
   - **리소스 그룹**: 새 그룹을 만들거나 기존 그룹을 사용할 수 있습니다.
   - **위치**: 가장 적합한 데이터 센터를 선택합니다. 이는 대개 대부분의 데이터가 저장되어 있거나 네트워크에 가장 빠르게 액세스하기 위해 물리적 위치와 가장 가까이 있는 데이터 센터입니다.

  **b. 크기**:

   - 기능 요구 사항과 비용 제약 조건에 부합하는 서버 유형 중 하나를 선택합니다. "모두 보기"를 선택하면 보다 다양한 VM 크기를 선택할 수 있습니다.

  **c. 설정**

   - **디스크 유형**: SSD(반도체 드라이브)를 선호하는 경우 프리미엄을 선택하고 그렇지 않은 경우 "표준"을 선택합니다.
   - **저장소 계정**: 구독에서 새 Azure 저장소 계정을 만들거나 마법사의 기본 단계에서 선택했던 것과 동일한 *위치*에 있는 기존 계정을 사용할 수 있습니다.
   - **기타 매개 변수**: 대부분의 경우, 기본값을 사용합니다. 기본값이 아닌 것을 사용하려는 경우 특정 필드의 정보 링크를 가리키면 도움말을 볼 수 있습니다.

  **d. 요약**:

   - 입력한 모든 정보가 올바른지 확인합니다.

  **e. 구입**:

   - **구입**을 클릭하여 프로비전을 시작합니다. 트랜잭션의 조건에는 링크가 제공됩니다. VM은 **크기** 단계에서 선택한 서버 크기에 대한 계산 이외에 추가 요금이 발생하지 않습니다.


프로비전은 약 10-20분 정도 소요됩니다. 프로비전의 상태는 Azure 포털에 표시됩니다.

## Linux 데이터 과학 가상 컴퓨터에 액세스하는 방법

VM이 만들어지면 텍스트 셸 인터페이스의 3단계 기본 사항 섹션에서 만든 계정 자격 증명으로 SSH를 사용하여 로그인할 수 있습니다. Windows에서는 [Putty](http://www.putty.org)와 같은 SSH 클라이언트 도구를 다운로드할 수 있습니다. 그래픽 데스크톱(X Windows 시스템)을 사용하려는 경우 Putty에서 X11 전달을 사용하거나 X2Go 클라이언트를 설치할 수 있습니다.

>[AZURE.NOTE] 테스트 결과 X2go 클라이언트는 X11 전달보다 훨씬 우수했습니다. 따라서 그래픽 데스크톱 인터페이스에 대해 X2Go 클라이언트를 사용하는 것이 좋습니다.


## X2Go 클라이언트 설치 및 구성

Linux VM은 이미 X2Go 서버에 프로비전되어 있어 클라이언트 연결을 사용할 수 있습니다. Linux VM 그래픽 데스크톱에 연결하려면 클라이언트에서 다음을 수행해야 합니다.

1. [여기](http://wiki.x2go.org/doku.php/doc:installation:x2goclient)에서 클라이언트 플랫폼용 X2Go 클라이언트를 다운로드 및 설치합니다.
2. X2Go 클라이언트를 실행하고 "*새 세션*"을 선택합니다. 여러 탭이 있는 구성 창이 열립니다. 다음 구성 매개 변수를 입력합니다.
    * **세션 탭**:
        - **호스트**: Linux 데이터 과학 VM의 호스트 이름 또는 프로그램 IP 주소입니다.
        - **로그인**: Linux VM의 로그인 사용자 이름입니다.
        - **SSH 포트**: 기본값 22를 그대로 사용합니다.
        - **세션 유형**: 값을 XFCE로 변경합니다. 참고: 현재 Linux VM은 XFCE 데스크톱만 지원합니다.
    * **미디어 탭**: 사운드 지원 및 클라이언트 인쇄를 사용하지 않으려면 해제할 수 있습니다.
    * **공유 폴더**: 클라이언트 컴퓨터의 디렉터리를 Linux VM에 탑재하려면 이 탭에서 VM과 공유하려는 클라이언트 컴퓨터 디렉터리를 추가합니다.

X2Go 클라이언트를 통해 XFCE 그래픽 데스크톱 또는 SSH 클라이언트를 사용하여 VM에 로그인한 경우 VM에 설치 및 구성된 도구를 사용할 수 있습니다. XFCE에서 다양한 도구에 대한 응용 프로그램 메뉴 바로 가기와 바탕 화면 아이콘을 볼 수 있습니다.


## Linux 데이터 과학 가상 컴퓨터에 설치된 도구

### Microsoft R Open 
R은 가장 많이 사용되는 데이터 분석 및 기계 학습 언어 중 하나입니다. 분석에 R을 사용하려는 경우 VM에 MKL(Math Kernel Library)과 함께 MRO(Microsoft R Open)가 설치되어 있어야 합니다. MKL은 분석 알고리즘에 공통되는 수학 연산을 최적화합니다. MRO는 CRAN-R과 100% 호환되며 CRAN에 게시된 모든 R 라이브러리를 MRO에 설치할 수 있습니다. vi, Emacs 또는 gedit와 같은 기본 편집기 중 하나에서 R 프로그램을 편집할 수 있습니다. 또한 [RStudio](http://www.rstudio.com)와 같은 다른 IDE도 다운로드하여 사용할 수 있습니다. 사용자의 편의를 위해 **/dsvm/tools** 디렉터리에 RStudio를 설치하는 간단한 스크립트(installRStudio.sh)가 제공됩니다. Emacs 편집기를 사용 중인 경우 Emacs 편집기에는 R 파일 작업을 간소화하는 Emacs 패키지 ESS(Emacs Speaks Statistics)가 미리 설치되어 있습니다.

R을 시작하려면 셸에서 ***R***을 입력합니다. 이렇게 하면 대화형 환경으로 이동됩니다. R 프로그램을 개발하려면 일반적으로 Emacs, vi 또는 gedit와 같은 편집기를 사용한 다음 R 내에서 스크립트를 실행합니다. RStudio를 설치하면 R 프로그램을 개발하기 위한 전체 그래픽 IDE 환경이 설정됩니다.

또한 원하는 경우 [상위 20 R 패키지](http://www.kdnuggets.com/2015/06/top-20-r-packages.html)를 설치하기 위한 R 스크립트를 사용할 수도 있습니다. 이 스크립트는 R 대화형 인터페이스에서 셸에 R을 입력하여(언급한 대로) 실행할 수 있습니다.

### Python
Python을 사용하여 개발하는 경우를 위해, Anaconda Python 배포 2.7 및 3.5가 설치되었습니다. 이 배포 버전에는 약 300개의 가장 인기 있는 수학, 엔지니어링 및 데이터 분석 패키지와 함께 기본 Python이 포함되어 있습니다. 기본 텍스트 편집기를 사용할 수 있습니다. 또한 Anaconda Python 배포에 번들로 포함된 Python IDE인 Spyder를 사용할 수 있습니다. Spyder를 사용하려면 그래픽 데스크톱 또는 X11 전달이 필요합니다. 그래픽 데스크톱에 Spyder에 대한 바로 가기가 제공됩니다.

Python은 2.7 및 3.5 버전이 있으므로 현재 세션에서 작업하려는 Python 버전을 활성화해야 합니다. 활성화 프로세스는 PATH 변수를 원하는 Python 버전으로 설정합니다.

Python 2.7을 활성화하려면 셸에서 다음을 실행합니다.

	source /anaconda/bin/activate root

Python 2.7은 */anaconda/bin*에 설치됩니다.

Python 3.5를 활성화하려면 셸에서 다음을 실행합니다.

	source /anaconda/bin/activate py35


Python 3.5는 */anaconda/envs/py35/bin*에 설치됩니다.

python 대화형 세션을 호출하려면 셸에 ***python***을 입력합니다. 그래픽 인터페이스를 사용 중이거나 X11 전달이 설정된 경우 ***spyder*** 명령을 입력하여 Python IDE를 시작할 수 있습니다.

### Jupyter 노트북 

Anaconda 배포는 코드 및 분석을 공유하는 환경인 Jupyter Notebook도 제공됩니다. Jupyter Notebook은 JupyterHub을 통해 액세스합니다. 로컬 Linux 사용자 이름 및 암호를 사용하여 로그인합니다.

Jupyter Notebook 서버는 Python 2, Python 3 및 R 커널로 미리 구성되었습니다. Notebook 서버에 액세스하려면 브라우저를 시작하는 "Jupyter Notebook이라는 바탕 화면 아이콘이 있습니다. SSH 또는 X2go 클라이언트를 통해 VM을 사용하는 경우 [https://localhost:8000/](https://localhost:8000/)를 방문하여 Jupyter Notebook 서버에 액세스할 수도 있습니다.

>[AZURE.NOTE] 인증서 경고가 나타나는 경우 계속 진행하세요.

모든 호스트에서 Jupyter Notebook 서버에 액세스할 수 있습니다. "https://<VM DNS 이름 또는 IP 주소>:8000/"만 입력하면 됩니다.

>[AZURE.NOTE] VM이 프로비전될 때 포트 8000이 방화벽에 기본적으로 열립니다.

몇 가지 샘플 Notebook을 Python 및 R에 각각 패키징했습니다. 로컬 Linux 사용자 이름 및 암호를 사용하여 Jupyter Notebook에 인증한 후에 Notebook 홈페이지에서 샘플에 대한 링크를 볼 수 있습니다. "새로 만들기"를 선택하고 언어 커널을 선택하여 새 Notebook을 만들 수 있습니다. "새로 만들기" 단추가 보이지 않으면 왼쪽 위에서 Jupyter 아이콘을 클릭하여 Notebook 서버의 홈페이지로 이동합니다.


### IDE 및 편집기 

여러 코드 편집기 중에서 선택할 수 있습니다. 편집기 종류로는 Vi/VIM, Emacs, gEdit 및 Eclipse가 있습니다. gEdit 및 Eclipse는 그래픽 편집기이며 그래픽 데스크톱에 로그인해야 사용할 수 있습니다. 이러한 편집기는 데스크톱 및 응용 프로그램 바로 가기 메뉴를 사용하여 시작할 수 있습니다.

**VIM** 및 **Emacs**는 텍스트 기반 편집기입니다. Emacs의 경우 Emacs 편집기 내에서 R 작업을 쉽게 수행할 수 있도록 해주는 ESS(Emacs Speaks Statistics)라는 추가 기능 패키지가 설치되어 있습니다. 자세한 내용은 [ESS](http://ess.r-project.org/)를 참조하세요.

**Eclipse**는 다국어를 지원하는 확장형 오픈 소스 IDE입니다. Java 개발자 버전은 VM에 설치된 인스턴스입니다. 주요 언어에 사용할 수 있는 몇 가지 플러그 인을 설치하여 Eclipse 환경을 확장할 수 있습니다. 또한 Eclipse에는 Java와 같은 언어를 지원하는 Eclipse 개발 환경을 사용하여 Azure 응용 프로그램을 쉽게 작성, 개발, 테스트 및 배포할 수 있는 **Azure Toolkit for Eclipse** 플러그 인이 설치되어 있습니다. 또한 Java 환경 내에서 다른 Azure 서비스에 액세스할 수 있는 **Java용 Azure SDK**도 있습니다. Eclipse용 Azure 도구 키트에 대한 자세한 내용은 [Eclipse용 Azure 도구 키트](../azure-toolkit-for-eclipse.md)를 참조하세요.

**LaTex**는 texlive 패키지를 통해 Emacs 추가 기능 [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) 패키지와 함께 설치되며 Emacs 내에서의 LaTex 문서 작성을 단순화합니다.

### 데이터베이스

#### Postgres
오픈 소스 데이터베이스 **Postgres**는 실행 중인 서비스 및 이미 완료된 initdb와 함께 VM에서 사용할 수 있습니다. 데이터베이스 및 사용자를 만들어야 합니다. Postgres 설명서를 참조하세요.

####  그래픽 SQL 클라이언트
서로 다른 데이터베이스(Microsoft SQL Server, Postgres, MySQL 등)에 연결하고 SQL 쿼리를 실행할 수 있도록 그래픽 SQL 클라이언트인 **SQuirrel SQL**이 제공됩니다. SQuirrel SQL은 그래픽 데스크톱 세션(예: X2Go 클라이언트 사용)에서 실행할 수 있습니다. SQuirrel SQL을 호출하려면 바탕 화면에서 아이콘을 클릭하거나 셸에서 다음 명령을 실행합니다.

	/usr/local/squirrel-sql-3.7/squirrel-sql.sh 

처음 사용하기 전에 드라이버 및 데이터베이스 별칭을 설정해야 합니다. JDBC 드라이버는 다음 위치에 있습니다.

*/usr/share/java/jdbcdrivers*

SQuirrel SQL에 대한 자세한 내용은 [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots) 웹 사이트를 참조하세요.

#### Microsoft SQL Server에 액세스하기 위한 명령줄 도구

Microsoft SQL Server용 ODBC 드라이버 패키지는 두 가지 명령줄 도구와 함께 제공됩니다.

**bcp** - bcp 유틸리티는 Microsoft SQL Server 인스턴스와 사용자 지정 형식의 데이터 파일 간에 데이터를 대량 복사합니다. bcp 유틸리티를 사용하여 SQL Server 테이블에 많은 수의 새 행을 가져오거나 테이블에서 데이터 파일로 데이터를 내보낼 수 있습니다. 데이터를 테이블로 가져오려면 해당 테이블용으로 만들어진 형식 파일을 사용하거나 해당 열에 유효한 데이터 형식 및 테이블 구조를 이해해야 합니다.

자세한 내용은 [bcp를 사용하여 연결](https://msdn.microsoft.com/library/hh568446.aspx)을 참조하세요.

**sqlcmd** - sqlcmd 유틸리티를 사용하면 명령 프롬프트에 Transact-SQL 문, 시스템 프로시저 및 스크립트 파일을 입력할 수 있습니다. 이 유틸리티는 ODBC를 사용하여 TRANSACT-SQL 일괄 처리를 실행합니다.

자세한 내용은 [sqlcmd을 사용하여 연결](https://msdn.microsoft.com/library/hh568447.aspx)을 참조하세요.

>[AZURE.NOTE] 이 유틸리티는 Linux 및 Windows 플랫폼 간에 일부 차이점이 있습니다. 자세한 내용은 위의 설명서 페이지를 참조하세요.


#### 데이터베이스 액세스 라이브러리

Python 및 R에는 데이터베이스에 액세스하는 데 사용할 수 있는 라이브러리가 있습니다.

- R에서 **RODBC** 패키지 또는 **dplyr** 패키지를 사용하면 데이터베이스 서버에서 SQL 문을 쿼리 또는 실행할 수 있습니다.
- Python에서 **pyodbc** 라이브러리는 ODBC를 사용한 데이터베이스 액세스를 기본 계층으로 제공합니다.

**Postgres**에 액세스하려면

- Python에서: **psycopg2** 라이브러리를 사용합니다.
- R에서: **RPostgreSQL** 패키지를 사용합니다.


### Azure 도구 
다음 Azure 도구가 VM에 설치됩니다.

- **Azure 명령줄 인터페이스**: Azure CLI(명령줄 인터페이스)를 사용하여 셸 명령을 통해 Azure 리소스를 만들고 관리할 수 있습니다. Azure 도구를 호출하려면 ***azure help***를 입력합니다. 자세한 내용은 [Azure CLI 설명서 페이지](../virtual-machines-command-line-tools.md)를 참조하세요.
- **Microsoft Azure Storage 탐색기**: Microsoft Azure 저장소 탐색기는 Azure 저장소 계정에 저장된 개체를 통해 이동하고 Azure Blob에서 데이터를 업로드/다운로드하는 데 사용되는 그래픽 도구입니다. 바탕 화면 바로 가기 아이콘을 사용하여 저장소 탐색기에 액세스할 수 있습니다. ***StorageExplorer***를 입력하여 셸 프롬프트에서 저장소 탐색기를 호출합니다. X2go 클라이언트에서 로그인하거나 X11 전달을 설정해야 합니다.
- **Azure 라이브러리**: 설치되어 사용할 수 있는 일부 라이브러리는 다음과 같습니다.
- **Python**: Python에 설치된 Azure 관련 라이브러리는 ***azure***, ***azureml***, ***pydocumentdb***, ***pyodbc***입니다. 처음 세 개의 라이브러리를 사용하면 Azure 저장소 서비스, Azure 기계 학습 및 Azure DocumentDB(Azure의 NoSQL 데이터베이스)에 액세스할 수 있습니다. 네 번째 라이브러리인 pyodbc를 Microsoft ODBC Driver for SQL Server와 함께 사용하면 ODBC 인터페이스를 사용하여 Python에서 Microsoft SQL Server, Azure SQL 데이터베이스 및 Azure SQL 데이터 웨어하우스에 액세스할 수 있습니다. 나열된 라이브러리를 모두 보려면 ***pip list***를 입력하세요. Python 2.7 및 3.5 환경 모두에서 이 명령을 실행해야 합니다.
- **R**: 설치된 R에서 Azure 관련 라이브러리는 ***AzureML*** 및 ***RODBC***입니다.
- **Java**: VM의 ***/dsvm/sdk/AzureSDKJava*** 디렉터리에서 Azure Java 라이브러리 목록을 찾을 수 있습니다. 핵심 라이브러리는 Azure 저장소 및 관리 API, DocumentDB 및 SQL Server용 JDBC 드라이버입니다.

미리 설치된 Firefox 브라우저에서 [Azure 포털](https://portal.azure.com)에 액세스할 수 있습니다. Azure 포털에서 Azure 리소스를 작성, 관리 및 모니터링할 수 있습니다.

### Azure 기계 학습

Azure ML(Azure 기계 학습)은 예측 분석 솔루션을 빌드, 배포 및 공유할 수 있는 완전 관리형 클라우드 서비스입니다. Azure 기계 학습 스튜디오에서 실험 및 모델을 빌드합니다. 데이터 과학 가상 컴퓨터의 웹 브라우저에서 [Microsoft Azure 기계 학습](https://studio.azureml.net)을 방문하여 액세스할 수도 있습니다.

Azure 기계 학습 스튜디오에 로그인하면 기계 학습 알고리즘을 위한 논리적 흐름을 빌드할 수 있는 실험 캔버스에 액세스하게 됩니다. 또한 Azure ML에 호스트된 Jupyter Notebook에 액세스하고 스튜디오에서 원활하게 실험할 수 있습니다. Azure ML을 사용하면 빌드한 ML 모델을 웹 서비스 인터페이스에서 래핑하여 조작할 수 있습니다. 이렇게 하면 모든 언어로 작성된 클라이언트를 사용하여 ML 모델에서 예측을 호출할 수 있습니다. Azure ML에 대한 자세한 내용은 [기계 학습 설명서](https://azure.microsoft.com/documentation/services/machine-learning/)를 참조하세요.

VM의 R 또는 Python에서 모델을 빌드한 다음 프로덕션 환경에서 Azure ML에 배포할 수 있습니다. R 및 Python에는 이 기능을 위한 라이브러리가 설치되어 있습니다.

- R의 라이브러리를 ***AzureML***이라고 합니다.
- Python에서는 ***azureml***이라고 합니다.

R 및 Python의 모델을 Azure ML에 배포하는 방법에 대한 자세한 내용은 [데이터 과학 가상 컴퓨터로 할 수 있는 10가지 일](machine-learning-data-science-vm-do-ten-things.md)의 *R 또는 Python을 사용하여 모델을 구축하고 Azure 기계 학습을 사용하여 운영* 섹션을 참조하세요.
 
>[AZURE.NOTE] 이러한 지침은 Windows 버전의 데이터 과학 VM용으로 작성되었지만 Azure ML로의 모델 배포에 대한 내용은 Linux VM에도 적용할 수 있습니다.

### 기계 학습 도구

VM에는 로컬에서 미리 컴파일되고 미리 설치된 몇 가지 ML 도구/알고리즘이 제공됩니다. 내용은 다음과 같습니다.

* **CNTK**(Microsoft Research의 Computational Network Toolkit) - 심화 학습 도구 키트입니다.
* **Vowpal Wabbit** - 속성 온라인 학습 알고리즘입니다.
* **xgboost** - 최적화되고 향상된 트리 알고리즘을 제공하는 도구입니다.
* **Python** - Anaconda Python에는 Scikit-learn 등의 라이브러리가 있는 ML 알고리즘이 번들로 제공됩니다. pip 설치를 실행하여 다른 라이브러리를 설치할 수 있습니다.
* **R** - R에 대해 ML 기능의 풍부한 라이브러리를 사용할 수 있습니다. 미리 설치된 일부 라이브러리는 lm, glm, randomForest, rpart입니다. 다른 라이브러리를 설치하려면 다음을 실행합니다.

		install.packages(<lib name>)

목록에는 처음 세 개의 ML 도구에 대한 추가 정보가 있습니다.

#### CNTK
오픈 소스 심화 학습 도구 키트입니다. CNTK(명령줄 도구)이며 이미 PATH에 있습니다.

기본 샘플을 실행하려면 셸에서 다음을 수행하세요.

	# Copy samples to your home directory and execute cntk
	cp -r /dsvm/tools/CNTK-2016-02-08-Linux-64bit-CPU-Only/Examples/Other/Simple2d cntkdemo 
	cd cntkdemo/Data
	cntk configFile=../Config/Simple.cntk

*~/cntkdemo/Output/Models*에서 모델 출력을 찾습니다.

CNTK에 대한 자세한 내용은 [github.com/Microsoft/CNTK](https://github.com/Microsoft/CNTK) 및 [CNTK wiki](https://github.com/Microsoft/CNTK/wiki)에서 찾을 수 있습니다.


#### VW(Vowpal Wabbit):

Vowpal Wabbit은 온라인, 해시, allreduce, 단축, learning2search, 활성 및 대화형 학습 등의 기술을 사용하여 기계 학습 프런티어를 푸시하는 기계 학습 시스템입니다.

매우 기본적인 예제에서 도구를 실행하려면 다음을 수행하세요.

	cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
	cd vwdemo
	vw house_dataset

해당 디렉터리에는 더 큰 다른 데모도 있습니다. VW에 대한 자세한 내용은 [github.com/JohnLangford/vowpal\_wabbit](https://github.com/JohnLangford/vowpal_wabbit) 및 [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki)에서 찾을 수 있습니다.

#### xgboost
향상된 (트리) 알고리즘을 위해 디자인되고 최적화된 라이브러리입니다. 이 라이브러리의 목적은 확장 및 이동이 가능하고 정확한 대용량 트리 향상 기능을 제공하는 데 필요한 최대 한계까지 기계의 계산 제한을 푸시하는 것입니다.

R 라이브러리뿐만 아니라 명령줄로도 제공됩니다.

R에서 이 라이브러리를 사용하려면 셸에서 *R*을 입력하여 대화형 R 세션을 시작하고 라이브러리를 로드합니다.

R 프롬프트에서 실행할 수 있는 간단한 예제는 다음과 같습니다.

	library(xgboost)

	data(agaricus.train, package='xgboost')
	data(agaricus.test, package='xgboost')
	train <- agaricus.train
	test <- agaricus.test
	bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
	pred <- predict(bst, test$data)

xgboost 명령줄을 실행하려면 셸에서 다음 명령을 실행합니다.

	cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
	cd xgboostdemo
	xgboost mushroom.conf


.model 파일이 지정된 디렉터리에 기록됩니다. 이 데모 예제에 대한 정보는 [여기](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification)에서 찾을 수 있습니다.

xgboost에 대한 자세한 내용은 [xgboost 설명서 페이지](https://xgboost.readthedocs.org/en/latest/) 및 해당 [Github 리포지토리](https://github.com/dmlc/xgboost)를 참조하세요.

#### Rattle
Rattle(R Analytical Tool To Learn Easily)을 사용하면 GUI 기반의 데이터 탐색 및 모델링을 사용하여 R에서 매우 쉽게 데이터 마이닝을 시작할 수 있습니다. Rattle은 통계 및 시각적 데이터 요약을 표시하고, 쉽게 모델링할 수 있는 데이터를 변환하고, 데이터를 사용하여 감독되거나 감독되지 않는 모델을 빌드하고, 모델의 성능을 그래픽으로 표시하고, 새 데이터 집합의 점수를 매깁니다. 또한 UI에서 작업을 복제하여 R에서 직접 실행하거나 추가 분석을 위한 시작점으로 사용할 수 있는 R 코드를 생성할 수도 있습니다.

Rattle을 실행하려면 그래픽 데스크톱 로그인 세션에 있어야 합니다. 터미널에서 ```R```을 입력하여 R 환경을 설정합니다. R 프롬프트에서 다음 명령을 입력합니다.

	library(rattle)
	rattle()
	
이제 그래픽 인터페이스가 열리고 일련의 탭이 표시됩니다. 다음은 Rattle의 샘플 날씨 데이터 집합을 사용하여 모델을 빌드하는 빠른 시작 단계입니다. 아래 일부 단계에서 아직 시스템에 없는 필수 R 패키지를 자동으로 설치 및 로드할 것인지 묻는 메시지가 나타납니다. **참고**: 시스템 디렉터리(기본값)에서 패키지를 설치할 액세스 권한이 없는 경우 R 콘솔 창에 패키지를 개인 라이브러리에 설치할지 여부를 묻는 메시지가 나타납니다. 이러한 메시지가 표시되면 "y"로 응답합니다.

1. 실행을 클릭합니다.
2. 예제 날씨 데이터 집합을 사용할지 묻는 팝업 대화 상자가 표시됩니다. 예를 클릭하여 예제를 로드합니다.
3. 모델 탭을 클릭합니다.
4. 실행을 클릭하여 의사 결정 트리를 빌드합니다.
5. 그리기를 클릭하여 의사 결정 트리를 표시합니다.
6. 포리스트 라디오 단추를 클릭하고 실행을 클릭하여 임의 포리스트를 빌드합니다.
7. 평가 탭을 클릭합니다.
8. 위험 라디오 단추를 클릭하고 실행을 클릭하여 두 개의 위험(누적) 성능 그림을 표시합니다.
9. 로그 탭을 클릭하여 위의 작업을 위한 R 코드 생성을 표시합니다(참고: 현재 Rattle 릴리스에 버그가 있습니다. 로그 텍스트의 '이 로그 내보내기...' 앞에 '#'을 삽입하세요.).
10. 내보내기 단추를 클릭하여 weather\_script.R 파일에 대한 R 스크립트를 홈 폴더에 저장합니다.

Rattle 및 R을 종료합니다. 이제 생성된 R 스크립트를 수정하거나 언제든지 그대로 사용하여 Rattle UI 내에서 수행된 모든 작업을 반복할 수 있습니다. 이 방법을 사용하면 특히 R 초보자가 간단한 그래픽 인터페이스에서 분석 및 기계 학습을 신속하게 수행하고 R에서 코드를 자동으로 생성하여 수정 및/또는 학습할 수 있습니다.


## 다음 단계
학습 및 탐색을 계속하려면 다음 단계는 다음과 같습니다.

* 이 문서에 설명된 도구를 사용하여 데이터 과학 VM에서 다양한 데이터 과학 도구를 살펴봅니다. 가상 컴퓨터의 셸에서 *dsvm-more-info*를 실행하여 VM에 설치된 도구에 대한 기본 소개 및 자세한 내용을 참조할 수도 있습니다.
* 체계적으로 [팀 데이터 과학 프로세스](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)를 사용하여 종단 간 분석 솔루션을 구축하는 방법을 알아봅니다.
* Cortana 분석 모음을 사용하는 기계 학습 및 데이터 분석 샘플은 [Cortana 분석 갤러리](http://gallery.cortanaanalytics.com)를 방문하세요.

<!---HONumber=AcomDC_0810_2016-->