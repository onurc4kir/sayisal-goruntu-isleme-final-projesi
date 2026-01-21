### Giriş

Bu raporun temel amacı, cilt kanseri lezyonlarının otomatik segmentasyonu için tasarlanmış kapsamlı bir görüntü işleme hattını belgelemektir. Bu dokümantasyon, ham dijital dermoskopi görüntülerinden başlayarak, lezyon bölgelerini doğru bir şekilde izole etmek ve bu bölgelerden nicel özellikler çıkarmak için uygulanan her bir adımı ayrıntılı olarak açıklamayı hedeflemektedir. Süreç, RGB'den gri tonlamaya dönüşüm, ön işleme teknikleri (kırpma, kontrast iyileştirme ve gürültü azaltma), eşikleme yoluyla segmentasyon ve bağlı bileşen etiketlemeye kadar uzanmaktadır. Her aşama, lezyon segmentasyonunun doğruluğunu ve güvenilirliğini artırmak için özenle seçilmiş ve uygulanmıştır. Nihai hedefimiz, bu işlenmiş verileri kullanarak cilt kanseri tespiti ve sınıflandırması için sağlam bir temel oluşturmaktır.


### RGB'den Gri Tonlamaya Dönüşümünün Amacı ve Süreci

Cilt kanseri lezyon segmentasyonu sürecinde, görüntülerin RGB (Kırmızı, Yeşil, Mavi) renk formatından gri tonlamaya dönüştürülmesi, ilk ve kritik bir adımdır. Bu dönüşüm, aşağıdaki temel amaçlar doğrultusunda gerçekleştirilir:

1.  **Veri Boyutunu Azaltma ve Karmaşıklığı Giderme:** RGB görüntüler, her piksel için üç renk kanalına (Kırmızı, Yeşil, Mavi) sahiptir. Gri tonlamaya dönüşüm, bu üç kanalı tek bir parlaklık kanalına indirger. Bu, işlenecek veri miktarını önemli ölçüde azaltır ve sonraki görüntü işleme adımlarının (gürültü azaltma, eşikleme, segmentasyon gibi) hesaplama verimliliğini artırır. Modelin renk yerine lezyonun yapısal özelliklerine (şekil, boyut, doku) odaklanmasına olanak tanır.

2.  **Renk Bilgisini Ortadan Kaldırma:** Segmentasyon ve özellik çıkarımı gibi görevlerde, renk bilgisi bazen yanıltıcı veya gereksiz olabilir. Gri tonlama, renk bilgisini ortadan kaldırarak algoritmaların sadece parlaklık ve yoğunluk değişimlerine odaklanmasını sağlar, bu da lezyon sınırlarının ve iç yapısının daha belirgin hale gelmesine yardımcı olabilir.

### Kullanılan Ağırlıklı Ortalama Formülü

Bu dönüşüm için kullanılan yaygın ağırlıklı ortalama formülü şu şekildedir:

`Gri = 0.2989 * Kırmızı + 0.5870 * Yeşil + 0.1140 * Mavi`

Bu formül, insan gözünün farklı renklere olan hassasiyetini taklit eder. İnsan gözü, yeşil renge kırmızıdan daha duyarlıyken, maviye en az duyarlıdır. Bu nedenle, yeşil kanala en yüksek ağırlık (0.5870), kırmızı kanala orta ağırlık (0.2989) ve mavi kanala en düşük ağırlık (0.1140) atanır. Bu ağırlıklar, elde edilen gri tonlama görüntüsünün, orijinal RGB görüntünün algılanan parlaklığına en yakın olmasını sağlar.

### Dönüşümün Faydaları

*   **Basitleştirme:** Görüntü işleme sürecini basitleştirerek, algoritmaların daha az karmaşık veriyle çalışmasını sağlar.
*   **Hesaplama Verimliliği:** İşlem süresini ve bellek kullanımını azaltır, özellikle büyük veri kümeleri veya gerçek zamanlı uygulamalar için önemlidir.
*   **Yapısal Özelliklere Odaklanma:** Lezyonun renginden ziyade şekli, boyutu ve dokusu gibi yapısal özelliklerine odaklanılmasını kolaylaştırır. Bu özellikler, genellikle lezyonun doğası hakkında daha kritik bilgiler sağlar.

### Gri Tonlama Dönüşümünün Görsel Etkisi

