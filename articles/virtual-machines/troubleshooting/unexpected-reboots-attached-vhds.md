---
title: Azure VM에서 VHD가 연결된 VM이 예기치 않게 다시 부팅되는 문제 해결 | Microsoft Docs
description: VM이 예기치 않게 다시 부팅되는 문제 해결 방법입니다.
keywords: ssh 연결 거부, ssh 오류, azure ssh, SSH 연결 실패
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 3a06db1afd130d936af868d0d20632c3ec4fbfd2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75358529"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>VHD가 연결된 VM이 예기치 않게 다시 부팅되는 문제 해결

Azure VM(Virtual Machine)에 같은 스토리지 계정의 VHD가 여러 개 연결되어 있으면 개별 스토리지 계정의 확장성 목표가 초과되어 VM이 예기치 않게 다시 부팅될 수 있습니다. 저장소 계정**TotalRequests** / 에 대 한 확장성 목표를 초과 하는 급증에 대해 저장소 계정에 대 한 분 메트릭 (TotalRequests**totalingress** / **totalingress**)을 확인 합니다. 스토리지 계정에 대해 제한이 발생했는지 확인하는 데 도움이 필요하면 [메트릭에서 PercentThrottlingError가 증가하는 것으로 표시됨](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError)을 참조하세요.

일반적으로 Virtual Machine에서 수행하는 VHD의 개별 입력 또는 출력 작업은 기본 페이지 Blob에서 **페이지 가져오기** 또는 **페이지 배치** 작업으로 변환됩니다. 따라서 환경에 대한 예상 IOPS를 사용하여 애플리케이션의 특정 동작을 기준으로 단일 스토리지 계정에 포함할 수 있는 VHD의 수를 조정할 수 있습니다. 단일 스토리지 계정에 40개 이하의 디스크를 보유하는 것이 좋습니다. Standard storage 계정의 확장성 목표에 대 한 자세한 내용은 [standard storage 계정에 대 한 확장성 목표](../../storage/common/scalability-targets-standard-account.md)를 참조 하세요. 프리미엄 페이지 blob storage 계정의 확장성 목표에 대 한 자세한 내용은 [프리미엄 페이지 blob storage 계정에 대 한 확장성 목표](../../storage/blobs/scalability-targets-premium-page-blobs.md)를 참조 하세요.

스토리지 계정의 확장성 목표가 초과되는 경우에는 VHD를 여러 스토리지 계정에 배치하여 개별 계정마다 작업량을 줄여야 합니다.
