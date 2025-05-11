---
layout: post
title: "[문제해결] PDF 파일처리 성능 개선기" 
description: 마켓빌 프로젝트에서
date: 2022-07-01 17:01:28 -0400
tags: []
categories: [문제해결, 서버 성능최적화]
---

# 영수증 PDF 대량 파일 처리 성능 개선기

![Banner](https://d585tldpucybw.cloudfront.net/sfimages/default-source/productsimages/telerik-document-processing-libraries/3-3-pdfprocessing---geometries.png?sfvrsn=3fa88917_0)

### **문제**

- 기존에는 코어 API 서버를 제작하느라 영수증 처리 영역을 잠시 미뤄두고 있었는데, PM 팀원이 python으로 돕고자 영수증 처리 서버를 제작한 것을 발견.
- 로직상 처리되는 양상을 확인해보니 google spreadsheet API를 통해 구글사의 API를 활용하여 원격으로 엑셀파일을 만들고, 이를 pdf로 export하는 방식을 채택하는 것으로 확인.
- 작동시간을 보니 1건당 60초 정도 걸리는 것을 발견. 이는 사용자 입장에서 심리적으로 상당한 불편함을 초래할 것이고 아무리 해도 UX로는 해결하기 힘들어서 성능 최적화를 하기로 판단.
- 그리고 이 API가 조건부 무료인 상황이라 사이드 프로젝트인 수준에서 장기적으로 부담을 줄 것으로 판단.

---

### **원인**

- 기존 방식은 Disk IO를 고민안해도 되지만 문제점으로는 외부 API 호출에 따른 Network IO가 발생하고 절차상 Excel을 거쳐 PDF 파일을 만든다는 점에서 시간이 많이 걸릴 수 밖에 없다는 점이 있었음.
    
    ```python
        # gspread
        def get_formed_gspreadsheet_doc(self):
            # authenticate
            credential = ServiceAccountCredentials.from_json_keyfile_name(ReceiptServiceExcel.JSON_KEY_PATH,
                                                                          ReceiptServiceExcel.SCOPE)
            self.credential = credential
            gc: Client = gspread.authorize(credential)
    
            # create spreadsheet using pre-created-form
            doc: Spreadsheet = gc.open(self.receipt_form_file_name)
            new_spreadsheet = gc.copy(doc.id, title=self.file_name)
            new_spreadsheet.share(ReceiptServiceExcel.GOOGLE_SPREAD_SHEET_ACCOUNT, perm_type='user', role='writer')
            doc = gc.open(self.file_name)
            return doc
    ```
    
- 이를 비동기적으로 메세지큐로 분리하려고도 생각했으나 오버엔지니어링이기도 하고, 불필요한 절차가 원인이라고 판단하여 다른 방법을 고안할 필요가 있다고 판단.
- Google Spreadsheet API 호출의 **서버 대기 시간**과 **병렬 처리되지 않는 순차적 요청이 시간 증가의 원인으로 판단.**
- 엑셀 파일을 통해 PDF로 변환하는 과정에서 **엑셀 파일 포맷** 자체가 **구조적인 변환 비용**을 발생시킬 수 있음. 엑셀 파일은 데이터를 시각적으로 표현하기 위한 도구로, 내부적으로는 **데이터 및 포맷 정보**를 많이 포함하고 있어 PDF 변환 시 불필요한 처리가 발생할 가능성이 큼.
- **프로파일링 결과**: 로그 분석을 통해 Google API 호출에서 약 70%, 변환 과정에서 약 30%의 시간이 소요되는 것을 확인. 특히 100건 이상 처리 시 지수적으로 시간이 증가하는 현상을 발견.

---

### **대안**

- 메세지 큐를 사용해서 비동기로 호출하여 처리
    - `문제`: 근본적인 원인은 비효율적인 API 호출과 불필요한 다단계 절차이기에 이는 오버엔지니어링이 될 위험이 있음.
- Google Spreadsheet API를 비동기로 호출하여 처리
    - `문제`: 비동기 처리를 해도 외부 API 호출 자체의 시간은 단축되지 않으며, 사용자는 여전히 결과를 기다려야 함. 또한 외부 API 의존성과 비용 문제가 여전히 존재.
- **직접 PDF 생성 라이브러리 활용**
    - ReportLab과 같은 Python 네이티브 PDF 생성 라이브러리를 사용하여 JSON 데이터에서 직접 PDF 생성
    - `장점`: 외부 의존성 제거, 불필요한 중간 단계 제거, 완전한 제어 가능

---

### **해결**

- **ReportLab 기반 PDF 생성 시스템 개발**
    - PdfGenerator 클래스 설계: PDF 템플릿 컴포넌트화 및 재사용 가능한 구조로 설계
    - 최적화 기법 적용:
        1. BytesIO 스트림 활용한 메모리 효율적 처리
        2. TTF 폰트 최적화 및 사전 등록 처리
            
            ```python
            def __init__(self, file_name: str, directory: Union[None, str] = None):
                    ...
                    self.cm = 2.54
                    self.font_400 = PdfGenerator.NANUM_GOTHIC_400
                    self.font_600 = PdfGenerator.NANUM_GOTHIC_600
                    self.font_700 = PdfGenerator.NANUM_GOTHIC_700
                    # Setup fonts
                    pdfmetrics.registerFont(TTFont("nanum_gothic", f'{self.font_dir}/NanumGothic-Regular.ttf'))
                    pdfmetrics.registerFont(TTFont("nanum_gothic_600", f'{self.font_dir}/NanumGothic-Bold.ttf'))
                    pdfmetrics.registerFont(
                        TTFont("nanum_gothic_700", f'{self.font_dir}/NanumGothic-ExtraBold.ttf'))
            				...
            ```
            
        3. 영수증 요소별 모듈화를 통한 유지보수성 향상
            
            ```python
            def create_form_elements(self, ...):
                    try:
                        title = "영수증"
                        created_at = PdfGenerator._get_today()
            
                        header = self.create_header(title=title)
                        sub_header = self.create_sub_header(order_no=order_no, name=receipt_owner)
                        supply_section = self.create_supply_section(business_no=business_no,
                                                                    company_name=company_name,
                                                                    name=employer_name,
                                                                    address=address,
                                                                    business_category=business_category,
                                                                    business_sub_category=business_sub_category,
                                                                    stamp_img_url=stamp_img_url)
                        upper_tot_price_section = self.create_upper_tot_price_section(created_at=created_at, tot_price=tot_price,
                                                                                      etc=etc)
                        footer = self.create_footer(prev_balance=prev_balance, deposit=deposit, tot_price=tot_price,
                                                    balance=balance)
                        extra_footer = self.create_extra_footer(bank_account=bank_account)
            
                        return [header, sub_header, supply_section, upper_tot_price_section, footer, extra_footer]
                    except Exception as e:
                        logger.error(e)
                        raise
            ```
            
        4. 이미지 다운로드 및 처리 최적화
- **성능 최적화 포인트**:
    1. 불필요한 중간 단계(Excel) 제거로 변환 비용 감소
    2. 로컬 처리로 네트워크 지연 제거
    3. 효율적인 메모리 관리 (try-except-finally 패턴 적용)
    4. 이미지 리사이징 최적화 (원본 비율 유지하며 크기 조정)
    5. 테이블 구조 최적화로 렌더링 속도 향상
- **성능 개선 결과**:
    1. 1~2건 처리: **8초 → 0.486초** (16.4배 향상)
    2. 10건 처리: **86초 → 0.949초** (90.6배 향상)
    3. 50건 처리: **389초 → 3.5초** (111.1배 향상)
    4. 100건 처리: **처리 불가능 → 7.3초** (획기적 개선)

---

### **고찰**

이 프로젝트는 기존 **Google API 기반 PDF 생성 방식**에서 발생한 **심각한 성능 병목**과 **외부 의존성 문제**를 해결하는 과정에서 얻은 교훈입니다:

- **외부 API 의존성 제거의 가치**: Google Spreadsheet API를 ReportLab으로 대체함으로써, **처리 시간**을 **1~2건 기준 8초→0.486초**(16.4배 향상)로 단축했습니다. 이는 사용자 경험 측면에서 극적인 개선을 가져왔습니다.
- **중간 단계 제거의 효과**: Excel 파일 생성이라는 불필요한 중간 과정을 제거하고 JSON에서 직접 PDF를 생성하는 방식으로 전환함으로써, **50건 처리 시간**을 **389초→3.5초**(111.1배 향상)로 단축했습니다.
- **기술 선택의 중요성**: 외부 API 대신 직접 제어 가능한 라이브러리를 선택함으로써 성능과 비용 측면에서 큰 차이를 만들어냈습니다. 특히 **기존에 불가능했던 100건 처리**가 **7.3초** 만에 가능해진 점이 이를 증명합니다.
- **프로파일링과 데이터 기반 의사결정**: 로그 분석을 통해 외부 API 호출이 전체 처리 시간의 70%를 차지한다는 사실을 발견했고, 이를 집중적으로 개선함으로써 **10건 처리 시간**을 **86초→0.949초**(90.6배 향상)로 단축할 수 있었습니다.
- **불필요한 단계 제거의 효율성**: "더 빠르게 하는 방법"보다 "꼭 필요한가?"라는 질문이 더 중요함을 깨달았습니다. Excel 변환이라는 불필요한 단계를 제거하는 단순한 접근이 복잡한 최적화보다 훨씬 효과적이었습니다.
- **외부 의존성 관리의 중요성**: 외부 서비스에 의존하면 비용, 성능, 안정성 측면에서 통제력을 잃게 됩니다. 이번 프로젝트를 통해 핵심 기능은 가능한 내재화하는 것이 장기적으로 유리함을 직접 경험했습니다.
- **성능과 코드 품질 사이의 균형**: PDF 생성 로직을 컴포넌트화하고 재사용 가능한 구조로 설계함으로써, 성능을 개선하면서도 유지보수성을 높이는 균형점을 찾을 수 있었습니다.

결론적으로, 이 경험은 성능 최적화를 위해 가장 먼저 아키텍처 레벨에서 병목 지점을 파악하고 제거하는 접근법의 중요성을 명확히 보여주었습니다. 또한 기술적 결정이 비즈니스 가치에 직접적인 영향을 미친다는 사실을 체감할 수 있는 값진 경험이었습니다.