Gri tonlama dönüşümü, orijinal RGB görüntüdeki tüm renkleri bir parlaklık veya gri ton değerine indirger. Bu sayede, lezyonun ana hatları ve çevresindeki dokular arasındaki kontrast daha belirgin hale gelebilir. Renklerin dikkat dağıtıcı etkisi ortadan kalktığında, bir lezyonun düzensiz kenarları, asimetrisi veya dokusal heterojenliği gibi morfolojik özellikleri daha net bir şekilde görülebilir. Bu durum, özellikle lezyonun tanımlanması ve sınırlarının hassas bir şekilde belirlenmesi gereken segmentasyon görevleri için büyük bir avantajdır.


### Görüntü İşleme Adımlarının Önemi ve Faydaları:

1.  **RGB'den Gri Tonlamaya Dönüşüm:** Renk bilgilerini tek bir yoğunluk kanalına indirgeyerek işlem karmaşıklığını azalttık ve görüntüdeki yapısal ve dokusal detaylara odaklanmamızı sağladık.

2.  **Kırpma (Cropping):** Görüntülerin çevresindeki ilgisiz arka plan alanlarını elimine ederek lezyonun kendisi üzerindeki odak noktasını artırdık. Bu, hem hesaplama maliyetini düşürdü hem de modelin gereksiz gürültüden öğrenmesini engelledi.

3.  **Kontrast İyileştirme (Histogram Eşitleme):** Görüntülerin kontrastını artırarak, özellikle düşük kontrastlı lezyonlarda detayların daha belirgin hale gelmesini sağladık. Bu, lezyon sınırlarının ve iç yapılarının daha net görünmesine yardımcı oldu.

4.  **Gürültü Azaltma (Median Bulanıklaştırma):** Medyan bulanıklaştırma, görüntülerdeki 'tuz ve biber' gibi gürültüyü giderirken, lezyon sınırları gibi önemli kenar bilgilerini korudu. Bu sayede, segmentasyon adımları için daha temiz bir giriş sağlandı.

5.  **Eşikleme (Otsu Yöntemi):** Otsu eşikleme, ön işlenmiş görüntülerden lezyon bölgelerini otomatik olarak ikili (binary) maskeler halinde ayırmak için kullanıldı. Bu yöntem, ön plan ve arka plan arasındaki optimal ayrımı sağlayarak lezyonun ana hatlarını doğru bir şekilde segmentlere ayırmada etkili oldu.

6.  **Morfolojik Operatörler (Açma ve Kapama):** Segmentasyon sonrası elde edilen maskelerdeki küçük gürültüleri (açma ile) temizledik ve lezyon içindeki küçük delikleri veya kopuklukları (kapama ile) doldurarak lezyonun daha pürüzsüz ve bütünleşik bir temsilini elde ettik.

7.  **Bağlı Bileşen Etiketleme (CCL) ve En Büyük Bileşeni Seçme:** Görüntüde birden fazla bağlı bileşen tespit edildiğinde, en büyük bileşeni seçerek ana lezyonu izole ettik. Bu strateji, tek bir ana lezyonun beklendiği senaryolarda doğru ROI'yi belirlemek için kritik öneme sahiptir.

### Birinci Dereceden İstatistiksel Özelliklerin Rolü:

Lezyonların izole edilen ROI'sinden ortalama, standart sapma, çarpıklık, basıklık, entropi ve enerji gibi birinci dereceden istatistiksel özellikler çıkarıldı. Bu özellikler, lezyonun parlaklık dağılımı, homojenliği, dokusal karmaşıklığı ve ton farklılıkları hakkında nicel bilgiler sağlar. Örneğin, yüksek standart sapma, lezyon içinde çeşitli parlaklık değerlerinin olduğunu (heterojen doku) gösterirken, yüksek entropi daha rastgele ve karmaşık bir doku dağılımına işaret eder. Bu sayısal değerler, lezyonların klinik özellikleri ile ilişkilendirilebilecek objektif ölçütler sunar.

### Genel Sonuç:

Uygulanan bu görüntü işleme hattı, ham tıbbi görüntülerden cilt kanseri lezyonlarını başarılı bir şekilde izole etmek ve nitelendirmek için yapılandırılmış, tekrarlanabilir ve etkili bir yöntem sunmaktadır. Her bir adım, verinin kalitesini artırmak ve lezyonun en ayırt edici özelliklerini ortaya çıkarmak üzere tasarlanmıştır. Bu detaylı ön işleme ve öznitelik çıkarma süreci, makine öğrenimi modellerinin daha doğru ve güvenilir tahminler yapabilmesi için vazgeçilmez bir temel oluşturarak, otomatik cilt kanseri tanısı ve sınıflandırması alanındaki araştırmalara önemli katkılar sağlamaktadır.
