---
title: Azure 데이터 과학 Virtual Machines 사용
description: Azure Data Science Virtual Machine (DSVM)에 연결 하 여 Azure Notebooks에서 사용할 수 있는 계산 능력을 확장 합니다.
services: app-service
documentationcenter: ''
author: getroyer
manager: andneil
ms.assetid: 0ccc2529-e17f-4221-b7c7-9496d6a731cc
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/13/2019
ms.author: getroyer
ms.openlocfilehash: 0f06475708adaf1324eedbd8e315fe4d731339be
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970109"
---
# <a name="use-azure-data-science-virtual-machines"></a>Azure 데이터 과학 Virtual Machines 사용

기본적으로 프로젝트는 **무료 계산** 계층에서 실행 됩니다 .이는 악성을 방지 하기 위해 4gb의 메모리와 1gb의 데이터로 제한 됩니다. Azure 구독에서 프로비전한 다른 가상 머신을 사용하여 이러한 제한 사항을 무시할 수 있습니다. 이러한 목적을 위해 **Ubuntu (Linux 용 Data Science Virtual Machine)** 이미지를 사용 하는 Azure Data Science Virtual Machine (dsvm)를 선택 하는 것이 가장 좋습니다. 이러한 DSVM은 Azure Notebooks 하는 데 필요한 모든 항목으로 미리 구성 되며 Azure Notebooks의 **실행** 드롭다운 목록에 자동으로 표시 됩니다.

> [!Note]
> Azure Notebooks은 Linux Ubuntu 이미지를 사용 하 여 만든 DSVMs 에서만 지원 됩니다. 노트북은 Windows 2012, Windows 2016 또는 Linux CentOS 이미지에서 지원 되지 않습니다.

## <a name="create-a-dsvm-instance"></a>DSVM 인스턴스 만들기

새 DSVM 인스턴스를 만들려면 [Ubuntu Data Science VM 만들기](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)의 지침을 따르세요. 가격 정보를 비롯 한 자세한 내용은 [데이터 과학 Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)를 참조 하세요.

## <a name="connect-to-the-dsvm"></a>DSVM에 연결

DSVM을 만들었으면 Azure Notebooks 프로젝트 대시보드에서 **실행** 드롭다운 목록을 선택 하 고 적절 한 dsvm 인스턴스를 선택 합니다. 드롭다운 목록에는 다음 조건에 해당 하는 경우 DSVM 인스턴스가 표시 됩니다.

- 회사 계정 등의 AAD(Azure Active Directory)를 사용하는 계정으로 Azure Notebooks에 로그인했습니다.
- 계정이 Azure 구독에 연결되어 있습니다.
- Linux (Ubuntu) 이미지에 대 한 Data Science Virtual Machine를 사용 하는 최소한의 판독기 액세스 권한이 있는 해당 구독에 하나 이상의 가상 머신이 있습니다.

![프로젝트 대시보드의 드롭다운 목록에 있는 Data Science Virtual Machine 인스턴스](media/project-compute-tier-dsvm.png)

DSVM 인스턴스를 선택하는 경우 Azure Notebooks에서 VM을 만들 때 사용한 특정 머신 자격 증명을 묻는 메시지가 표시될 수도 있습니다.

조건 중 하나라도 충족 되지 않으면 DSVM에 연결할 수 있습니다. 드롭다운 목록에서 **직접 계산** 옵션을 선택 합니다 .이 옵션을 선택 하면 목록에 표시 되는 이름을 입력 하 라는 메시지가 표시 되 고, VM의 IP 주소와 포트 (일반적으로 8000, JupyterHub가 수신 하는 기본 포트) 및 vm 자격 증명이 표시 됩니다.

![직접 컴퓨팅 옵션에 대한 서버 정보 수집 프롬프트](media/project-compute-tier-direct.png)

이러한 값은 Azure Portal의 DSVM 페이지에서 가져옵니다.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>DSVM에서 Azure Notebooks 파일 액세스

파일 시스템 액세스는 DSVM 버전 19.06.15 이상에서 지원 됩니다. 버전을 확인 하려면 먼저 SSH를 통해 DSVM에 연결한 후 다음 명령을 실행 합니다. `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` (여기에 표시 된 정확한 IP 주소를 사용 해야 함). 버전 번호는 "버전"에 대 한 출력에 표시 됩니다.

**무료 계산** 계층으로 파일 경로의 패리티를 유지 하기 위해 dsvm에서 한 번에 하나의 프로젝트만 열 수 있습니다. 새 프로젝트를 열려면 먼저 열려 있는 프로젝트를 종료 해야 합니다.

프로젝트가 VM에서 실행 되는 경우 파일은 Jupyter 서버 (JupyterHub에 표시 된 디렉터리)의 루트 디렉터리에 탑재 되어 기본 Azure Notebooks 파일을 대체 합니다. 노트북 UI의 **종료** 단추를 사용 하 여 VM을 종료 하면 Azure Notebooks 기본 파일이 복원 됩니다.

![Azure Notebooks의 종료 단추](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>새 DSVM 사용자 만들기

여러 사용자가 DSVM을 공유 하는 경우 각 노트북 사용자에 대해 DSVM 사용자를 만들고 사용 하 여 서로 차단 하지 않도록 방지할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 가상 머신으로 이동 합니다.
1. 왼쪽 여백의 **지원 + 문제 해결** 에서 **암호 재설정**을 선택 합니다.
1. 새 사용자 이름 및 암호를 입력 하 고 **업데이트**를 선택 합니다. 기존 사용자 이름은 영향을 받지 않습니다.
1. 모든 추가 사용자에 대해 이전 단계를 반복 합니다.

## <a name="next-steps"></a>다음 단계

[Azure 데이터 과학 Virtual Machines 소개](/azure/machine-learning/data-science-virtual-machine/overview)에서 dsvms에 대해 자세히 알아보세요.
