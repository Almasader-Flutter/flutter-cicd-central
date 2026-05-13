# 🚀 Flutter CI/CD Central

نظام CI/CD مركزي لرفع مشاريع Flutter على Google Play و App Store باستخدام GitHub Actions + Fastlane.

---

## 📁 الهيكل

```
flutter-cicd-central/               ← الـ repo ده (مرة واحدة بس)
├── .github/workflows/
│   ├── reusable-android.yml        ← كل منطق الـ Android
│   └── reusable-ios.yml            ← كل منطق الـ iOS
├── fastlane/
│   └── Fastfile
├── Gemfile
└── README.md

أي مشروع Flutter عندك:
└── .github/workflows/
    └── deploy.yml                  ← 3 سطور بس تغيّرهم
```

---

## ⚙️ الإعداد (مرة واحدة بس)

### الخطوة 1 — ارفع الـ Central Repo

```bash
cd flutter-cicd-central
git init
git add .
git commit -m "Initial CI/CD setup"
git remote add origin https://github.com/YOUR_ORG/flutter-cicd-central.git
git push -u origin main
```

---

### الخطوة 2 — اعمل Repo لـ Match (iOS بس)

```
1. اعمل repo جديد اسمه: certificates (Private)
2. مش محتاج تحط فيه أي حاجة
```

---

### الخطوة 3 — ضيف الـ Secrets في الـ Organization

```
GitHub → YOUR_ORG → Settings → Secrets and variables → Actions
```

#### Android Secrets:

| Secret | إزاي تجيبه |
|--------|-----------|
| `ANDROID_KEYSTORE_BASE64` | `base64 -i keystore.jks` |
| `ANDROID_STORE_PASSWORD` | كلمة سر الـ keystore |
| `ANDROID_KEY_PASSWORD` | كلمة سر الـ key |
| `ANDROID_KEY_ALIAS` | اسم الـ alias |
| `GOOGLE_PLAY_JSON_KEY` | Play Console → Setup → API access → Service Account → JSON |

#### iOS Secrets:

| Secret | إزاي تجيبه |
|--------|-----------|
| `ASC_KEY_ID` | App Store Connect → Users → Keys → (+) |
| `ASC_ISSUER_ID` | نفس الصفحة فوق |
| `ASC_PRIVATE_KEY` | محتوى الـ `.p8` file |
| `MATCH_PASSWORD` | أي كلمة سر أنت تختارها (هتحتاجها دايمًا) |
| `MATCH_GIT_BASIC_AUTHORIZATION` | `echo -n "username:github_token" \| base64` |
| `MATCH_REPO_URL` | `https://github.com/YOUR_ORG/certificates.git` |

---

## 🆕 إضافة مشروع Flutter جديد

**بس 3 خطوات:**

1. انسخ الملف ده في مشروعك:
```
.github/workflows/deploy.yml
```

2. غيّر سطرين بس:
```yaml
PACKAGE_NAME: "com.yourcompany.yourapp"   # ← package name بتاعك
BUNDLE_ID: "com.yourcompany.yourapp"      # ← bundle id بتاعك
```

3. غيّر `YOUR_ORG` بـ اسم الـ Organization بتاعتك:
```yaml
uses: YOUR_ORG/flutter-cicd-central/.github/workflows/reusable-android.yml@main
uses: YOUR_ORG/flutter-cicd-central/.github/workflows/reusable-ios.yml@main
```

**خلاص!** 🎉

---

## ▶️ إزاي تشغّله

```
الـ Repo → Actions → Deploy to Stores → Run workflow

اختار:
  Platform:  android | ios | both
  Track:     internal | beta | production
  (اختياري) Build Number

ثم: Run workflow ✅
```

---

## 🎯 الـ Tracks

| Track | Android | iOS |
|-------|---------|-----|
| `internal` | Internal Testing | TestFlight |
| `beta` | Open/Closed Testing | TestFlight |
| `production` | Production | App Store |

---

## 🔄 تحديث الـ CI/CD

أي تعديل في `flutter-cicd-central` بيأثر على **كل المشاريع** فورًا بدون أي تغيير فيهم.
