---
categories:
  - CBNU-AI-EX   
tags:
  - Blog
  - CBNU
  - AI
last_modified_at: 2024-03-11T08:06:00-05:00
---

# Rule-based System 프로젝트 with Durable Rules

## 개요

이 프로젝트는 심스리얼리티의 제품 규칙을 관리하기 위해 Rule-based System을 만드는 것입니다. Durable Rules 라이브러리를 사용하여 다양한 제품 환경, 장비, 서비스 등에 대한 규칙을 정의하고 구현했습니다.

### 목표
- 심스리얼리티의 6종 제품군에 대한 규칙 정의
- Durable Rules를 사용하여 규칙 구현
- 예제 사실을 통해 규칙 기반 시스템 시연

### 제품군
- **ENVUE**
- **VATTAIN**
- **Then&There**
- **M_SAM_Simulator**
- **hydroelectricity_Simulator**
- **Metaverse_Film_Fastival_Platform**

## 규칙 정의

규칙은 다양한 환경, 컨텐츠 유형, 개발 환경 및 언어 등을 기반으로 정의됩니다. 다음은 몇 가지 예제입니다:

### 예제 규칙
```python
from durable.lang import *

with ruleset('sims_product'):
    @when_all(c.first << (m.predicate == 'P_environment') & (m.object == 'Hybrid web'),
             (m.predicate == 'E_environment') & (m.object == 'PC'),
             (m.predicate == 'S_environment') & (m.object == 'real world'),
             (m.predicate == 'Content') & (m.object == 'RGB-D Image') & (m.subject == c.first.subject))
    def ENVUE(c):
        c.assert_fact({'subject': c.first.subject, 'predicate': 'is', 'object': 'ENVUE'})
        
    @when_all(c.first << (m.predicate == 'P_environment') & (m.object == 'Hybrid app'),
             (m.predicate == 'E_environment') & (m.object == 'PC'),
             (m.predicate == 'S_environment') & (m.object == 'vitual world'),
             (m.predicate == 'Content') & (m.object == '3D modeling') & (m.subject == c.first.subject))
    def VATTAIN(c):
        c.assert_fact({'subject': c.first.subject, 'predicate': 'is', 'object': 'VATTAIN'})
    
    @when_all(c.first << (m.predicate == 'P_environment') & (m.object == 'Mobile app'),
             (m.predicate == 'E_environment') & (m.object == 'Mobile'),
             (m.predicate == 'S_environment') & (m.object == 'real world'),
             (m.predicate == 'Content') & (m.object == 'AR') & (m.subject == c.first.subject))
    def Then_There(c):
        c.assert_fact({'subject': c.first.subject, 'predicate': 'is', 'object': 'Then&There'})
        
    @when_all(c.first << (m.predicate == 'P_environment') & (m.object == 'Native app'),
             (m.predicate == 'E_environment') & (m.object == 'PC'),
             (m.predicate == 'S_environment') & (m.object == 'vitual world'),
             (m.predicate == 'Content') & (m.object == '3D modeling') & (m.subject == c.first.subject))
    def M_SAM_Simulator(c):
        c.assert_fact({'subject': c.first.subject, 'predicate': 'is', 'object': 'M_SAM_Simulator'})
        
    @when_all(c.first << (m.predicate == 'P_environment') & (m.object == 'Hybrid web'),
             (m.predicate == 'E_environment') & (m.object == 'PC'),
             (m.predicate == 'S_environment') & (m.object == 'real world'),
             (m.predicate == 'Content') & (m.object == '3D modeling') & (m.subject == c.first.subject))
    def hydroelectricity_Simulator(c):
        c.assert_fact({'subject': c.first.subject, 'predicate': 'is', 'object': 'hydroelectricity_Simulator'})
        
    @when_all(c.first << (m.predicate == 'P_environment') & (m.object == 'Mobile app'),
             (m.predicate == 'E_environment') & (m.object == 'Mobile'),
             (m.predicate == 'S_environment') & (m.object == 'vitual world'),
             (m.predicate == 'Content') & (m.object == '3D modeling') & (m.subject == c.first.subject))
    def Metaverse_Film_Fastival_Platform(c):
        c.assert_fact({'subject': c.first.subject, 'predicate': 'is', 'object': 'Metaverse_Film_Fastival_Platform'})

# 예제 사실 추가
assert_fact('sims_product', {'subject': 'ENVUE', 'predicate': 'P_environment', 'object': 'Hybrid web'})
assert_fact('sims_product', {'subject': 'ENVUE', 'predicate': 'E_environment', 'object': 'PC'})
assert_fact('sims_product', {'subject': 'ENVUE', 'predicate': 'S_environment', 'object': 'real world'})
assert_fact('sims_product', {'subject': 'ENVUE', 'predicate': 'Content', 'object': 'RGB-D Image'})

assert_fact('sims_product', {'subject': 'VATTAIN', 'predicate': 'P_environment', 'object': 'Hybrid app'})
assert_fact('sims_product', {'subject': 'VATTAIN', 'predicate': 'E_environment', 'object': 'PC'})
assert_fact('sims_product', {'subject': 'VATTAIN', 'predicate': 'S_environment', 'object': 'vitual world'})
assert_fact('sims_product', {'subject': 'VATTAIN', 'predicate': 'Content', 'object': '3D modeling'})

assert_fact('sims_product', {'subject': 'Then&There', 'predicate': 'P_environment', 'object': 'Mobile app'})
assert_fact('sims_product', {'subject': 'Then&There', 'predicate': 'E_environment', 'object': 'Mobile'})
assert_fact('sims_product', {'subject': 'Then&There', 'predicate': 'S_environment', 'object': 'real world'})
assert_fact('sims_product', {'subject': 'Then&There', 'predicate': 'Content', 'object': 'AR'})

assert_fact('sims_product', {'subject': 'M-SAM', 'predicate': 'P_environment', 'object': 'Native app'})
assert_fact('sims_product', {'subject': 'M-SAM', 'predicate': 'E_environment', 'object': 'PC'})
assert_fact('sims_product', {'subject': 'M-SAM', 'predicate': 'S_environment', 'object': 'vitual world'})
assert_fact('sims_product', {'subject': 'M-SAM', 'predicate': 'Content', 'object': '3D modeling'})

assert_fact('sims_product', {'subject': 'hydroelectricity', 'predicate': 'P_environment', 'object': 'Hybrid web'})
assert_fact('sims_product', {'subject': 'hydroelectricity', 'predicate': 'E_environment', 'object': 'PC'})
assert_fact('sims_product', {'subject': 'hydroelectricity', 'predicate': 'S_environment', 'object': 'real world'})
assert_fact('sims_product', {'subject': 'hydroelectricity', 'predicate': 'Content', 'object': '3D modeling'})

assert_fact('sims_product', {'subject': 'Metaverse_Platform', 'predicate': 'P_environment', 'object': 'Mobile app'})
assert_fact('sims_product', {'subject': 'Metaverse_Platform', 'predicate': 'E_environment', 'object': 'Mobile'})
assert_fact('sims_product', {'subject': 'Metaverse_Platform', 'predicate': 'S_environment', 'object': 'vitual world'})
assert_fact('sims_product', {'subject': 'Metaverse_Platform', 'predicate': 'Content', 'object': '3D modeling'})
```

