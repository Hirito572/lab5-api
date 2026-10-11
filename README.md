# Лаборатори №5 — API систем тест: Postman ба Newman

## 1. Оюутны мэдээлэл

- **Оюутны нэр:** Э. Мөнх-Очир
- **Оюутны код:** B232270022
- **Хичээл:** F.CSA313
- **Лабораторийн ажил:** №5
- **Сэдэв:** API систем тест — Postman ба Newman

## 2. Лабораторийн зорилго

Энэхүү лабораторийн ажлын зорилго нь REST API-ийн функциональ ажиллагааг Postman ашиглан тестлэх, хүлээгдэж буй үр дүнг oracle-аар шалгах, мөн Newman ашиглан collection-ийг командын мөрөөс автомат ажиллуулахад оршино.

Туршилтаар амжилттай бүртгэл, идэвхгүй болон байхгүй оюутан, байхгүй хичээл, prerequisite дутуу байх, шаардлагатай талбар дутуу байх болон олон алдаа зэрэг тохиолдох нөхцөлийг шалгасан.

## 3. Ашигласан технологи

| Технологи | Хувилбар / утга |
|---|---|
| Node.js (`node -v`) | `v26.0.0` |
| Newman (`newman -v`) | `6.2.2` |
| npm | `11.12.1` |
| API хаяг | `http://localhost:3000` |

Хувилбаруудыг дараах командаар шалгасан:

```bash
node -v
newman -v
```

## 4. API-ийн мэдээлэл

API серверийг дараах командаар ажиллуулна:

```bash
node server.js
```

Үндсэн endpoint:

```text
POST /registrations
```

Тестийн өмнө оюутан болон хичээлийн өгөгдлийг тохируулахад дараах endpoint-үүдийг ашигласан:

```text
PUT /students/:studentID
PUT /courses/:courseID
```

## 5. Choice / Value analysis

| Хувьсагч | Боломжит утгууд |
|---|---|
| Student status | active, inactive, байхгүй |
| Student coursesTaken | CS201 үзсэн, үзээгүй, хэсэгчлэн үзсэн |
| Course existence | байгаа, байхгүй |
| Course prerequisites | prerequisite байхгүй, нэг prerequisite, олон prerequisite |
| Request fields | хоёр талбар бүрэн, studentID дутуу, courseID дутуу |
| Алдааны нөхцөл | нэг алдаа, хоёр алдаа зэрэг |
| HTTP status | 200, 201, 400 |

## 6. Test specification

Нийт 10 үндсэн registration test боловсруулсан.

| № | Тест | Нөхцөл | Хүлээгдэж буй status | Хүлээгдэж буй result |
|---|---|---|---:|---|
| 01 | Successful Registration | Идэвхтэй оюутан, хичээл байгаа, prerequisite хангасан | 201 | `OK` |
| 02 | Inactive Student | Оюутан идэвхгүй | 200 | `ERROR_INACTIVE_STUDENT` |
| 03 | Student Not Found | Оюутан байхгүй | 200 | `ERROR_NO_STUDENT` |
| 04 | Course Not Found | Хичээл байхгүй | 200 | `ERROR_NO_COURSE` |
| 05 | Missing Prerequisite | Шаардлагатай prerequisite дутуу | 200 | `ERROR_PREREQUISITES` |
| 06 | Partial Prerequisite | CS201 үзсэн боловч CS202 дутуу | 200 | `ERROR_PREREQUISITES` |
| 07 | Missing StudentID | studentID талбар байхгүй | 400 | `ERROR_BAD_REQUEST` |
| 08 | Missing CourseID | courseID талбар байхгүй | 400 | `ERROR_BAD_REQUEST` |
| 09 | No Prerequisite Course | Хичээл prerequisite шаардахгүй | 201 | `OK` |
| 10 | Double Error | Оюутан болон хичээл хоёулаа байхгүй | 200 | `ERROR_NO_STUDENT` |

Тест 06-д `B232270026` оюутныг `PUT /students/B232270026` хүсэлтээр тухайн тестийн өмнө тохируулсан. Оюутан зөвхөн CS201 үзсэн бөгөөд CS317 хичээл CS201 болон CS202 гэсэн хоёр prerequisite шаарддаг. Иймээс CS202 дутуу гэсэн хариу гарах ёстой.

## 7. Newman-ийн үр дүн

### 7.1 PASS test

Ажиллуулсан команд:

```bash
newman run lab05-collection.json 2>&1 | tee results/newman-pass.txt
```

