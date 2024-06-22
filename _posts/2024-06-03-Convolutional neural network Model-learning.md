---
categories:
  - CBNU-AI-EX
  - 24년1학기  
tags:
  - Blog
  - CBNU
  - AI
---

# VGG-16 신경망 알고리즘을 활용하여 CNN 모델을 만들고 학습해보자

## 개요
이미지 분류에 대해서 학습을 해보려고 한다.  
이미지 분류에서 가장 많이 사용하는 VGG-16 알고리즘을 활용하여 CNN 모델을 만들어 인물을 분류하는 모델에 대해 이해하고 학습해보자

## 데이터 설명
- 데이터 수집: 우리 가족 구성원 총 3명의 얼굴 사진을 수집하여 사용하였으며, 각 학습 데이터 100장, 테스트 데이터 20장으로 총 360장의 이미지를 사용하였음
- 데이터 전처리: 얼굴 각도 변화가 적은 이미지를 되도록이면 사용하고자 하였으며, 데이터 증강을 통해 부족한 학습데이터를 보완하였음
<img src="https://Kangpildo.github.io/assets/images/CNN-Model-Example-image1.png" alt="3인 사진 데이터">

## 모델의 구조 (VGG-16)
- 입력: 224x224 픽셀 크기의 3채널(RGB) 컬러 이미지.
- VGG-16: 마지막 합성곱 레이어의 출력은 전역 평균 풀링을 적용하여 512개의 특징 벡터를 얻음.
- 특징 벡터: 512개의 특징 값을 32개의 값으로 줄임.
- 완전 연결 레이어: 32개의 값을 3개의 클래스에 대한 확률로 줄임. Softmax 활성화 함수를 사용.
- 다만, Top-layer층은 기존의 VGG-16의 1,000 클래스에 대한 분류를 사용하지 않고 내가 분류하고자 하는 3개의 클래스 층을 추가하여 사용하였음
<img src="https://Kangpildo.github.io/assets/images/CNN-Model-Example-image2.png" alt="CNN 모델 구조">

## 학습 과정
기존 VGG16모델을 사용하되, 추가로 몇 개의 레이어를 더 쌓고 새로운 데이터로 모델을 학습
### 1. 라이브러리, 데이터 불러오기
```python
import tensorflow as tf
from tensorflow.keras.preprocessing.image import ImageDataGenerator
from tensorflow.keras.preprocessing import image
from tensorflow.keras.models import Model
from tensorflow.keras.layers import Input, Flatten, Dense, GlobalAveragePooling2D
from tensorflow.keras.applications.vgg16 import VGG16, preprocess_input
import math
import numpy as np

TRAIN_DATA_DIR = '/content/gdrive/MyDrive/data_people/train'
VALIDATION_DATA_DIR = '/content/gdrive/MyDrive/data_people/validation'
TEST_DATA_DIR = '/content/gdrive/MyDrive/data_people/test'
```
### 2. 데이터 생성
ImageDataGenerator를 이용해서 학습용 이미지를 추가로 더 생성  
학습 데이터가 많지 않은 경우 과적합 가능성이 높아지는데, 원본 이미지를 변형하여 새로운 이미지를 생성하는 data augmentation을 실시하였음  
단, validation 이미지는 정규화만 실시하고 데이터 augmentaion은 실시하지 않았음
```python
train_datagen = ImageDataGenerator(preprocessing_function=preprocess_input,
                                   rotation_range=20,
                                   width_shift_range=0.2,
                                   height_shift_range=0.2,
                                   zoom_range=0.2)

val_datagen = ImageDataGenerator(preprocessing_function=preprocess_input)
```
### 3. 모델의 정의
VGG16 모델을 불러오고, include_top=False로 설정하고, 내가 분류하고자 하는 인물 3인에 대한 클래스를 정의함.  
라인 5,6에서 기본 모델에 포함되어 있는 레이어는 학습을 진행하지 않도록 설정하였음
```python
def model_maker():
    base_model = VGG16(include_top=False, input_shape=(IMG_WIDTH, IMG_HEIGHT, 3))
    print(len(base_model.layers))

    for layer in base_model.layers[:]:
        layer.trainable = False

    input = Input(shape=(IMG_WIDTH, IMG_HEIGHT, 3))
    custom_model = base_model(input)
    custom_model = GlobalAveragePooling2D()(custom_model)
    custom_model = Dense(32, activation='relu')(custom_model)
    predictions = Dense(NUM_CLASSES, activation='softmax')(custom_model)

    return Model(inputs=input, outputs=predictions)
model_final = model_maker()
model_final.summary()
```
<img src="https://Kangpildo.github.io/assets/images/CNN-Model-Example-image2.png" alt="CNN 모델 구조">

### 4. 모델 학습
##모델 컴파일과 학습을 진행함##
```python
model_final.compile(loss='categorical_crossentropy',
              optimizer=tf.keras.optimizers.Adam(0.001),
              metrics=['acc'])

history = model_final.fit(
    train_generator, steps_per_epoch=TRAIN_SAMPLES // BATCH_SIZE, # number of updates
    epochs=10,
    validation_data=validation_generator,
    validation_steps=VALIDATION_SAMPLES // BATCH_SIZE)
```
##Validation 손실함수 확인##
```python
import matplotlib.pyplot as plt
plt.plot(history.history['loss'])
plt.plot(history.history['val_loss'])
plt.xlabel('epoch')
plt.ylabel('loss')
plt.legend(['train','val'])
plt.show()
```
##정확도 확인##
```python
import matplotlib.pyplot as plt
plt.plot(history.history['acc'])
plt.plot(history.history['val_acc'])
plt.xlabel('epoch')
plt.ylabel('accuracy')
plt.legend(['train','val'])
plt.show()
```
<img src="https://Kangpildo.github.io/assets/images/CNN-Model-Example-image3.png" alt="모델 학습 결과">

### 5. 모델 성능 평가
train, validation과 동일한 방식으로 test set을 불러와서 모델의 성능 평가를 실시함
```python
test_datagen = ImageDataGenerator(preprocessing_function=preprocess_input)

test_generator = val_datagen.flow_from_directory(
    TEST_DATA_DIR,
    target_size=(IMG_WIDTH, IMG_HEIGHT),
    batch_size=BATCH_SIZE,
    shuffle=False,
    class_mode='categorical')

model_final.evaluate(test_generator, steps=60 // BATCH_SIZE)
```
<img src="https://Kangpildo.github.io/assets/images/CNN-Model-Example-image5.png" alt="모델 성능 평가 결과1">
<img src="https://Kangpildo.github.io/assets/images/CNN-Model-Example-image4.png" alt="모델 성능 평가 결과2">



## 학습 결과
- 훈련 모델 평가 결과: Validation에서 손실 함수가 감소하고 정확도가 증가함.
- 전반적인 과정: Validation에서 손실 함수가 일정하게 감소하고 정확도가 증가함. 데이터 증강 기법을 적용하여 모델의 일반화 성능을 높임.
- 과적합 방지: 훈련 데이터에 대한 정확도가 검증 데이터에 비해 낮은 현상을 보임.
