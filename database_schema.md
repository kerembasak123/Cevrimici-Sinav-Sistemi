# Çevrimiçi Sınav Sistemi - Veritabanı Şeması

Bu belge, **Çevrimiçi Sınav Sistemi** projesi için Firebase Firestore (NoSQL) veritabanı şemasını tanımlar. Şema, proje gereksinimlerine uygun olarak tasarlanmıştır ve öğrenci, öğretmen ve yönetici rollerini destekleyen kullanıcı yönetimi, sınav yönetimi ve sonuç takibi işlevlerini kapsar. Firestore'un referans tabanlı ilişkisel veri yapısı kullanılarak koleksiyonlar arasında bağlantılar kurulmuştur.

---

## 🔍 Genel Bakış

- **Veritabanı:** Firebase Firestore (NoSQL, gerçek zamanlı veri senkronizasyonu)
- **Amaç:** 
  - Kullanıcı verilerini, sınavları ve sınav sonuçlarını saklamak
  - Performans analizleri ve oyunlaştırma özelliklerini desteklemek
- **Koleksiyonlar:**
  - `users`: Kullanıcı bilgilerini saklar (öğrenci, öğretmen, yönetici)
  - `exams`: Sınav detaylarını ve soruları saklar
  - `results`: Öğrencilerin sınav sonuçlarını saklar
- **Referanslar:** Koleksiyonlar arasında ilişkisel bağlantılar için Firestore `Reference` türü kullanılır.

---

## 📁 Koleksiyonlar ve Alanlar

### 1. `users` Koleksiyonu

Kullanıcı bilgilerini saklar. Her kullanıcı, öğrenci, öğretmen veya yönetici rolüne sahiptir.

- **Yol:** `/users/{userId}`

| Alan   | Tür     | Açıklama                                    |
|--------|---------|----------------------------------------------|
| id     | String  | Kullanıcının benzersiz kimliği (Auth UID)   |
| role   | String  | Kullanıcı rolü: `student`, `teacher`, `admin` |
| email  | String  | Kullanıcının e-posta adresi                 |
| name   | String  | Kullanıcının tam adı                        |

**Örnek Doküman:**

# Veritabanı Şeması ve Dokümantasyon

## 1. users Koleksiyonu
Kullanıcı bilgilerini saklar.

**Yol:** `/users/{userId}`

| Alan   | Tür    | Açıklama                     |
|--------|--------|------------------------------|
| id     | String | Kullanıcının benzersiz kimliği |
| role   | String | Kullanıcı rolü (örneğin "student") |
| email  | String | Kullanıcı e-posta adresi     |
| name   | String | Kullanıcı adı                |

**Örnek Doküman:**
```json
{
  "id": "abc123",
  "role": "student",
  "email": "ogrenci@ornek.com",
  "name": "Ali Veli"
}
{
  "id": "exam001",
  "title": "Matematik Final",
  "duration": 60,
  "questions": [
    {
      "questionId": "q1",
      "type": "mcq",
      "text": "2 + 2 kaçtır?",
      "options": ["2", "4", "6", "8"],
      "correctAnswer": "4"
    },
    {
      "questionId": "q2",
      "type": "true_false",
      "text": "Dünya düz müdür?",
      "correctAnswer": false
    }
  ]
}
{
  "studentId": "/users/abc123",
  "examId": "/exams/exam001",
  "score": 90,
  "answers": [
    {
      "questionId": "q1",
      "answer": "4"
    },
    {
      "questionId": "q2",
      "answer": false
    }
  ]
}
