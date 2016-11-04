---
title: VM 크기 집합이란? | Microsoft Docs
description: VM 크기 집합에 대해 알아봅니다.
keywords: linux 가상 컴퓨터, 가상 컴퓨터 크기 집합
services: virtual-machines-linux
documentationcenter: ''
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-machine-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/24/2016
ms.author: gatneil

---
# 가상 컴퓨터 크기 집합이란?
가상 컴퓨터 크기 집합을 사용하여 여러 VM을 집합으로 관리할 수 있습니다. 개략적으로 크기 집합의 장점과 단점은 다음과 같습니다.

장점:

1. 고가용성. 각 크기 집합은 가용성을 보장하기 위해 5개의 FD(장애 도메인)와 5개의 UD(업데이트 도메인)가 있는 가용성 집합에 VM을 배치합니다(FD와 UD에 대한 자세한 내용은 [VM 가용성](virtual-machines-linux-manage-availability.md) 참조). 
2. Azure 부하 분산 장치 및 앱 게이트웨이와의 간편한 통합.
3. Azure 자동 크기 조정과의 간편한 통합.
4. VM 배포, 관리 및 정리 간소화.
5. 일반적인 Windows 및 Linux 버전과 사용자 지정 이미지 지원.

단점:

1. 크기 집합의 VM 인스턴스에 데이터 디스크를 연결할 수 없습니다. 대신 Blob 저장소, Azure 파일, Azure 테이블 또는 다른 저장소 솔루션을 사용해야 합니다.

## Azure CLI를 사용한 빠른 생성
[!INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## 다음 단계
일반적인 정보는 [크기 집합에 대한 주 방문 페이지](https://azure.microsoft.com/services/virtual-machine-scale-sets/)를 확인하세요.

추가 설명서는 [크기 집합에 대한 주 설명서 페이지](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)를 참조하세요.

크기 집합을 사용하는 예제 리소스 관리자 템플릿은 [Azure 빠른 시작 템플릿 github 리포지토리](https://github.com/Azure/azure-quickstart-templates)에서 "vmss"를 검색하세요.

<!---HONumber=AcomDC_0413_2016-->