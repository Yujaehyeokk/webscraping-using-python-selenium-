# 파이썬-selenium패키지를 사용하여 웹스크래핑 이후 이것 저것 해보기
**작업환경 : colab**

* 웹스크래핑 , 웹 크롤링을 하기전 이러한 작업에도 윤리(?) 비스무리한 것이 있다고 한다.
  * [robots.txt](https://namu.wiki/w/robots.txt)에 관련된 내용으로 기억이 나지 않을 때마다 꼭 구글에 검색해보자

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

* *사실 위의 길이도 짧은 코드들을 얻기까지 꽤나 시간이 걸렸다. 처음에는 정적웹과 동적웹의 개념, 존재, 차이들도 몰랐기에 request 패키지와
  beautifulsoup패키지를 통해 스크래핑 하려 하였으나 request로 불러온 웹사이트의 html 내용에는 필요한 부분의 데이터의 주소에 해당하는 html부분이 모두 빠져 있었고 구글링을 통해 동적웹이었다는 것을 깨달았다... 이후 selenium을 활용한 방향으로 전환하였고...*  
  _(구글링하며 보긴했지만 request와 beautifulsoup를 활용해서도 동적웹을 스크래핑할 수 있다고한다...(?) 추후 기회가 된다면...)_

## 본격적인 스크래핑

```python
#공항
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C1')
print('공항')
for i in search:
  print(i.text)

#화물
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C5')
print('화믈')

for i in search:
  print(i.text)

#여객
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C4')
print('여객')

for i in search:
  print(i.text)

#공급
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C2')
print('공급')

for i in search:
  print(i.text)

#운항
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C3')
print('운항')

for i in search:
  print(i.text)
```

* 위의 코드 결과로 웹에서 내가 원하는 부분의 테이블 데이터를 가져왔다. 그러나, 옵션을 주지 않았기에 웹사이트에 처음 접속하면 나타나는
  옵션에 대한 데이터가 나온다. 이제 옵션을 원하는대로 조정해보자


```python
#사전준비
from selenium.webdriver import ActionChains #동작을 위한 요소
act = webdriver.ActionChains(driver) #동작 제어문 지정

from selenium.webdriver.support.ui import Select

#웹사이트에서 가용한 옵션 경로지정(이후 코드에서 옵션을 선택할때 visible_text를 활용해 선택할것임 //다른 method를 사용해 [index or value로 선택 가능])

##이 코드를 작성하는 순간의 웹사이트 기본 옵션은 다음과 같음
####2023년도 7월~ 2023년도 7월 , 유임 + 환승, 전체, 전체, 전체, 국내선, 전체, 출발

start_year_option=driver.find_element(By.CSS_SELECTOR,'#start_year') #시작년도 옵션(2009~2023 년도)

end_year_option=driver.find_element(By.CSS_SELECTOR,'#end_year') #끝년도 옵션(2009~2023 년도)

start_month_option=driver.find_element(By.CSS_SELECTOR,'#start_month') #시작 달 옵션(1~12 월)

end_month_option=driver.find_element(By.CSS_SELECTOR,'#end_month') #끝 달 옵션(1~12 월)

search_button=driver.find_element(By.CSS_SELECTOR,'.mainSearchBtn') #검색버튼

pass_option=driver.find_element(By.CSS_SELECTOR,'#passGubun') # 여객옵션('전체', '유임여객', '무임여객', '유임 + 환승')

cargo_option=driver.find_element(By.CSS_SELECTOR,'#cargeGubun') #화물 옵션('순화물','수화물','우편물)

airport_option=driver.find_element(By.CSS_SELECTOR,'#airportGubun') #공항 옵션 ('인천(ICN)','김포(GMP)','김해(PUS)','제주(CJU)','대구(TAE)','광주(KWJ),'청주(CJJ)','무안(MWX)','여수(RSU)','울산(USN)','사천(HIN)','포항(KPO)','군산(KUV)','원주(WJU)','양양(YNY)')

operations_option=driver.find_element(By.CSS_SELECTOR,'#snGubun') #운항 옵션('전체','부정기','정기')

route_option=driver.find_element(By.CSS_SELECTOR,'#diGubun') #노선 옵션('국내선','국제선')

passorcargo_option=driver.find_element(By.CSS_SELECTOR,'#pYNGubun') #여객화물 옵션('여객기','화물기')

arrival_type_option=driver.find_element(By.CSS_SELECTOR,'#arvlType') #출발/도착 옵션('출발','출발+도착') ####'출발'옵션밖에 없으나, 노선옵션에서 국제선으로 변경시 '출발+도착'옵션으로 변경됨 (어떻게 활용할 수 있을지 의문)

```
- 웹의 옵션을 선택하거나, 검색버튼을 클릭하는 작용을 위한 구성을 하고, 각 옵션들의 웹 내에서 주소값을 저장해놓았다.
   - 이후 코드에서 보겠지만 옵션을 조정할 때 각 옵션의 주소에 접근한 후 선택하거나 작동시키는 것이기 때문이다.
```python
### 예시 ###

select = Select(start_month_option) # 시작 달 경로 선택
select.select_by_visible_text('6') #옵션 중 6을 선택
select = Select(end_month_option)
select.select_by_visible_text('6')
search_button.click() #검색버튼 클릭

time.sleep(2) #웹페이지 로딩을 위해 충분한 시간이 있어야함(시간이 부족하면 원하는 결과값이 나오지 않거나, 덜 나오는 현상 발생) 이것은 명시적 대기에 해당함
#공항
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C1')
print('공항')
for i in search:
  print(i.text)
#화물
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C5')
print('화믈')

for i in search:
  print(i.text)
#여객
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C4')
print('여객')

for i in search:
  print(i.text)
#공급
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C2')
print('공급')

for i in search:
  print(i.text)
#운항
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C3')
print('운항')

for i in search:
  print(i.text)
```
  * 위 코드의 결과로 다른 옵션은 처음과 동일하지만, 시작달과 끝달이 6월인 즉, 2023년도 6월 한달간의 데이터를 조회할수 있을 터......인데.....? (에러가 발생할 것이다.....)

    
### _**아직도 모르겠는 부분**_

```python
from selenium.webdriver import ActionChains #동작을 위한 요소
act = webdriver.ActionChains(driver) #동작 제어문 지정

from selenium.webdriver.support.ui import Select

### 예시 ###
start_year_option=driver.find_element(By.CSS_SELECTOR,'#start_year') #시작년도 경로지정

end_year_option=driver.find_element(By.CSS_SELECTOR,'#end_year') #끝년도 경로지정

start_month_option=driver.find_element(By.CSS_SELECTOR,'#start_month') #시작 달 경로지정

end_month_option=driver.find_element(By.CSS_SELECTOR,'#end_month') #끝 달 경로지정

search_button=driver.find_element(By.CSS_SELECTOR,'.mainSearchBtn') #검색버튼 경로지정

select = Select(start_month_option) # 시작 달 경로 선택
select.select_by_visible_text('6') #옵션 중 6을 선택
select = Select(end_month_option)
select.select_by_visible_text('6')
search_button.click() #검색버튼 클릭

time.sleep(2) #웹페이지 로딩을 위해 충분한 시간이 있어야함(시간이 부족하면 원하는 결과값이 나오지 않거나, 덜 나오는 현상 발생) 이것은 명시적 대기에 해당함
#공항
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C1')
print('공항')
for i in search:
  print(i.text)
#화물
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C5')
print('화믈')

for i in search:
  print(i.text)
#여객
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C4')
print('여객')

for i in search:
  print(i.text)
#공급
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C2')
print('공급')

for i in search:
  print(i.text)
#운항
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C3')
print('운항')

for i in search:
  print(i.text)
```
* 애초에 위 코드가 정상작동한 것을 확인한뒤, 옵션의 주소는 주소대로, 데이터를 조회하는 구문은 따로 작성하고 싶어서 바로 위 코드를 두개로 나눈것이 그 위의 코드 2개이다. 아직 이해가 가지 않는 부분이다.  
* chatgpt에게도 몇번을 물어봤으나 의미있는 답은 얻어내지 못했다... 계속 공부해봐야겠다.


## 언제까지고 조회만 하면안된다. 
* 확정통계이기에 과거의 데이터는 변하지 않는다. 언제까지 귀찮은 조회를 반복해야하나?
* 데이터를 한번에 놓고 볼 수 있게 저장을 하자 
  * 게다가 언제까지고 웹에 접속하여 조회를 하게되면 웹사이트에서도 지속적인 부담을 주게 되는 것이므로 지양해야한다고한다.


```python
airportlist = [] #공항명 리스트
supplylist = [] # 공급(석) 리스트
operationlist= [] # 운항(편) 리스트
passengerlist = [] # 여객(명) 리스트
cargolist = [] #화물(톤) 리스트

#공항
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C1')
for i in search:
  airportlist.append(i.text)

#화물
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C5')
for i in search:
  cargolist.append(i.text)

#여객
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C4')
for i in search:
  passengerlist.append(i.text)

#공급
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C2')
for i in search:
  supplylist.append(i.text)

#운항
search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C3')
for i in search:
  operationlist.append(i.text)

#조회기간
search = driver.find_element(By.CSS_SELECTOR,'#searchText')
period = search.text[7:24]

import pandas as pd
data={'공항명' : airportlist,'기간' : period , '공급(석)' : supplylist,'운항(편)' : operationlist,'여객(명)' : passengerlist,'화물(톤)':cargolist}
df=pd.DataFrame(data)
```
* 다음과 같이 데이터프레임 형식으로 정리하면 나중에 시각화를 통해 무언가를 보이기에도 쉬울 것 같다.

```python
#한 시점의 데이터만 추출하여 데이터프레임으로 보여주고, 저장하는 함수정의
def search_one_period(year, month, pas ='유임 + 환승', cargo = '전체', airport = '전체', operations = '전체', route = '국내선', passorcargo = '전체'):


  select = Select(route_option) # 국내선인지 국제선인지
  select.select_by_visible_text(str(route))

  select = Select(start_year_option) # 시작년도
  select.select_by_visible_text(str(year))

  select = Select(end_year_option) # 끝 년도
  select.select_by_visible_text(str(year))

  select = Select(start_month_option) #시작 달
  select.select_by_visible_text(str(month))

  select = Select(end_month_option) # 끝 달
  select.select_by_visible_text(str(month))

  select = Select(pass_option) # 여객옵션
  select.select_by_visible_text(str(pas))

  select = Select(cargo_option) # 화물 옵션
  select.select_by_visible_text(str(cargo))

  select = Select(airport_option) # 공항 옵션
  select.select_by_visible_text(str(airport))

  select = Select(operations_option) # 운항 옵션
  select.select_by_visible_text(str(operations))

  select = Select(route_option) # 노선 옵션
  select.select_by_visible_text(str(route))

  select = Select(passorcargo_option) # 여객화물 옵션
  select.select_by_visible_text(str(passorcargo))

  search_button.click() #검색버튼 클릭

  time.sleep(2) #웹페이지 로딩을 위해 충분한 시간이 있어야함(시간이 부족하면 원하는 결과값이 나오지 않거나, 덜 나오는 현상 발생) 이것은 명시적 대기에 해당함

  airportlist = [] #공항명 리스트
  supplylist = [] # 공급(석) 리스트
  operationlist= [] # 운항(편) 리스트
  passengerlist = [] # 여객(명) 리스트
  cargolist = [] #화물(톤) 리스트

  #공항
  search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C1')
  for i in search:
    airportlist.append(i.text)

  #화물
  search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C5')
  for i in search:
    cargolist.append(i.text)

  #여객
  search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C4')
  for i in search:
    passengerlist.append(i.text)

  #공급
  search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C2')
  for i in search:
    supplylist.append(i.text)

  #운항
  search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C3')
  for i in search:
    operationlist.append(i.text)

  #조회기간
  search = driver.find_element(By.CSS_SELECTOR,'#searchText')
  period = search.text[7:24]

  import pandas as pd
  data={'공항명' : airportlist,'기간' : period , '공급(석)' : supplylist,'운항(편)' : operationlist,'여객(명)' : passengerlist,'화물(톤)':cargolist}
  df=pd.DataFrame(data).set_index('공항명')

  return df
```
```python
#여러 시점의 데이터만 추출하여 데이터프레임으로 보여주고, 저장하는 함수정의
def search_many_period(start_year, start_month, end_year , end_month , pas ='유임 + 환승', cargo = '전체', airport = '전체', operations = '전체', route = '국내선', passorcargo = '전체'):

  select = Select(start_year_option) # 시작년도
  select.select_by_visible_text(str(start_year))

  select = Select(end_year_option) # 끝 년도
  select.select_by_visible_text(str(end_year))

  select = Select(start_month_option) #시작 달
  select.select_by_visible_text(str(start_month))

  select = Select(end_month_option) # 끝 달
  select.select_by_visible_text(str(end_month))

  select = Select(pass_option) # 여객옵션
  select.select_by_visible_text(str(pas))

  select = Select(cargo_option) # 화물 옵션
  select.select_by_visible_text(str(cargo))

  select = Select(airport_option) # 공항 옵션
  select.select_by_visible_text(str(airport))

  select = Select(operations_option) # 운항 옵션
  select.select_by_visible_text(str(operations))

  select = Select(route_option) # 노선 옵션
  select.select_by_visible_text(str(route))

  select = Select(passorcargo_option) # 여객화물 옵션
  select.select_by_visible_text(str(passorcargo))



  search_button.click() #검색버튼 클릭

  time.sleep(2) #웹페이지 로딩을 위해 충분한 시간이 있어야함(시간이 부족하면 원하는 결과값이 나오지 않거나, 덜 나오는 현상 발생) 이것은 명시적 대기에 해당함

  airportlist = [] #공항명 리스트
  supplylist = [] # 공급(석) 리스트
  operationlist= [] # 운항(편) 리스트
  passengerlist = [] # 여객(명) 리스트
  cargolist = [] #화물(톤) 리스트

  #공항
  search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C1')
  for i in search:
    airportlist.append(i.text)

  #화물
  search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C5')
  for i in search:
    cargolist.append(i.text)

  #여객
  search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C4')
  for i in search:
    passengerlist.append(i.text)

  #공급
  search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C2')
  for i in search:
    supplylist.append(i.text)

  #운항
  search = driver.find_elements(By.CSS_SELECTOR,'#mySheet-table > tbody > tr:nth-child(3) > td > div > div.GMPageOne > table > tbody .HideCol0C3')
  for i in search:
    operationlist.append(i.text)

  #조회기간
  search = driver.find_element(By.CSS_SELECTOR,'#searchText')
  period = search.text[7:24]

  import pandas as pd
  data={'공항명' : airportlist,'기간' : period , '공급(석)' : supplylist,'운항(편)' : operationlist,'여객(명)' : passengerlist,'화물(톤)':cargolist}
  df=pd.DataFrame(data)

  return df.set_index('공항명')
```
* 한 시점의 데이터 조회, 여러 시점의 데이터 조회를 할 수 있는 함수를 정의하여 사용하기 용이하게 하였다.
  
