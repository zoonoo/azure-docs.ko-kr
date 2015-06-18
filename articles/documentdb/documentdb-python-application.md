<properties 
    pageTitle="DocumentDB를 사용하여 Python 및 Flask로 웹앱 작성 | Azure" 
    description="DocumentDB를 사용하여 Azure에 호스팅되는 Python 및 Flask(MVC) 웹 응용 프로그램의 데이터를 저장하고 액세스하는 방법을 알아봅니다." 
    services="documentdb" 
    documentationCenter="python" 
    authors="crwilcox" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="03/23/2015" 
    ms.author="crwilcox"/>

# DocumentDB를 사용하여 Python 및 Flask(MVC)로 웹 응용 프로그램 빌드
------------------------------------------------------------------------

고객이 Azure DocumentDB를 효율적으로 활용하여 JSON 문서를 저장 및 쿼리할 수 있는 방법을 강조하기 위해 이 문서에서는 Azure DocumentDB를 사용하여 투표 웹 응용 프로그램을 빌드하는 방법을 보여 주는 종합적인 자습서를 제공합니다.

이 자습서에서는 Azure가 제공하는 DocumentDB 서비스를 사용하여 Azure에 호스트된 Python 웹 응용 프로그램에서 데이터를 저장하고 액세스하는 방법을 보여 주며 이전에 Python 및 Azure 웹 사이트를 사용한 경험이 있다고 가정합니다.

이 자습서에서는 다음 내용을 다룹니다.

1. DocumentDB 계정 만들기 및 프로비전
2. Python MVC 응용 프로그램 만들기
3. 웹 응용 프로그램에서 Azure DocumentDB에 연결 및 사용
4. Azure 웹 사이트에 웹 응용 프로그램 배포

이 자습서를 따르면 설문 조사에 투표할 수 있는 간단한 투표 응용 프로그램을 빌드합니다.

![Screen shot of the todo list web application created by this tutorial](./media/documentdb-python-application/image1.png)


## 필수 조건

이 문서의 지침을 따르기 전에 다음이 설치되어 있는지 확인해야 합니다.

