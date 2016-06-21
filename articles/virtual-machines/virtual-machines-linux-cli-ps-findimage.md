<properties
   pageTitle="Linux VM 이미지 탐색 및 선택 | Microsoft Azure"
   description="리소스 관리자 배포 모델로 Linux 가상 컴퓨터를 만들 경우 이미지의 게시자, 제품 및 SKU를 확인하는 방법에 대해 알아보세요."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="06/06/2016"
   ms.author="rasquill"/>

# CLI 또는 Powershell로 Azure의 Linux 가상 컴퓨터 이미지 이동 및 선택

이 항목은 배포할 수 있는 각 위치에 대한 게시자, 제안, SKU 및 버전을 찾는 방법을 설명합니다. 예를 들면 일부 자주 사용되는 Linux VM 이미지는 다음과 같습니다.

**일반적으로 사용하는 Linux 이미지 테이블**


| PublisherName | 제안 | SKU |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| RedHat | RHEL | 7\.2 |
| credativ | Debian | 8 | 
| SUSE | openSUSE | 13\.2 |
| SUSE | SLES | 12-SP1 |
| OpenLogic | CentOS | 7\.1 |
| Canonical | UbuntuServer | 14\.04.4-LTS |
| CoreOS | CoreOS | Stable |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<!---HONumber=AcomDC_0608_2016-->