# KDX_2020_project
소비트렌드 코리아 2020_ 유통소비데이터분석 &amp; 시각화경진대회 from KDX 한국데이터거래소  

**대회 결과 : 우수상**

# contest
---
## 1st. KDX_소비트렌드 코리아 2020   
![img_kdx1](img/kdx_event1.png)  

**KDX 유통·소비데이터 분석 & 시각화 경진대회**  
- **일정:** 2020.09.23 - 2020.10.25  
- **주최 및 주관:** MBN, KDX한국데이터거래소  
- **후원:** NIA 한국정보화진흥원
- **데이터 제공기업:** 삼성카드, 신한카드, Gin+, MCorporation  

## 개요 
---

**1. 과제**  
- KDX의 다양한 데이터와 외부 데이터를 활용해 한국의 소비 트렌드를 분석해 인사이트를 도출하고 이를 보기 좋게 시각화 해주세요.

**2. 설명**  
- KDX가 제공하는 다양한 소비 데이터(온라인 쇼핑, 오프라인 신용카드, 부동산 등)를 분석해주세요.  
- 분석 결과는 참신한 아이디어와 창의력을 이용해 시각화 해주세요.  
- 공공과 민간을 가리지 않고 많은 분야에 활용될 수 있는 결과가 나온다면 더욱 환영입니다.  

**3. 평가기준** 
|기준 항목|내용|  
|-|-|  
|인사이트 점수|인사이트 도출 / 논리 명확성 / 주제 선정과 분석 창의성|  
|기술 점수|분석 기술 수준|  
|실용 점수|대시보드 시장성과 품질| 

**4. 규칙**
- 내부 분석환경에서 코드 저장 후 제출 페이지에서 제출
 - 제공 분석환경: Python Jupyter Notebook, R studio)
- 제출 파일은 final_analysis (.rmd, .rmarkdown, .ipynb) 이름으로 저장 
- 시각화된 자료는 캡쳐 또는 다운받아서 ppt로 대시보드 구성 > 제출 페이지 통해 제출
- 기획안 ppt는 pdf로 변환하여 제출 페이지에서 첨부 파일로 제출
- 대시보드는 5개 이상의 차트로 구성, 인사이트에 대한 설명 필요

**5. 시상 내역**
- 대상(1팀) 300만원
- 최우수상(1팀) 150만원
- 우수상(1팀) 100만원
- 장려상(2팀) 각 25만원  

**6. 팀구성 및 역할**
- 작업툴 : R Studio
- 인원 : 3명 
- 주요 업무 : 주제 및 세부 뱡향성 선정, PPT 제작, 코드 시각화

**7. 팀 주제**
- 주제 : "코로나19 발생 이후 색조 및 기초 화장품 수요 비교분석"  
- 주제 선정 이유 : 코로나19로 인한 마스크 일상화/의무화 시대, 화장품 업계 매출 추이를 살펴보고 발전 방향 제언하기 

**8. Use Data**
- 엠코퍼레이션 온라인 구매 데이터 Mcorporation online purchase data    
- 신한카드 오프라인 구매 데이터 Shinhancard offline purchase data  
- 네이버 키워드 검색량 데이터 Naver keyword search data
  
**9.  대회 결과** 
: 3등, 우수상(R러뷰 팀)   
![대회결과](img/kdx_resurt.png)   

## 소스코드 리뷰
---
## 1. 준비 작업
### 1.1 패키지 설치 및 라이브러리 불러오기
```r
# 패키지 설치하기
install.packages("readxl")
install.packages("dplyr")
install.packages("tidyr")
install.packages("reshape2")
install.packages("ggplot2")
install.packages("lubridate")
install.packages("labeling")
install.packages("extrafont")
install.packages('devtools')
devtools::install_github('bbc/bbplot')
```
```r
# 라이브러리 불러오기
library(readxl)
library(dplyr)
library(tidyr)
library(reshape2)
library(ggplot2)
library(lubridate)
library(labeling)
library(extrafont)
```
- readxl: 엑셀 파일 읽어오기
- dplyr: bind_rows, glimpse, filter, select, group_by, summarise 함수 사용
- tidyr: seperate, join 함수 사용
- reshape2: 피벗테이블 제작 시 사용(dcast, 최종 코드에는 이용 x)
- ggplot2: 데이터 시각화
- lubridate: 문자형 데이터 날짜형으로 변형
- labeling: 그래프 축 단위 설정
- extrafont: 그래프 내 폰트 설정  

