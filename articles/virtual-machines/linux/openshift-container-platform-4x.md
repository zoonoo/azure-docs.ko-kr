---
title: Azure에서 OpenShift 컨테이너 플랫폼 4.x 배포
description: Azure에서 OpenShift 컨테이너 플랫폼 4.x를 배포합니다.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 14af110b5cf50f167d0c4961e26454bc33c6ed7d
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759482"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Azure에서 OpenShift 컨테이너 플랫폼 4.x 배포

이제 설치 관리자 프로비저닝된 인프라(IPI) 모델을 통해 Azure에서 OPENShift 컨테이너 플랫폼(OCP) 4.2 배포를 지원합니다.  OpenShift 4를 시도하기 위한 방문 페이지는 [try.openshift.com.](https://try.openshift.com/) Azure에 OCP 4.2를 설치하려면 [Red Hat OpenShift 클러스터 관리자](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) 페이지를 방문하십시오.  이 사이트에 액세스하려면 Red Hat 자격 증명이 필요합니다.


## <a name="notes"></a>메모 

 - Azure에서 OCP 4.x를 설치하고 실행하려면 Azure AAD(AAD) 서비스 보안 주체(SP)가 필요합니다.
     - SP는 응용 프로그램의 API 권한을 부여해야 **합니다.ReadWrite.OwnedBy** Azure 활성 디렉터리 그래프
     - AAD 테넌트 관리자는 이 API 권한을 적용하려면 관리자 동의를 부여해야 합니다.
     - SP는 구독에 **기여자** 및 **사용자 액세스 관리자** 역할을 부여해야 합니다.
 - OCP 4.x의 설치 모델은 3.x와 다르며 Azure에서 OCP 4.x를 배포하는 데 사용할 수 있는 Azure 리소스 관리자 템플릿이 없습니다.
 - 설치 과정에서 문제가 발생하면 해당 회사(Microsoft 또는 Red Hat)에 문의하십시오.

| 문제 설명 | 접점 |
|-------------------|---------------|
| Azure 특정 문제(AAD, SP, Azure 구독 등)                              | Microsoft |
| OpenShift 관련 문제(설치 오류/오류, Red Hat 구독 등) |  Red Hat  |




## <a name="next-steps"></a>다음 단계

- [OpenShift Container Platform 시작](https://docs.openshift.com)
