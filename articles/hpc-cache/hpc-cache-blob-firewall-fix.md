---
title: 스토리지 방화벽 설정 해결
description: 저장소 계정 네트워크 방화벽 설정은 Azure HPC 캐시에서 Azure Blob 저장소 대상을 만들 때 오류가 발생할 수 있습니다. 이 문서에서는 소프트웨어 수정 이 제자리에 놓일 때까지 제한에 대한 해결 방법을 제공합니다.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/7/2019
ms.author: rohogue
ms.openlocfilehash: 6643662d498db8cbcffcb120a9ceabc46cfc04cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74174409"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Blob 스토리지 계정 방화벽 설정 해결

저장소 계정 방화벽에 사용되는 특정 설정으로 인해 Blob 저장소 대상 생성에 실패할 수 있습니다. Azure HPC 캐시 팀은 이 문제에 대한 소프트웨어 수정 작업을 진행 중이지만 이 문서의 지침에 따라 해결할 수 있습니다.

"선택한 네트워크"에서만 액세스할 수 있는 방화벽 설정으로 인해 캐시가 Blob 저장소 대상을 만들지 못할 수 있습니다. 이 구성은 저장소 계정의 방화벽 및 가상 네트워크 설정 페이지에 **있습니다.**

문제는 캐시 서비스가 고객 환경과 는 별개의 숨겨진 서비스 가상 네트워크를 사용한다는 것입니다. 이 네트워크가 저장소 계정에 액세스하도록 명시적으로 권한을 부여할 수는 없습니다.

Blob 저장소 대상을 만들 때 캐시 서비스는 이 네트워크를 사용하여 컨테이너가 비어 있는지 여부를 확인합니다. 방화벽에서 숨겨진 네트워크에서 액세스를 허용하지 않으면 검사가 실패하고 저장소 대상 생성이 실패합니다.

이 문제를 해결하려면 저장소 대상을 만드는 동안 방화벽 설정을 일시적으로 변경하십시오.

1. 저장소 계정 **방화벽 및 가상 네트워크** 페이지로 이동하여 모든 **네트워크에**대한 "액세스 허용" 설정을 변경합니다.
1. Azure HPC 캐시에서 Blob 저장소 대상을 만듭니다.
1. 저장소 대상이 성공적으로 생성된 후 계정의 방화벽 설정을 **선택한 네트워크로**다시 변경합니다.

Azure HPC 캐시는 서비스 가상 네트워크를 사용하여 완료된 저장소 대상에 액세스하지 않습니다.

이 해결 방법을 해결하려면 [Microsoft 서비스 및 지원 팀에 문의하십시오.](hpc-cache-support-ticket.md)