### 1.2. 시각화 테마를 위한 bbplot 패키지 설치
```r 
if(!require(pacman))install.packages("pacman")

pacman::p_load('dplyr', 'tidyr', 'gapminder',
               'ggplot2',  'ggalt',
               'forcats', 'R.utils', 'png', 
               'grid', 'ggpubr', 'scales',
               'bbplot')

```
- 좀 더 보기좋은 시각화를 위해 bbc 스타일 테마 적용

## 2. Mcorpotarion Data
### 2.1 기초 & 색조 화장품 엑셀 정리 
```{r}
# 사용할 데이터만 정리하기(메이크업, 스킨케어)
# 분석에 필요한 메이크업, 스킨케어 제품 구매율만 따로 use_data에 넣어 사용
files <- list.files(path = "use_data", pattern = "*.xlsx", full.names = T) 
products <- sapply(files, read_excel, simplify = FALSE) %>% 
  bind_rows(.id = "id")

glimpse(products)
```
![](KDX_project/img/2.1 기초 & 색조 화장품 엑셀 정리.png)
### 2.2 월별 추이 확인을 위한 전처리 및 시각화
```r
# 전체 필터 넣기
filter_products <- group_by(products, 카테고리명, 구매날짜, 고객성별, 고객나이, 구매금액, 구매수) %>%
  separate(구매날짜, into = c("구매연월", "삭제(일자)"), sep = 6) %>% 
  select(카테고리명, 구매연월, 고객성별, 고객나이, 구매금액, 구매수)

head(filter_products, 2)
```
- 구매날짜가 총 8자리 ex) 20200630 으로 되어있어 보기도 지저분하고 예쁘지 못해 일자는 정리하고 월까지만 표시되도록 수정  
- 필요한 카테고리만 선별

```r
# 성별&나이 결측치 제거하기(성별 F, M, 나이 0 이상만 추출)
nomiss_products <- filter_products %>%
  filter(!is.na(고객성별) & !is.na(고객나이)) %>%
  filter((고객성별 %in% c("F", "M")), 고객나이 > 0)

head(nomiss_products)
```
- 고객나이가 (-)로 설정되어있는 결측치가 몇몇 있어 정확한 데이터 분석을 위해 제거  
- NA 결측값을 모두 제거  

```r
# "메이크업 용품" 카테고리 추출
cosmetics <- filter(nomiss_products, 카테고리명 == "메이크업 용품")

cosmetics
```

```r
# 월별 데이터 합계_메이크업 용품
summarise_cosmetics <- cosmetics %>%
  group_by(구매연월, 고객성별) %>%
  summarise(금액합계 = sum(구매금액))

summarise_cosmetics
```
- summarise 함수를 이용하여 일자별 금액 합산  

```r
# "스킨 케어" 카테코리 추출
skincare <- filter(nomiss_products, 카테고리명 == "스킨케어")

skincare
```

```r
# 월별 데이터 합계_스킨케어
summarise_skincare <- skincare %>%
  group_by(구매연월, 고객성별) %>%
  summarise(금액합계 = sum(구매금액))

summarise_skincare
```

```r
# 시각화하기
## '단위: 억' 적용
label_ko_num = function(num){
  ko_num = function(x){
    new_num = x %/% 100000000
    return(paste(new_num, '억', sep = ''))
  }
  return(sapply(num, ko_num))
}
```
- 구매금액의 합계가 너무 커 단위가 지저분하게 나오기에 억단위를 적용하여 보기 좋게 바꿈  

```r
#색조 화장품(메이크업 용품)_월별 추이_ppt.12p
library(ggplot2)

graph_cosmetics <- ggplot(summarise_cosmetics, aes(x = 구매연월, y = 금액합계, color = 고객성별)) +
  geom_point(size = 2) +
  scale_y_continuous(labels = label_ko_num) +
theme(
      axis.text.x = element_text(size = 8,family= "NanumSquare_ac", hjust = 1, angle = 45),
    axis.text.y = element_text(size = 8,family = "NanumSquare_ac"),
    legend.position = "bottom",
    axis.title.x = element_text(size = 12, family = "NanumSquare_ac"),
    axis.title.y = element_text(size = 12, family = "NanumSquare_ac")) +
  geom_hline(yintercept = 0, size = 1, colour="#999999") +
  scale_colour_manual(values = c("#EB3232", "#FAAB18")) +
  bbc_style()

graph_cosmetics
```

