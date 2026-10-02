# Лаборатори №5 — API систем тест: Postman ба Newman

## 1. Оюутны мэдээлэл

- **Оюутны нэр:** [Таны нэр]
- **Оюутны код:** B232270022
- **Хичээл:** F.CSA313 — Программ хангамжийн тест
- **Лаборатори:** №5
- **Сэдэв:** API систем тест — Postman ба Newman

---

## 2. Лабораторийн зорилго

Энэхүү лабораторийн ажлын зорилго нь REST API системийн функциональ шаардлагуудыг
Postman ашиглан тестлэх, тестийн oracle буюу хүлээгдэж буй үр дүнг тодорхойлох,
мөн Newman ашиглан Postman collection-ийг command line орчноос автомат ажиллуулахад оршино.

Туршилтаар `/registrations` endpoint-ийн амжилттай бүртгэл,
оюутан байхгүй байх, оюутан идэвхгүй байх, хичээл байхгүй байх,
prerequisite дутуу байх, буруу request болон олон алдаатай request зэрэг
тохиолдлуудыг шалгасан.

---

## 3. Ашигласан технологи

| Технологи | Хувилбар |
|---|---|
| Node.js | v26.0.0 |
| npm | 11.12.1 |
| Newman | 6.2.2 |
| Postman | Postman |
| API | Node.js REST API |
| API URL | http://localhost:3000 |

---

## 4. API систем

Лабораторид локал REST API ашигласан.

API server-ийг дараах командаар ажиллуулна:

```bash
node server.js
```

API:

```text
http://localhost:3000
```

Үндсэн тестлэсэн endpoint:

```text
POST /registrations
```

Мөн тест бүрийн өмнө шаардлагатай student болон course мэдээллийг:

```text
PUT /students/:studentID
PUT /courses/:courseID
```

endpoint-үүдээр тохируулсан.

---

## 5. API-ийн үндсэн үр дүн

### Амжилттай бүртгэл

```text
HTTP 201
{
  "result": "OK",
  "registrationID": ...
}
```

### Оюутан олдохгүй

```text
HTTP 200
{
  "result": "ERROR_NO_STUDENT"
}
```

### Оюутан идэвхгүй

```text
HTTP 200
{
  "result": "ERROR_INACTIVE_STUDENT"
}
```

### Хичээл олдохгүй

```text
HTTP 200
{
  "result": "ERROR_NO_COURSE"
}
```

### Prerequisite дутуу

```text
HTTP 200
{
  "result": "ERROR_PREREQUISITES",
  "missing": [...]
}
```

### Request-ийн шаардлагатай field дутуу

```text
HTTP 400
{
  "result": "ERROR_BAD_REQUEST"
}
```

---

## 6. Choice / Value analysis

| Choice | Values |
|---|---|
| Student status | active, inactive, байхгүй |
| Student coursesTaken | CS201-тэй, CS201-гүй, хэсэгчлэн prerequisite хангасан |
| Course existence | байгаа, байхгүй |
| Course prerequisites | prerequisite байхгүй, нэг prerequisite, олон prerequisite |
| Request fields | studentID + courseID, studentID дутуу, courseID дутуу |
| Registration result | OK, ERROR_NO_STUDENT, ERROR_INACTIVE_STUDENT, ERROR_NO_COURSE, ERROR_PREREQUISITES, ERROR_BAD_REQUEST |
| HTTP status | 200, 201, 400 |
| Multiple errors | studentID болон courseID хоёулаа буруу |

---

## 7. Test specification

Нийт 10 үндсэн registration test specification боловсруулсан.