## 코드 실행 방법

이 프로젝트를 실행하려면 Python과 Durable Rules 라이브러리가 필요합니다. Durable Rules는 다음과 같이 설치할 수 있습니다:

```bash
pip install durable-rules
```

라이브러리를 설치한 후, 스크립트를 실행하여 규칙 기반 시스템이 작동하는지 확인할 수 있습니다. 스크립트의 `assert_fact` 문은 규칙을 트리거하고 해당 출력을 생성합니다.

## 느낀 점

이 프로젝트를 통해 인공지능의 규칙 기반 시스템에 대해 배울 수 있었습니다. 처음에는 복잡하고 어렵게 느껴졌지만, 실제로 규칙을 정의하고 적용하면서 점차 그 원리를 이해할 수 있었습니다. 다양한 조건과 상황에 따라 시스템이 자동으로 결정을 내리는 것을 보면서 인공지능의 강력함과 효율성을 체감할 수 있었습니다.

특히, Durable Rules와 같은 도구를 활용하면 복잡한 비즈니스 로직을 보다 체계적으로 관리할 수 있다는 점이 인상적이었습니다. 앞으로 더 많은 규칙과 시나리오를 추가해보면서 이 시스템을 발전시켜 나가고 싶습니다. 인공지능을 처음 배우는 입장에서 이러한 프로젝트는 매우 유익하고 보람찬 경험이었습니다.

이 프로젝트를 통해 얻은 지식을 바탕으로 더 나은 AI 시스템을 개발하고, 다양한 문제를 해결할 수 있는 능력을 키워나가겠습니다.