```r
# 기초 화장품(스킨케어)_월별 추이_ppt.12p
graph_skincare <- ggplot(summarise_skincare, aes(x = 구매연월, y = 금액합계, color = 고객성별)) +
  geom_point(size = 2) +
  scale_y_continuous(labels = label_ko_num) +
  theme(
    axis.text.x = element_text(size = 8,family= "NanumSquare_ac",hjust = 1, angle = 45),
    axis.text.y = element_text(size = 8,family = "NanumSquare_ac"),
    legend.position = "bottom",
    axis.title.x = element_text(size = 12, family = "NanumSquare_ac"),
    axis.title.y = element_text(size = 12, family = "NanumSquare_ac")) +
  geom_hline(yintercept = 0, size = 1, colour="#999999") +
  scale_colour_manual(values = c("#EB3232", "#FAAB18")) +
  bbc_style()

graph_skincare
```
- 더 나은 시각화를 위해 x축과 y축의 사이즈와 글씨체를 설정해주고, 선의 색을 지정  
- bbc_style이라는 테마를 사용하여 보기 좋게 시각화  

### 2.3 실제 분석을 위한 데이터 전처리 및 시각화

```r
# 성별&나이 결측치 제거하기(성별 F, M, 나이 0 이상만 추출)
nomiss_products <- products %>%
  filter(!is.na(고객성별) & !is.na(고객나이)) %>%
  filter((고객성별 %in% c("F", "M")), 고객나이 > 0) %>%
  select(카테고리명, 구매날짜, 고객성별, 고객나이, OS유형, 구매금액, 구매수)
```

```r
# 비교값 만들기
compare_products <- nomiss_products %>%
  group_by(카테고리명, 구매날짜, 고객성별) %>%
  summarise(금액합계 = sum(구매금액))

head(compare_products)
```

```r
# 억 원 단위 생성
label_ko_num = function(num){
  ko_num = function(x){
    new_num = x %/% 100000000
    return(paste(new_num, '억', sep = ''))
   }
    return(sapply(num, ko_num))
   }
```

```r
# 문자형 데이터 -> 날짜 데이터로 전환
library(lubridate)

final_products <- compare_products %>%
  mutate(구매일 = ymd(구매날짜))
```

* 시각화
```r
# 색조화장품(메이크업 용품) 데이터 시각화 _ppt.14p

final_products

cosmetics <- final_products %>%
  filter(카테고리명 == "메이크업 용품")

font_import(pattern = "NanumSquare")

# loadfonts(device = "win")

theme_update(text = element_text(family = "NanumSquare_ac Bold"))

graph_cosmetics <- ggplot(cosmetics, aes(x = 구매일, y = 금액합계, color = 고객성별)) +
  geom_smooth() + geom_point(size = 0.1) +
  scale_y_continuous(labels = label_ko_num, breaks = seq(0, 2000000000, by = 250000000)) +
  scale_x_date(date_breaks="3 month", minor_breaks=NULL, date_labels = "%Y.%m") +
  theme(
    axis.text.x = element_text(size = 8,family= "NanumSquare_ac", hjust = 1),
    axis.text.y = element_text(size = 8,family = "NanumSquare_ac"),
    axis.title.x = element_text(size = 12, family = "NanumSquare_ac"),
    axis.title.y = element_text(size = 12, family = "NanumSquare_ac"),
  ) +
  geom_hline(yintercept = 0, size = 1, colour="#999999") +
  scale_colour_manual(values = c("#EB3232", "#FAAB18")) +
  bbc_style()

graph_cosmetics
```
- 3개월 단위로 날짜를 정리
```r
# 기초화장품(스킨케어) 데이터 시각화_ppt.14p
skincare <- final_products %>%
  filter(카테고리명 == "스킨케어")

font_import(pattern = "NanumSquare")

# loadfonts(device = "win")

theme_update(text = element_text(family = "NanumSquare_ac Bold"))

graph_skincare <- ggplot(skincare, aes(x = 구매일, y = 금액합계, color = 고객성별)) +
  geom_smooth() + geom_point(size = 0.1) +
  scale_y_continuous(labels = label_ko_num, breaks = seq(0, 600000000, by = 100000000)) +
  scale_x_date(date_breaks="3 month", minor_breaks=NULL, date_labels = "%Y.%m") +
  theme(
    axis.text.x = element_text(size = 8,family= "NanumSquare_ac", hjust = 1),
    axis.text.y = element_text(size = 8,family = "NanumSquare_ac"),
    axis.title.x = element_text(size = 12, family = "NanumSquare_ac"),
    axis.title.y = element_text(size = 12, family = "NanumSquare_ac"),
  ) +
  geom_hline(yintercept = 0, size = 1, colour="#999999") +
  scale_colour_manual(values = c("#EB3232", "#FAAB18")) +
  bbc_style()

graph_skincare
```

