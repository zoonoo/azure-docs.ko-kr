---
title: '참조: 알려진 문제 및 문제 해결'
titleSuffix: Azure Data Science Virtual  Machine
description: Azure Data Science Virtual Machine에 대해 알려진 문제, 해결 방법 및 문제 해결의 목록을 가져옵니다.
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: reference
ms.date: 05/27/2021
ms.openlocfilehash: 8e3393f236152e591b054ee2d7c4c2753c2ff670
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111408752"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine에 대해 알려진 문제 및 문제 해결

본 문서는 Azure Data Science Virtual Machine을 사용할 때 발생할 수 있는 오류나 실패를 찾아 수정하는 데 도움이 됩니다.

## <a name="prompted-for-password-when-running-sudo-command-ubuntu"></a>sudo 명령을 실행할 때 암호를 묻는 메시지 표시(Ubuntu)

Ubuntu 컴퓨터에서 `sudo` 명령을 실행할 때 로그인한 사용자가 실제로 맞는지 확인하기 위해 암호를 다시 입력하라는 메시지가 표시될 수 있습니다. Ubuntu와 같은 Linux 시스템의 경우 예상되는 동작과 기본값입니다.
그러나 경우에 따라 반복되는 인증이 필요하지 않고 귀찮게 됩니다.

대부분의 경우 다시 인증을 사용하지 않도록 설정하려면 터미널에서 다음 명령을 실행하면 됩니다.

`echo -e "\n$USER ALL=(ALL) NOPASSWD: ALL\n" | sudo tee -a /etc/sudoers`

터미널을 다시 시작한 후 sudo는 다른 로그인을 요청하지 않고 세션 로그인의 인증이 충분한 것으로 간주합니다.

## <a name="accessing-sql-server-windows"></a>SQL Server 액세스(Windows)

사전 설치된 SQL Server 인스턴스에 연결하려고 할 때 "로그인 실패"오류가 발생할 수 있습니다. SQL Server 인스턴스에 성공적으로 연결하려면 연결하는 프로그램을 실행해야 합니다. 예: 관리자 모드의 SSMS(SQL Server Management Studio). 관리자 모드에서는 DSVM의 기본값 때문에 관리자만 연결할 수 있어야 합니다.

## <a name="python-package-installation-issues"></a>Python 패키지 설치 문제

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Linux에서 pip 중단 종속성이 있는 패키지 설치

패키지 설치 시 `pip install` 대신 `sudo pip install`을 사용합니다.

## <a name="disk-encryption-issues"></a>디스크 암호화 문제

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Ubuntu DSVM에서 디스크 암호화가 실패합니다.

ADE(Azure Disk Encryption)는 현재 Ubuntu DSVM에서 지원되지 않습니다. 문제를 해결하려면 [Azure 관리 디스크의 서버측 암호화](../../virtual-machines/disk-encryption.md)를 구성하는 것이 좋습니다.

## <a name="tool-appears-disabled"></a>도구 표시 사용하지 않음

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Windows DSVM에서 Hyper-V가 작동하지 않음

Hyper-V가 처음에 Windows에서 작동하지 않는 것은 예상된 동작입니다. 부팅 성능을 위해 일부 서비스를 사용하지 않도록 설정했습니다. Hyper-V를 사용하도록 설정하려면 다음을 수행합니다.

1. Windows DSVM에서 검색 창 열기
1. "서비스"를 입력
1. 모든 Hyper-V 서비스를 "수동"으로 설정
1. "Hyper-V 가상 머신 관리"를 "자동"으로 설정

최종 화면이 다음과 같아야 합니다.

   ![Hyper-V 사용](./media/workaround/hyperv-enable-dsvm.png)