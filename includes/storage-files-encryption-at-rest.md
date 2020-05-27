---
title: 파일 포함
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ed061234da27716bfcdaf1cc065568908108f90a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77597839"
---
Azure Files에 저장 된 모든 데이터는 Azure SSE (storage 서비스 암호화)를 사용 하 여 미사용으로 암호화 됩니다. Storage 서비스 암호화는 Windows의 BitLocker와 유사 하 게 작동 합니다. 데이터는 파일 시스템 수준 아래에서 암호화 됩니다. 데이터는 디스크에 인코딩되어 있으므로 Azure 파일 공유의 파일 시스템 아래에서 암호화 되므로 Azure 파일 공유에 대 한 읽기 또는 쓰기를 위해 클라이언트의 기본 키에 액세스할 필요가 없습니다.

기본적으로 Azure Files에 저장 된 데이터는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. Microsoft는 Microsoft에서 관리 하는 키를 사용 하 여 데이터를 암호화/암호 해독 하는 키를 보유 하 고 있으며이를 정기적으로 회전 해야 합니다. 사용자 고유의 키를 관리 하도록 선택 하 여 회전 프로세스를 제어할 수도 있습니다. 고객 관리 키를 사용 하 여 파일 공유를 암호화 하도록 선택 하는 경우 클라이언트의 읽기 및 쓰기 요청을 충족 하기 위해 키에 액세스할 수 있는 권한이 Azure Files 됩니다. 고객이 관리 하는 키를 사용 하 여 언제 든 지이 권한 부여를 취소할 수 있지만,이는 Azure 파일 공유에 더 이상 SMB 또는 FileREST API를 통해 액세스할 수 없음을 의미 합니다.

Azure Files는 Azure Blob storage와 같은 다른 Azure storage 서비스와 동일한 암호화 체계를 사용 합니다. Azure SSE (storage 서비스 암호화)에 대 한 자세한 내용은 [미사용 데이터에 대 한 azure storage 암호화](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조 하세요.