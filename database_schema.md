# Çevrimiçi Sınav Sistemi - Veritabanı Şeması

Bu belge, **Çevrimiçi Sınav Sistemi** projesi için **Firebase Firestore** (NoSQL) veritabanı şemasını tanımlar. Şema, proje gereksinimlerine uygun olarak tasarlanmıştır ve öğrenci, öğretmen ve yönetici rollerini destekleyen kullanıcı yönetimi, sınav yönetimi ve sonuç takibi işlevlerini kapsar. Firestore'un referans tabanlı ilişkisel veri yapısı kullanılarak koleksiyonlar arasında bağlantılar kurulmuştur.

## Genel Bakış

- **Veritabanı**: Firebase Firestore (NoSQL, gerçek zamanlı veri senkronizasyonu).
- **Amaç**: Kullanıcı verilerini, sınavları ve sınav sonuçlarını saklamak; performans analizleri ve oyunlaştırma özelliklerini desteklemek.
- **Koleksiyonlar**:
  - `users`: Kullanıcı bilgilerini saklar (öğrenci, öğretmen, yönetici).
  - `exams`: Sınav detaylarını ve soruları saklar.
  - `results`: Öğrencilerin sınav sonuçlarını saklar.
- **Referanslar**: Koleksiyonlar arasında ilişkisel bağlantılar için Firestore `Reference` türü kullanılır.

## Koleksiyonlar ve Alanlar

### 1. `users` Koleksiyonu
Kullanıcı bilgilerini saklar. Her kullanıcı, öğrenci, öğretmen veya yönetici rolüne sahiptir.

**Yol**: `/users/{userId}`

**Alanlar**:
| Alan        | Tür           | Açıklama                                      |
|-------------|---------------|-----------------------------------------------|
| `id`        | String        | Kullanıcının benzersiz kimliği (Firebase Auth UID). |
| `role`      | String        | Kullanıcı rolü: `student`, `teacher`, `admin`. |
| `email`     | String        | Kullanıcının e-posta adresi.                  |
| `name`      | String        | Kullanıcının tam adı.                         |

**Örnek Doküman**:
```json
{
  "id": "abc123",
  "role": "student",
  "email": "ogrenci@ornek.com",
  " ]name": "Ali Veli"
}
```

### 2. `exams` Koleksiyonu
Sınav bilgilerini ve soruları saklar. Her sınav, birden fazla soru içerebilir.

**Yol**: `/exams/{examId}`

**Alanlar**:
| Alan          | Tür             | Açıklama                                      |
|---------------|-----------------|-----------------------------------------------|
| `id`          | String          | Sınavın benzersiz kimliği.                    |
| `title`       | String          | Sınav başlığı (örn. "Matematik Final").       |
| `duration`    | Number          | Sınav süresi (dakika cinsinden).              |
| `questions`   | Array<Object>   | Soru listesi (her soru bir nesne).            |

**Soru Nesnesi (questions[])**:
| Alan          | Tür             | Açıklama                                      |
|---------------|-----------------|-----------------------------------------------|
| `questionId`  | String          | Sorunun benzersiz kimliği.                    |
| `type`        | String          | Soru türü: `mcq`, `true_false`, `short_answer`. |
| `text`        | String          | Soru metni.                                   |
| `options`     | Array<String>   | Çoktan seçmeli seçenekler (mcq için).         |
| `correctAnswer` | String/Boolean | Doğru cevap (türe göre string veya boolean).  |

**Örnek Doküman**:
```json
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
```

### 3. `results` Koleksiyonu
Öğrencilerin sınav sonuçlarını saklar. Her sonuç, bir öğrenci ve sınavla ilişkilidir.

**Yol**: `/results/{resultId}`

**Alanlar**:
| Alan          | Tür             | Açıklama                                      |
|---------------|-----------------|-----------------------------------------------|
| `studentId`   | Reference       | `users` koleksiyonundaki öğrenci dokümanına referans. |
| `examId`      | Reference       | `exams` koleksiyonundaki sınav dokümanına referans. |
| `score`       | Number          | Sınav puanı (örn. 85).                        |
| `answers`     | Array<Object>   | Öğrencinin cevapları.                         |

**Cevap Nesnesi (answers[])**:
| Alan          | Tür             | Açıklama                                      |
|---------------|-----------------|-----------------------------------------------|
| `questionId`  | String          | Sorunun kimliği (exams.questions ile eşleşir). |
| `answer`      | String/Boolean  | Öğrencinin verdiği cevap.                     |

**Örnek Doküman**:
```json
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
```

## İlişkisel Veriler ve Referanslar

Firestore, NoSQL bir veritabanı olduğundan ilişkisel veriler **Reference** türü ile yönetilir:
- **`results.studentId`**: `users` koleksiyonundaki bir kullanıcı dokümanına işaret eder (örn. `/users/abc123`).
- **`results.examId`**: `exams` koleksiyonundaki bir sınav dokümanına işaret eder (örn. `/exams/exam001`).
- Bu referanslar, Firestore sorgularında ilişkili verilerin çekilmesini sağlar. Örneğin:
  ```dart
  // Öğrencinin sınav sonuçlarını çekme
  FirebaseFirestore.instance
      .collection('results')
      .where('studentId', isEqualTo: FirebaseFirestore.instance.doc('/users/abc123'))
      .get();
  ```

## Tasarım Notları

- **NoSQL Avantajları**: Firestore’un esnek yapısı, sınav sorularının dinamik olarak eklenmesine ve oyunlaştırma verilerinin (rozetler, puanlar) kolayca yönetilmesine olanak tanır.
- **Gerçek Zamanlı Senkronizasyon**: Firestore’un gerçek zamanlı güncelleme özelliği, sınav sonuçlarının anında hesaplanmasını ve öğretmen panosunda canlı analizlerin görüntülenmesini destekler.
- **Güvenlik**: Firestore Güvenlik Kuralları ile rol tabanlı erişim kontrolü (RBAC) uygulanacaktır:
  - Öğrenciler yalnızca kendi sonuçlarını görebilir.
  - Öğretmenler, kendi sınavlarına ve ilgili sonuçlara erişebilir.
  - Yöneticiler tüm verilere erişebilir.
- **Ölçeklenebilirlik**: Firestore, 100 eşzamanlı kullanıcıyı destekleyecek şekilde optimize edilmiştir (gereksinim: sınav yükleme süresi < 2 saniye).

## Belgeler ve Kaynaklar

- **Firestore Belgeleri**: [Firebase Firestore Docs](https://firebase.google.com/docs/firestore)
- **Eğitim Videoları**: Flutter ile Firestore entegrasyonu için [YouTube Flutter Firestore Tutorials](https://www.youtube.com/results?search_query=flutter+firestore+tutorial)
- **Güvenlik Kuralları**: [Firestore Security Rules](https://firebase.google.com/docs/firestore/security/get-started)

Bu şema, projenin tüm fonksiyonel gereksinimlerini (kullanıcı yönetimi, sınav yönetimi, sonuç analitiği) karşılar ve bonus puan için NoSQL (Firestore) kullanımını destekler.
