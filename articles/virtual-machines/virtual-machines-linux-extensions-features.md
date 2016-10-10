<properties
 pageTitle="가상 컴퓨터 확장 및 기능 | Microsoft Azure"
 description="확장이 제공하거나 개선하는 기능별로 그룹화하여 Azure 가상 컴퓨터에 사용할 수 있는 확장을 알아봅니다."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="neilpeterson"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager"/>

<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/22/2016"
 ms.author="nepeters"/>

# 가상 컴퓨터 확장 및 기능 정보

## Azure VM 확장

Azure 가상 컴퓨터 확장은 Azure 가상 컴퓨터에서 배포 후 구성 및 자동화 작업을 제공하는 작은 응용 프로그램입니다. 예를 들어 가상 컴퓨터에서 소프트웨어가 설치되도록 요구하거나, 바이러스 백신 보호 또는 Docker 구성을 요구하는 경우 VM 확장을 사용하여 이러한 작업을 완료할 수 있습니다. Azure CLI, PowerShell, Resource Manager 템플릿 및 Azure Portal을 사용하 여 Azure VM 확장을 실행할 수 있습니다. 확장을 새 가상 컴퓨터 배포와 번들로 제공하거나 기존 시스템에 대해 실행할 수 있습니다.

이 문서는 Azure 가상 컴퓨터 확장에 대한 필수 구성 요소와 사용 가능한 VM 확장을 검색하는 방법에 대한 지침을 제공합니다.

## Azure VM 에이전트

Azure VM 에이전트는 Azure 가상 컴퓨터와 Azure 패브릭 컨트롤러 간 상호 작용을 관리합니다. VM 에이전트는 VM 확장 실행을 포함하여 Azure 가상 컴퓨터 배포 및 관리의 다양한 기능적 측면을 담당합니다. Azure VM 에이전트는 Azure 갤러리 이미지에 미리 설치되며 지원되는 운영 체제에 설치될 수 있습니다.

지원되는 운영 체제 및 설치 지침에 대한 자세한 내용은 [Azure Linux 에이전트 사용자 가이드](./virtual-machines-linux-agent-user-guide.md)를 참조하세요.

## VM 확장 검색

Azure 가상 컴퓨터와 함께 여러 다양한 VM 확장을 사용할 수 있습니다. 전체 목록을 보려면 Azure CLI와 함께 다음 명령을 실행하고 해당 위치를 선택한 위치로 바꿉니다.

```none
azure vm extension-image list westus
```

<br />

## 일반 VM 확장

|확장 이름 |설명 |추가 정보 |
|---|---|---|
|Linux용 사용자 지정 스크립트 확장 | Azure 가상 컴퓨터에 대해 스크립트 실행 |[Linux용 사용자 지정 스크립트 확장](./virtual-machines-linux-extensions-customscript.md) |
|Docker 확장 |원격 Docker 명령을 지원하기 위해 Docker 데몬을 설치합니다. | [Docker VM 확장](./virtual-machines-linux-dockerextension.md) |
|VM 액세스 확장 | Azure 가상 컴퓨터에 대한 액세스 권한 복구 |[VM 액세스 확장](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
|Azure 진단 확장 |Azure 진단 관리 |[Azure 진단 확장](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |

<!---HONumber=AcomDC_0928_2016-->