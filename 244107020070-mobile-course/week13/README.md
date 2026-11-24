# Minggu 13 - CI/CD dan Otomatisasi

## 1. Workflow CI

| Bagian | Isi |
|---|---|
| Trigger | `push` ke `main`, `pull_request` ke `main`, `workflow_dispatch` |
| Permission | `contents: read` (minimum) |
| Concurrency | `${{ github.workflow }}-${{ github.ref }}`, `cancel-in-progress: true` |
| Runner | `ubuntu-latest`, JDK 17 (Temurin), Flutter `3.41.7` stable (dipin) |
| Langkah | `pub get` → `dart format --set-exit-if-changed` → `flutter analyze --fatal-infos` → `flutter test --coverage` → `flutter build apk --release` → `flutter build appbundle --release` → `upload-artifact` |

- `build/app/outputs/flutter-apk/app-release.apk`
- `build/app/outputs/bundle/release/app-release.aab`

## 2. Pemeriksaan yang dijalankan

| Perintah | Hasil |
|---|---|
| `dart format --output=none --set-exit-if-changed .` | 78 berkas, 0 berubah |
| `flutter analyze --fatal-infos` | `No issues found!` (13,0 s) |
| `flutter test --coverage` | 82 tes lulus, 0 gagal |
| `lcov` dari `coverage/lcov.info` | 523/614 baris = **85,2%** |

## 3. Build

| Artefak | Ukuran | Isi |
|---|---|---|
| `app-release.apk` (universal, 3 ABI) | 52.255.996 byte ≈ **49,8 MiB** | `lib/` 16,6 MiB (arm64) + 14,2 MiB (armeabi-v7a) + 17,9 MiB (x86_64), sisanya dex + aset |
| `app-release.aab` | 43.519.500 byte ≈ **41,5 MiB** | sama |

## 4. Versioning

`pubspec.yaml`: `version: 1.0.1+13`

- `1.0.1` - patch: tidak ada perubahan perilaku dibanding Minggu 12, hanya
  penambahan pipeline dan penyesuaian format.
- `+13` - build number dinaikkan ke minggu ke-13; setiap unggahan baru harus
  menaikkan build number.
- Tag yang disarankan setelah workflow hijau: `v1.0.1`, menunjuk tepat pada
  commit yang artefaknya diunduh.

## 5. Menjalankan workflow

1. Commit dan push `.github/workflows/flutter-ci.yml` beserta folder `week13`.
2. Buka tab **Actions** → pilih run `Flutter CI`.
3. Periksa tiap langkah; kegagalan `flutter test` harus membuat job gagal.
4. Download artefak `android-builds` dan catat commit hash-nya.
