# 파이썬-selenium패키지를 사용하여 웹스크래핑 이후 이것 저것 해보기
**작업환경 : colab**


* [스크래핑하려는 사이트](https://www.airportal.go.kr/knowledge/statsnew/air/airport.jsp#)
  
* [공공데이터포털(다양한정보조회가능)](https://www.data.go.kr/index.do)

* [selenium 공식사이트(버전업데이트에 따른 코드 작동불가 가능성 존재)](https://www.selenium.dev/blog/2023/selenium-4-11-0-released/)


## 웹사이트 살펴보기

<img width="1710" alt="스크린샷 2023-09-01 오후 2 02 09" src="https://github.com/Yujaehyeokk/webscraping-using-python-selenium-/assets/100750100/1482a6a8-2190-41e9-8810-6cedd8448418">

- 시작년도, 시작 월 , 종료 년도, 종료 월을 비롯한 11개의 옵션과 1개의 검색버튼을 통해서 우리가 원하는 종류의 정보에 접근이 가능하다.



## 웹스크래핑을 위한 사전준비
```python
!pip install selenium
```
당연하게도 selenium 패키지가 필요하다.

```python
from google.colab import files
import zipfile

# 크롬 드라이버 파일 업로드
uploaded = files.upload()

# 업로드한 드라이버 파일의 이름을 정확하게 수정해주세요.
driver_zip_filename = 'chromedriver-linux64.zip'

# 드라이버 파일 압축 해제
with zipfile.ZipFile(driver_zip_filename, 'r') as zip_ref:
    zip_ref.extractall('/content/')

```
아쉽게도 내가 사용한 환경에서는 코랩 내부의 드라이버와 크롬의 버전이 호환되지 않아
위의 상기한 웹에서 최신버전의 크롬드라이버 파일을 직접 업로드하여 호환성을 해결하였다.
```python
import os

# 드라이버 파일의 경로와 이름을 정확하게 수정해주세요.
driver_path = '/content/chromedriver-linux64/chromedriver'

# 실행 권한 부여
os.chmod(driver_path, 0o755)
```
```python
from selenium import webdriver

# ChromeOptions 객체 생성
chrome_options = webdriver.ChromeOptions()

# Headless 모드 설정
chrome_options.add_argument('--headless')
chrome_options.add_argument('--no-sandbox')
chrome_options.add_argument('--disable-dev-shm-usage')

# 크롬 드라이버 실행
driver = webdriver.Chrome(options=chrome_options)

# 원하는 웹 스크래핑 작업 수행
# ...

# 브라우저 종료
#driver.quit()
```
여타 플랫폼에서는 크롬드라이버를 실행하면 새창이 나타나며 동적인 명령을 수행하면 그 창에 결과가 나타나지만
코랩에서는 그렇지 못해서 headless 모드를 설정해야 하는 것 같다.  
(그래도 가상의 창이 있다고 생각하고 그 창에서 내가 부여한 동적인 작업을 수행한다고 생각하면 될 것 같다.)
```python
# -*- coding: UTF-8 -*-
import time
from selenium.webdriver.common.by import By
```
```python
#해당 url로 이동
url = "https://www.airportal.go.kr/knowledge/statsnew/air/airport.jsp#"
driver.get(url)
```
```python
# 페이지 소스 가져오기 & title 태그 웹 사이트 제목 가져오기
html = driver.page_source
print(html.split("<title>")[1].split("</title>")[0])
```
바로 위의 코드 결과로 '항공통계'라는 결과 값을 얻었다면, 웹과 정상적으로 연결이 되었으며 이제부터 입맛대로 옵션을 조정하여 정보를 조회할 수 있다.

* 사실 위의 길이도 짧은 코드들을 얻기까지 꽤나 시간이 걸렸다. 처음에는 정적웹과 동적웹의 개념, 존재, 차이들도 몰랐기에 request 패키지와
  beautifulsoup패키지를 통해 스크래핑 하려 하였으나 
