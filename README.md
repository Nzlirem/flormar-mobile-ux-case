# 💄 Flormar Mobil Uygulaması: Profil ve Veri Kalitesi Vakası

> **Bağımsız Ürün Yönetimi ve Veri Analizi Vakası (Product Case Study)**  
> **Hazırlayan:** Nazlı İrem Türkmen | İş Analisti / Ürün Yönetimi  

---

## 🎯 Projenin Amacı
Bu vaka çalışması, Flormar mobil uygulamasında keşfedilen üyelik ve profil akışı aksaklıklarının; CRM veri kalitesine, kişiselleştirilmiş pazarlama stratejilerine (doğum günü kampanyaları) ve kullanıcı deneyimine (UX) etkilerini incelemek ve buna yönelik uçtan uca bir **PRD (Product Requirements Document)** çözümü sunmak amacıyla bağımsız bir ürün analisti gözüyle hazırlanmıştır.

---

## 🔍 Gözlem ve Kritik Problem Tespiti (Bug Analizi)
Uygulama üzerinde yapılan bizzat testlerde şu kritik akış ve hata (silent failure) tespit edilmiştir:
1. **Kayıt Aşamasında Eksik Bilgi:** Üyelik formunda Ad, Soyad, E-posta, Şifre ve Telefon istenmesine rağmen "Doğum Tarihi" bilgisi talep edilmemektedir.
2. **State Yönetimi ve Kayıt Hatası (Silent Failure):** Üyelik sonrası profil sayfasına girildiğinde doğum tarihi alanı seçilebilmekte ancak "Kaydet" denildiğinde **herhangi bir başarı/hata pop-up (toast message) döngüsü çalışmamakta** ve yapılan değişiklik veritabanına **hiçbir şekilde kaydedilmemektedir**.
3. **Kullanıcı Kontrol Kaybı:** Kullanıcı, kendi profili üzerinde temel bir bilgiyi güncelleyemediği için ürün kontrol hissinden mahrum kalmaktadır.

---

## 💡 Geliştirilen Çözüm ve Faz Planı

| Faz | Kapsam | Amaç |
| :--- | :--- | :--- |
| **Faz 1 (Hızlı Kazanım & Bug Fix)** | Profildeki doğum tarihi güncelleme API entegrasyonunun düzeltilmesi ve kullanıcıya net geri bildirim (pop-up/toast) verilmesi | Mevcut hatalı akışın düzeltilmesi ve temel veri tutarlılığının sağlanması |
| **Faz 2 (Optimizasyon)** | Kayıt ekranına "isteğe bağlı" doğum tarihi alanının eklenmesi ve amacın şeffafça açıklanması | Kayıt sürtünmesini artırmadan temiz CRM verisi toplanması |
| **Faz 3 (Veri Temizliği)** | Mevcut yer tutucu (default) veya hatalı verilerin segmentasyon dışı bırakılması | Veri kirliliğinin (Data Pollution) önlenmesi |

---

## 📊 Metrikler ve Başarı Kriterleri (KPIs)
* **Birincil Metrik:** Geçerli doğum tarihine sahip ve profilde başarıyla güncelleyen aktif üye oranı.
* **Koruyucu (Guardrail) Metrik:** Kayıt tamamlama oranında relatif olarak %2'den fazla düşüş olmaması.
* **İkincil Metrik:** Doğum tarihi düzeltme talebiyle açılan destek kayıtlarında azalma.

---

## 🛠️ Örnek SQL Analitiği (H1 Hipotez Doğrulaması)
Sistemin kullanıcılara veri atama veya saklama süreçlerindeki anomalileri (H1 hipotezi) şirket içi veritabanında test etmek için kurgulanan örnek SQL sorgusu (`sql/verify_birthdate_pollution.sql`):

```sql
-- En sık görülen doğum tarihleri ve toplam içindeki payları (Veri kirliliği ve state analizi)
SELECT 
    birth_date,
    COUNT(*) AS total_users,
    ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (), 2) AS percentage_of_total
FROM user_profiles
WHERE birth_date IS NOT NULL
GROUP BY birth_date
ORDER BY users DESC
LIMIT 10;
