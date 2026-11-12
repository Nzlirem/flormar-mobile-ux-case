# 💄 Flormar Mobil Uygulaması: Profil ve Veri Kalitesi Vakası

> **Bağımsız Ürün Yönetimi ve Veri Analizi Vakası (Product Case Study)**  
> **Hazırlayan:** Nazlı İrem Türkmen | İş Analisti / Ürün Yönetimi  

---

## 🎯 Projenin Amacı
Bu vaka çalışması, Flormar mobil uygulamasında keşfedilen bir üyelik ve profil akışı aksaklığının; CRM veri kalitesine, kişiselleştirilmiş pazarlama stratejilerine (özellikle doğum günü kampanyalarına) ve kullanıcı deneyimine (UX) etkilerini incelemek ve buna yönelik uçtan uca bir **PRD (Product Requirements Document)** çözümü sunmak amacıyla bağımsız bir ürün analisti gözüyle hazırlanmıştır.

---

## 🔍 Gözlem ve Problem Tespiti
Uygulama üzerinde yapılan incelemelerde şu kritik akış hatası tespit edilmiştir:
1. **Kayıt Aşamasında Eksik Bilgi:** Üyelik formunda Ad, Soyad, E-posta, Şifre ve Telefon istenmesine rağmen "Doğum Tarihi" bilgisi talep edilmemektedir.
2. **Otomatik Atanan Veri (Default Value):** Üyelik tamamlandıktan sonra profil sayfasına girildiğinde, sistemin arka planda ortak/varsayılan bir doğum tarihi atadığı görülmektedir.
3. **Kilitli Alan (Read-Only):** Kullanıcı kendi profiline girdiğinde bu tarihi değiştirememektedir, alan kilitli kalmaktadır.

---

## 💡 Geliştirilen Çözüm ve Faz Planı

| Faz | Kapsam | Amaç |
| :--- | :--- | :--- |
| **Faz 1 (Hızlı Kazanım)** | Profildeki doğum tarihi alanının kullanıcı tarafından en az bir kez güncellenebilir hale getirilmesi | Kullanıcı kontrol hissinin artırılması ve mevcut yanlış verinin düzeltilmesi |
| **Faz 2 (Optimizasyon)** | Kayıt ekranına "isteğe bağlı" doğum tarihi alanının eklenmesi ve amacın şeffafça açıklanması | Kayıt sürtünmesini artırmadan temiz CRM verisi toplanması |
| **Faz 3 (Veri Temizliği)** | Mevcut yer tutucu (default) verilerin segmentasyon dışı bırakılması | Veri kirliliğinin (Data Pollution) önlenmesi |

---

## 📊 Metrikler ve Başarı Kriterleri (KPIs)
* **Birincil Metrik:** Geçerli (yer tutucu olmayan) doğum tarihine sahip aktif üye oranı.
* **Koruyucu (Guardrail) Metrik:** Kayıt tamamlama oranında relatif olarak %2'den fazla düşüş olmaması.
* **İkincil Metrik:** Doğum tarihi düzeltme talebiyle açılan destek kayıtlarında azalma.

---

## 🛠️ Örnek SQL Analitiği (H1 Hipotez Doğrulaması)
Sistemin kullanıcılara aynı varsayılan tarihi atayıp atamadığını (H1 hipotezi) şirket içi veritabanında test etmek için kurgulanan örnek SQL sorgusu:

```sql
-- En sık görülen doğum tarihleri ve toplam içindeki payları (Veri kirliliği tespiti)
SELECT 
    birth_date,
    COUNT(*) AS users,
    ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (), 2) AS pct_of_users
FROM user_profiles
WHERE birth_date IS NOT NULL
GROUP BY birth_date
ORDER BY users DESC
LIMIT 10;