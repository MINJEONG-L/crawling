# crawling
## 기본알기
* 필요한 모듈 로딩
```python
from selenium import webdriver
from selenium.webdriver.common.by ipmort By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.chrome.service import Service
```
* 크롬 드라이버 설정 및 웹 페이지 열기
```python
s = Service("c:/py_temp/chromedriver.exe")
driver = webdriver.Chrome(service=s)
url = 'https://naver.com/'
driver.get(url)
time.sleep(5) #창이 켜지는 동안 시간 주기
driver.maximize_window()  #창 전체화면
```  

* Beautiful Soup 로 본문 내용만 추출하기
```python
from bs4 import BeautifulSoup
html = driver.page_source
soup = BeautifulSoup(html, 'html.parser')
```

## Beautiful Soup  
``` python
from bs4 import BeautifulSoup
```
1) find() 함수 : 조건 만족하는 첫 번째 태그 값만 가져오기  
  - soup.find('p','left')
  - soup.find('p')
  - soup.find('p','center')
2) find_all() 함수 : 해당 태그가 여러 개 있을 경우 한꺼번에 모두 가져오기
  - soup.find_all('p')
  - 한 가지 태그가 아닌 여러가지의 태그 찾기 : soup.find_all(['p','img'])
3) select() 함수
  - select('태그이름')
  - select('.클래스명')
  - select(' 상위태그 > 하위태그 > 하위태그') ==> __부등호 앞 뒤로 공백 반드시 들어가야함__  
    ==> 인덱싱 가능
  - select('상위태그.클래스이름 > 하위태그.클래스이름')
  - select('#아이디명')
  - select('#아이디명 > 태그명.클래스명')
  - select('태그명[속성1=값1]')  __인덱싱가능__  
  
4) 태그 뒤의 텍스트만 추출하기
* string 사용
```python
txt = soup.find('p)
txt.string
```
* get_text() 사용
```python
txt = soup.find('p')
txt.get_text()
```  

5) 표준 출력 방향 바꾸어 txt 파일에 저장하기
```python
import sys
f_name 에 파일명 저장
orig_stdout = sys.stdout
file = open(f_name, 'a', encoding = 'UTF-8')
sys.stdout = file #모니터에 출력하지말고 file 에 출력해라
..
print(i.get_text().replace('\n',''))

file.close()

sys.stdout = orig_stdout  #원래대로 변경 : 다시 화면에 출력시켜라
```  

  * 참고용 strip() 함수
```
  string.strip([문자])
  문자 : 선택사항으로 주어진 문자는 원래 문자열의 시작 또는 끝에서 제거된다.
  문자 매개변수가 제공되지 않으면 문자열의 시작과 끝에서 공백이 제거된다.
  문자열의 시작, 끝에 공백이 없는 경우 문자열을 있는 그대로 반환하고 원래 문자열과 일치시킴
  문자 매개변수가 제공되고 문자가 일치하면 문자열의 시작 또는 끝에 잇는 문자가 원래 문자열에서 제거되고 나머지 문장려이 반환된다.
  주어진 문자가 원래 문자열의 시작 또는 끝과 일치하지 않는 경우 문자열을 있는 그대로 반환
  
  str = 'Welcome to helloworld'
  after_strip = str.strip()
  #output : Welcome to helloworld
  
```  
   **strip()함수는 문자열에서만 작동하며 list, tuple 등은 오류**
   
   * driver.find_element 의 By 종류  
    |---|---|---|  
    |---|---|---|  
    |---|---|---|  
    |---|---|---|  
    