- [Visual Studio 2013](http://www.visualstudio.com/) 이상 또는 [Visual Studio Express](무료 버전)
- Python Tools for Visual Studio([여기][]서 사용 가능).
- Azure SDK for Visual Studio 2013 버전 2.4 이상([여기][1]서 사용 가능).
- Python 2.7([여기][2]서 사용 가능).
- Microsoft Visual C++ Compiler for Python 2.7([여기][3]서 사용 가능).

## 1단계: DocumentDB 데이터베이스 계정 만들기

먼저 DocumentDB 계정을 만듭니다. 이미 계정이 있으면 [2단계: 새 Python Flask 웹 응용 프로그램 만들기]로 건너뛸 수 있습니다(#Step-2:-Create-a-new-Python-Flask-Web-Application).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

<br/>
이제 새 Python Flask 웹 응용 프로그램을 처음부터 만드는 방법을 살펴보겠습니다.

## 2단계: 새 Python Flask 웹 응용 프로그램 만들기

1. Visual Studio를 열고 **파일** -> **새 프로젝트** -> **Python** ->, **Flask Web 프로젝트**를 클릭한 다음 이름이 **tutorial**인 새 프로젝트를 만듭니다. 

	Flask를 처음 사용하는 경우 Flask는 Python에서 웹 응용 프로그램을 더 빨리 작성하는 데 도움이 되는 웹 프레임워크입니다. [Flask 자습서에 액세스하려면 여기를 클릭하세요][].

	![Screen shot of the New Project window in Vidual Studio with Python highlighted on the left, Flask Web Project selected in the middle, and the name tutorial in the Name box](./media/documentdb-python-application/image9.png)

2. 외부 패키지를 설치할지 여부를 묻습니다. **가상 환경에 설치**를 클릭합니다. PyDocumentDB에서는 현재까지 Python 3.x가 지원되지 않으므로 Python 2.7을 기본 환경으로 사용해야 합니다.  프로젝트에 필요한 Python 가상 환경이 설정됩니다.

	![Screen shot of the tutorial - Python Tools for Visual Studio window](./media/documentdb-python-application/image10.png)


## 3단계: Python Flask 웹 응용 프로그램 수정

### 프로젝트에 Flask 패키지 추가

프로젝트가 설정된 후 DocumentDB용 python 패키지인 pydocumentdb를 포함해서 프로젝트에 필요한 특정 flask 패키지를 추가해야 합니다. 

1. **requirements.txt** 파일을 열고 내용을 다음으로 바꿉니다.

    	flask==0.9
    	flask-mail==0.7.6
    	sqlalchemy==0.7.9
    	flask-sqlalchemy==0.16
    	sqlalchemy-migrate==0.7.2
    	flask-whooshalchemy==0.55a
    	flask-wtf==0.8.4
    	pytz==2013b
    	flask-babel==0.8
    	flup
    	pydocumentdb>=0.9.4-preview

2. **env**를 마우스 오른쪽 단추로 클릭하고 **requirements.txt에서 설치**를 클릭합니다.

	![Screen shot showing env (Python 2.7) selected with Install from requirements.txt highlighted in the list](./media/documentdb-python-application/image11.png)

> [AZURE.NOTE] 출력 창에 실패가 표시되는 경우가 드물게 발생합니다. 이 경우 오류가 정리와 관련이 있는지 확인하세요. 정리에 실패하지만 설치는 성공하는 경우가 있습니다(이를 확인하려면 출력 창에서 위로 스크롤). 이 경우 계속해도 됩니다.

### <a name="verify-the-virtual-environment"></a>가상 환경 확인

모두 올바르게 설치되었는지 확인해 봅시다. 

- **F5**를 눌러서 웹 사이트를 시작합니다. 그러면 flask 개발 서버가 실행되고 웹 브라우저가 시작됩니다. 다음 페이지를 참조해야 합니다.

	![The empty flask project displayed in a browser](./media/documentdb-python-application/image12.png)

### 데이터베이스, 컬렉션 및 문서 정의 만들기

이제 투표 응용 프로그램을 만들겠습니다.

- 솔루션 탐색기의 **tutorial** 폴더에서 마우스 오른쪽 단추로 클릭하여 Python 파일을 추가합니다.  파일 이름을 **forms.py**로 지정합니다.  

    	from flask.ext.wtf import Form
    	from wtforms import RadioField

    	class VoteForm(Form):
        	deploy_preference  = RadioField('Deployment Preference', choices=[
            	('Web Site', 'Web Site'),
            	('Cloud Service', 'Cloud Service'),
            	('Virtual Machine', 'Virtual Machine')], default='Web Site')

### 필요한 가져오기를 views.py에 추가합니다.

- 다음 가져오기 문을 **views.py** 맨 위에 추가합니다. 이렇게 하면 DocumentDB의 PythonSDK 및 Flask 패키지를 가져옵니다.

    	from forms import VoteForm
    	import config
    	import pydocumentdb.document_client as document_client
    

### 데이터베이스, 컬렉션 및 문서 만들기

- 다음 코드를 **views.py**에 추가합니다. 이 코드는  폼에서 사용되는 데이터베이스를 만듭니다. **views.py**의 기존 코드를  삭제하지 마세요. 단순히 끝 부분에 추가합니다.

    	@app.route('/create')
    	def create():
        	"""Renders the contact page."""
        	client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})
    
        	# Attempt to delete the database.  This allows this to be used to recreate as well as create
        	try:
            	db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
            	client.DeleteDatabase(db['_self'])
        	except:
            	pass
    
       		# Create database
        	db = client.CreateDatabase({ 'id': config.DOCUMENTDB_DATABASE })
        	# Create collection
        	collection = client.CreateCollection(db['_self'],{ 'id': config.DOCUMENTDB_COLLECTION })
        	# Create document
        	document = client.CreateDocument(collection['_self'],
            	{ 'id': config.DOCUMENTDB_DOCUMENT,
            	'Web Site': 0,
            	'Cloud Service': 0,
            	'Virtual Machine': 0,
            	'name': config.DOCUMENTDB_DOCUMENT })
    
        	return render_template(
            	'create.html', 
            	title='Create Page', 
            	year=datetime.now().year,
            	message='You just created a new database, collection, and document.  Your old votes have been deleted')

