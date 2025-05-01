# Çevrimiçi Sınav Sistemi - Mimari Planlama

Bu belge, **Çevrimiçi Sınav Sistemi** projesinin mimari tasarımını tanımlar. Proje, **Flutter** ile geliştirilmiş olup **MVVM (Model-View-ViewModel)** mimari deseni, **Repository** deseni ve **Singleton** deseni kullanılarak yapılandırılmıştır. Bu mimari, kodun sürdürülebilirliğini, test edilebilirliğini ve modülerliğini artırmayı amaçlar. Firebase servisleri ile entegrasyon, soyutlanmış veri erişimi ve kullanıcı arayüzü yönetimi bu tasarımın temel bileşenleridir.

## Mimari Genel Bakış

- **Platform**: Flutter (iOS ve Android için çapraz platform).
- **Mimari Desen**: MVVM (Model-View-ViewModel).
- **Tasarım Desenleri**:
  - **Repository**: Veritabanı (Firestore) ve kimlik doğrulama (Firebase Auth) işlemlerini soyutlar.
  - **Singleton**: Firebase servislerinin (örn. `FirebaseAuth`, `FirebaseFirestore`) tekil örneklerini yönetir.
- **Amaç**:
  - İş mantığını (ViewModel) ve veri erişimini (Repository) kullanıcı arayüzünden (View) ayırmak.
  - Kodun yeniden kullanılabilirliğini ve test edilebilirliğini artırmak.
  - Firebase entegrasyonunu merkezi ve güvenli bir şekilde yönetmek.

## Mimari Bileşenler

### 1. MVVM Deseni

MVVM, projenin temel mimari desenidir ve üç ana katmandan oluşur:

#### 1.1. Model (`lib/src/models`)
- **Açıklama**: Veri yapılarını temsil eden sınıflar. Firestore'dan gelen verileri nesnelere dönüştürür ve iş mantığı için temel sağlar.
- **Sınıflar**:
  - `User`: Kullanıcı bilgilerini temsil eder (id, role, email, name).
  - `Exam`: Sınav bilgilerini ve soruları temsil eder (id, title, duration, questions).
  - `Result`: Sınav sonuçlarını temsil eder (studentId, examId, score, answers).
- **Örnek**:
  ```dart
  class User {
    final String id;
    final String role; // student, teacher, admin
    final String email;
    final String name;

    User({required this.id, required this.role, required this.email, required this.name});

    factory User.fromJson(Map<String, dynamic> json) {
      return User(
        id: json['id'],
        role: json['role'],
        email: json['email'],
        name: json['name'],
      );
    }

    Map<String, dynamic> toJson() => {
          'id': id,
          'role': role,
          'email': email,
          'name': name,
        };
  }
  ```
- **Konum**: `lib/src/models/` (örn. `user.dart`, `exam.dart`, `result.dart`).

#### 1.2. ViewModel (`lib/src/view_models`)
- **Açıklama**: İş mantığını ve veri yönetimini üstlenir. View ile Model arasında aracıdır, kullanıcı arayüzünü veri değişikliklerine karşı reaktif tutar.
- **Sınıflar**:
  - `AuthViewModel`: Kullanıcı girişi, kayıt ve oturum yönetimini kontrol eder.
  - `ExamViewModel`: Sınav listeleme, sınav alma ve sonuç hesaplama işlemlerini yönetir.
  - `ResultViewModel`: Öğrenci sonuçlarını ve performans analizlerini sağlar.
- **Özellikler**:
  - `provider` paketi ile durum yönetimi (state management).
  - Firestore ve Firebase Auth ile veri alışverişi için Repository sınıflarını kullanır.
- **Örnek**:
  ```dart
  import 'package:provider/provider.dart';

  class AuthViewModel extends ChangeNotifier {
    final AuthRepository _authRepository;

    AuthViewModel(this._authRepository);

    bool _isLoading = false;
    bool get isLoading => _isLoading;

    Future<User?> signInWithEmail(String email, String password) async {
      _isLoading = true;
      notifyListeners();
      try {
        final user = await _authRepository.signInWithEmail(email, password);
        _isLoading = false;
        notifyListeners();
        return user;
      } catch (e) {
        _isLoading = false;
        notifyListeners();
        rethrow;
      }
    }
  }
  ```
- **Konum**: `lib/src/view_models/` (örn. `auth_view_model.dart`, `exam_view_model.dart`).

#### 1.3. View (`lib/src/views`)
- **Açıklama**: Kullanıcı arayüzü bileşenlerini içerir. ViewModel'dan veri alır ve kullanıcı etkileşimlerini ViewModel'a iletir.
- **Bileşenler**:
  - `LoginScreen`: E-posta ve Google ile giriş ekranı.
  - `ExamScreen`: Sınav listesi ve sınav alma ekranı.
  - `ResultScreen`: Sınav sonuçları ve analiz ekranı.
- **Özellikler**:
  - Flutter widget'ları (StatelessWidget veya StatefulWidget) ile oluşturulur.
  - `provider` paketi ile ViewModel'dan veri dinler.
