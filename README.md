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
  try:
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