## 3. Shinhancard Data
### 3.1 신한카드 '화장품' 카테고리 데이터 전처리

```r
# 신한카드 오프라인 구매 데이터 불러오기 
shinhancard <- read_xlsx("sample/Shinhancard.xlsx")
```

```r
# 신한카드 오프라인 구매 데이터 결측치 제거
shinhancard <- shinhancard %>%
  select(-c(6:8))

head(shinhancard)
```
- 6열에서 부터 8열까지는 무의미한 열이므로 제거
```r
# 신한카드 데이터 필터링
filter_sh_beauty <- shinhancard %>%
  select(업종, 일별, 성별, 연령대별, '카드이용건수(천건)') %>%
  filter(업종 == "M018_화장품")

head(filter_sh_beauty)
```

```r
# 신한카드 성별&나이 결측치 제거하기(성별 F, M, 나이 0 이상만 추출)
nomiss_sh_beauty <- filter_sh_beauty %>%
  filter(!is.na(성별) & !is.na(연령대별)) %>%
  filter((성별 %in% c("F", "M")), 연령대별 > 0)

nomiss_sh_beauty
```

```r
# 신한카드 '화장품' 카테고리 구매수 합계
sum_sh_beauty <- nomiss_sh_beauty %>%
  group_by(일별, 성별) %>%
  summarise('구매횟수' = sum(`카드이용건수(천건)`))

sum_sh_beauty
```

```r
# 신한카드 데이터 시계열 데이터로 변환
final_sh_beauty <- sum_sh_beauty %>%
  mutate(구매일자 = ymd(일별))

final_sh_beauty
```

### 3.2 신한카드 데이터 시각화 

```r
# 신한카드 '화장품' 카테고리 데이터 시각화_ppt.13p
graph_sh_beauty <- ggplot(final_sh_beauty, aes(x = 구매일자, y = 구매횟수, color = 성별)) +
  geom_smooth() + geom_point(size = 0.1) +
  scale_x_date(date_breaks="3 month", minor_breaks=NULL, date_labels = "%Y.%m") +
  scale_y_continuous(breaks = seq(0, 200, by = 20)) +
  theme(
    axis.text.x = element_text(size = 8,family= "NanumSquare_ac", hjust = 1),
    axis.text.y = element_text(size = 8,family = "NanumSquare_ac"),
    axis.title.x = element_text(size = 12, family = "NanumSquare_ac"),
    axis.title.y = element_text(size = 12, family = "NanumSquare_ac"),
  ) +
  geom_hline(yintercept = 0, size = 1, colour="#999999") +
  scale_colour_manual(values = c("#EB3232", "#FAAB18")) +
  bbc_style()
   
graph_sh_beauty
```

## 4. Naver Keyword Data
### 4.1 마스크 키워드 검색량 데이터 
```r
# 마스크 키워드 검색량 데이터 불러오기
mask <- read_excel("mask_keywords_data.xlsx")
```

```r
# 문자형 데이터를 숫자형으로 변환

mask$마스크검색량 <- as.numeric(mask$마스크검색량)
```
- 데이터가 문자형(chr)으로 되어있어 ggplot이 인식을 하지 못함. as.numeric 함수를 이용하여 숫자형 데이터로 전환

```r
# 문자형 데이터를 날짜형으로 변환
final_mask <- mask %>%
  mutate(검색일자 = ymd(구매날짜))

final_mask
```
- 날짜 데이터를 r이 날짜로 인식하지 못하고 문자형으로 인식하여 날짜형 데이터로 전환
```r
# 마스크 키워드 검색량 데이터 시각화_ppt.15p
graph_mask <- ggplot(final_mask, aes(x = 검색일자, y = 마스크검색량)) +
  geom_smooth(color = "#EB3232") +
  scale_y_continuous(breaks = seq(0, 100, by = 10)) +
  scale_x_date(date_breaks="3 month", minor_breaks=NULL, date_labels = "%Y.%m") +
  theme(
    axis.text.x = element_text(size = 8,family= "NanumSquare_ac",  hjust = 1),
    axis.text.y = element_text(size = 8,family = "NanumSquare_ac"),
    axis.title.x = element_text(size = 12, family = "NanumSquare_ac"),
    axis.title.y = element_text(size = 12, family = "NanumSquare_ac")) +
  bbc_style()
  
graph_mask
```
                                                              
                                                              
### 4.2 (색조 & 기초) 화장품 키워드 검색량 데이터 
```r
# (색조 & 기초) 화장품 키워드 검색량 데이터 불러오기
makeup <- read_excel("색조 vs 기초 화장품 키워드 검색량.xlsx")
```