| № | Test specification | Input / Condition | Expected Status | Expected Result |
|---|---|---|---:|---|
| 01 | Successful Registration | Active student + existing course + prerequisite satisfied | 201 | `OK` |
| 02 | Inactive Student | Student status = inactive | 200 | `ERROR_INACTIVE_STUDENT` |
| 03 | Student Not Found | Student байхгүй | 200 | `ERROR_NO_STUDENT` |
| 04 | Course Not Found | Course байхгүй | 200 | `ERROR_NO_COURSE` |
| 05 | Missing Prerequisite | Student prerequisite аваагүй | 200 | `ERROR_PREREQUISITES` |
| 06 | Partial Prerequisite | Олон prerequisite-ээс нэг нь дутуу | 200 | `ERROR_PREREQUISITES` |
| 07 | Missing StudentID | `studentID` field байхгүй | 400 | `ERROR_BAD_REQUEST` |
| 08 | Missing CourseID | `courseID` field байхгүй | 400 | `ERROR_BAD_REQUEST` |
| 09 | No Prerequisite Course | Course prerequisite байхгүй | 201 | `OK` |
| 10 | Double Error | Student болон course хоёулаа байхгүй | 200 | `ERROR_NO_STUDENT` |

---

## 8. Postman collection

Үндсэн collection:

```text
lab05-collection.json
```

FAIL collection:

```text
lab05-collection-fail.json
```

Collection-д дараах төрлийн request-үүд орсон:

- Student setup
- Course setup
- Successful registration
- Inactive student
- Student not found
- Course not found
- Missing prerequisite
- Partial prerequisite
- Missing StudentID
- Missing CourseID
- No prerequisite
- Double error

Test бүрт response-ийн:

1. HTTP status
2. `result` field

гэсэн хоёр oracle ашигласан.

Ингэснээр үндсэн registration test-үүдээс нийт 20 assertion ажилласан бөгөөд
Postman-ийн default `Get data` болон `Post data` request-үүд тус бүр нэг assertion
ажиллуулсан тул Newman-ийн нийт assertion count **22** болсон.

---

## 9. Test oracle

Жишээ нь successful registration-ийн test:

```javascript
pm.test("Status is 201", function () {
    pm.response.to.have.status(201);
});

pm.test("Result is OK", function () {
    const json = pm.response.json();
    pm.expect(json.result).to.eql("OK");
});
```

Ингэснээр зөвхөн HTTP status биш, response-ийн бизнесийн үр дүнг мөн шалгасан.

---

# 10. Newman PASS test

PASS collection:

```text
lab05-collection.json
```

Ажиллуулсан command:

```bash
newman run lab05-collection.json 2>&1 | tee results/newman-pass.txt
```

### Үр дүн

```text
iterations                1
requests                 23
test-scripts             12
prerequest-scripts        0
assertions               22
failed                    0
```

### Exit code

```text
exit=0
```

### PASS дүгнэлт

- 23 request ажилласан.
- 22 assertion ажилласан.
- 0 assertion failed.
- Newman exit code 0 гарсан.
- Иймээс үндсэн collection-ийн бүх тест амжилттай ажилласан.

---

# 11. Newman FAIL test

FAIL collection:

```text
lab05-collection-fail.json
```

FAIL test-ийн зорилгоор `01 - Successful Registration` тестийн status oracle-ийг
зориудаар буруу болгосон.

Зөв утга:

```text
201
```

FAIL collection дээр зориудаар:

```text
200
```

гэж тохируулсан.

Ажиллуулсан command:

```bash
newman run lab05-collection-fail.json 2>&1 | tee results/newman-fail.txt
```

### Үр дүн

```text
iterations                1
requests                 23
test-scripts             12
prerequest-scripts        0
assertions               22
failed                    1
```

Failed assertion:

```text
AssertionError: Status is 200
expected response to have status code 200 but got 201
```

### Exit code

```text
exit=1
```

### FAIL дүгнэлт

FAIL collection дээр зориудаар буруу oracle ашигласнаар нэг assertion failed болсон.
Newman энэ failure-ийг зөв илрүүлж, exit code 1 буцаасан.

---

# 12. Newman DOWN test

DOWN test хийхийн өмнө Node.js API server-ийг зогсоосон.

```text
Ctrl + C
```

Дараа нь үндсэн collection-ийг дахин ажиллуулсан:

```bash
newman run lab05-collection.json 2>&1 | tee results/newman-down.txt
```

### Үр дүн

API server ажиллахгүй байсан тул:

```text
connect ECONNREFUSED 127.0.0.1:3000
```

гэсэн connection error гарсан.

Summary:

