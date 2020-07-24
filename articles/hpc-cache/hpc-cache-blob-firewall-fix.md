---
title: 저장소 방화벽 설정 문제 해결
description: 저장소 계정 네트워크 방화벽 설정은 azure HPC 캐시에서 Azure Blob 저장소 대상을 만들 때 오류가 발생할 수 있습니다. 이 문서에서는 소프트웨어 수정이 준비 될 때까지 제한 사항에 대 한 해결 방법을 제공 합니다.
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.author: v-erkel
ms.openlocfilehash: 6916c79e9110a88beff65d487fac72441382c2f4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092373"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Blob 스토리지 계정 방화벽 설정 해결

저장소 계정 방화벽에서 사용 되는 특정 설정을 사용 하면 Blob 저장소 대상 만들기가 실패할 수 있습니다. Azure HPC 캐시 팀은이 문제에 대 한 소프트웨어 수정 작업을 수행 하 고 있지만이 문서의 지침에 따라 해결할 수 있습니다.

"선택한 네트워크" 에서만 액세스할 수 있도록 하는 방화벽 설정으로 인해 캐시에서 Blob 저장소 대상을 만들지 못할 수 있습니다. 이 구성은 저장소 계정의 **방화벽 및 가상 네트워크** 설정 페이지에 있습니다.

문제는 캐시 서비스가 고객 환경과는 별개의 숨겨진 서비스 가상 네트워크를 사용 하는 것입니다. 저장소 계정에 액세스 하도록이 네트워크에 명시적으로 권한을 부여할 수 없습니다.

Blob 저장소 대상을 만들 때 캐시 서비스는이 네트워크를 사용 하 여 컨테이너가 비어 있는지 여부를 확인 합니다. 방화벽이 숨겨진 네트워크에서의 액세스를 허용 하지 않는 경우 확인이 실패 하 고 저장소 대상 만들기가 실패 합니다.

문제를 해결 하려면 저장소 대상을 만드는 동안 일시적으로 방화벽 설정을 변경 합니다.

1. 저장소 계정 **방화벽 및 가상 네트워크** 페이지로 이동 하 여 "액세스 허용" 설정을 **모든 네트워크**로 변경 합니다.
1. Azure HPC 캐시에서 Blob 저장소 대상을 만듭니다.
1. 저장소 대상이 성공적으로 생성 된 후에는 계정의 방화벽 설정을 **선택한 네트워크**로 다시 변경 합니다.

Azure HPC 캐시는 서비스 가상 네트워크를 사용 하 여 완성 된 저장소 대상에 액세스 하지 않습니다.

이 해결 방법에 대 한 도움말을 보려면 [Microsoft 서비스 및 지원에 문의 하세요](hpc-cache-support-ticket.md).
