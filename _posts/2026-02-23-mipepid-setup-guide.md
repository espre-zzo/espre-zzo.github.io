---
title: "MiPepid 설치 및 이해"
date: 2026-02-23 10:00:00 +0900
categories: [Study, ribosome_profiling]
tags: [MiPepid, sORF]
---

# MiPepid 설치 및 이해

## MiPepid에 대해 알아보자

[MiPepid](https://github.com/MindAI/MiPepid)(**Mi**cro**pep**tide **id**entification tool)는 sORF(short Open Reading Frame)의 코딩 가능성을 머신러닝으로 예측하는 도구이다.

sORF는 stop codon을 포함하여 303bp 이하의 짧은 ORF로, 번역 시 100개 아미노산 이하의 micropeptide를 인코딩한다. Micropeptide는 크기가 작아 역할이 규명되지 않았던 과거에 비해, 최근에는 생물학적인 역할이 암 등에서 활발하게 연구되고 있다.

MiPepid는 입력으로 DNA fasta 파일을 받아 3개의 translation frame에서 모든 sORF를 찾고, 각 sORF에 대해 coding/noncoding 여부와 그 확률을 예측해준다.

> RNA나 단백질 서열이 아닌 순수한 DNA 서열로 구성된 FASTA 파일이어야 input으로 제대로 작동한다. (ATCG 4가지 염기로만 구성되어야 함)
{: .prompt-tip }
> Mengmeng Zhu, Michael Gribskov. MiPepid: Micropeptide identification tool using machine learning. *BMC Bioinformatics* 20, 559 (2019)

## 설치

### 1. 레포지토리 클론

실행하는데 있어서 linux 기반의 시스템이나 python3를 실행할 수 있으면 크게 상관없다.

```bash
git clone https://github.com/MindAI/MiPepid.git
cd MiPepid
```

클론 후 디렉토리 구조:

```
MiPepid/
├── README.md
├── datasets.tar.gz
├── MiPepid_predictions_on_uORF_and_lncRNA.tar.gz
├── demo_files/
│   └── sample_seqs.fasta
└── src/
    ├── mipepid.py      # 메인 실행 스크립트
    ├── ML.py            # 머신러닝 모델 로딩 및 예측
    ├── ORF.py           # ORF 탐색 로직
    └── model/
        └── model.pkl    # 학습된 Logistic Regression 모델
```

### 2. Python 의존성 설치

README에 명시된 의존성은 다음 3가지이다:

```bash
pip3 install numpy pandas biopython
```

하지만, 위 dependency만 설치해서 실행하는 경우 에러가 뜨는데, `model.pkl`은 scikit-learn의 `LogisticRegression` 모델을 pickle로 직렬화한 파일이므로, **scikit-learn**과 그 의존성인 **scipy**도 함께 설치해야 한다.

```bash
pip3 install scikit-learn
```

### 3. scikit-learn 호환성 문제 해결

Dependency를 모두 설치한 것 같지만, 서로서로 버전 호환이 되지 안는 문제가 발생힌다:

```
ModuleNotFoundError: No module named 'sklearn.linear_model.logistic'
```

`model.pkl` 파일은 **scikit-learn 0.19.1**에서 생성되었기 때문. Logistic Regression 클래스의 모듈 경로는 `sklearn.linear_model.logistic`이었으나, 최신 버전에서는 `sklearn.linear_model._logistic`으로 변경되었다고 한다. pickle은 직렬화 시 모듈 경로를 그대로 저장하기 때문에, 구버전의 모듈 경로를 찾으려다 실패하는 것이다.

#### 해결: Custom Unpickler 작성

구버전 scikit-learn을 설치해도 해결이 되지 않아서, `pickle.Unpickler`를 상속하여 모듈 경로를 매핑할 수 밖에 없었다. `src/ML.py`의 `load_model` 함수를 다음과 같이 수정한다:

```python
# src/ML.py 수정 내용

class _CompatUnpickler(pickle.Unpickler):
  def find_class(self, module, name):
    # Handle renamed sklearn module from older pickle files
    if module == 'sklearn.linear_model.logistic':
      module = 'sklearn.linear_model._logistic'
    return super().find_class(module, name)

def load_model(model_fname = './src/model/model.pkl'):
  f = open(model_fname, 'rb')
  logr = _CompatUnpickler(f).load()
  threshold = _CompatUnpickler(f).load()
  f.close()
  return logr, threshold
```

**변경 포인트:**
- `pickle.load(f)` → `_CompatUnpickler(f).load()`
- `_CompatUnpickler.find_class()`에서 구버전 모듈 경로를 신버전으로 리다이렉트


### 4. 실행 및 검증

#### 기본 사용법

```bash
cd MiPepid
python3 ./src/mipepid.py <입력_fasta_파일> [출력_csv_파일]
```

- 출력 파일을 지정하지 않으면 `Mipepid_results.csv`가 현재 디렉토리에 생성된다.
- 반드시 `MiPepid/` 디렉토리에서 실행해야 한다 (모델 파일 경로가 `./src/model/model.pkl`로 하드코딩되어 있음).

#### 데모 실행

```bash
python3 ./src/mipepid.py ./demo_files/sample_seqs.fasta ./demo_files/MiPepid_results.csv
```

#### 출력 결과 예시

| sORF_ID | sORF_seq | transcript_DNA_sequence_ID | start_at | end_at | classification | probability |
|---------|----------|---------------------------|----------|--------|---------------|-------------|
| seq1_ORF1 | ATGGGCCGGAAACG... | seq1 | 925 | 1209 | coding | 0.991 |
| seq1_ORF6 | ATGAACCTGAAGGT... | seq1 | 683 | 964 | coding | 0.998 |
| seq1_ORF10 | ATGGTGAGGGCATGGTGA | seq1 | 947 | 964 | noncoding | 0.596 |

#### output 해석

- `sORF_ID`: 발견된 sORF의 고유 ID
- `sORF_seq`: 해당 sORF의 DNA서열 (시작 코돈 ATG와 종결 코돈 포함)
- `transcript_DNA_sequence_ID`: 이 sORF가 추출된 원본 DNA 서열의 ID
- `start_at`: 원본 DNA 서열에서의 sORF 시작 위치 (1-based index)
- `end_at`: 원본 DNA 서열에서의 sORF 종료 위치 (1-based index)
- `classification`: 예측된 클래스 (coding 또는 noncoding)
- `probability`: 해당 클래스로 분류될 확률
