<properties
   pageTitle="Windows VM 이미지 탐색 및 선택 | Microsoft Azure"
   description="리소스 관리자 배포 모델로 Windows 가상 컴퓨터를 만들 경우 이미지의 게시자, 제품 및 SKU를 확인하는 방법에 대해 알아보세요."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="12/08/2015"
   ms.author="rasquill"/>

# Powershell 또는 CLI로 Azure의 Windows 가상 컴퓨터 이미지 이동 및 선택

이 항목은 배포할 수 있는 각 위치에 대한 게시자, 제안, SKU 및 버전을 찾는 방법을 설명합니다. 예를 들면 일부 자주 사용되는 Windows VM 이미지는 다음과 같습니다.

## 일반적으로 사용하는 Windows 이미지 테이블


| PublisherName | 제안 | SKU |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| CoreOS | CoreOS | 베타 |
| CoreOS | CoreOS | Stable |
| MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
| MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
| Microsoft | Oracle-Database-12c-Weblogic-Server-12c | Standard |
| Microsoft | Oracle-Database-12c-Weblogic-Server-12c | Enterprise |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-DW |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-OLTP |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<!---HONumber=AcomDC_0601_2016-->