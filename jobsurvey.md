# python_data

# 1.1 필요 정보 탐색
이제는 학생(취업준비를 했으나 실패한 사람)이 실제로 취업을 위해 준비한 스펙과 취업자가 취업을 위해 준비한 스펙을 통해 실제로 각각의 스펙을 준비하는 것이 취업에 도움이 되었는지를 확인해보려고 한다.

문 31A "님께서 취업을 위해 지금까지 준비했거나 현재 준비하고 있는 스펙은 무엇입니까?"와 문6A로 "님께서 취업을 위해 준비한 스펙은 무엇입니까?"를 통해 각각이 준비한 스펙을 알 수 있을 것으로 보인다. 항목 또한 이전에 분석했던 항목들과 일치한다.(다만 학벌과 학/지/혈연이 빠져있다.) 

문 31A와 문6A에 해당하는 답변 또한 이전에 분석했던 "주관적으로 느낀 각 항목이 취업에 미친 영향"과 똑같이 1~5점 척도라고 생각했다. 하지만 이번 질문들에 대한 대답은 (1) 예, (2) 아니오 두 가지로 나뉜다. 이에 따라 연속형 -> 범주형인 로지스틱 회귀에서 범주형 -> 범주형인 카이제곱 검증으로 변경하였다.

# 1.2 데이터 준비
## 1.1 데이터 가져오기
데이터는 "y14a265"와 "y14b279"를 모두 답하지 않은 사람을 제외하여 준비하였다. pandas의 read_csv를 이용하여 ypdata_w14_fin.csv 파일을 불러와 사용하였다. 
```python
dataframe = pd.read_csv('c:\\workspace\\downloads\\ypdata_w14_fin.csv',  low_memory=False)
```
column에 Nan이나 type이 다른 데이터들이 섞여있는 경우 오류가 발생한다. 이 문제를 해결해주기 위해 low_memory=False를 썼다.

## 1.2 취업 여부 column 추가해주기
취업 여부를 알려주는 column이 없기 때문에 추가할 필요가 있다. 이때, 'y14a***'에 대해 답변을 한 사람은 미취업자, 'y14b***'에 답변을 한 사람은 취업자라고 볼 수 있다. 이에 따라 'y14a265'에 대한 답변이 Nan인 경우는 1 (취업자), Nan이 아닌 경우는 2 (미취업자)로 나타내주었다.

np.where은 조건을 만족하는 인덱스를 반환해주는 함수이다. 조건으로 Nan인지를 확인하기 위해 isna() 메서드를 사용했다.
```python
dataframe['취업성공여부'] = np.where(pd.isna(dataframe['y14a265'])==True, 1, 2)
```
## 1.3 데이터 분할
```python
data_1 = dataframe[['y14a265', 'y14a266', 'y14a267', 'y14a268', 'y14a269', 'y14a270', 'y14a271', 'y14a272', 'y14a273', 'y14a274', 'y14a275', 'y14a276', 'y14a277']].to_numpy()
data_2 = dataframe[['y14b279', 'y14b280', 'y14b281', 'y14b282', 'y14b283', 'y14b284', 'y14b285', 'y14b286', 'y14b287', 'y14b288', 'y14b289', 'y14b290', 'y14b291']].to_numpy()
target = dataframe['취업성공여부'].to_numpy()


df_1 = pd.DataFrame(data_1)
df_1 = df_1.fillna(0)

df_2 = pd.DataFrame(data_2)
df_2 = df_2.fillna(0)
```