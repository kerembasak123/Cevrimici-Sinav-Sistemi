# Çevrimiçi Sınav Sistemi

![Flutter](https://img.shields.io/badge/Flutter-%2302569B.svg?style=flat&logo=flutter&logoColor=white) ![Firebase](https://img.shields.io/badge/Firebase-%23FFCA28.svg?style=flat&logo=firebase&logoColor=black) ![GitHub](https://img.shields.io/badge/GitHub-%23121011.svg?style=flat&logo=github&logoColor=white)

## Proje Özeti

**Çevrimiçi Sınav Sistemi**, **Flutter** ile geliştirilmiş, öğrenciler, öğretmenler ve yöneticiler için çapraz platform bir mobil uygulamadır. Öğrencilerin sınavlara katılmasına, öğretmenlerin soru yönetimine ve sonuç analizine, yöneticilerin ise kullanıcı hesaplarını denetlemesine olanak tanır. Sistem, **Firebase Firestore** (NoSQL) ile gerçek zamanlı veri entegrasyonu, **MVVM** mimari deseni ve motivasyonel alıntılar için **Quotable API** entegrasyonu kullanır. Proje, **Jira** ile **Kanban** tabanlı Çevik (Agile) metodoloji, **Git Flow** versiyon kontrol stratejisi ve **GitHub Actions** ile CI/CD pipeline üzerinden dağıtım ile geliştirilmiştir.

Bu proje, BMÜ326 dersi dönem ödevi olarak, modern yazılım mühendisliği uygulamalarına uygun şekilde hazırlanmıştır.

---

## İçindekiler

- [Özellikler](#özellikler)
- [Kurulum](#kurulum)
- [Kullanım](#kullanım)
- [Proje Yapısı](#proje-yapısı)
- [Katkıda Bulunma](#katkıda-bulunma)
- [Lisans](#lisans)
- [İletişim](#iletişim)

---

## Özellikler

- **Kullanıcı Rolleri**:
  - **Öğrenciler**: E-posta veya Google OAuth ile kayıt/giriş, sınavlara katılma, sonuçları görüntüleme, oyunlaştırma ile rozet kazanma.
  - **Öğretmenler**: Sınav oluşturma/düzenleme, CSV ile toplu soru yükleme, detaylı analizler (örn. sınıf performansı).
  - **Yöneticiler**: Kullanıcı hesaplarını yönetme, sınav ayarlarını yapılandırma, kullanım raporları oluşturma.

- **Temel İşlevler**:
  - Çoktan seçmeli, doğru/yanlış ve kısa cevap gibi çeşitli soru türleri.
  - Kopya çekmeyi önlemek için rastgele soru ve cevap sırası.
  - Zamanlayıcı tabanlı sınavlar, süre bitiminde otomatik gönderim.
  - Çevrimdışı mod: Sınavlar çevrimdışı yapılabilir, cevaplar çevrimiçi olunduğunda senkronize edilir.
  - Gerçek zamanlı sonuç hesaplama ve performans analizleri.

- **Ek Özellikler**:
  - **Quotable API** entegrasyonu ile motivasyonel alıntılar.
  - Sınav hatırlatıcıları ve sonuç duyuruları için push bildirimleri.
  - Erişilebilirlik: Ekran okuyucu desteği ve yüksek kontrast modu.

- **Teknik Özellikler**:
  - **Flutter** ile Android için geliştirildi.
  - **Firebase Firestore** ile NoSQL veritabanı.
  - **MVVM** mimarisi ile **Singleton** ve **Repository** tasarım desenleri.
  - **Git Flow** dallanma stratejisi (main, develop, feature, release).
  - **GitHub Actions** ile otomatik test ve dağıtım için CI/CD pipeline.
  - En az %70 kod kapsamı ile birim ve widget testleri.

---

## Kurulum

### Gereksinimler
- **Flutter SDK**: Sürüm 3.0 veya üstü ([Flutter Kurulumu](https://flutter.dev/docs/get-started/install)).
- **Dart**: Flutter ile birlikte gelir.
- **Firebase Hesabı**: Firestore ve Kimlik Doğrulama için ([Firebase Konsolu](https://console.firebase.google.com/)).
- **IDE**: Flutter/Dart eklentileri ile Android Studio veya VS Code.
- **Git**: Depoyu klonlamak için.

### Adımlar
1. **Depoyu Klonlayın**:
   ```bash
   git clone https://github.com/kullanici-adiniz/cevrimeici-sinav-sistemi.git
   cd cevrimeici-sinav-sistemi
   ```

2. **Bağımlılıkları Yükleyin**:
   ```bash
   flutter pub get
   ```

3. **Firebase’ı Kurun**:
   - [Firebase Konsolu](https://console.firebase.google.com/)'nda yeni bir proje oluşturun.
   - Android ve/veya iOS uygulamasını Firebase projenize ekleyin.
   - `google-services.json` (Android) veya `GoogleService-Info.plist` (iOS) dosyasını indirin ve uygun dizinlere yerleştirin (`android/app` veya `ios/Runner`).
   - Firebase CLI’yi kurun ve FlutterFire’ı yapılandırın:
     ```bash
     npm install -g firebase-tools
     dart pub global activate flutterfire_cli
     flutterfire configure
     ```

4. **Uygulamayı Çalıştırın**:
   ```bash
   flutter run
   ```

**Not**: Bir emülatör veya fiziksel cihazın bağlı olduğundan emin olun. Firebase kurulum detayları için [FlutterFire Belgeleri](https://firebase.flutter.dev/)’ni inceleyin.

---

## Kullanım

1. **Uygulamayı Çalıştırma**:
   - `flutter run` komutuyla uygulamayı bir emülatör veya cihazda başlatın.
   - Alternatif olarak, dağıtım sonrası [Vercel/Firebase Hosting URL](#) adresinden erişin (dağıtımdan sonra eklenecek).

2. **Kullanıcı Akışları**:
   - **Öğrenciler**:
     - E-posta/Google ile kayıt olun veya giriş yapın.
     - Mevcut sınavları görüntüleyin, zamanlı sınavlara katılın ve sonuçları görün.
     - Performans analizlerini ve kazanılan rozetleri kontrol edin.
   - **Öğretmenler**:
     - Giriş yapın, sınav oluşturun/düzenleyin veya CSV ile soru yükleyin.
     - Öğrenci sonuçlarını ve sınıf performans analizlerini görüntüleyin.
   - **Yöneticiler**:
     - Giriş yapın, kullanıcı hesaplarını yönetin ve sınav ayarlarını yapılandırın.

3. **Örnek**:
   - Bir öğrenci giriş yapar, “Matematik Sınavı” adlı bir sınavı (10 soru, 30 dakika) seçer, soruları yanıtlar ve gönderir. Uygulama puanı anında hesaplar ve Quotable API’den bir motivasyonel alıntı gösterir.

---

## Proje Yapısı

```plaintext
cevrimeici-sinav-sistemi/
├── lib/
│   ├── src/
│   │   ├── models/         # Veri modelleri (örn. User, Exam, Result)
│   │   ├── view_models/    # İş mantığı (örn. AuthViewModel, ExamViewModel)
│   │   ├── views/          # Kullanıcı arayüzü ekranları (örn. LoginScreen, ExamScreen)
│   │   ├── repositories/   # Veri erişimi (örn. AuthRepository, ExamRepository)
│   ├── main.dart           # Uygulama giriş noktası
├── docs/                   # Belgeler (örn. veritabanı şeması, API kullanımı)
├── test/                   # Birim ve widget testleri
├── .github/                # CI/CD iş akışları (örn. ci.yml)
├── README.md               # Proje dokümantasyonu
├── pubspec.yaml            # Bağımlılıklar ve meta veriler
```

---

## Katkıda Bulunma

Çevrimiçi Sınav Sistemi’ni geliştirmek için katkılarınızı bekliyoruz! Aşağıdaki adımları izleyin:

1. **Depoyu Fork Edin**:
   - GitHub’da “Fork” butonuna tıklayarak deponun bir kopyasını oluşturun.

2. **Özellik Dalı Oluşturun**:
   ```bash
   git checkout -b feature/ozellik-adi
   ```

3. **Değişiklikleri Kaydedin**:
   - Açık ve açıklayıcı commit mesajları kullanın (örn. `feat: kullanıcı kayıt formu eklendi`).
   ```bash
   git commit -m "feat: özellik açıklaması"
   ```

4. **Push Yapın ve Pull Request Oluşturun**:
   ```bash
   git push origin feature/ozellik-adi
   ```
   - GitHub’da bir Pull Request (PR) oluşturun ve değişikliklerinizi tarif edin.
   - PR’nin bir Jira görevi/story’si ile bağlantılı olduğundan emin olun (varsa).

5. **Kod İncelemesi**:
   - En az bir ekip üyesi PR’nizi inceleyecek.
   - Geri bildirimleri adresleyin ve gerekli değişiklikleri yapın.

6. **Birleştirme**:
   - Onay alındıktan sonra PR, `develop` dalına birleştirilecek.

**Kurallar**:
- [Git Flow](#) dallanma stratejisini takip edin.
- Yeni özellikler için birim testleri yazın (%70+ kapsama hedefleyin).
- Commit mesajlarını açık ve tutarlı tutun.

---

## Lisans

Bu proje **MIT Lisansı** ile lisanslanmıştır. Detaylar için [LICENSE](LICENSE) dosyasını inceleyin.

---

## İletişim

Sorularınız veya geri bildirimleriniz için proje ekibiyle iletişime geçin:
- **E-posta**: [e-posta-adresiniz@ornek.com](mailto:e-posta-adresiniz@ornek.com)
- **GitHub Issues**: Depoda bir [sorun](https://github.com/kullanici-adiniz/cevrimeici-sinav-sistemi/issues) oluşturun.

Çevrimiçi Sınav Sistemi’ni incelediğiniz için teşekkürler! 🎓
