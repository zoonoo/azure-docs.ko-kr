---
title: Azure Lab 서비스를 사용하여 딥 러닝에 중점을 둔 랩 설정 | 마이크로 소프트 문서
description: Linux에서 셸 스크립팅을 가르치는 랩을 설정하는 방법에 대해 알아봅니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 889d0d1e98f5c9947588011774d02e54f05edca1
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257762"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Azure Lab Services를 사용하여 자연어 처리의 딥 러닝에 초점을 맞춘 랩을 설정
이 문서에서는 Azure Lab 서비스를 사용하여 NLP(자연어 처리)에서 딥 러닝에 중점을 둔 랩을 설정하는 방법을 보여 주며 있습니다. NLP(자연어 처리)는 번역, 음성 인식 및 기타 언어 이해 기능을 갖춘 컴퓨터를 가능케하는 AI(인공 지능) 형태입니다.  

NLP 클래스를 수강하는 학생은 Linux VM(가상 머신)을 통해 작성된 인간 언어 분석에 사용되는 딥 러닝 모델을 개발하는 신경망 알고리즘을 적용하는 방법을 알아봅니다. 

## <a name="lab-configuration"></a>랩 구성
이 랩을 설정하려면 Azure 구독을 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들거나 기존 랩 계정을 사용할 수 있습니다. 새 랩 계정을 만드는 다음 자습서를 참조하십시오: [랩 계정 설정 자습서](tutorial-setup-lab-account.md).
 
랩 계정을 만든 후 랩 계정에서 다음 설정을 사용하도록 설정합니다. 

| 랩 계정 설정 | Instructions |
| ----------- | ------------ |  
| 마켓플레이스 이미지 | 리눅스용 데이터 사이언스 가상 머신(우분투) 이미지를 사용하여 랩 계정 내에서 사용할 수 있습니다.  지침은 다음 문서를 참조하십시오: [랩 작성자가 사용할 수 있는 마켓플레이스 이미지 지정.](specify-marketplace-images.md) | 

[이 자습서를](tutorial-setup-classroom-lab.md) 수행하여 새 랩을 만들고 다음 설정을 적용합니다.

| 랩 설정 | 값/지침 | 
| ------------ | ------------------ |
| 가상 컴퓨터(VM) 크기 | 소형 GPU(컴퓨팅). 이 크기는 인공 지능 및 딥 러닝과 같은 컴퓨팅 집약적이고 네트워크 집약적인 응용 프로그램에 가장 적합합니다. |
| VM 이미지 | [리눅스 (우분투)에 대한 데이터 과학 가상 머신](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). 이 이미지는 기계 학습 및 데이터 과학을 위한 딥 러닝 프레임워크와 도구를 제공합니다. 이 이미지에 설치된 도구의 전체 목록을 보려면 다음 문서를 참조하십시오: [DSVM에 포함 된 내용?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| 원격 데스크톱 연결 사용 | 사용. <p>이 설정을 사용하면 교사와 학생이 RDP(원격 데스크톱)를 사용하여 VM(가상 컴퓨터)에 연결할 수 있습니다.</p><p>**중요**: RDP는 이미 설치 및 리눅스 이미지에 대한 데이터 과학 가상 머신에 구성되어 있습니다. 따라서 교사/학생은 추가 단계 없이 RDP를 통해 VM에 연결할 수 있습니다. 또한 그래픽 데스크톱에 연결해야 하는 경우 이 이미지에는 가상 시스템에 [X2Go 서버가](https://wiki.x2go.org/doku.php/doc:newtox2go) 이미 설치되어 있습니다. 학생은 로컬 컴퓨터에 X2Go 클라이언트를 설치해야 하며 연결시 클라이언트를 사용해야 합니다. 자세한 내용은 다음 가이드를 참조하십시오. <ul><li>[Linux용 데이터 과학 Virtual Machine에 액세스하는 방법](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[템플릿 VM에 연결하여 RDP 및 GUI 패키지를 설치합니다.](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)</li></ul></p>   |

Linux 용 데이터 과학 가상 머신 이미지는 이러한 유형의 클래스에 필요한 딥 러닝 프레임워크와 도구를 제공합니다. 따라서 템플릿 컴퓨터를 만든 후에는 더 이상 사용자 지정할 필요가 없습니다. 학생이 사용할 수 있도록 게시할 수 있습니다. 템플릿 페이지에서 **게시** 단추를 선택하여 템플릿을 랩에 게시합니다.  

## <a name="cost"></a>비용
이 랩의 비용을 추정하려면 다음 예제를 사용할 수 있습니다. 

25명의 학생이 20시간의 수업 시간 및 숙제 또는 과제에 대한 할당량 10시간의 수업을 받은 경우, 랩 가격은 25명 * (20 + 10) 시간 * 139랩 유닛 * 시간당 0.01 USD = 1042.5 USD입니다.

가격에 대한 자세한 내용은 [Azure 랩 서비스 가격 을](https://azure.microsoft.com/pricing/details/lab-services/)참조하십시오.

## <a name="conclusion"></a>결론
이 문서에서는 자연어 처리 클래스를 위한 랩을 만드는 단계를 안내했습니다. 다른 딥 러닝 수업에서도 비슷한 설정을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다음 단계는 랩을 설정하는 데 일반적입니다.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [학생에게 전자 메일 등록 링크](how-to-configure-student-usage.md#send-invitations-to-users). 

