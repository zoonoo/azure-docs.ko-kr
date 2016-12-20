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
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bf51a9835448339b4fb4d35bd0c97097bf767ec1


---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Azure에서 OpenSUSE Linux를 실행하는 가상 컴퓨터에 MySQL 설치
[MySQL][MySQL] 은 인기 있는 오픈 소스 SQL 데이터베이스입니다. 이 자습서에서는 OpenSUSE Linux를 실행하는 가상 컴퓨터를 만든 다음 MySQL을 설치하는 방법을 보여 줍니다.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

<br>

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>OpenSUSE Linux를 실행하는 가상 컴퓨터 만들기
[!INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>가상 컴퓨터에 MySQL 설치 및 실행
[!INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>다음 단계
MySQL에 대한 자세한 내용은 [MySQL 설명서][MySQLDocs]를 참조하세요.

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com




<!--HONumber=Nov16_HO3-->