```r
# 문자형 데이터를 숫자형으로 변환
makeup$색조화장품 <- as.numeric(makeup$색조화장품)
makeup$기초화장품 <- as.numeric(makeup$기초화장품)
```

```r
# 문자형 데이터를 날짜형으로 변환
trans_makeup <- makeup %>%
  mutate(검색일자 = ymd(날짜))

trans_makeup
```


```r
# 색조 & 기초 메이크업 화장품 키워드 검색량 데이터 시각화_ppt.16p

graph_makeup <- ggplot(trans_makeup, aes(x = 검색일자, y = `색조 & 기초 화장품 검색량`)) +
  geom_line(aes(y = `색조화장품`), color = "#EB3232") + 
  geom_line(aes(y = `기초화장품`), color = "#FAAB18") +
  scale_y_continuous(breaks = seq(0, 100, by = 10)) +
  scale_x_date(date_breaks="3 month", minor_breaks = NULL, date_labels = "%Y.%m") +
  theme(
    axis.text.x = element_text(size = 8,family= "NanumSquare_ac", hjust = 1),
    axis.text.y = element_text(size = 8,family = "NanumSquare_ac"),
    axis.title.x = element_text(size = 12, family = "NanumSquare_ac"),
    axis.title.y = element_text(size = 12, family = "NanumSquare_ac")) +
  geom_hline(yintercept = 0, size = 1, colour="#999999") +
  bbc_style()

graph_makeup
```

### 4.3 (립 & 아이) 화장품 키워드 검색량 데이터 
```r
# (립 & 아이) 화장품 키워드 검색량 데이터 불러오기
lipeye <- read_excel("메이크업 제품 비교(아이, 립).xlsx")
```


```r
# 문자형 데이터를 날짜형으로 변환
trans_lipeye <- lipeye %>%
  mutate(검색일자 = ymd(날짜))

trans_lipeye
```


```r
# 립 & 아이 메이크업 화장품 키워드 검색량 데이터 시각화_ppt.15p

graph_lipeye <- ggplot(trans_lipeye, aes(x = 검색일자, y = `립 & 아이 메이크업 검색량`)) +
  geom_line(aes(y = `립 메이크업`), color = "#EB3232") + 
  geom_line(aes(y = `아이 메이크업`), color = "#FAAB18") +
  scale_y_continuous(breaks = seq(0, 100, by = 10)) +
  scale_x_date(date_breaks="3 month", minor_breaks=NULL, date_labels = "%Y.%m") +
  theme(
    axis.text.x = element_text(size = 8,family= "NanumSquare_ac", hjust = 1),
    axis.text.y = element_text(size = 8,family = "NanumSquare_ac"),
    axis.title.x = element_text(size = 12, family = "NanumSquare_ac"),
    axis.title.y = element_text(size = 12, family = "NanumSquare_ac")) +
  geom_hline(yintercept = 0, size = 1, colour="#999999") +
  bbc_style()

graph_lipeye
```
- y축의 단위를 설정, 10단위로 끊어서 보기  

### 4.4 (마스크프루프) 화장품 키워드 검색량 데이터 
```r
# (마스크프루프) 화장품 키워드 검색량 데이터 불러오기
maskproof <- read_excel("마스크프루프 키워드 데이터.xlsx")
```

```r
# 문자형 데이터를 숫자형으로 변환
maskproof$마스크프루프 <- as.numeric(maskproof$마스크프루프)
```


```r
# 문자형 데이터를 날짜형으로 변환
trans_maskproof <- maskproof %>%
  mutate(검색일자 = ymd(날짜))

trans_maskproof
```

```r
# 마스크프루프 화장품 키워드 검색량 데이터 시각화

graph_maskproof <- ggplot(trans_maskproof, aes(x = 검색일자, y = `마스크프루프 제품 검색량`)) +
  geom_line(aes(y = `마스크프루프`), color = "#EB3232") +
  scale_y_continuous(breaks = seq(0, 100, by = 10)) +
  scale_x_date(date_breaks="3 month", minor_breaks=NULL, date_labels = "%Y.%m") +
  theme(
    axis.text.x = element_text(size = 8,family= "NanumSquare_ac", hjust = 1),
    axis.text.y = element_text(size = 8,family = "NanumSquare_ac"),
    axis.title.x = element_text(size = 12, family = "NanumSquare_ac"),
    axis.title.y = element_text(size = 12, family = "NanumSquare_ac")) +
  bbc_style()
  
graph_maskproof
```




