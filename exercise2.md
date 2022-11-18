# ISP Documentation for normalizeOpts(options) 
Pada direktori js/src/core/options.js

## Input Domain Model

| Characteristics                           | b1          | b2            | b3              |
|-------------------------------------------|-------------|---------------|-----------------|
| q1 = "Banyak elemen dictionary"           | 0 elemen    | 1 elemen      | >1 elemen       |
| q2 = "Keberadaan letak karakter dash (-)" | Tidak ada   | Pada 1 elemen | Pada >1 elemen  |
| q3 = "Banyak karakter dash (-)"           | 0 ditemukan | 1 ditemukan   | >1 ditemukan    |

Input domain dari parameter options fungsi normalizeOpts tersebut adalah sebuah dictionary atau object dengan key:string dan value:any. Fungsi ini digunakan untuk mengubah semua karakter dash (-) pada key dictionary menjadi underscore (_). 

Partisi dibagi menjadi 3 karakteristik, berdasarkan elemen dictionary karena isinya bisa berbagai macam banyak elemen, lalu berdasarkan letak kebaradaan dash yang bisa pada beberapa elemen berbeda, dan banyak karakter dash yang ada pada input keseluruhan. Dengan ketiga karakteristik ini dikombinasikan akan cukup sensitif untuk memodelkan kemungkinan test case karena pengubahan karakter dash nantinya akan terpastikan berjalan pada setiap elemen-elemen pada dictionary, bahkan juga untuk beberapa dash yang ada pada satu elemen sekaligus secara rigorous.

## IDM Relabeling Table

| Characteristics | b1  | b2  | b3  |
|-----------------|-----|-----|-----|
| A               | A1  | A2  | A3  |
| B               | B1  | B2  | B3  |
| C               | C1  | C2  | C3  |

## Constraints

1. Isi dictionary 0 elemen implies tidak ada keberadaan karakter dash, ditemukan sebanyak 0 karakter dash (jika A1 maka hanya perlu A1B1C1, tidak perlu A1BxCx lain)
2. Isi dictionary 1 elemen implies karakter dash tidak pada >1 elemen (jika A2 maka hanya perlu A2B1Cx dan A2B2Cx, tidak perlu A2B3Cx)
3. Keberadaan karakter dash tidak ada implies 0 karakter dash ditemukan (jika B1 maka hanya perlu AxB1C1, tidak perlu AxB1Cx lain), dan sebaliknya (jika C1 maka hanya perlu AxB1C1, tidak perlu AxBxC1 lain)
4. Ditemukan 1 karakter dash implies dash hanya ada pada 1 elemen (jika C2 maka hanya perlu AxB2C2, tidak perlu AxBxC2 lain)

## Test Values and Example I/O

Criteria Used: ACoC

ACoC digunakan karena meskipun blok pada domain model terlihat cukup banyak (3x3x3 -> 27 total cases), namun dengan constraints yang ditetapkan berdasarkan pengetahuan akan problem domain, dapat diprune menjadi hanya tinggal 8 test cases. Dengan begitu menggunakan kriteria ACoC yang 'paling kuat' ini akan masih managable dengan keuntungan tingkat sensitivitas yang tinggi.

| Test Value  | Example Input           | Expected Output         |
|-------------|-------------------------|-------------------------|
| A1B1C1      | {}                      | {}                      |
| A2B1C1      | {'a_b': 1}              | {'a_b': 1}              |
| A2B2C2      | {'a-b': 1}              | {'a_b': 1}              |
| A2B2C3      | {'a-b-c': 2}            | {'a_b_c': 2}            |
| A3B1C1      | {'a_b': 1, 'c': 2}      | {'a_b': 1, 'c': 2}      |
| A3B2C2      | {'a': 1, 'b-c': 2}      | {'a': 1, 'b_c': 2}      | 
| A3B2C3      | {'a-b-c': 1, 'c': 2}    | {'a_b_c': 1, 'c': 2}    | 
| A3B3C3      | {'a-b-c': 1, 'c-d': 2}  | {'a-b-c': 1, 'c-d': 2}  | 
