# 보험사의 효율적인 운영을 위한 예측 모델 개발
데이터 청년 캠퍼스 프로젝트 1조

개발기간 : 2021/08/02 ~ 2021/08/27

Notion Link : https://nervous-stranger-60b.notion.site/Insurance-Fraud-Prediction-9a9d4408c8c64753afa16497aec26d60

## 보험 사기 현황 & 프로젝트 목적
금융감독원 자료에 따르면 2015년도 이후 보험사기 건수는 꾸준히 증가하였다. 

특히, 2020년도부터 코로나 바이러스 판데믹 이후에는 보험사기 금액이 급격히 증가하였음을 확인할 수 있었다.

이러한 상황에서 각종 증권사는 머신러닝 기술을 접목한 보험사기 예측 시스템을 개발하였으며, 대표적인 예로는 교보생명의 K-FDS, KB손해보험의 SMA 시스템 등이 있다.

그러나 기존 보험사의 예측 시스템에는 보험금을 받아야할 소비자를 사기자로 진단하는 등 시스템에 대한 신뢰도가 떨어지는 문제가 있다.

따라서 이번 프로젝트는 사기 여부에 대한 예측을 확률로 나타내어 보완하고, 보험사가 고객을 관리함에 있어 더 효율적인 운영을 할 수 있도록 예측모델을 구축하는 데에 개발 목적을 둔다.

## 데이터 소개
Raw_Data 폴더 내부의 4개의 데이터 셋 파일은 각각 보험사 가입 회원정보(CUST), 고객별 청구(CLAIM), 보험설계사 관련 데이터(FPINFO), 보험 계약 관련 데이터(CNTT)이다.

이들의 정제를 위하여 인코딩 방식을 UTF-8로 맞추고, CSV파일로 변환한 것이 Converted_Data 폴더 내부 컨텐츠이다. 폴더 내부 각 파일에 대응하는 내용은 RAW_DATA와 같다.

정제 과정에 있어 첫번째 목표는 분석에 가장 용이한 최적의 데이터 셋을 산출하는 것이었다. 모든 데이터셋의 컬럼을 합치게 되면 70개 이상의 컬럼이 존재하게 되고, 이렇게 과한 수의 컬럼은 데이터 분석을 용이하게 하지 못하는 문제를 야기하였다. 

때문에, 우리는 1. CNTT + CUST + FPINFO 병합본, 2. CUST + CLAIM 병합본, 3. 전 데이터 셋 병합본으로 나누어 정제를 진행하였다. (이들과 관련된 코드 및 전처리 후 CSV파일은 Data_Processing branch에서 확인할 수 있다.)

각 전처리된 데이터셋의 컬럼 정보와 유효 컬럼은 아래와 같다.

### Cust + Claim
CUST_ID : 고객의 고유 아이디이다. 병합의 PK로서 역할을 하며, 분석용 데이터 셋에서 고유하다.

SIU_CUST_YN : 보험사기자 여부이며, 분석 과정에 있어 Target Data가 된다. 1 : Y, 2 : N의 binary형태 데이터이며, 지도 학습에 있어 가장 중요한 역할을 하게 된다.

SEX : 고객의 성별. 1 : MALE, 2 : FEMALE

AGE : 고객의 나이

FP_CAREER : 고객의 보험설계사 이력 여부

MAX_PRM : 최대 보험료, 당사에 최대 규모 보험료를 납입한 월 보험료 수준으로, 10만 단위로 1씩 누계

RESL_CD1 : 보험 사고에 대한 결과 코드

ACCI_OCCP_GRP : 보험 청구자의 직업 코드

CHME_LICE_NO : 대표 담당의사면허번호

DMND_AMT : 사고보험금청구금액

PAYM_AMT : 실지급금액

NON_PAY_RATIO : 실손비급여비율

HEED_HOSP_YN : 유의병원여부

CLAIM_CNT : 보험 청구 건수

TOTAL_VLID_HOSP_OTDA : 유효입원 및 통원일수의 합

HOSP_VARIES : 보험 청구건에 대하여 다닌 병원의 갯수

HOSP_DVSN_VARIES : 보험 청구건에 대하여 다닌 병원의 종류 구분 누계

CHME_LICE_COUNT : 담당의사 면허 건수

상관분석 결과
![image](https://user-images.githubusercontent.com/80696846/130572167-9d5b6d11-7451-4dc8-b90c-d185fb829b8d.png)

다중회귀분석 결과
![image](https://user-images.githubusercontent.com/80696846/130572245-944178e1-1a39-493a-aca9-c4a99a33f4d4.png)

RandomForest 변수중요도 분석 결과
![image](https://user-images.githubusercontent.com/80696846/130572377-f86ef01d-8387-45e3-a213-d4eace94b33f.png)

------------------------------------------------------------------------------------------------

위 결과를 종합한 경우, 아래와 같은 5개의 컬럼이 보험사기자 여부와 상대적으로 높은 관련성을 보였다.
1. CHME_LICE_COUNT
2. HOSP_DVSN_VARIES
3. HOSP_VARIES
4. TOTAL_VLID_HOSP_OTDA
5. CLAIM_CNT
