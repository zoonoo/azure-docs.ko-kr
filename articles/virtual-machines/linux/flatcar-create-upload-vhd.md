---
title: Azure에서 사용할 Flatcar 컨테이너 Linux VHD 만들기 및 업로드
description: Flatcar Container Linux 운영 체제가 포함 된 VHD를 만들고 업로드 하는 방법에 대해 알아봅니다.
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 555e53899ed78a5200009d04659e974f8157057e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87268242"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>Azure에 미리 작성 된 Flatcar 이미지 사용

Flatcar 지원 되는 각 채널에 대해 Flatcar Container Linux의 미리 작성 된 Azure 가상 하드 디스크 이미지를 다운로드할 수 있습니다.

- [안정](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [베타](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [채널](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [테두리](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

이 이미지는 이미 완전 하 게 설정 되 고 Azure에서 실행 되도록 최적화 되었습니다. 압축을 푸는 데만 필요 합니다.

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>Azure에 대 한 사용자 고유의 Flatcar 기반 가상 머신 빌드

또는 사용자 고유의 Flatcar Container Linux 이미지를 빌드하도록 선택할 수 있습니다.

Linux 기반 컴퓨터에서 [Flatcar Container linux 개발자 SDK 가이드](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/)에 설명 된 지침을 따릅니다. 스크립트를 실행할 때를 전달 하 여 `image_to_vm.sh` `--format=azure` Azure 가상 하드 디스크를 만들어야 합니다.

## <a name="next-steps"></a>다음 단계

.Vhd 파일이 있으면 결과 파일을 사용 하 여 Azure에서 새 가상 컴퓨터를 만들 수 있습니다. .Vhd 파일을 Azure에 처음으로 업로드 하는 경우 [사용자 지정 디스크에서 LINUX VM 만들기](upload-vhd.md#option-1-upload-a-vhd)를 참조 하세요.
