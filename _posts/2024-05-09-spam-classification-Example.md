---
categories:
  - CBNU-AI-EX
  - 24년1학기  
tags:
  - Blog
  - CBNU
  - AI
---

# 스팸 메일 분류 모델 프로젝트

## 개요

이 프로젝트는 한글 데이터를 사용하여 스팸 메일을 분류하는 모델을 만드는 것입니다. 순환 신경망(RNN) 모델을 사용하여 스팸 메일과 정상 메일을 구분합니다.

### 목표
- 한글 데이터로 스팸 메일 분류 모델 구현
- RNN 모델을 사용하여 스팸 메일과 정상 메일을 이진 분류

## 데이터 설명

- **데이터 수집**: 사내 메일 및 외부 메일에서 스팸 메일 관련 데이터 수집 (총 122개 샘플)
- **데이터 구성**: 텍스트 형태의 메일 내용과 레이블 (스팸/정상)

## 사용 모델

- **모델**: 순환 신경망(RNN) 모델
- **구현**: 바닐라 RNN (1:1 RNN)

## 학습 과정

1. **데이터 파악**
   - 한글 스팸 데이터 생성 및 로드
   - 총 데이터 수 및 데이터 확인

2. **데이터 전처리**
   - 불필요한 컬럼 삭제
   - 중복 데이터 제거
   - 학습 데이터와 테스트 데이터 분리
   - 토큰화 및 정수 인코딩

3. **모델 학습 및 평가**
   - 바닐라 RNN 모델을 사용하여 학습 및 평가

### 코드 예시
```python
import pandas as pd
from keras.preprocessing.text import Tokenizer
from keras.preprocessing.sequence import pad_sequences
from keras.models import Sequential
from keras.layers import Embedding, SimpleRNN, Dense
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score

# 데이터 로드
data = pd.read_csv('/path/to/spam_kr.csv')

# 데이터 전처리
data = data.dropna()
texts = data['text'].values
labels = data['label'].values

# 데이터 토큰화 및 패딩
tokenizer = Tokenizer()
tokenizer.fit_on_texts(texts)
sequences = tokenizer.texts_to_sequences(texts)
x_data = pad_sequences(sequences, maxlen=100)
y_data = labels

# 학습 데이터와 테스트 데이터 분리
x_train, x_test, y_train, y_test = train_test_split(x_data, y_data, test_size=0.2, random_state=42)

# 모델 생성
model = Sequential()
model.add(Embedding(len(tokenizer.word_index) + 1, 128, input_length=100))
model.add(SimpleRNN(128))
model.add(Dense(1, activation='sigmoid'))

# 모델 컴파일
model.compile(optimizer='adam', loss='binary_crossentropy', metrics=['accuracy'])

# 모델 학습
model.fit(x_train, y_train, epochs=10, batch_size=32, validation_split=0.2)

# 모델 평가
y_pred = model.predict_classes(x_test)
accuracy = accuracy_score(y_test, y_pred)
print(f'테스트 정확도: {accuracy}')

## 성능
총 샘플 수: 122
정확도: 0.7297

## 느낀 점
이 프로젝트를 통해 데이터 전처리의 중요성을 깨달았습니다. 특히, 한글 데이터의 전처리 과정에서 불용어 제거와 특수 문자 처리의 필요성을 느꼈습니다. 또한, 모델의 성능을 향상시키기 위해 더 많은 데이터를 수집하고, 다양한 전처리 방법을 적용해야 한다는 것을 배웠습니다.

이 프로젝트는 인공지능 모델의 학습 과정을 이해하는 데 큰 도움이 되었으며, 앞으로 더 나은 성능을 내기 위한 방법을 고민하게 되었습니다. 인공지능을 처음 배우는 입장에서 이러한 프로젝트는 매우 유익하고 보람찬 경험이었습니다.