### 데이터베이스, 컬렉션 및 문서를 읽고 폼 제출

- 다음 코드를 **views.py**에 추가합니다. 이 코드는 데이터베이스, 컬렉션 및 문서를 읽고 폼을 설정합니다. **views.py**의 기존 코드를 삭제하지 마세요. 단순히 끝 부분에 추가합니다.
    
    	@app.route('/vote', methods=['GET', 'POST'])
    	def vote(): 
        	form = VoteForm()
        	replaced_document ={}
        	if form.validate_on_submit(): # is user submitted vote  
            	client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})
            
            	# Read databases and take first since id should not be duplicated.
            	db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
            
            	# Read collections and take first since id should not be duplicated.
            	coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config.DOCUMENTDB_COLLECTION))
    
            	# Read documents and take first since id should not be duplicated.
            	doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config.DOCUMENTDB_DOCUMENT))
    
            	# Take the data from the deploy_preference and increment our database
            	doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
            	replaced_document = client.ReplaceDocument(doc['_self'], doc)
    
            	# Create a model to pass to results.html
            	class VoteObject:
                	choices = dict()
                	total_votes = 0
    
            	vote_object = VoteObject()
            	vote_object.choices = {
                	"Web Site" : doc['Web Site'],
                	"Cloud Service" : doc['Cloud Service'],
                	"Virtual Machine" : doc['Virtual Machine']
            	}
            	vote_object.total_votes = sum(vote_object.choices.values())
    
            	return render_template(
                	'results.html', 
                	year=datetime.now().year, 
                	vote_object = vote_object)
    
        	else :
            	return render_template(
                	'vote.html', 
                	title = 'Vote',
                	year=datetime.now().year,
                	form = form)


### html 파일 만들기

templates 폴더 아래에 다음 html 파일을 추가합니다. create.html, results.html, vote.html.

1. **create.html**에 다음 코드를 추가합니다. 이 코드는 새 데이터베이스, 컬렉션 및 문서를 만들었다는 메시지 표시를 처리합니다.

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>{{ title }}.</h2>
    	<h3>{{ message }}</h3>
    	<p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    	{% endblock %}

2. **results.html**에 다음 코드를 추가합니다. 이 코드는 설문 조사 결과를 표시합니다.

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>Results of the vote</h2>
   	 	<br />
	
    	{% for choice in vote_object.choices %}
    	<div class="row">
        	<div class="col-sm-5">{{choice}}</div>
        	<div class="col-sm-5">
            	<div class="progress">
                	<div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" 
                     aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                    	{{vote_object.choices[choice]}}
                	</div>
            	</div>
        	</div>
    	</div>
    	{% endfor %}

    	<br />
    	<a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    	{% endblock %}

3. **vote.html**에 다음 코드를 추가합니다. 이 코드는 설문 조사 표시 및 투표 수락을 처리합니다. 투표를 등록하면 제어가 views.py로 전달되며, 여기서 투표 완료를 인식하고 그에 따라 문서를 추가합니다.

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>What is your favorite way to host an application on Azure?</h2>
    	<form action="" method="post" name="vote">
        	{{form.hidden_tag()}}
        	{{form.deploy_preference}}
        	<button class="btn btn-primary" type="submit">Vote</button>
    	</form>
    	{% endblock %}

4. **index.html**의 내용을 다음과 같이 바꿉니다. 이 코드는 응용 프로그램의 방문 페이지 역할을 합니다.

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>Python + DocumentDB Voting Application.</h2>
    	<h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
    	<p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    	<p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    	{% endblock %}


