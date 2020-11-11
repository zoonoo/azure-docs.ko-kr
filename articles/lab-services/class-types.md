---
title: Azure Lab Services의 클래스 유형 예제 | Microsoft Docs
description: Azure Lab Services를 사용하여 랩을 설정할 수 있는 몇 가지 유형의 클래스를 제공합니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1c2caac35125c3f69c2257750bac417971af5dbd
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94490987"
---
# <a name="class-types-overview---azure-lab-services"></a>클래스 유형 개요 - Azure Lab Services

Azure Lab Services를 사용하면 클라우드에서 클래스룸 랩 환경을 빠르게 설정할 수 있습니다. 이 섹션의 문서에서는 Azure Lab Services를 사용하여 여러 유형의 클래스룸 랩을 설정하는 방법에 대한 지침을 제공합니다.

## <a name="big-data-analytics"></a>빅 데이터 분석
빅 데이터 분석 클래스를 학습 하도록 GPU 랩을 설정할 수 있습니다. 이러한 종류의 클래스를 사용 하 여 많은 양의 데이터를 처리 하는 방법을 알아보고 기계 및 통계적 학습 알고리즘을 적용 하 여 데이터 정보를 도출 합니다. 학생의 핵심 목표는 빅 데이터를 저장, 관리 및 처리 하기 위한 도구를 제공 하는 Apache Hadoop의 오픈 소스 소프트웨어 패키지와 같은 데이터 분석 도구를 사용 하는 방법을 배우는 것입니다. 

이러한 유형의 랩을 설정 하는 방법에 대 한 자세한 내용은 [HortonWorks Data Platform의 Docker 배포를 사용 하 여 빅 데이터 분석에 대 한 랩 설정](class-type-big-data-analytics.md)을 참조 하세요.

