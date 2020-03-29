---
title: Azure 데이터 과학 가상 컴퓨터 사용
description: Azure 전자 과학 가상 컴퓨터(DSVM)에 연결하여 Azure 전자 필기장 미리 보기에서 사용할 수 있는 컴퓨팅 성능을 확장하는 방법을 알아봅니다.
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: b4da63b7b2a6da4316215b85a09ca7420745251c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898412"
---
# <a name="use-azure-data-science-virtual-machines"></a>Azure 데이터 과학 가상 컴퓨터 사용

기본적으로 프로젝트는 **사용 되지** 않는 컴퓨팅 계층에서 실행 되며, 메모리 4 GB 및 데이터 1 GB로 제한 되어 남용을 방지 합니다. Azure 구독에서 프로비전한 다른 가상 머신을 사용하여 이러한 제한 사항을 무시할 수 있습니다. 이를 위해 가장 좋은 선택은 **Linux용 데이터 과학 가상 머신(우분투)** 이미지를 사용하는 Azure 데이터 과학 가상 머신(DSVM)입니다. 이러한 DSVM은 Azure 전자 필기장에 필요한 모든 것으로 미리 구성되어 제공되며 Azure 전자 필기장의 **실행** 드롭다운 목록에 자동으로 나타납니다.

> [!Note]
> Azure 노트북은 리눅스 우분투 이미지로 만든 DSVM에서만 지원됩니다. 노트북은 윈도우에서 지원되지 않습니다 2012, 윈도우 2016, 또는 리눅스 센트OS 이미지.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-dsvm-instance"></a>DSVM 인스턴스 만들기

새 DSVM 인스턴스를 만들려면 [Ubuntu Data Science VM 만들기](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)의 지침을 따르세요. 가격 세부 정보를 포함한 자세한 내용은 [데이터 과학 가상 컴퓨터를](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)참조하십시오.

## <a name="connect-to-the-dsvm"></a>DSVM에 연결

DSVM을 만든 후 Azure 노트북 프로젝트 대시보드에서 **실행** 드롭다운 목록을 선택하고 적절한 DSVM 인스턴스를 선택합니다. 드롭다운 목록에는 다음과 같은 조건이 true인 경우 DSVM 인스턴스가 표시됩니다.

- 회사 계정 등의 AAD(Azure Active Directory)를 사용하는 계정으로 Azure Notebooks에 로그인했습니다.
- 계정이 Azure 구독에 연결되어 있습니다.
- 해당 구독에 하나 이상의 가상 컴퓨터가 있으며, 최소한 리더 액세스 권한으로 리눅스용 데이터 과학 가상 머신(우분투) 이미지를 사용합니다.

![프로젝트 대시보드의 드롭다운 목록에 있는 Data Science Virtual Machine 인스턴스](media/project-compute-tier-dsvm.png)

DSVM 인스턴스를 선택하는 경우 Azure Notebooks에서 VM을 만들 때 사용한 특정 머신 자격 증명을 묻는 메시지가 표시될 수도 있습니다.

> [!Important]
> JupyterHub와 함께 사용하려면 사용자 이름을 소문자여야 합니다.

조건이 충족되지 않으면 DSVM에 계속 연결할 수 있습니다. 드롭다운 목록에서 이름(목록에 표시함), VM의 IP 주소 및 포트(일반적으로 8000, JupyterHub가 수신하는 기본 포트) 및 VM 자격 증명을 묻는 **직접 계산** 옵션을 선택합니다.

![직접 컴퓨팅 옵션에 대한 서버 정보 수집 프롬프트](media/project-compute-tier-direct.png)

Azure 포털의 DSVM 페이지에서 이러한 값을 가져옵니다.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>DSVM에서 Azure 전자 필기장 파일에 액세스

파일 시스템 액세스는 DSVM 버전 19.06.15 이상에서 지원됩니다. 버전을 확인하려면 먼저 SSH를 통해 DSVM에 연결한 다음 `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` 다음 명령을 실행합니다(여기에 표시된 정확한 IP 주소를 사용해야 합니다). 버전 번호는 "버전"의 출력에 표시됩니다.

**무료 계산** 계층과 파일 경로의 패리티를 유지하려면 DSVM에서 한 번에 하나의 프로젝트만 열 수 있습니다. 새 프로젝트를 열려면 먼저 열려 있는 프로젝트를 종료해야 합니다.

프로젝트가 VM에서 실행되면 파일은 Jupyter 서버의 루트 디렉토리(JupyterHub에 표시된 디렉토리)에 탑재되어 기본 Azure 전자 필기장 파일을 대체합니다. 노트북 UI의 **종료** 단추를 사용하여 VM을 종료하면 Azure 노트북이 기본 파일을 복원합니다.

![Azure 노트북의 종료 단추](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>새 DSVM 사용자 만들기

여러 사용자가 DSVM을 공유하는 경우 각 노트북 사용자에 대해 DSVM 사용자를 만들고 사용하여 서로 차단하지 않도록 할 수 있습니다.

1. Azure [포털에서](https://portal.azure.com)가상 컴퓨터로 이동합니다.
1. 왼쪽 여백의 **지원 + 문제 해결** 에서 암호 **재설정을**선택합니다.
1. 새 **사용자 이름을**입력합니다. JupyterHub와 함께 사용하려면 사용자 이름을 소문자여야 합니다. 암호를 입력합니다. 그런 다음 **업데이트를**선택합니다. 기존 사용자 이름은 영향을 받지 않습니다.
1. 추가 사용자에 대해 이전 단계를 반복합니다.

## <a name="next-steps"></a>다음 단계

Azure 데이터 과학 가상 [시스템 소개에](/azure/machine-learning/data-science-virtual-machine/overview)대한 DSVM에 대해 자세히 알아보십시오.
