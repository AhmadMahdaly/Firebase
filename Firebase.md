# 1/ ما هي Firebase ولماذا نستخدمها؟
## هي منصة (تابعة لجوجل) توفر أدوات وخدمات لتطوير التطبيقات على الويب والجوال.

- إدارة قواعد البيانات (Database) -في الوقت الحقيقي أو NoSQL-.
- المصادقة (Authentication).
- إرسال الإشعارات (Notifications).
- تخزين الملفات مثل الصور والفيديوهات.
- إدارة استضافة التطبيقات (Hosting).
- مراقبة أداء التطبيق (Analytics and Performance Monitoring).

## 2/ إنشاء مشروع Firebase وربطه بـ Flutter
- فتح Firebase Console.
- تسجيل الدخول.
- انقر على "Add Project".
- أدخل اسم المشروع (مثلاً: my_flutter_app).
- إلغي تفعيل Google Analytics إن لم تكن بحاجة له.
- انقر على "Create Project" وانتظر حتى يتم الإنشاء.
  
### إضافة تطبيق Android:
- انقر على أيقونة Android في لوحة Firebase.
### - أدخل التفاصيل التالية:
- **Android package name**: هو اسم الحزمة لتطبيقك (يمكنك العثور عليه في ملف `android/app/build.gradle` تحت `applicationId`).
- **App nickname**: (اختياري) اسم التطبيق في Firebase.
- **SHA-1**: (اختياري الآن، لكنه ضروري لبعض الميزات مثل المصادقة باستخدام Google).
  نفتح Command Prompt على مسار المشروع في فولدر `android` وأنفذ الأمر التالي لتظهر معلومات SHA-1 و SHA-256 ضمن التقرير:
 ```
gradlew signingReport
```
- انقر على "Register App".
- بعد التسجيل، سيتم توليد ملف `google-services.json`. قم بتنزيله وضعه في مسار: `android/app/`
### - إعداد ملفات Gradle:
افتح ملف `android/app/build.gradle` وأضف السطر التالي في `plugins`:
```gradle
id 'com.google.gms.google-services' version '4.4.2' apply false
```

- إضافة مكتبات Firebase إلى Flutter في ملف `pubspec.yaml`.

```yaml
dependencies:
  firebase_core: ^2.15.1
  firebase_auth: ^4.7.0
  cloud_firestore: ^5.8.0
```

- تهيئة Firebase في كود التطبيق: افتح ملف `main.dart` وأضف التالي في بداية main():

```dart
import 'package:firebase_core/firebase_core.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  Platform.isAndroid
      ? await Firebase.initializeApp(
          options: const FirebaseOptions(
            apiKey: 'current_key', // google_services موجود في ملف  
            appId: 'mobilesdk_app_id', // google_services موجود في ملف  
            messagingSenderId: 'project_number', // google_services موجود في ملف  
            projectId: 'project_id', // google_services موجود في ملف  
          ),
        )
      : await Firebase.initializeApp();
  runApp(MyApp());
}
```
## 3/ إعداد Firebase Authentication في تطبيق Flutter:

- توجه إلى `Firebase Console`. واختر مشروعك الذي أنشأته مسبقًا.
- انقر على `Authentication` من الشريط الجانبي. و `Get Started`.
- في قسم `Sign-in methods`، قم بتفعيل الطرق التي تحتاجها (مثلاً: `Email/Password` أو `Google Sign-In`):
اختر `Email/Password` واضغط على زر `Enable` ثم `Save`.
- إذا كنت تريد تسجيل الدخول باستخدام Google، ستحتاج إلى تفعيل `Google Sign-In` وإعداد `SHA-1`.

- تأكد من إضافة مكتبة `Firebase Authentication` في `pubspec.yaml`:
```yaml
dependencies:
  firebase_auth: ^4.7.0
```

### تسجيل مستخدم جديد باستخدام البريد الإلكتروني وكلمة المرور:
- في الملف، أضف وظيفة لتسجيل المستخدمين:

```dart
import 'package:firebase_auth/firebase_auth.dart';

Future<void> registerUser(String email, String password) async {
  try {
    final UserCredential userCredential = await FirebaseAuth.instance
        .createUserWithEmailAndPassword(email: email, password: password);
    print('User registered: ${userCredential.user?.email}');
  } catch (e) {
    print('Error: $e');
  }
}
```
- استخدام الوظيفة:
```dart
ElevatedButton(
  onPressed: () {
    registerUser('test@example.com', 'mypassword123');
  },
  child: Text('Register'),
),
```

### تسجيل الدخول باستخدام البريد الإلكتروني وكلمة المرور:
أضف وظيفة تسجيل الدخول:
```dart
Future<void> loginUser(String email, String password) async {
  try {
    final UserCredential userCredential = await FirebaseAuth.instance
        .signInWithEmailAndPassword(email: email, password: password);
    print('User logged in: ${userCredential.user?.email}');
  } catch (e) {
    print('Error: $e');
  }
}
```
- استخدام الوظيفة:
```dart
ElevatedButton(
  onPressed: () {
    loginUser('test@example.com', 'mypassword123');
  },
  child: Text('Login'),
),
```

### تسجيل الخروج:
- لإضافة وظيفة تسجيل الخروج:
```dart
Future<void> logoutUser() async {
  await FirebaseAuth.instance.signOut();
  print('User logged out');
}
```
- استخدام الوظيفة:
```dart
ElevatedButton(
  onPressed: () {
    logoutUser();
  },
  child: Text('Logout'),
),
```
