---
title: 데이터 상주
description: Azure 원격 렌더링을 사용 하는 경우 데이터 상주 설명 합니다.
author: rapete
ms.author: rapete
ms.date: 02/04/2021
ms.topic: reference
ms.openlocfilehash: f20b3bb3de877ac627f5f6909c98cb9e1553f2a9
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576895"
---
# <a name="azure-remote-rendering-data-residency"></a>Azure 원격 렌더링 데이터 상주 
이 문서에서는 데이터 상주의 개념과 Azure 원격 렌더링에 적용 되는 방식을 설명 합니다. 

## <a name="data-residency"></a>데이터 상주 
Azure 원격 렌더링은 사용자가 제공한 Azure Blob 컨테이너를 모델 저장소에 사용 합니다. 모델을 사용 하지 않는 경우 사용자가 제공한 Azure Blob Storage 지역에 유지 됩니다. 렌더링에 모델을 사용 하면 사용자가 렌더링 세션을 시작할 때 선택한 지역에 데이터가 복사 됩니다.

## <a name="next-steps"></a>다음 단계
Azure 원격 렌더링을 위해 Azure Blob Storage 컨테이너를 설정 하는 방법을 알아보려면 [모델을 렌더링 하도록 변환](../quickstarts/convert-model.md)을 확인 하세요.
