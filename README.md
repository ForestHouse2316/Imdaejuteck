# Imdaejuteck

- 연령별 인구현황 : 행정안전부
- 부동산 가격공시 : 공공 데이터포털
- 임대주택 공급현황 : 공공 데이터포털
- 주택 매매가 동향 : [국토교통부](https://www.index.go.kr/potal/main/EachDtlPageDetail.do?idx_cd=1240)
- 추가된 2개의 자료에 대해서는 코드 내에 출처가 포함되어있습니다. (동일하게 국토교통부 자료입니다.)  
[추가1](https://www.index.go.kr/potal/main/EachDtlPageDetail.do?idx_cd=1230)
/ [추가2](https://www.index.go.kr/potal/main/EachDtlPageDetail.do?idx_cd=1232)    
  
__Analyzer.ipynb 가 메인 파일이며 colab 에서도 확인할 수 있습니다.__  
__파일 내에 추가적인 데이터 분석 설명이 들어있습니다.__
  
  
## Source Description  
- ipynb 내 태그와 동일순으로 설명합니다.  
- File Import 등의 반복적인 내용에 대해서는 설명을 생략하겠습니다.
  
### 임대주택 총합  
~~(Deprecated : 해당 데이터는 [임대주택과 청년의 수] 파트에서 제외되었습니다.)~~  
국민임대에 해당하는 col의 모든 index에 대해 합연산을 한다.  
`영구임대`와 `행복주택`은 제외하고 `국민임대`만 합한다.  
합한 값을 `TNo_ImdaeHouse` 에 담아놓는다.
```
TNo_ImdaeHouse_df = pd.read_csv(TNo_ImdaeHouse_path)
TNo_ImdaeHouse_df.head()
TNo_ImdaeHouse = TNo_ImdaeHouse_df['국민임대'].sum()
print("국민 임대주택 총합 : ", TNo_ImdaeHouse)
```

### 연도별 연령분포표  
행안부에서 제공하는 10세 단위 인구수 파일을 로드한다.
![Failed to load](/Document/ScreenShot_20210609184206.png)  
반복되는 칼럼형식에 대해 다음과 같은 str 포맷을 생성하여 `AgeDistribution_ColumnShape`에 저장한다.  
해당 코드는 `[YYYY]년_[성별]_[연령범위]` 형식을 만들어낸다.
```
AgeDistribution_ColumnShape = "YEAR년_GENDER_AGE" # YEAR / GENDER / AGE
AgeRange = list([f"{x}0~{x}9세" for x in range(0,10)])
AgeRange.append("100세 이상")
AgeRange[0] = '0~9세'
```
이렇게 만들어진 형식을 다음과 같이 `replace()`로 변환하여 칼럼에 대입하고 데이터를 추출한다.  
```
people[gender].append(int(AgeDistribution_df[AgeDistribution_ColumnShape.replace('YEAR',str(year)).replace('GENDER',gender).replace('AGE',age)][0].replace(',','')))
```
해당 데이터를 BackToBack 형식의 그래프로 나타낸다.

### 임대주택과 청년의 수
- [연도별 연령분포표] 에서 얻은 데이터에서 20~39세의 데이터만 추출하여 꺾은선으로 나타낸다.  
성별의 구분이 없어야 하므로 위 `AgeDistribution_ColumnShape`의 `GENDER`요소에 `계`를 채워넣는다.
- [추가1] 과 [추가2] 로부터 얻은 자료를 바탕으로 임대주택 분양 호수와 공급량을 꺾은선으로 나타낸다.  
해당 자료의 값은 코드 내 리스트 형태로 직접 입력되어있다.  
  
matplotlib의 레이아웃을 2x2로 조정하여 아래 그림에서 `청년 인구수`, `임대 호수`, `임대주택 공급량` 순으로 표시했다.
  
![Failed to load](/Document/download.png)
  
### 부동산 가격 추이
`전국`, `서울`, `강남`, `강북`, `수도권` 에서의 전년 대비 집값 상승률에 대해 그래프로 나타내었다. 연도범위는 2011~2020 이다.  
csv파일의 인덱스를 연도로 교체하고 지역별로 순차적으로 뽑아내었다.
