---
title: "requests와 BeautifulSoup"
category:
  - python
tag:
  - python
  - requests
  - BeautifulSoup
toc: true
toc_sticky: true
---

이 포스트는 python에서 크롤링할 때 유용한 모듈 사용법을 정리한 글이다.

각 모듈의 용도

|requests 모듈|다양한 HTTP 요청(Request)<br>응답(Response) 메시지 분석<br>세션 연결|
|bs4 모듈|HTTP 응답(Response)의 HTML 파서|

# 참고 사이트

- requests 문서 : https://requests.readthedocs.io/en/master/user/quickstart/
- requests 연습용 사이트 : https://httpbin.org
- bs4 문서 : https://www.crummy.com/software/BeautifulSoup/bs4/doc/

# reqeusts

## 기본적인 요청(Request)

```python
import requests

# get 방식
r = requests.get('https://byeonggwonchae.github.io/?key=value')
r = requests.get('https://byeonggwonchae.github.io/', params={'key':'value'})

# post 방식
r = requests.post('https://byeonggwonchae.github.io/', data={'key':'value'})
r1 = requests.post('https://byeonggwonchae.github.io/', data=[('key1', 'value1'), ('key1', 'value2')])
r2 = requests.post('https://byeonggwonchae.github.io/', data={'key1': ['value1', 'value2']})
```

## 응답(Response) 메시지

요청(Request) 후에 전달받은 응답(Response)의 내용을 확인할 수 있는 필드들이다.

```python
# 내용 확인할 때, 주로 사용하는 필드들
heml = r.text           # HTML 소스 가져오기
header = r.headers      # HTTP Header 가져오기
status = r.status_code  # HTTP Status 가져오기 (200: 정상)
is_ok = r.ok            # HTTP가 정상적으로 되었는지 (True/False)
encoding = r.encoding   # encoding 방식

print(dir(r))           # 모든 필드 확인
```

Reponse가 일반적인 HTML 형식이 아닐 경우는 아래와 같은 방법들을 사용한다.

```python
## Reponse가 바이너리 데이터일 경우
from PIL import Image
from io import BytesIO
i = Image.open(BytesIO(r.content))

## Reponse가 JSON 데이터일 경우
r.json()

## Reponse가 raw 데이터일 경우
r = requests.get('https://api.github.com/events', stream=True)
r.raw
r.raw.read(10)
#보통은 아래와 같이 사용하다.
with open(filename, 'wb') as fd:
    for chunk in r.iter_content(chunk_size=128):
        fd.write(chunk)
#Response.iter_content는 gzip과 deflate로 자동으로 디코딩해주는데, Response.raw는 그냥 bytes의 스트림이다.
```

## 헤더 수정

```python
# 요청 header 수정
r=request.get('https://byeonggwonchae.github.io/?key=value', header={'user-agent': 'my-app/0.0.1'})

# 응답 header 확인
r.request.headers # Request 헤더
r.headers         # Response 헤더
```

## 쿠키 수정

```python
# 쿠키 생성/수정
r = requests.get('https://httpbin.org/cookies', cookies=dict(cookies_are='working'))

# 쿠키 확인
r.cookies
```

## 세션 연결

```python
with requests.Session() as s:
    s.get('https://httpbin.org/cookies/set/sessioncookie/123456789')
```

Session 객체는 requests api의 모든 메소드를 가지고 있어서 특별한 사용법은 없다.



# BeautifulSoup

## 기본적인 사용법

```python
## 가장 기본
import requests
from bs4 import BeautifulSoup

r = requests.get('https://byeonggwonchae.github.io/')
soup = BeautifulSoup(req.text, 'html.parser')  # html을 파싱
print(soup.prettify())  # 알기 쉽게 보여줌
```

`soup.prettify()` 는 출력용이라서, 검색, 수정 등을 한 후 마지막에 사용해줘야 한다.

## 트리 탐색

`soup = BeautifulSoup(req.text, 'html.parser')` 로 만들어진 soup는<br>
jquery처럼 하위 요소들을 선택할 수 있다.

### 자손 선택

선택한 태그의 바로 밑의 자손들을 선택한다.

- .contents는 자손들을 list로 만들어주고
- .children은 자손들을 generator로 만들어준다.

```python
for i in range(len(soup.html.contents)):
    print(soup.html.contents[i].name)

for child in soup.html.children:
    print(child.name)

print(soup.html.contents)
print(soup.html.children)
```

### 후손 선택 (모든 하위 요소)

바로 밑의 자손뿐만 아니라 모든 하위 요소(후손)를 선택한다.

```python
print(len(list(soup.html.children)))
print(len(list(soup.html.descendants)))
```

### 부모 선택

- .parent : 바로 위의 부모 선택
- .parents : 모든 부모 선택

```python
for parent in soup.html.body.div.parents:
    if parent is None:
        print(parent)
    else:
        print(parent.name)
```

### 형제 선택

형제 요소를 선택한다.

- .next_sibling 와 .previous_sibling
- .next_siblings 와 .previous_siblings

```python
print(soup.title.next_sibling)

for sibling in soup.title.next_siblings:
    print(repr(sibling))
```

