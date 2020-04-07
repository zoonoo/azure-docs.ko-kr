---
title: '참조: 데이터 과학 가상 머신 이미지 사용 중단'
titleSuffix: Azure Data Science Virtual Machine
description: Azure 데이터 과학 가상 컴퓨터에 영향을 미치는 사용 중단에 대한 세부 정보
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754759"
---
# <a name="reference-deprecation-of-dsvm-images"></a>참조: DSVM 이미지 의 사용 중단

아래에서는 Azure 데이터 과학 가상 컴퓨터에서 예정된 사용 중단을 처리하기 위한 제안에 대해 설명합니다.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: 데이터 디스크 마이그레이션

2019년 12월 31일에 Windows 2012 DSVM 이미지 지원이 중단됩니다. 기존 Windows 2012 DSVM에서 Windows 2016 DSVM으로 데이터 디스크를 마이그레이션하려면 다음 단계를 수행하십시오.

1. [여기에](./provision-vm.md#create-your-dsvm)표시된 지침에 따라 새 Windows 2016 DSVM을 만듭니다.
1. [다음 지침을](../../virtual-machines/windows/detach-disk.md)사용하여 Windows 2012 이미지에서 기존 데이터 디스크를 분리합니다.
1. 다음 지침을 사용하여 이전 단계의 디스크를 Windows 2016 이미지에 [연결합니다.](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)

## <a name="centos"></a>CentOS

새로운 사용자는 가장 최근의 우분투 또는 Windows 이미지를 사용 해야 합니다. CentOS는 기존 솔루션 템플릿과 함께 사용할 수 있습니다.