### 구성 파일 추가 및 __init__.py 변경

1. 프로젝트 이름 tutorial을 마우스 오른쪽 단추로 클릭하고 **config.py** 파일을 추가합니다.
이 구성은 Flask의 폼에 필요합니다. 이 파일을 사용하여 비밀 키를 제공할 수도 있습니다. 하지만 이 자습서에서는 이 키가 필요하지 않습니다. 

2. config.py에 다음 코드를 추가합니다. **DOCUMENTDB_HOST** 및 **DOCUMENTDB_KEY** 값을 수정합니다.

    	CSRF_ENABLED = True
    	SECRET_KEY = 'you-will-never-guess'
    
    	DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
    	DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
    
    	DOCUMENTDB_DATABASE = 'voting database'
    	DOCUMENTDB_COLLECTION = 'voting collection'
    	DOCUMENTDB_DOCUMENT = 'voting document'

3. 마찬가지로 **__init__.py**의 내용을 다음과 같이 바꿉니다.

    	from flask import Flask
    	app = Flask(__name__)
    	app.config.from_object('config')
    	import tutorial.views

4. 위에서 언급한 단계를 따르면 솔루션 탐색기가 다음과 같이 표시됩니다.

	![Screen shot of the Visual Studio Solution Explorer window](./media/documentdb-python-application/image15.png)


## 4단계: 로컬에서 응용 프로그램 실행

1. Visual Studio에서 F5 키 또는 실행 단추를 누르면 화면에 다음이 표시됩니다.

	![Screen shot of the Python + DocumentDB Voting Application displayed in a web browser](./media/documentdb-python-application/image16.png)

2. **투표 데이터베이스 만들기/지우기**를 클릭하여 데이터베이스를 생성합니다.

	![Screen shot of the Create Page of the web application](./media/documentdb-python-application/image17.png)

3. 그런 다음 **투표**를 클릭하고 옵션을 선택합니다.

	![Screen shot of the web application with a voting question posed](./media/documentdb-python-application/image18.png)

4. 투표할 때마다 해당 카운터가 증가합니다.

	![Screen shot of the Results of the vote page shown](./media/documentdb-python-application/image19.png)


## 5단계: Azure 웹 사이트에 응용 프로그램 배포

이제 완료된 응용 프로그램이 DocumentDB에 대해 올바르게 작동하므로 Azure 웹 사이트에 배포하겠습니다. 

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고(로컬에서 실행하고 있지 않은지 확인) 게시를 선택합니다.  그런 후 Microsoft Azure 웹 사이트를 선택합니다.

 	![Screen shot of the tutorial selected in Solution Explorer, with the Publish option highlighted](./media/documentdb-python-application/image20.png)

2. 자격 증명을 제공하여 Azure 웹 사이트를 만들고 **게시**를 클릭합니다.

	![Screen shot of the Publish Web window](./media/documentdb-python-application/image21.png)

3. 몇 초 후에 Visual Studio에서 웹 응용 프로그램 게시를 완료하고 브라우저를 시작하며, Azure에서 실행되는 작업 내용을 확인할 수 있습니다.

## 다음 단계

축하합니다. 지금까지 Azure DocumentDB를 사용하여 첫 Python 응용 프로그램을 빌드하고 Azure 웹 사이트에 게시했습니다.

응용 프로그램에 다른 기능을 추가하려면 [DocumentDB Python SDK](https://pypi.python.org/pypi/pydocumentdb)에서 사용할 수 있는 API를 검토하세요.

  [Flask 자습서에 액세스하려면 여기를 클릭하세요.]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world
  [Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
  [여기]: http://aka.ms/ptvs
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [2]: https://www.python.org/downloads/windows/
  [3]: http://aka.ms/vcpython27 
  [Microsoft 웹 플랫폼 설치 관리자]: http://www.microsoft.com/web/downloads/platform.aspx
  [Azure 관리 포털]: http://portal.azure.com

<!--HONumber=49--> 