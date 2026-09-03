# Teknofest x Trendyol — Sorgu-Ürün Eşleştirme Yarışması

Trendyol'un Teknofest kapsamında düzenlediği bir yarışma için geliştirilmiş bir arama-relevans (query-product relevance) modeli. Amaç: bir arama sorgusuyla (örneğin "koşu ayakkabısı") bir ürünün ne kadar alakalı olduğunu tahmin etmek.

## Veri

- 962 bin ürün (başlık, kategori, marka, özellikler)
- 50 bin arama sorgusu
- 250 bin doğrulanmış (sorgu, ürün) eşleşmesi
- 3.3 milyon tahmin edilmesi gereken çift

## Yaklaşım

1. **Veri temizleme** (`preprocessing.ipynb`): Ürün bilgileri (başlık, kategori, marka, renk, materyal, beden) tek bir metinde birleştirildi, gereksiz alanlar (cinsiyet, yaş) çıkarıldı.
2. **Embedding:** Çok dilli E5 modeliyle ürünler ve sorgular 768 boyutlu vektörlere dönüştürüldü.
3. **Hard negative mining:** Sorguya benzer ama doğru eşleşme olmayan ~324 bin "zor negatif" örnek bulundu — modelin sadece kolay örneklerle değil, gerçekten ayırt etmesi gereken örneklerle eğitilmesini sağlıyor.
4. **Model** (`model.ipynb`): Türkçe BERT (`dbmdz/bert-base-turkish-cased`) fine-tune edildi, ardından LightGBM ile (TF-IDF, Jaccard benzerliği gibi özellikler kullanarak) ikinci bir aşama modeli eklendi. İki modelin tahminleri %80-%20 ağırlıkla birleştirildi.

## Sonuçlar

| Model | Validation F1 |
|---|---|
| BERT (1. aşama) | 0.9842 |
| LightGBM | 0.9829 |
| **Ensemble — Kaggle Public LB** | **0.86** |

## Neden önemli?

Bu proje, gerçek bir e-ticaret arama motorunun arkasındaki problemi (sorgu-ürün eşleştirme) uçtan uca çözüyor: veri temizleme, embedding, transformer fine-tuning, ensemble öğrenme ve model değerlendirme. İleri seviye yapay zeka/NLP bilgisini gösteren en kapsamlı projelerimden biri.
