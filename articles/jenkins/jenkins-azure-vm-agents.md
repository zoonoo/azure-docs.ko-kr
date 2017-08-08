---
title: "Jenkins와 연속 통합을 위해 Azure VM 에이전트를 사용합니다."
description: "Jenkins 슬레이브로 Azure VM 에이전트입니다."
services: multiple
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 5f2df414b4d0e8798b7ed6d90d0ea0fb79d42fc2
ms.contentlocale: ko-kr
ms.lasthandoff: 06/21/2017

---
# <a name="use-azure-vm-agents-for-continuous-integration-with-jenkins"></a>Jenkins와 연속 통합을 위해 Azure VM 에이전트를 사용합니다.

이 빠른 시작에서는 Jenkins Azure VM 에이전트 플러그 인을 사용하여 Azure에서 주문형 Linux(Ubuntu)를 만드는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다.

* Jenkins 마스터가 아직 없는 경우 [솔루션 템플릿](install-jenkins-solution-template.md)으로 시작할 수 있습니다. 
* Azure 서비스 주체가 아직 없는 경우 [Azure CLI 2.0을 사용하여 Azure 서비스 주체 만들기](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)를 참조하세요.

## <a name="install-azure-vm-agents-plugin"></a>Azure VM 에이전트 플러그 인 설치

[솔루션 템플릿](install-jenkins-solution-template.md)에서 시작하는 경우 Azure VM 에이전트 플러그 인이 Jenkins 마스터에 설치됩니다.

그렇지 않은 경우 Jenkins 대시보드 내에서 **Azure VM 에이전트** 플러그 인을 설치합니다.

## <a name="configure-the-plugin"></a>플러그 인 구성

* Jenkins 대시보드 내에서 **Manage Jenkins -> Configure System ->**을 클릭합니다. 페이지 아래로 스크롤하고 **새 클라우드 추가** 드롭다운이 있는 섹션을 찾습니다. 메뉴에서 **Microsoft Azure VM 에이전트**를 선택합니다.
* Azure 자격 증명 드롭다운에서 기존 계정을 선택합니다.  새 **Microsoft Azure 서비스 주체**를 추가하려면 구독 ID, 클라이언트 ID, 클라이언트 암호 및 OAuth 2.0 토큰 끝점 값을 입력합니다.

![Azure 자격 증명](./media/jenkins-azure-vm-agents/service-principal.png)

* **구성 확인**을 클릭하여 프로필 구성이 올바른지 확인합니다.
* 구성을 저장하고 다음 단계를 계속합니다.

## <a name="template-configuration"></a>템플릿 구성

### <a name="general-configuration"></a>일반 구성
다음으로, Azure VM 에이전트를 정의하는 데 사용할 템플릿을 구성합니다. 

* **추가**를 클릭하여 템플릿을 추가합니다. 
* 새 템플릿의 이름을 제공합니다. 
* 레이블에 "ubuntu"를 입력합니다. 이 레이블은 작업을 구성하는 동안 사용됩니다.
* 콤보 상자에서 원하는 지역을 선택합니다.
* 원하는 VM 크기를 선택합니다.
* Azure Storage 계정 이름을 지정하거나 비워 두어 기본 이름인 "jenkinsarmst"를 사용하도록 합니다.
* 보존 시간을 분 단위로 지정합니다. 이 설정은 Jenkins가 유휴 에이전트를 자동으로 삭제할 때까지 대기할 수 있는 시간(분)을 정의합니다. 유휴 에이전트를 자동으로 삭제하지 않으려는 경우 0을 지정합니다.

![일반 구성](./media/jenkins-azure-vm-agents/general-config.png)

### <a name="image-configuration"></a>이미지 구성

Linux(Ubuntu) 에이전트를 만들려면 **이미지 참조**를 선택하고 예를 들어 다음 구성을 사용합니다. Azure에서 지원하는 최신 이미지는 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1)를 참조하세요.

* 이미지 게시자: Canonical
* 이미지 제품: UbuntuServer
* 이미지 SKU: 14.04.5-LTS
* 이미지 버전: 최신
* OS 유형: Linux
* 시작 방법: SSH
* 관리자 자격 증명 제공
* VM 초기화 스크립트의 경우 다음을 입력합니다.
```
# Install Java
sudo apt-get -y update
sudo apt-get install -y openjdk-7-jdk
sudo apt-get -y update --fix-missing
sudo apt-get install -y openjdk-7-jdk
```
![이미지 구성](./media/jenkins-azure-vm-agents/image-config.png)

* **템플릿 확인**을 클릭하여 구성을 확인합니다.
* **Save**를 클릭합니다.

## <a name="create-a-job-in-jenkins"></a>Jenkins에서 작업 만들기

* Jenkins 대시보드 내에서 **New Item**을 클릭합니다. 
* 이름을 입력하고 **프리스타일 프로젝트**를 선택하고 **확인**을 클릭합니다.
* **일반** 탭에서 [Restrict where project can be run(프로젝트를 실행할 수 있는 위치 제한)]을 선택하고 레이블 식에 "ubuntu"를 입력합니다. 이제 드롭다운에 "ubuntu"가 표시됩니다.
* **Save**를 클릭합니다.

![작업 설정](./media/jenkins-azure-vm-agents/job-config.png)

## <a name="build-your-new-project"></a>새 프로젝트 빌드

* Jenkins 대시보드로 돌아갑니다.
* 만든 새 작업을 마우스 오른쪽 단추로 클릭하고 **지금 빌드**를 클릭합니다. 빌드가 시작됩니다. 
* 빌드가 완료되면 **콘솔 출력**으로 이동합니다. 해당 빌드가 Azure에서 원격으로 수행된 것을 확인할 수 있습니다.

![콘솔 출력](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="reference"></a>참조

* Azure Friday 비디오: [Azure VM 에이전트를 사용하여 Jenkins와 연속 통합](https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents)(영문)
* 지원 정보 및 구성 옵션: [Azure VM 에이전트 Jenkins 플러그 인 Wiki](https://wiki.jenkins-ci.org/display/JENKINS/Azure+VM+Agents+Plugin) 