```text
iterations                1
requests                 23
failed requests          21
test-scripts             12
assertions               22
failed assertions        20
```

### Exit code

```text
exit=1
```

### DOWN дүгнэлт

API server унтарсан үед Newman localhost:3000 руу холбогдож чадаагүй.
Үүний үр дүнд `ECONNREFUSED` алдаа гарсан бөгөөд Newman exit code 1 буцаасан.

---

# 13. Test result summary

| Test run | Collection | Requests | Assertions | Failed | Exit code |
|---|---|---:|---:|---:|---:|
| PASS | `lab05-collection.json` | 23 | 22 | 0 | 0 |
| FAIL | `lab05-collection-fail.json` | 23 | 22 | 1 | 1 |
| DOWN | `lab05-collection.json` | 23 | 22 | 20 assertions* | 1 |

> DOWN test-ийн failed count нь server connection error-уудаас үүссэн. Үндсэн шинж тэмдэг нь `ECONNREFUSED 127.0.0.1:3000` байсан.

---

# 14. Results files

Туршилтын үр дүнг `results` folder-т хадгалсан.

```text
results/
├── newman-pass.txt
├── newman-fail.txt
└── newman-down.txt
```

### PASS

```text
results/newman-pass.txt
```

### FAIL

```text
results/newman-fail.txt
```

### DOWN

```text
results/newman-down.txt
```

---

# 15. Repository structure

```text
lab5-api/
├── results/
│   ├── newman-pass.txt
│   ├── newman-fail.txt
│   └── newman-down.txt
│
├── lab05-collection.json
├── lab05-collection-fail.json
├── server.js
└── README.md
```

---

# 16. Ажиллуулах заавар

## 16.1 Repository clone хийх

```bash
git clone https://github.com/Hirito572/lab5-api.git
cd lab5-api
```

## 16.2 API server ажиллуулах

```bash
node server.js
```

API:

```text
http://localhost:3000
```

## 16.3 Newman version шалгах

```bash
newman -v
```

Гаралт:

```text
6.2.2
```

## 16.4 PASS test

```bash
newman run lab05-collection.json
```

## 16.5 FAIL test

```bash
newman run lab05-collection-fail.json
```

## 16.6 DOWN test

API server-ийг зогсоосны дараа:

```bash
newman run lab05-collection.json
```

---

# 17. Git commits

Лабораторийн ажлыг нэг дор commit хийхгүйгээр үе шаттайгаар repository-д хадгалсан.

Үндсэн commit-үүд:

```text
chore: add lab05 API server
test: add lab05 postman collection
test: add newman pass results
test: add newman fail results
test: add newman down result
```

Repository:

https://github.com/Hirito572/lab5-api

---

# 18. Дүгнэлт

Энэхүү лабораторийн ажлаар REST API системийг Postman ашиглан функциональ байдлаар тестэлсэн.
`/registrations` endpoint-ийн амжилттай болон алдаатай олон төрлийн нөхцөлүүдийг тодорхойлсон.
Active student, inactive student, байхгүй student, байхгүй course болон prerequisite нөхцөлүүдийг тус тусад нь шалгасан.
Мөн `studentID` болон `courseID` field дутуу request-үүдийг 400 status болон `ERROR_BAD_REQUEST` үр дүнгээр шалгасан.
Олон алдаа зэрэг тохиолдсон үед API-ийн буцааж буй үр дүнг тусгай test specification болгон шалгасан.
Postman collection-ийг Newman ашиглан command line орчноос ажиллуулж, нийт 22 assertion гүйцэтгэснийг баталгаажуулсан.
PASS collection нь 0 failed assertion болон exit code 0 гаргасан.
Буруу oracle бүхий FAIL collection нь assertion failure-ийг зөв илрүүлж, exit code 1 гаргасан.
API server унтарсан үед Newman `ECONNREFUSED` алдааг илрүүлж, exit code 1 буцаасан.
Ингэснээр Postman болон Newman ашиглан API-ийн хэвийн ажиллагаа, алдааны нөхцөл болон server unavailable нөхцөлүүдийг системтэйгээр тестлэх дадлага хийсэн.