## <a name="database-management"></a>데이터베이스 관리
데이터베이스 개념은 대학의 대부분의 컴퓨터 과학 부서에서 학습하는 소개 과정 중 하나입니다. Azure Lab Services에서 기본 데이터베이스 관리 클래스에 대한 랩을 설정할 수 있습니다. 예를 들어 [MySQL](https://www.mysql.com/) Database Server 또는 [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) 서버를 사용하여 랩에서 가상 머신 템플릿을 설정할 수 있습니다.

이 유형의 랩을 설정하는 방법에 대한 자세한 내용은 [관계형 데이터베이스에 대한 데이터베이스 관리를 가르치기 위한 랩 설정](class-type-database-management.md)을 참조하세요.

## <a name="deep-learning-in-natural-language-processing"></a>자연어 처리의 딥 러닝
Azure Lab Services를 사용하여 NLP(자연어 처리)의 딥 러닝에 초점을 맞춘 랩을 설정할 수 있습니다. NLP(자연어 처리)는 번역, 음성 인식 및 기타 언어 이해 기능을 갖춘 컴퓨터를 가능케하는 AI(인공 지능) 형태입니다. NLP 클래스를 수강하는 학생은 Linux VM(가상 머신)을 통해 작성된 인간 언어 분석에 사용되는 딥 러닝 모델을 개발하는 신경망 알고리즘을 적용하는 방법을 알아봅니다.

이러한 유형의 랩을 설정하는 방법에 대한 자세한 내용은 [Azure Lab Services를 사용한 자연어 처리의 딥 러닝에 초점을 맞춘 랩 설정](class-type-deep-learning-natural-language-processing.md)을 참조하세요.

## <a name="ethical-hacking"></a>윤리적 해킹
윤리적 해킹의 법적 고지에 초점을 맞춘 클래스에 대해 랩을 설정할 수 있습니다. 윤리적 해킹 커뮤니티에서 사용하는 방법인 침투 테스트는 누군가가 악의적인 공격자가 악용할 수 있는 취약성을 입증하기 위해 시스템 또는 네트워크에 대한 액세스 권한을 얻으려고 할 때 발생합니다.

윤리적 해킹 클래스에서 학생은 취약점을 방어하기 위한 최신 기술을 배울 수 있습니다. 각 학생에게는 두 개의 중첩된 가상 머신이 있는 Windows Server 호스트 가상 머신이 있습니다. 즉 [Metasploitable3](https://github.com/rapid7/metasploitable3) 이미지가 있는 가상 머신과 [Kali Linux](https://www.kali.org/) 이미지가 있는 가상 머신이 있습니다. Metasploitable 가상 머신은 악용 목적으로 사용됩니다.  Kali Linux 가상 머신은 법정 작업을 실행하는 데 필요한 도구에 대한 액세스를 제공합니다.

이 유형의 랩을 설정하는 방법에 대한 자세한 내용은 [윤리적 해킹 클래스를 가르치기 위한 랩 설정](class-type-ethical-hacking.md)을 참조하세요.

## <a name="matlab"></a>MATLAB
행렬 실험을 의미 하는 [MATLAB](https://www.mathworks.com/products/matlab.html)는 [MathWorks](https://www.mathworks.com/)의 프로그래밍 플랫폼입니다.  계산 기능과 시각화를 결합 하 여 수학, 엔지니어링, 물리학 및 화학 분야에서 널리 사용 되는 도구를 만듭니다.

이러한 유형의 랩을 설정 하는 방법에 대 한 자세한 내용은 [MATLAB를 학습 하기 위한 랩 설정](class-type-matlab.md)을 참조 하세요.

## <a name="project-lead-the-way-pltw"></a>프로젝트 리드 (PLTW)
[프로젝트 리드 (PLTW)](https://www.pltw.org/) 는 컴퓨터 과학, 엔지니어링 및 생명 공학 과학의 미국에서 사전 k-12 교육 과정을 제공 하는 비영리 조직입니다.  각 PLTW 클래스에서 학생 들은 실습 교육 환경의 일부로 다양 한 소프트웨어 응용 프로그램을 사용 합니다.

이러한 유형의 랩을 설정 하는 방법에 대 한 자세한 내용은 [프로젝트 리드에 대 한 랩 설정 클래스](class-type-pltw.md)를 참조 하세요.

## <a name="python-and-jupyter-notebooks"></a>Python 및 Jupyter Notebook
학생들에게 [Jupyter Notebook](http://jupyter-notebook.readthedocs.io)을 사용하는 방법을 가르치는 데 필요한 도구를 사용하여 Azure Lab Services에서 템플릿 머신을 설정할 수 있습니다. Jupyter Notebook은 서식 있는 텍스트와 실행 가능한 [Python](https://www.python.org/) 원본 코드를 노트북이라는 단일 캔버스에 쉽게 결합할 수 있는 오픈 소스 프로젝트입니다. 노트북을 실행하면 입력 및 출력의 선형 레코드가 생성됩니다.  이러한 출력에는 텍스트, 정보 테이블, 분산형 플롯 등이 포함될 수 있습니다.

이 유형의 랩을 설정하는 방법에 대한 자세한 내용은 [Python 및 Jupyter Notebook을 사용하여 데이터 과학을 가르치기 위한 랩 설정](class-type-jupyter-notebook.md)을 참조하세요.

## <a name="shell-scripting-on-linux"></a>Linux에서 셸 스크립팅
Linux에서 셸 스크립팅을 학습하도록 랩을 설정할 수 있습니다. 스크립팅은 관리자가 반복적인 작업을 방지할 수 있도록 하는 시스템 관리의 유용한 부분입니다. 이 샘플 시나리오에서 클래스는 기존 bash 스크립트와 향상된 스크립트를 포함합니다. 향상된 스크립트는 bash 명령과 Ruby를 결합하는 스크립트입니다. 이 접근 방식을 사용하면 Ruby에서 데이터를 전달하고, bash 명령이 셸과 상호 작용할 수 있습니다.

이러한 스크립팅 클래스를 수강하는 학생은 Linux 가상 머신을 통해 Linux의 기본 사항을 알아보고 bash 셸 스크립팅에 익숙해질 수 있습니다. Linux 가상 머신은 원격 데스크톱 액세스를 사용하도록 설정되어 있고 [gedit](https://help.gnome.org/users/gedit/stable/) 및 [Visual Studio Code](https://code.visualstudio.com/) 텍스트 편집기가 설치 되어 있습니다.

이 유형의 랩을 설정하는 방법에 대한 자세한 내용은 [Linux에서 셸 스크립팅](class-type-shell-scripting-linux.md)을 참조하세요.

## <a name="solidworks-computer-aided-design-cad"></a>SolidWorks CAD(computer-aided design)
엔지니어링 학생 들에 게 [SolidWorks](https://www.solidworks.com/)에 대 한 액세스를 제공 하는 GPU 랩을 설정할 수 있습니다.  SolidWorks는 솔리드 개체 모델링을 위한 3D CAD 환경을 제공 합니다.  SolidWorks를 사용 하면 엔지니어가 디자인을 쉽게 만들고, 시각화 하 고, 시뮬레이션 하 고, 문서화할 수 있습니다.

이러한 유형의 랩을 설정 하는 방법에 대 한 자세한 내용은 SolidWorks를 [사용 하 여 엔지니어링 클래스에 대 한 랩 설정](class-type-solidworks.md) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [Azure Lab Services를 사용하여 자연어 처리의 딥 러닝에 초점을 맞춘 랩을 설정](class-type-deep-learning-natural-language-processing.md)
- [Linux에서 셸 스크립팅](class-type-shell-scripting-linux.md)
- [윤리적 해킹](class-type-ethical-hacking.md)