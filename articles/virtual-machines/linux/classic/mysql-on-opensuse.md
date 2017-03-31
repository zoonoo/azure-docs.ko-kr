---
title: "OpenSUSE VM에 MySQL 설치 | Microsoft Docs"
description: "Azure에서 OpenSUSE Linux 가상 컴퓨터에 MySQL을 설치하는 방법을 알아봅니다."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/19/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 01b798a25575b66f89057315ce80d6cc0cde53b5
ms.lasthandoff: 03/27/2017


---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Azure에서 OpenSUSE Linux를 실행하는 가상 컴퓨터에 MySQL 설치
[MySQL][MySQL]은 인기 있는 오픈 소스 SQL Database입니다. 이 자습서에서는 OpenSUSE Linux를 실행하는 가상 컴퓨터를 만든 다음 MySQL을 설치하는 방법을 보여 줍니다.

> [!IMPORTANT] 
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.

<br>

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>OpenSUSE Linux를 실행하는 가상 컴퓨터 만들기
[!INCLUDE [create-and-configure-opensuse-vm-in-portal](../../../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>가상 컴퓨터에 MySQL 설치 및 실행
[!INCLUDE [install-and-run-mysql-on-opensuse-vm](../../../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>다음 단계
MySQL에 대한 자세한 내용은 [MySQL 설명서][MySQLDocs]를 참조하세요.

[MySQLDocs]:http://dev.mysql.com/doc/index-topic.html
[MySQL]:http://www.mysql.com


