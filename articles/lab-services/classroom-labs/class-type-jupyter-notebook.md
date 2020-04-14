---
title: 파이썬과 주피터 노트북으로 데이터 과학을 가르치는 실험실 설치 | 마이크로 소프트 문서
description: 파이썬 및 Jupyter 노트북을 사용하여 데이터 과학을 가르치기 위해 랩을 설정하는 방법에 대해 알아봅니다.
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: 222f5647248d27c3bdfabd0feaeb66dd9f543652
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257728"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>파이썬 및 주피터 노트북으로 데이터 과학을 가르치는 실험실 설정

이 문서에서는 학생들에게 [Jupyter 노트북을](http://jupyter-notebook.readthedocs.io)사용하는 방법을 가르치는 데 필요한 도구를 사용하여 랩 서비스에서 템플릿 컴퓨터를 설정하는 방법을 설명합니다.  Jupyter Notebook은 서식 있는 텍스트와 실행 가능한 [Python](https://www.python.org/) 원본 코드를 노트북이라는 단일 캔버스에 쉽게 결합할 수 있는 오픈 소스 프로젝트입니다.  노트북을 실행하면 입력 및 출력의 선형 레코드가 생성됩니다.  이러한 출력에는 텍스트, 정보 테이블, 분산형 플롯 등이 포함될 수 있습니다.

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정하려면 Azure 구독 및 랩 계정을 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독을 받으면 Azure Lab 서비스에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정을 만드는 자세한 내용은 [랩 계정을 설정하는 자습서를](tutorial-setup-lab-account.md)참조하십시오.  기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정

랩 계정에 대해 아래 표에 설명된 설정을 사용하도록 설정합니다. 마켓플레이스 이미지를 사용하도록 설정하는 방법에 대한 자세한 내용은 [랩 작성자가 사용할 수 있는 마켓플레이스 이미지 지정을](specify-marketplace-images.md)참조하십시오.

| 랩 계정 설정 | Instructions |
| ------------------- | ------------ |
| 마켓플레이스 이미지 | 랩 계정 내에서 사용할 수 있도록 [데이터 과학 가상 컴퓨터 - Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) 이미지를 사용하도록 설정합니다. |

>[!TIP]
>이 문서에서는 Windows Server 운영 체제를 사용하는 템플릿 컴퓨터를 구성하는 데 중점을 둡니다.  또한 Azure 마켓플레이스의 [데이터 과학 가상 머신(우분투)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) 이미지를 사용하여 파이썬 및 Jupyter 노트북을 사용하여 데이터 과학 클래스를 설정할 수도 있습니다.

### <a name="lab-settings"></a>랩 설정

교실 실습을 설정할 때 아래 표의 설정을 사용합니다.  강의실 랩을 만드는 방법에 대한 자세한 내용은 [강의실 랩 자습서 설정을](tutorial-setup-classroom-lab.md)참조하십시오.

| 랩 설정 | 값/지침 |
| ------------ | ------------------ |
|Virtual Machine 크기| 소형 GPU(컴퓨팅). 이 크기는 인공 지능 및 딥 러닝과 같은 컴퓨팅 집약적이고 네트워크 집약적인 응용 프로그램에 가장 적합합니다. |
|가상 머신 이미지| 데이터 과학 가상 머신 - 윈도우 2016|

## <a name="template-machine"></a>템플릿 기계

[데이터 과학 가상 머신 - Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) 이미지는 이러한 유형의 클래스에 필요한 딥 러닝 프레임워크와 도구를 제공합니다.  이미지에는 Jupyter 노트북과 비주얼 스튜디오 코드가 포함되어 있습니다.  [Jupyter Notebooks는](http://jupyter-notebook.readthedocs.io) 데이터 과학자가 원시 데이터를 취하고 계산을 실행하고 동일한 환경에서 결과를 볼 수 있게 해주는 웹 응용 프로그램입니다.  템플릿 컴퓨터의 경우 웹 응용 프로그램이 로컬로 실행됩니다.  [Visual Studio 코드는](https://code.visualstudio.com/) 노트북을 작성하고 테스트할 때 풍부한 대화형 환경을 제공하는 IDE입니다.  자세한 내용은 [Visual Studio 코드의 Jupyter 노트북 작업을](https://code.visualstudio.com/docs/python/jupyter-support)참조하십시오.

클래스를 설정하는 나머지 작업은 로컬 전자 필기장을 제공하는 것입니다.  Azure 기계 학습 샘플을 사용하는 방법에 대한 지침은 [Jupyter 노트북으로 환경을 구성하는 방법을 참조하세요.](../../machine-learning/how-to-configure-environment.md#jupyter)  템플릿 컴퓨터에서 사용자 고유의 전자 필기장을 제공할 수도 있습니다.  템플릿이 게시되면 전자 필기장이 모든 학생 컴퓨터에 복사됩니다.

## <a name="cost-estimate"></a>비용 추정

이 클래스에 대한 가능한 비용 견적을 살펴보겠습니다.  25명의 학생으로 구성된 수업을 사용합니다.  20시간의 수업 시간이 예정되어 있습니다.  또한 각 학생은 예정된 수업 시간 이외에 숙제 또는 과제에 대해 10시간 할당량을 받습니다.  우리가 선택한 가상 머신 크기는 139 개의 실험실 단위인 소형 GPU (계산)였습니다.

다음은 이 클래스에 대한 가능한 비용 추정의 예입니다.

25명의 \* 학생(20시간 + 10시간) \* 139개의 실험실 단위 \* 시간당 0.01 USD = 1042.5 USD

가격에 대한 자세한 내용은 [Azure 랩 서비스 가격 을](https://azure.microsoft.com/pricing/details/lab-services/)참조하십시오.

## <a name="conclusion"></a>결론

이 문서에서는 Jupyter 노트북 클래스에 대 한 랩을 만드는 단계를 통해 이동 했습니다. 다른 기계 학습 클래스에도 유사한 설정을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 단계는 랩을 설정하는 데 일반적입니다.

- [템플릿 만들기 및 관리](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [학생에게 이메일 등록 링크](how-to-configure-student-usage.md#send-invitations-to-users)
