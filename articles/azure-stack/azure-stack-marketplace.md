---
title: Azure Stack (클라우드 운영자)에서 사용자 지정 마켓플레이스 항목 게시 | Microsoft Docs
description: Azure Stack 운영자로 Azure Stack에서 사용자 지정 마켓플레이스 항목을 게시 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: df1d263b321361c00ceb37c84858dd2c62033228
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245352"
---
# <a name="azure-stack-marketplace-overview"></a>Azure Stack Marketplace 개요

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack Marketplace는 서비스, 응용 프로그램 및 Azure Stack에 대 한 사용자 지정 리소스의 컬렉션입니다. 리소스에는 네트워크, virtual machines, storage 및 더이 포함 됩니다. Marketplace를 사용 하 여 새 리소스를 만들고 새 응용 프로그램 배포 또는 검색 하 고 사용 하려는 항목을 선택 합니다. 마켓플레이스 항목을 사용 하려면 사용자는 항목에 대 한 액세스를 부여 하는 제품을 구독 해야 합니다.

Azure Stack 운영자로 항목을 추가 하려는 marketplace (게시) 합니다. 데이터베이스, App Services와 같은 항목을 게시 하 고 등 수 있습니다. 게시 되도록 모든 사용자에 게 표시 합니다. 사용자가 만든 사용자 지정 항목을 게시할 수 있습니다 하거나 증가 하는 항목을 게시할 수 있습니다 [Azure Marketplace 항목 목록을](azure-stack-marketplace-azure-items.md)합니다. Marketplace에 항목을 게시할 때 사용자가 5 분 내에 볼 수 있습니다.

> [!CAUTION]  
> 이미지 및 JSON 파일을 포함 하 여 모든 갤러리 항목 아티팩트를 Azure Stack Marketplace에서 사용할 수 있도록 한 후 인증 없이 액세스할 수 있습니다. 자세한 게시 시 고려 사항 사용자 지정 마켓플레이스 항목을 참조 하세요 [만들기 및 마켓플레이스 항목 게시](azure-stack-create-and-publish-marketplace-item.md)합니다.

관리자 포털에서 Marketplace를 열려면 **+ 리소스 만들기**합니다.

![Marketplace](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Marketplace 항목

Azure Stack Marketplace 항목 서비스, 응용 프로그램 또는 사용자가 다운로드 하 고 사용할 수 있는 리소스입니다. 모든 Azure Stack Marketplace 항목 계획 및 제품과 같은 관리 항목을 포함 하 여 모든 사용자에 게 표시 됩니다. 이러한 항목에는 사용자에 게 작동 하지 않는 되지만 보기에 대 한 구독이 필요 하지 않습니다.

모든 Marketplace 항목은 다음이 있습니다.

* 리소스 프로 비전에 Azure Resource Manager 템플릿입니다.
* 같은 메타 데이터를 문자열, 아이콘 및 기타 마케팅 참고 자료.
* 포털에서 항목을 표시 하려면 형식 지정 정보입니다.

Marketplace에 게시 된 모든 항목에는 Azure 갤러리 패키지 (.azpkg) 형식을 사용 합니다. 마켓플레이스 항목의 일부가 아니라 Azure Stack에 배포 또는 런타임 리소스 (코드, 소프트웨어 또는 가상 머신 이미지를 사용 하 여 zip 파일)를 개별적으로 추가 합니다.

버전 1803 이상에서 사용 하 여 Azure Stack에서 Azure 또는 사용자 지정 이미지를 업로드 하는 경우 다운로드할 때 스파스 파일에 이미지를 변환 합니다. 이 프로세스는 시간이 추가 이미지를 추가할 때 하지만 공간을 절약 하며 해당 이미지 배포 속도가 향상 됩니다. 변환이 새 이미지에만 적용 됩니다. 기존 이미지 변경 되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Marketplace 항목 다운로드](azure-stack-download-azure-marketplace-item.md)  
* [Marketplace 항목 만들기 및 게시](azure-stack-create-and-publish-marketplace-item.md)
