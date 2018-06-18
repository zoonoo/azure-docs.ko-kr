---
title: Azure Lab Services 정보 | Microsoft 문서 도구
description: Lab Services를 사용하면 개발자, 테스터, 교육자, 학생 및 기타 사람들이 사용할 수 있는 가상 머신으로 랩을 쉽게 만들고, 관리하고, 보안을 유지할 수 있는 방법에 대해 알아봅니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: e9c3cae7c7129cc489ddd38b5b2de18dd6f52e58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660592"
---
# <a name="introduction-to-classroom-labs"></a>클래스룸 랩 소개
Azure Lab Services를 사용하면 클라우드에서 클래스룸 랩 환경을 빠르게 설정할 수 있습니다. 강사가 클래스룸 랩을 만들고, Windows 또는 Linux 가상 머신을 프로비전하고, 수업에 필요한 소프트웨어 및 도구 랩을 설치하고, 학생들이 사용할 수 있게 합니다. 학생들은 클래스룸 랩에서 랩의 VM(가상 머신)에 연결하여 프로젝트, 과제, 클래스룸 연습에 사용합니다. 

클래스룸 랩은 Azure에서 관리되는 랩입니다. 서비스 자체는 VM(가상 머신) 가동에서 오류 처리 및 인프라 크기 조정까지 관리되는 랩에 대한 모든 인프라 관리를 처리합니다. 필요한 인프라 종류를 지정하고 수업에 필요한 도구 또는 소프트웨어를 설치합니다. 관리되는 랩은 현재 미리 보기로 제공됩니다.  

## <a name="scenarios"></a>시나리오
Azure Lab Services의 클래스룸 랩에서 지원하는 기본 시나리오는 다음과 같습니다. 

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>클래스룸을 위해 크기 조정이 가능한 컴퓨터 랩을 클라우드에 설치  

- 관리되는 클래스룸 랩 만들기 단지 필요한 서비스를 정확히 알려주고 랩의 인프라를 만들고 관리하므로 랩에 대한 기술적 세부 사항이 아니라 수업에 집중하여 강의할 수 있습니다. 
- 학생들에게 클래스에 필요한 것으로 정확히 구성된 가상 머신 랩을 제공합니다. 각 학생에게 VM을 클래스 학습에 사용하도록 제한된 시간을 줍니다.  
- 학교의 실제 컴퓨터 랩을 클라우드로 옮깁니다. VM 수를 랩에 설정한 최대 사용량 및 비용 임계 값에 이르기 까지만 자동으로 조정합니다. 
- 마치면 클릭 한 번으로 랩을 삭제합니다. 

## <a name="user-profiles"></a>사용자 프로필
이 문서에서는 Azure Lab Services에 있는 다른 사용자 프로필을 설명합니다. 

### <a name="lab-account-owner"></a>랩 계정 소유자
Azure 구독을 소유한 조직내 클라우드 리소스의 IT 관리자는 일반적으로 랩 계정 소유자 역할을 하며 다음 작업을 수행합니다.   

- 조직에 대한 랩 계정을 설정합니다.
- 모든 랩에서 정책을 관리하고 구성합니다.
- 조직의 사람들에게 랩 계정으로 랩을 만들 수있는 권한을 부여합니다.

### <a name="educator"></a>강사
일반적으로 강사 또는 온라인 강사와 같은 사용자는 랩 계정에 속한 클래스룸 랩을 만듭니다. 강사가 수행하는 작업은 다음과 같습니다. 

- 클래스룸 랩을 만듭니다.
- 랩에서 가상 머신을 만듭니다. 
- 가상 머신에 적절한 소프트웨어를 설치합니다.
- 랩에 액세스할 수 있는 사용자를 지정합니다.
- 학생들에게 랩에 대한 등록 링크를 제공합니다.

### <a name="student"></a>학생
학생이 수행하는 작업은 다음과 같습니다.

- 랩 사용자는 랩 작성자로부터 받은 등록 링크를 사용하여 랩에 등록합니다. 
- 랩의 가상 머신에 연결하고 수업, 과제 및 프로젝트를 수행하는 데 사용합니다. 

## <a name="next-steps"></a>다음 단계
Azure Lab Services를 사용하여 클래스룸 랩을 만드는 데 필요한 랩 계정을 설정합니다.

- [랩 계정 설정](tutorial-setup-lab-account.md)
