---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: ccc2b574ea054a1b0ecf32a1e59691050fb66fcf
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66166088"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>템플릿을 통해 Virtual Machine에 태그 지정
먼저 템플릿을 통한 태그 지정을 살펴보겠습니다. [이 템플릿은](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) 다음 리소스에 태그를 배치 합니다. Compute (Virtual Machine), 저장소 (저장소 계정) 및 네트워크 (공용 IP 주소, 가상 네트워크 및 네트워크 인터페이스). 이 템플릿은 Windows VM에 대한 것이지만 Linux VM에도 적용할 수 있습니다.

**템플릿 링크** 에서 [Azure에 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags)단추를 클릭합니다. 이렇게 하면 이 템플릿을 배포할 수 있는 [Azure 포털](https://portal.azure.com/) 로 이동합니다.

![태그가 포함된 간단한 배포](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

이 템플릿은 다음 태그를 포함합니다. *부서*, *응용 프로그램*, 및 *에서 만든*합니다. 다른 태그 이름을 사용하려는 경우 템플릿에서 직접 이러한 태그를 추가/편집할 수 있습니다.

![템플릿의 Azure 태그](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

여기서 볼 수 있듯이 태그는 콜론(:)으로 구분된 키/값 쌍으로 정의됩니다. 다음 형식으로 태그를 정의해야 합니다.

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

편집을 마친 후 선택한 태그와 함께 템플릿 파일을 저장합니다.

이어서 **매개 변수 편집** 섹션에서 태그의 값을 입력할 수 있습니다.

![Azure 포털에서 태그 편집](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

**만들기** 를 클릭하여 태그 값과 함께 이 템플릿을 배포합니다.

## <a name="tagging-through-the-portal"></a>포털을 통해 태그 지정
태그와 함께 리소스를 만든 후 포털에서 태그를 표시, 추가 및 삭제할 수 있습니다.

태그 아이콘을 선택하여 태그를 표시합니다.

![Azure 포털의 태그 아이콘](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

사용자 고유의 키/값 쌍을 정의하여 포털을 통해 새 태그를 추가하고 저장합니다.

![Azure 포털에서 새 태그 추가](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

이제 리소스에 대한 태그 목록에 새 태그가 나타납니다.

![Azure 포털에서 저장된 새 태그](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

