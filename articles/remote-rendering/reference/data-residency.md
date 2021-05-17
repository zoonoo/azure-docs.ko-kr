---
title: 데이터 상주
description: Azure Remote Rendering 사용 시 데이터 상주를 설명합니다.
author: rapete
ms.author: rapete
ms.date: 02/04/2021
ms.topic: reference
ms.openlocfilehash: f20b3bb3de877ac627f5f6909c98cb9e1553f2a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99576895"
---
# <a name="azure-remote-rendering-data-residency"></a>Azure Remote Rendering 데이터 상주 
이 문서에서는 데이터 상주 개념과 Azure Remote Rendering에 적용되는 방식을 설명합니다. 

## <a name="data-residency"></a>데이터 상주 
Azure Remote Rendering은 사용자가 제공한 Azure Blob 컨테이너를 모델 스토리지용으로 사용합니다. 모델을 사용하지 않는 경우 사용자가 제공한 Azure Blob Storage 지역에 유지됩니다. 모델이 렌더링에 사용되면 사용자가 렌더링 세션을 시작할 때 선택한 지역에 데이터가 복사됩니다.

## <a name="next-steps"></a>다음 단계
Azure Remote Rendering을 위해 Azure Blob Storage 컨테이너를 설정하는 방법을 알아보려면 [렌더링할 모델 변환](../quickstarts/convert-model.md)을 확인하세요.
