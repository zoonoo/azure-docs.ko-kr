---
title: 관계형 데이터베이스에 대 한 데이터베이스 관리를 학습 하는 랩 설정 | Microsoft Docs
description: 관계형 데이터베이스를 관리 하는 방법을 설명 하는 랩을 설정 하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77469921"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>관계형 데이터베이스에 대 한 데이터베이스 관리를 학습 하는 랩 설정

이 문서에서는 Azure Lab Services에서 기본 데이터베이스 관리 클래스에 대 한 랩을 설정 하는 방법을 설명 합니다. 데이터베이스 개념은 대학에서 대부분의 컴퓨터 과학 부서를 학습 하는 소개 과정 중 하나입니다. 구조적 쿼리 언어 (SQL)은 국제 표준입니다. SQL은 데이터베이스의 콘텐츠 추가, 액세스 및 관리를 포함 하 여 데이터베이스 관리와 관련 된 표준 언어입니다.  이는 빠른 처리, 검증 된 안정성, 편의성 및 사용의 유연성에 가장 많이 사용 됩니다.

이 문서에서는 MySQL 데이터베이스 서버와 SQL Server 2019 Server를 모두 사용 하 여 랩에서 가상 머신 템플릿을 설정 하는 방법을 보여 줍니다.  [MySQL](https://www.mysql.com/) 은 무료로 사용할 수 있는 오픈 소스 RDBMS (관계형 데이터베이스 관리 시스템)입니다.  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) 은 최신 버전의 Microsoft RDBMS입니다.

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정 하려면 시작 하려면 Azure 구독 및 랩 계정이 필요 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독을 받은 후 Azure Lab Services에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정을 만드는 방법에 대 한 자세한 내용은 자습서를 참조 [하 여 랩 계정 설정](tutorial-setup-lab-account.md)을 참조 하세요.  기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정

랩 계정에 대해 아래 표에 설명 된 설정을 사용 하도록 설정 합니다. Marketplace 이미지를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [랩 작성자에 게 제공 되는 marketplace 이미지 지정](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)을 참조 하세요.

| 랩 계정 설정 | Instructions |
| ------------------- | ------------ |
|Marketplace 이미지| 랩 계정 내에서 사용 하기 위해 Windows Server 2019에서 ' SQL Server 2019 Standard 사용 ' 이미지를 사용 하도록 설정 합니다.|

### <a name="lab-settings"></a>랩 설정

클래스 룸 랩을 설정 하는 경우 아래 표의 설정을 사용 합니다.  교실 랩을 만드는 방법에 대 한 자세한 내용은 [교실 랩 설정 자습서](tutorial-setup-classroom-lab.md)를 참조 하세요.

| 랩 설정 | 값/지침 |
| ------------ | ------------------ |
|Virtual Machine 크기| 중간. 이 크기는 관계형 데이터베이스, 메모리 내 캐싱 및 분석에 가장 적합 합니다.|
|가상 컴퓨터 이미지| Windows Server 2019 SQL Server 2019 Standard|

## <a name="template-machine-configuration"></a>템플릿 컴퓨터 구성

Windows Server 2019에 MySQL을 설치 하려면 [가상 머신에서 Mysql 커뮤니티 서버 설치 및 실행](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fclassic%2Ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine)에 설명 된 단계를 수행 하면 됩니다.

SQL Server 2019는 새 랩을 만들 때 선택한 가상 머신 이미지에 사전 설치 되어 있습니다.

## <a name="cost-estimate"></a>예상 비용

이 클래스에 대해 가능한 예상 비용을 살펴보겠습니다.  25 명의 학생 클래스를 사용 합니다.  예약 된 클래스 시간은 20 시간입니다.  또한 각 학생은 예약 된 클래스 시간 외에도 과제 또는 배정에 대해 10 시간 할당량을 얻습니다.  선택한 가상 컴퓨터 크기는 42 lab 단위인 medium입니다.

이 클래스에 대 한 예상 비용 예상 예는 다음과 같습니다.

학생 수가 25 개인 \* (20 개의 예약 된 시간 + 10 할당량 시간) \* 시간당 0.42 USD = 315.00 USD

가격 책정에 대 한 추가 세부 정보는 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조 하세요.

## <a name="conclusion"></a>결론

이 문서에서는 MySQL과 SQL Server를 모두 사용 하 여 기본적인 데이터베이스 관리 개념을 위한 랩을 만드는 데 필요한 단계를 안내 합니다. 다른 데이터베이스 클래스에 대해 유사한 설정을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 단계는 랩을 설정 하는 데 일반적입니다.

- [템플릿 만들기 및 관리](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [학생에 대 한 전자 메일 등록 링크](how-to-configure-student-usage.md#send-invitations-to-users)
