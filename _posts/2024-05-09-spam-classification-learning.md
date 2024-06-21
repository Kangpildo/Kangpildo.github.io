---
categories:
  - CBNU-AI-EX
  - 24년1학기  
tags:
  - Blog
  - CBNU
  - AI
---

# 순환신경망(RNN)을 활용한 한글 스팸 메일 분류 모델 학습 및 검증

## 개요
본격적으로 인공지능을 학습하면서 딥러닝 신경망에 대해서 학습을 하게되었고 이에 순환신경망(RNN)을 활용하여 모델 학습을 해보려고 한다.  
순환신경망 학습의 가장 기초적인 예제는 바로 '스팸 메일 분류'라고 할수 있을것 같다(개인적인 생각)  
하지만 영문 메일에 대한 스팸 분류 모델은 학습이 잘되고 성능도 잘 나오는 반면, 대한민국! 우리의 아름다운 한글은 어순이 유연하고 조사 사용이 많아 문장 구조가 영어보다 복잡해서 학습이 잘 되지 않는것 같아 한번 예시를 만들어 보면서 확인해보려고 한다.

### 목표
- 한글 데이터로 스팸 메일 분류 모델 구현
- RNN 모델을 사용하여 스팸 메일과 정상 메일을 이진 분류

## 데이터 설명
- **데이터 수집**: 사내 메일 및 외부 메일에서 스팸 메일 관련 데이터 수집 (총 122개 샘플)
- **데이터 구성**: 텍스트 형태의 메일 내용과 레이블 (스팸/정상)
<img src="https://Kangpildo.github.io/assets/images/spam-classification-Example-image1.png" alt="한글 스팸 데이터 예시">

## 사용 모델
- **모델**: 순환 신경망(RNN) 모델
- **구현**: 바닐라 RNN (1:1 RNN)

## 학습과정
### 1.데이터 파악
  - 한글 스팸 데이터 생성 및 로드
```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import urllib.request

from sklearn.model_selection import train_test_split
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences

data = pd.read_csv('spam_kr.csv', encoding='euc-kr')
print('총 샘플의 수 :',len(data))
```
  - 총 데이터 수 및 데이터 확인
```python
print('정상 메일과 스팸 메일의 개수')
print(data.groupby('v1').size().reset_index(name='count'))
print(f'정상 메일의 비율 = {round(data["v1"].value_counts()[0]/len(data) * 100,3)}%')
print(f'스팸 메일의 비율 = {round(data["v1"].value_counts()[1]/len(data) * 100,3)}%')
print('메일 본문의 개수: {}'.format(len(X_data)))
print('레이블의 개수: {}'.format(len(y_data)))
```  