### 형제 선택 (형제의 자손까지 검색)

- .next_element and .previous_element
- .next_elements and .previous_elements

.next_sibling와 비슷하지만 .next_element는 태그의 자손 태그로 넘어갔다가 자손 태그가 없으면 형제 태그로 넘어간다.
마치 .children과 .descent의 관계와 비슷하다.

```python
for element in last_a_tag.next_elements:
    print(repr(element))
```

### 텍스트 추출

- .string

선택한 태그의 텍스트를 추출한다.

선택한 태그가 2개 이상의 텍스트를 포함한다면 None을 출력한다.

```python
print(soup.title.string)
```

- .strings와 stripped_strings

둘 다 2개 이상의 텍스트를 추출할 때 사용한다.

stripped_strings는 문자열의 맨앞/맨뒤의 공백을 없앤다.

```python
for string in soup.strings:
    print(repr(string))
```

## Method로 검색 

트리 탐색만으로 원하는 부분을 얻기가 힘들 경우, 검색 method들을 사용하는 편이 간단하다.

대부분 find()와 find_all() 을 사용한다.

### find_all()

```python
find_all(name, attrs, recursive, string, limit, **kwargs)
```

1) 태그명으로 검색

```python
soup.find_all("title")
```

2) 속성으로 검색<br>
id, name, class, 기타 등등 검색이 가능하다.

```python
soup.find_all(attrs={"name": "email"})
soup.find_all(attrs={"id":True})        # id속성을 가진 모든 태그를 검색
soup.find_all("a", attrs={"class": "sister"})
```

3) 텍스트로 검색

```python
soup.find_all(string="Elsie")
soup.find_all(string=["Tillie", "Elsie", "Lacie"])
soup.find_all(string=re.compile("Dormouse"))
```

4) 기타

```python
# limit 파라미터는 검색 결과가 아무리 많아도, 정해진 수만큼만 출력한다.
soup.find_all("a", limit=2)

# recursive 파라미터는 자손만 검색할지, 자손의 자손들 까지 검색할지 정한다. (default는 True)
soup.html.find_all("title")                   # [<title>타이틀</title>]
soup.html.find_all("title", recursive=False)  # []

# find_all()은 다음과 같이 사용할 수도 있다. 다음 두 명령어는 동일하다.
soup.find_all("a")  #모든 a태그 검색
soup("a")           #모든 a태그 검색
```

### 그외 명령어들

1. find()

기본적으로 find_all()과 같지만 오직 하나만 검색한다.

```python
# 아래의 두 명령어의 결과는 동일하다.
soup.find_all('a', limit=1)
soup.find('a')
```

2. 기타 method들

다음 10개의 method들은 findall()과 비슷하게 사용가능하므로, 따로 살펴보진 않았다.

|find_parents() 와 find_parent()|
|find_next_siblings() 와 find_next_sibling()|
|find_previous_siblings() 와 find_previous_sibling()|
|find_all_next() 와 find_next()|
|find_all_previous() 와 find_previous()|

3. select()

역할은 find_all() 와 비슷하지만, CSS 선택자처럼 검색할 수 있다.

```python
soup.select("head > title")
```

## 변경

1. 태그명, 속성 변경

```python
soup = BeautifulSoup('<b class="boldest">Extremely bold</b>')
tag = soup.b

tag.name = "blockquote"    # 태그명 변경
tag['id'] = 1              # 속성 추가
tag['class'] = 'verybold'  # 속성 변경

tag.string = "New link text." # 텍스트 변경

del tag['class']  # 속성 삭제
del tag['id']
```

2. 기타 명령어들 

|append()|선택한 태그 내부의 끝부분에 내용 추가|
|extend()|append()와 용도는 같지만, python의 list처럼 사용 가능|
|insert()|append()와 비슷하지만, 텍스트가 여러 개일 경우 추가할 위치를 선택할 수 있다.|
|insert_before() and insert_after()|태그or텍스트를 선택한 태그의 처음/끝 을 선택해서 추가할 수 있다.<br>(insert_after()는 appned()와 비슷하다)|

|new_tag()|새로운 태그 추가|
|clear()|태그안의 모든 내용 삭제|

|extract()|선택한 태그를 트리에서 삭제하고, 그 내용을 리턴한다.|
|decompose()|extract()처럼 태그를 트리에서 삭제하지만, 그 내용은 완전히 사라진다.|
|replace_with()|태그나 문자열을 트리에서 삭제하고, 그 자리를 다른 태그/문자열로 채운다.|

|wrap()|선택한 태그의 앞뒤를 새로운 태그로 감싼다.|
|unwrap()|wrap()의 반대. 선택한 태그를 삭제하고 내용만 남긴다.|
|smooth()|트리를 여러 번 변조하다보면 문자열 객체(NavigableString)가 2번 이상 붙여질 수 있는데,<br>이 객체들을 하나의 NavigableString 객체로 만들어준다.|

3. new_tag() 예제

```python
soup = BeautifulSoup("<b></b>")
original_tag = soup.b

new_tag = soup.new_tag("a", href="http://www.example.com")
original_tag.append(new_tag)
```