Үр дүн:

| Үзүүлэлт | Утга |
|---|---:|
| Iterations | 1 |
| Requests | 22 |
| Test scripts | 10 |
| Assertions | 20 |
| Failed assertions | 0 |
| Exit code | 0 |

PASS test-ийн бүх assertion амжилттай ажилласан.

### 7.2 FAIL test

FAIL collection дээр `01 - Successful Registration` тестийн status oracle-ийг зориудаар 201-ийн оронд 200 гэж өөрчилсөн.

Ажиллуулсан команд:

```bash
newman run lab05-collection-fail.json 2>&1 | tee results/newman-fail.txt
```

Үр дүн:

| Үзүүлэлт | Утга |
|---|---:|
| Requests | 22 |
| Assertions | 20 |
| Failed assertions | 1 |
| Exit code | 1 |

Алдааны мэдээлэл:

```text
expected response to have status code 200 but got 201
```

Энэ нь API амжилттай хариу өгсөн боловч зориудаар буруу oracle ашигласнаас assertion failed болсныг харуулсан.

### 7.3 DOWN test

DOWN test хийхийн өмнө API серверийг `Ctrl+C` ашиглан зогсоосон.

Ажиллуулсан команд:

```bash
newman run lab05-collection.json 2>&1 | tee results/newman-down.txt
```

Үр дүн:

| Үзүүлэлт | Утга |
|---|---:|
| Requests | 22 |
| Failed requests | 22 |
| Assertions | 20 |
| Failed assertions | 20 |
| Exit code | 1 |

Алдааны мэдээлэл:

```text
connect ECONNREFUSED 127.0.0.1:3000
```

Сервер унтарсан тул Newman API-тай холбогдож чадаагүй.

### 7.4 FAIL ба DOWN-ийн ялгаа

**FAIL test** нь API хариу өгсөн боловч хүлээгдэж буй үр дүнг зориудаар буруу oracle-той харьцуулж алдаа илрүүлдэг; харин **DOWN test** нь сервер унтарснаас хүсэлт API-д хүрч чадахгүй, `ECONNREFUSED` холболтын алдаа үүсгэдэг.

## 8. Үр дүнгийн файлууд

Туршилтын output-уудыг `results/` хавтсанд хадгалсан.

```text
results/
├── newman-pass.txt
├── newman-fail.txt
└── newman-down.txt
```

## 9. Repository бүтэц

```text
lab5-api/
├── .gitignore
├── README.md
├── server.js
├── lab05-collection.json
├── lab05-collection-fail.json
└── results/
    ├── newman-pass.txt
    ├── newman-fail.txt
    └── newman-down.txt
```

## 10. Дүгнэлт

Энэ лабораторийн ажлын хамгийн хэцүү хэсэг нь Postman collection-ийн хүсэлтүүдийг зөв дараалалд оруулж, тест бүрийг өмнөх тестийн өгөгдлөөс хамааралгүй ажиллуулах явдал байлаа. Тест 06-д оюутны setup хүсэлт дутуу байсан тул шинэ сервер дээр найдвартай ажиллахгүй байх эрсдэл илэрсэн. Үүнийг засаж, `B232270026` оюутны PUT setup-ийг prerequisite шалгах POST хүсэлтийн өмнө нэмсэн. Тестийн өгөгдлийг setup хүсэлтээр урьдчилан үүсгэснээр өмнөх run-ийн өгөгдлөөс хамаарах эрсдэлийг бууруулсан. CS317 хичээл хоёр prerequisite шаарддаг боловч оюутан зөвхөн CS201 үзсэн нөхцөлд CS202 дутуу гэсэн үр дүнг зөв шалгасан. PASS туршилтаар 22 хүсэлт, 20 assertion ажиллаж, ямар ч assertion failed болоогүй. FAIL туршилтаар зориуд буруу oracle ашиглахад нэг assertion failed болж, Newman exit code 1 буцаасан. DOWN туршилтаар сервер унтарсан үед `ECONNREFUSED` гарч, холболтын алдааг илрүүлсэн. Энэ туршилтууд нь oracle-ийн алдаа болон серверийн холболтын алдаа хоёр өөр шалтгаантай болохыг харуулсан. Ерөнхийдөө Postman болон Newman ашиглан API-ийн хэвийн ажиллагаа, алдааны нөхцөл болон сервер ажиллахгүй үеийн зан төлөвийг системтэйгээр шалгаж сурсан.