## 항목별 내용 추출 후 다양한 형식의 파일로 저장하기
- 항목별로 데이터를 추출하여 리스트에 저장하자
```python
no = []
title = []
writer = []
org = []
```
- try except else 문 사용하기
```python
cont = soup.find('div','srchResultListW').find_all('li')

for b in cont:
  try:  #제목이 있으면 ~
    title = b.find('div','cont').find('p','title').get_text()
   except:
    continue
   else:
    f = open(ft_name, 'a', encoding = 'UFT-8')
    print("1.번호",num)
    no.append(num)
    f.write('\n' + '1.번호' + str(num))

    print('2.논문제목:',title)
    title2.append(title)
    f.write('\n' + '2.논문제목:' + title)
    ...
    ...
    
    f.close()
    
    if num > cnt:
      break
    time.sleep(1) #페이지 변경 전 1초 대기
a+=1
b = str(a)

try:
  driver.find_element(By.LINK_TEXT,'%s'%b).click()
except:
  driver.find_element(By.LINK_TEXT('다음 페이지로')).click()
```  
![image](https://user-images.githubusercontent.com/82145878/178104239-63e66d0d-000c-4479-81dd-c891ae05ee17.png)  


## 수집된 데이터를 xls 와 csv 형태로 저장하기
```python
import pandas as pd

df = pd.DataFrame()
df['번호'] = no
df['제목'] = pd.Series(title)
df['저자'] = pd.Series(writer)
df['소속기관'] = pd.Series(org)
#Series를 쓰는 이유 : 중간에 데이터가 없는 등 비어있으면 DataFrame을 만들수 없다고 에러발생
# xls 형태로 저장하기
df.to_excel(fx_name, index = False, encoding = 'utf-8', engine='openpyxl')
# csv 형태로 저장하기
df.to_csv(fc_name, index = False, encoding = 'utf-8-sig')
```  


## 상세 정보 수집
![image](https://user-images.githubusercontent.com/82145878/178104350-ce9628c6-475a-40ff-8952-9d8c1e95c5ea.png)
  * 총 검색 건수를 보여주고 수집할 건수 입력받기
```python
import math
total_cnt = soup.find('div','serachBox pd')find('span','num').get_text()
cnt = int(input('이 중에서 몇 건을 수집하시겠습니까?: '))
page_cnt = math.ceil(cnt / 10) #한페이지에 10개 게시물
```  

  * url 주소 수집하기
    - 상세 페이지(수집한 url)로 가는 방법  
     (1) 해당 논문의 제목을 클릭하는 방법  
     (2) 해당 논문의 URL 주소를 찾아서 driver.get() 명령으로 접속하는 방법  
 ![image](https://user-images.githubusercontent.com/82145878/178104533-954198bf-0e19-4848-9172-d613344358f3.png)  
    - <a href='url 정보'> 이 논문의 제목에 있다.
    - 그러나 맨 앞에 도메인 주소가 빠져있음 즉 http://www.riss.kr 가 생략되어서 실제 사용할 때 넣어줘야함
  ```python
  url = i.find('p','title').find('a')[href']
  full = 'http://www.riss.kr' + url
  time.sleep(1)
  driver.get(full)
  
  html1 = driver.page_source
  soup1 = BeautifulSoup(html1, 'html.parser')
  ...
  ...
  driver.back()
  time.sleep(2)
  ```  
    **특정 태그의 특정 속성값을 추출하기 위해서는 find('태그이름')['속성이름']**

     
## 네이버 iframe

  * 저장될 파일위치 지정
```python
now = time.localtime()
s = '%04d-%02d-%02d-%02d-%02d-%02d' % (now.tm_year, now.tm_mon, now.tm_mday, now.tm_hour, now.tm_min, now.tm_sec)
os.makedirs(f_dir+s+'-'+query_txt)
os.chdir(f_dir+s+'-'+query_txt)
```
  
  * 옵션 버튼 클릭  
    - driver.find_element(By.LINK_TEXT,'평점').click()  
  **해당 페이지에 평점이라는 단어가 오직 하나일때 사용 가능**  
  
  * IFRAME 변경 : 네이버는 IFRAME 사용  
    ```python
    driver.switch_to.frame('pointAfterListIframe')  
    html = driver.page_source
    soup = BeautifulSoup(html, html.parser')  
    ```  
  

## 이미지 크롤링
**이미지를 수집 : 웹 페이지에서 원하는 이미지의 URL 주소를 추출한 후 웹서버에서 이미지를 다운로드**  
**인증 정보 없이 그냥 가져올 수 있는 방법과 인증 정보를 보낸 후 이미지를 가져올 수 있는 경우가 있다.**  
* 주의사항
  1) 소스코드에서 이미지 파일의 원본 주소를 추출하기
  2) 추출된 내용을 다운로드 하는 과정에서 생기는 크롬 경고창 해결하기
  3) 원본 이미지의 경로와 이름에 한글이 포함되어 있을 경우 발생하는 문제 해결하기
  
 (1) 클라이언트 정보가 필요 없는 경우
  ```python
  #추가된 필요한 모듈
  import urllib.request
  import urllib.parse
  
  #사용자가 요청한 건수만큼 더보기 클릭하기
  for a in range(0,page_cnt):
    try:
      driver.find_element(By.XPATH,'//*[@id="divEducationList"]/div/div[2]/div[5]/a[1]').click()
      time.sleep(2)
      try:
        result = driver.switch_to_alert()
        result.accept()
      except:
        continue
    except:
      print("페이지 이동이 끝났습니다. 이제 데이터를 수집하겠습니다.")
      break
 ```
  - switch_to_alert.accept() : 알람 메세지(에러 메세지) 발생시 확인(승인)하기
 ![image](https://user-images.githubusercontent.com/82145878/178143911-99821b2f-cb0e-4aca-aa15-fd9b994ac1a9.png)
  - switch_to_alert.dismiss() : 알람 메시지 발생시 취소 누르기
  - switch_to_alert.text : 알림창 메세지 가져오기
  - switch_to_alert.send_keys("입력글") : 알림창에 글 입력하기  
  
  
* 이미지 추출하여 저장하기
```python
img_src2 = [] #이미지 원본 URL 주소 저장할 리스트
n = time.localtime()
s = '%04d-%02d-%02d-%02d-%02d-%02d' % (n.tm_year, n.tm_mon, n.tm_mday, n.tm_hour, n.tm_min, n.tm_sec)
img_dir = f_dir+s+'-'+query_txt
os.makedirs(img_dir)
os.chdir(imt_dir)
  
html = driver.page_source
soup = BeautifulSoup(html,'html.parser')
img_src = soup.find('ul','vod_list').find_all('img')

for i in img_src:
  img = i['src']
  img_src2.append('img')
  print(img)
  count += 1
  if count > cnt:
    break
```
![image](https://user-images.githubusercontent.com/82145878/178144252-2da40e0c-09fb-4c8b-8f1e-94399fe7fbe8.png)  

* 이미지 파일에 한글 이름이 들어갈 경우 오류 발생 ==> 인코딩 지정
```python
for i in range(0,len(img_src2)):
  file_no += 1
  urllib.request.urlretrieve(urllib.parse.quote(img_src2[i].encode('utf8'),'/:'),str(file_no) + 'jpg')
  
  time.sleep(0.5)
  ```
  
* 스크롤 다운 함수
```python
 def scroll_down(driver):
    driver.execute_script("window.scrollBy(0,1000)")
    time.sleep(1)
  
for a in range(1,real_cnt + 1):
  for b in range(0,5):
    scroll_down(driver)
    time.sleep(1)
```    
    
  
    - driver.execute_script() 함수는 파이썬 코드에서 외부 os에 있는 특정 함수나 스크립트를 실행할 때 사용
    - 이 함수의 괄호안에 실행하고 싶은 OS의 함수나 기능을 적으면 된다.
    - window.scrollTo() 함수 : 윈도의 마우스 스크롤 하는 기능을 실행하기 위해서
    - window.scrollTo(x좌표, y좌표) : 기준값이 절대 좌표 (입력한 값 만큼 이동)
    - window.scrollBy() 함수는 기준값이 상대좌표
    - document.body.scrollHeight : 화면 끝까지 이동  
  
    
  
* 수집된 url 주소로 이미지 파일 가져와서 저장하기
```python
for e in range(0,len(img_src)):
  class AppURLopener(urllib.request.FancyURLopener):
      version = "Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, \like Gecko) Chrome/47.0.2526.69 Safari/537.36"
      urllib.urlopener = AppURLopener()
      urllib.urlopener.retrieve(img_src2[e],str(file_no) +'jpg')
```
      
