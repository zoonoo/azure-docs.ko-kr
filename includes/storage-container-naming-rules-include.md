Azure 저장소의 모든 Blob은 컨테이너에 있어야 합니다. 컨테이너는 Blob 이름의 일부를 형성합니다. 예를 들어 `mycontainer`은(는) 이러한 샘플 Blob URI에서 컨테이너의 이름입니다.

	https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
	https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg
 
> [AZURE.IMPORTANT]컨테이너의 이름은 항상 소문자여야 합니다. 컨테이너 이름에 대문자를 포함하거나 컨테이너 명명 규칙을 위반하는 경우에 400 오류(잘못된 요청) 메시지를 받을 수 있습니다. 컨테이너 명명 규칙에 대해서는 [컨테이너와 Blob, 메타데이터의 명명 및 참조](https://msdn.microsoft.com/library/azure/dd135715.aspx)를 참조하세요.

<!---HONumber=July15_HO3-->