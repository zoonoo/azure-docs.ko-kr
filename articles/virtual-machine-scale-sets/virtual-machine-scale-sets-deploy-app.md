---
title: "가상 컴퓨터 크기 집합에 앱 배포 | Microsoft Docs"
description: "가상 컴퓨터 크기 집합에 앱 배포"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: f13545d753690534e0e645af67efcf1b524837eb
ms.openlocfilehash: dad27b11b5f02ed41826b82882cc5089eb69cb04


---
# <a name="deploy-an-app-on-virtual-machine-scale-sets"></a>가상 컴퓨터 크기 집합에 앱 배포
VM 크기 집합에서 실행되는 응용 프로그램은 일반적으로 다음 세 가지 방법 중 하나로 배포됩니다.

* 배포 시 플랫폼 이미지에 새 소프트웨어 설치 이 컨텍스트의 플랫폼 이미지는 Azure 마켓플레이스의 운영 체제 이미지(Ubuntu 16.04, Windows Server 2012 R2 등)과 같은 운영 체제 이미지입니다.

[VM 확장](../virtual-machines/virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 사용하여 플랫폼 이미지에 새 소프트웨어를 설치할 수 있습니다. VM 확장은 VM이 배포될 때 실행되는 소프트웨어입니다. 사용자 지정 스크립트 확장을 사용하여 배포 시에 원하는 모든 코드를 실행할 수 있습니다. [여기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) 에는 Apache 및 PHP를 설치하기 위한 Linux 사용자 지정 스크립트 확장과 Azure 자동 크기 조정에서 사용되는 성능 데이터를 내보내기 위한 진단 확장의 두 가지 VM 확장을 포함하는 Azure Resource Manager 템플릿 예제가 나와 있습니다.

이 방법의 장점은 응용 프로그램 코드와 OS 간에 분리 수준이 형성되고 응용 프로그램을 별도로 유지할 수 있다는 것입니다. 물론 이동하는 부분도 있습니다. 다운로드하고 구성할 스크립트가 많이 있으면 VM 배포 시간이 더 오래 걸릴 수 있습니다.

**사용자 지정 스크립트 확장 명령에 중요한 정보(예: 암호)를 전달할 때는 `settings` 특성 대신, 사용자 지정 스크립트 확장의 `protectedSettings` 특성에 `commandToExecute`를 지정해야 합니다.**

* 단일 VHD에 OS 및 응용 프로그램을 둘 다 포함하는 사용자 지정 VM 이미지를 만듭니다. 크기 집합은 사용자가 만든 이미지에서 복사된 VM 집합으로 구성되며, 사용자가 관리해야 합니다. 이 방법을 사용할 때는 VM 배포 시에 추가 구성이 필요하지 않습니다. 그러나 `2016-03-30` 버전(및 이전 버전)의 VM 크기 집합에서 크기 집합에 포함된 VM에 대한 OS 디스크는 단일 저장소 계정으로 제한됩니다. 따라서 플랫폼 이미지에 대한 크기 집합당 100VM 제한과 달리, 한 크기 집합에 최대 40개의 VM을 유지할 수 있습니다. 자세한 내용은 [크기 집합 디자인 개요](virtual-machine-scale-sets-design-overview.md)를 참조하세요.

    >[!NOTE]
    >VM Scale Sets API 버전 `2016-04-30-preview`는 운영 체제 디스크 및 모든 추가 데이터 디스크에 대한 Azure Managed Disks 사용을 지원합니다. 자세한 내용은 [Managed Disks 개요](../storage/storage-managed-disks-overview.md) 및 [연결된 데이터 디스크 사용](virtual-machine-scale-sets-attached-disks.md)을 참조하세요. 

* 기본적으로 컨테이너 호스트에 해당하는 플랫폼 또는 사용자 지정 이미지를 배포하고, orchestrator 또는 구성 관리 도구로 관리하는 하나 이상의 컨테이너로 응용 프로그램을 설치합니다. 이 방법의 장점은 응용 프로그램 계층에서 클라우드 인프라를 추상화하고 별도로 유지할 수 있다는 것입니다.

## <a name="what-happens-when-a-vm-scale-set-scales-out"></a>VM 크기 집합이 확장되면 어떻게 되나요?
수동으로 또는 자동 크기 조정을 통해 용량을 늘림으로써 크기 집합에 하나 이상의 VM을 추가하면 응용 프로그램이 자동으로 설치됩니다. 예를 들어 크기 집합에 확장이 정의되면 VM이 만들어질 때마다 새 VM에서 해당 확장이 실행됩니다. 크기 집합이 사용자 지정 이미지를 기준으로 할 경우 새 VM은 원본 사용자 지정 이미지의 복사본이 됩니다. 크기 집합 VM이 컨테이너 호스트인 경우 사용자 지정 스크립트 확장에 컨테이너를 로드하는 시작 코드를 유지하거나, 클러스터 orchestrator에 등록되는 에이전트가 확장을 통해 설치될 수 있습니다(예: Azure Container Service).

## <a name="how-do-you-manage-application-updates-in-vm-scale-sets"></a>VM 크기 집합에서 응용 프로그램 업데이트를 관리하려면 어떻게 해야 할까요?
VM 크기 집합의 응용 프로그램 업데이트의 경우 세 가지 이전 응용 프로그램 배포 방법에 포함되는 세 가지 주요 접근 방법을 진행합니다.

* VM 확장으로 업데이트. 새 VM이 배포되거나, 기존 VM이 이미지로 다시 설치되거나, VM 확장이 업데이트될 때마다 VM 크기 집합에 대해 정의된 모든 VM 확장이 실행됩니다. 응용 프로그램을 업데이트해야 할 경우 확장을 통해 응용 프로그램을 직접 업데이트하는 것이 가능한 방법입니다. 확장 정의를 업데이트하기만 하면 됩니다. 이 작업을 수행할 수 있는 간단한 방법은 새 소프트웨어를 가리키도록 fileUris를 변경하는 것입니다.

* 변경할 수 없는 사용자 지정 이미지 접근 방법에 해당합니다. VM 이미지에 응용 프로그램(또는 앱 구성 요소)을 적용하는 경우 신뢰할 수 있는 파이프라인을 구축하여 이미지의 빌드, 테스트 및 배포를 자동화하는 데 주력할 수 있습니다. 미리 구성된 크기 집합을 용이하게 프로덕션으로 빠르게 교체할 수 있도록 아키텍처를 디자인할 수 있습니다. 이 방법의 예로 [Azure Spinnaker 드라이버 작업](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/)이 있습니다.

Packer 및 Terraform도 Azure Resource Manager를 지원하므로, 이미지를 "코드로" 정의하고 Azure에서 빌드한 다음 해당 VHD를 크기 집합에서 사용할 수 있습니다. 그러나 사용자가 마켓플레이스에서 직접 조작하지 않으므로 확장/사용자 지정 스크립트가 좀 더 중요한 경우 이렇게 하면 마켓플레이스 이미지에 문제가 될 수 있습니다.

* 컨테이너를 업데이트합니다. 응용 프로그램 및 앱 구성 요소를 컨테이너로 캡슐화하여 응용 프로그램 수명 주기 관리를 클라우드 인프라보다 한 수준 높게 추상화한 다음 컨테이너 orchestrator 및 Chef/Puppet와 같은 구성 관리자를 통해 이러한 컨테이너를 관리합니다.

그러면 크기 집합 VM은 컨테이너에 대해 안정화되고, 가끔씩 보안 및 OS 관련 업데이트만 요구합니다. 앞서 설명한 것처럼 Azure Container Service는 이 접근 방법을 따르고 그에 따라 서비스를 구축하는 좋은 예에 해당합니다.

## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>업데이트 도메인 간에 OS 업데이트를 롤아웃하려면 어떻게 해야 할까요?
VM 크기 집합을 계속 실행하면서 OS 이미지를 업데이트하려고 할 것입니다. 이렇게 하는 한 가지 방법은 VM 이미지를 VM 단위로 한 번에 하나씩 업데이트하는 것입니다. 이 작업은 PowerShell 또는 Azure CLI를 사용하여 수행할 수 있습니다. VM 크기 집합 모델(구성이 정의되는 방식)을 업데이트하고 개별 VM에서 "수동 업그레이드" 호출을 실행하는 별도의 명령이 있습니다. 또한 VM 크기 집합에서 OS 업그레이드를 수행하는 데 사용할 수 있는 옵션에 대한 자세한 내용은 [가상 컴퓨터 크기 집합 업그레이드](./virtual-machine-scale-sets-upgrade-scale-set.md) Azure 문서에서 제공합니다.




<!--HONumber=Feb17_HO2-->


