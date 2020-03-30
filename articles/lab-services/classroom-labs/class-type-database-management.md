---
title: 관계형 데이터베이스에 대한 데이터베이스 관리를 가르치는 랩 설정 | 마이크로 소프트 문서
description: 관계형 데이터베이스 관리를 가르치기 위해 랩을 설정하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 4c375487b30595251753021033c98cf0ca1e8dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469921"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>관계형 데이터베이스에 대한 데이터베이스 관리를 가르치기 위해 랩 설정

이 문서에서는 Azure Lab 서비스의 기본 데이터베이스 관리 클래스에 대한 랩을 설정하는 방법에 대해 설명합니다. 데이터베이스 개념은 대학의 대부분의 컴퓨터 과학 부서에서 학습하는 소개 과정 중 하나입니다. SQL(구조화 된 쿼리 언어)은 국제 표준입니다. SQL은 데이터베이스의 콘텐츠 추가, 액세스 및 관리를 비롯한 관계 데이터베이스 관리의 표준 언어입니다.  빠른 처리, 입증된 신뢰성, 용이성 및 사용 유연성으로 가장 유명합니다.

이 문서에서는 MySQL 데이터베이스 서버와 SQL Server 2019 서버가 모두 있는 랩에서 가상 컴퓨터 템플릿을 설정하는 방법을 설명합니다.  [MySQL은](https://www.mysql.com/) 자유롭게 사용할 수있는 오픈 소스 관계형 데이터베이스 관리 시스템 (RDBMS)입니다.  [SQL Server 2019는](https://www.microsoft.com/sql-server/sql-server-2019) 마이크로소프트의 RDBMS의 최신 버전입니다.

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정하려면 Azure 구독 및 랩 계정을 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독을 받으면 Azure Lab 서비스에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정 만들기에 대한 자세한 내용은 [랩 계정 설정 자습서를](tutorial-setup-lab-account.md)참조하십시오.  기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정

랩 계정에 대해 아래 표에 설명된 설정을 사용하도록 설정합니다. 마켓플레이스 이미지를 사용하도록 설정하는 방법에 대한 자세한 내용은 [랩 작성자가 사용할 수 있는 마켓플레이스 이미지 지정을](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)참조하십시오.

| 랩 계정 설정 | Instructions |
| ------------------- | ------------ |
|마켓플레이스 이미지| 랩 계정 내에서 사용할 수 있도록 'WINDOWS 서버 2019 표준 온 Windows 서버 2019' 이미지를 사용하도록 설정합니다.|

### <a name="lab-settings"></a>랩 설정

교실 실습을 설정할 때 아래 표의 설정을 사용합니다.  강의실 랩을 만드는 방법에 대한 자세한 내용은 [강의실 랩 자습서 설정을](tutorial-setup-classroom-lab.md)참조하십시오.

| 랩 설정 | 값/지침 |
| ------------ | ------------------ |
|Virtual Machine 크기| 중간. 이 크기는 관계형 데이터베이스, 메모리 내 캐싱 및 분석에 가장 적합합니다.|
|가상 머신 이미지| SQL 서버 2019 Windows 서버 2019 표준|

## <a name="template-machine-configuration"></a>템플릿 기계 구성

Windows Server 2019에 MySQL을 설치하려면 가상 [컴퓨터에서 MySQL 커뮤니티 서버 설치 및 실행에](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fclassic%2Ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine)언급된 단계를 따를 수 있습니다.

SQL Server 2019는 새 랩을 만들 때 선택한 가상 컴퓨터 이미지에 미리 설치되어 있습니다.

## <a name="cost-estimate"></a>비용 추정

이 클래스에 대한 가능한 비용 견적을 살펴보겠습니다.  25명의 학생으로 구성된 수업을 사용합니다.  20시간의 수업 시간이 예정되어 있습니다.  또한 각 학생은 예정된 수업 시간 이외에 숙제 또는 과제에 대해 10시간 할당량을 받습니다.  우리가 선택한 가상 머신 크기는 42 개의 실험실 단위인 중간 크기였습니다.

다음은 이 클래스에 대한 가능한 비용 추정의 예입니다.

25명의 \* 학생(20시간 + 10시간) \* 시간당 0.42 USD = 315.00 USD

가격에 대한 자세한 내용은 [Azure 랩 서비스 가격 을](https://azure.microsoft.com/pricing/details/lab-services/)참조하십시오.

## <a name="conclusion"></a>결론

이 문서에서는 MySQL 및 SQL Server를 모두 사용하여 기본 데이터베이스 관리 개념에 대한 랩을 만드는 데 필요한 단계를 안내했습니다. 다른 데이터베이스 클래스에 대해 유사한 설정을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 단계는 랩을 설정하는 데 일반적입니다.

- [템플릿 만들기 및 관리](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [학생에게 이메일 등록 링크](how-to-configure-student-usage.md#send-invitations-to-users)