- **Örnek**:
  ```dart
  class LoginScreen extends StatelessWidget {
    @override
    Widget build(BuildContext context) {
      final authViewModel = Provider.of<AuthViewModel>(context);
      return Scaffold(
        body: authViewModel.isLoading
            ? Center(child: CircularProgressIndicator())
            : Column(
                children: [
                  TextField(decoration: InputDecoration(labelText: 'E-posta')),
                  TextField(decoration: InputDecoration(labelText: 'Şifre')),
                  ElevatedButton(
                    onPressed: () async {
                      await authViewModel.signInWithEmail('email@ornek.com', 'sifre123');
                    },
                    child: Text('Giriş Yap'),
                  ),
                ],
              ),
      );
    }
  }
  ```
- **Konum**: `lib/src/views/` (örn. `login_screen.dart`, `exam_screen.dart`).

### 2. Repository Deseni

- **Açıklama**: Veritabanı (Firestore) ve kimlik doğrulama (Firebase Auth) işlemlerini soyutlar. ViewModel'lar, doğrudan Firebase servisleriyle etkileşime girmez; bunun yerine Repository sınıflarını kullanır.
- **Sınıflar**:
  - `AuthRepository`: Firebase Auth işlemlerini yönetir (giriş, kayıt, çıkış).
  - `ExamRepository`: Firestore'dan sınav verilerini alır ve kaydeder.
  - `ResultRepository`: Sonuç verilerini yönetir.
- **Örnek**:
  ```dart
  class AuthRepository {
    final FirebaseAuth _auth = FirebaseAuth.instance;

    Future<User?> signInWithEmail(String email, String password) async {
      final userCredential = await _auth.signInWithEmailAndPassword(
        email: email,
        password: password,
      );
      return userCredential.user;
    }

    Future<User?> signInWithGoogle() async {
      // Google Sign-In implementasyonu
      // https://firebase.google.com/docs/auth/flutter/federated-auth
      return null;
    }
  }
  ```
- **Konum**: `lib/src/repositories/` (örn. `auth_repository.dart`, `exam_repository.dart`).
- **Avantajlar**:
  - Veritabanı işlemlerini merkezi bir yerde toplar.
  - Testlerde sahte (mock) Repository sınıfları kullanılarak bağımlılıklar kolayca değiştirilebilir.

### 3. Singleton Deseni

- **Açıklama**: Firebase servislerinin (örn. `FirebaseAuth`, `FirebaseFirestore`) tekil örneklerini yönetir. Bu, kaynak kullanımını optimize eder ve servislerin birden fazla başlatılmasını önler.
- **Uygulama**:
  - Firebase servisleri, doğal olarak Singleton olarak sağlanır (örn. `FirebaseAuth.instance`, `FirebaseFirestore.instance`).
  - Repository sınıflarında bu örnekler kullanılır:
    ```dart
    class ExamRepository {
      final FirebaseFirestore _firestore = FirebaseFirestore.instance;

      Stream<List<Exam>> getExams() {
        return _firestore
            .collection('exams')
            .snapshots()
            .map((snapshot) => snapshot.docs
                .map((doc) => Exam.fromJson(doc.data()))
                .toList());
      }
    }
    ```
- **Not**: Singleton deseni, yalnızca Firebase servisleri için değil, uygulama genelinde tekil olması gereken diğer servisler (örn. API istemcisi) için de kullanılabilir.

## Proje Yapısı

```plaintext
lib/
├── src/
│   ├── models/
│   │   ├── user.dart          # Kullanıcı modeli
│   │   ├── exam.dart          # Sınav modeli
│   │   ├── result.dart        # Sonuç modeli
│   ├── view_models/
│   │   ├── auth_view_model.dart  # Kimlik doğrulama iş mantığı
│   │   ├── exam_view_model.dart  # Sınav iş mantığı
│   │   ├── result_view_model.dart # Sonuç iş mantığı
│   ├── views/
│   │   ├── login_screen.dart   # Giriş ekranı
│   │   ├── exam_screen.dart    # Sınav ekranı
│   │   ├── result_screen.dart  # Sonuç ekranı
│   ├── repositories/
│   │   ├── auth_repository.dart   # Kimlik doğrulama işlemleri
│   │   ├── exam_repository.dart   # Sınav veri işlemleri
│   │   ├── result_repository.dart # Sonuç veri işlemleri
├── main.dart                  # Uygulama giriş noktası
```

## Mimari Avantajları

- **Modülerlik**: MVVM, kodu katmanlara ayırarak bakım ve genişletmeyi kolaylaştırır.
- **Test Edilebilirlik**: Repository deseni, sahte veri kaynaklarıyla unit test yazımını basitleştirir.
- **Yeniden Kullanılabilirlik**: Repository ve Model sınıfları, farklı ViewModel'lar tarafından tekrar kullanılabilir.
- **Performans**: Singleton deseni, Firebase servislerinin verimli kullanımını sağlar.
- **Güvenlik**: Firestore işlemleri Repository içinde soyutlandığından, veri erişimi merkezi olarak kontrol edilir.

## Belgeler ve Kaynaklar

- **Flutter MVVM**: [Flutter MVVM Tutorial](https://www.youtube.com/results?search_query=flutter+mvvm+tutorial)
- **Repository Deseni**: [Flutter Repository Pattern](https://medium.com/flutter-community/flutter-architecture-the-repository-pattern-376803e645e7)
- **Firebase Flutter**: [FlutterFire Docs](https://firebase.flutter.dev/)
- **Provider**: [Provider Package](https://pub.dev/packages/provider)

Bu mimari, projenin gereksinimlerini (kullanıcı yönetimi, sınav yönetimi, sonuç analitiği) karşılar ve sürdürülebilir, test edilebilir bir kod tabanı sağlar.
