# asansor-simulasyonu
OOP - Asansör Simülasyonu

Kodluyoruz Sigorta Şirketi 12 katlı bir ofis binası inşa etmek ve onu en son asansör teknolojisi ile donatmak istiyor. Şirket, bina içindeki trafik akışı ihtiyaçlarını karşılayıp karşılamayacaklarını görmek için binanın asansörlerinin işlemlerini modelleyen bir yazılım simülatörü oluşturmanızı istiyor.

Binada, her biri binanın 12 katına çıkabilecek beş asansör bulunacaktır. Her asansörün yaklaşık altı yetişkin yolcu kapasitesi vardır. Asansörler enerji tasarruflu olacak şekilde tasarlanmıştır, bu nedenle yalnızca gerektiğinde hareket ederler. Her asansörün kendi kapısı, kat gösterge ışığı ve kontrol paneli vardır. Kontrol panelinde hedef düğmeleri, kapı açma ve kapama düğmeleri ve bir acil durum sinyal düğmesi bulunur.

Binadaki her katta, beş asansör boşluğunun her biri için bir kapı ve her kapı için bir varış zili vardır. Varış zili, asansörlerin bir kata vardığını gösterir. Her kapının üzerinde bulunan bir sinyal ışığı, asansörün gelişini ve asansörün hareket ettiği yönü gösterir. Her katta ayrıca üç set asansör çağrı düğmesi vardır.

Bir kişi uygun çağrı düğmesine (yukarı veya aşağı) basarak bir asansörü çağırır. Bir programlayıcı, aramanın başladığı kata gitmek için beş asansörden birini görevlendirir. Asansöre girdikten sonra, bir yolcu tipik olarak bir veya daha fazla hedef düğmesine basar. Asansör kattan kata hareket ederken, asansörün içindeki bir gösterge ışığı yolcuları asansörün konumu hakkında bilgilendirir. Bir asansörün bir kata varması, dış asansör kapısının üzerindeki gösterge lambasının yakılması ve kat zilinin çalmasıyla belirtilir. Bir asansör bir katta durduğunda, her iki kapı grubu da önceden belirlenmiş bir süre boyunca otomatik olarak açılarak yolcuların asansöre girip çıkmalarına izin verir.

Simülatör, gerçek zaman geçişini simüle etmek ve simülasyonda meydana gelen olayları zaman damgası ve günlüğe kaydetmek için bir "saat" kullanır. Simülatör tarafından yolcu oluşturmak ve her yolcu için kalkış ve varış katlarını belirlemek için rastgele bir sayı üreteci kullanılır.

```mermaid
classDiagram
    class Bina {
        -int katSayisi = 12
        -List~Kat~ katlar
        -List~Asansor~ asansorler
        -Programlayici programlayici
        +simulasyonuBaslat()
    }

    class DonanimBileseni {
        <<Abstract>>
        -bool durum
        +ac()
        +kapat()
    }

    class Kapi {
        -bool acikMi
        +otomatikAc(int sure)
    }

    class Isik {
        -string renk
        +yak()
        +sondur()
    }

    class Zil {
        +cal()
    }

    DonanimBileseni <|-- Kapi
    DonanimBileseni <|-- Isik

    class Asansor {
        -int id
        -int mevcutKat
        -int kapasite = 6
        -Yon mevcutYon
        -Durum mevcutDurum
        -List~Yolcu~ yolcular
        -Kapi asansorKapisi
        -KontrolPaneli panel
        -Isik katGostergesi
        +hareketEt(int hedefKat)
        +dur()
        +yolcuAl(Yolcu y)
        +yolcuIndir(Yolcu y)
    }

    class KontrolPaneli {
        -List~Buton~ hedefButonlari
        -Buton kapiAcButonu
        -Buton kapiKapatButonu
        -Buton acilDurumButonu
        +sinyalGonder(int katNumarasi)
    }

    class Kat {
        -int katNumarasi
        -List~Kapi~ katKapilari
        -List~Zil~ varisZilleri
        -List~Isik~ yonIsiklari
        -CagriButonSeti cagriButonlari
        +asansorCagir(Yon yon)
    }

    class Programlayici {
        +asansorGorevlendir(int baslangicKati, Yon yon)
        -enUygunAsansoruBul(int kat, Yon yon) Asansor
    }

    class Saat {
        -long suankiZaman
        +zamanIlerlet()
        +logYaz(string olay)
    }

    class Yolcu {
        -int agirlik
        -int baslangicKati
        -int hedefKati
        +butonaBas()
    }

    Bina "1" *-- "*" Kat : içerir
    Bina "1" *-- "5" Asansor : içerir
    Bina "1" *-- "1" Programlayici : yönetir
    Asansor "1" *-- "1" KontrolPaneli : sahiptir
    Kat "1" *-- "1" CagriButonSeti : sahiptir
    Programlayici ..> Asansor : Görevlendirir
    Saat ..> Bina : Simüle Eder
    Yolcu "*" -- "1" Asansor : Biner
```
</br>

Durum Yönetimi (State Management): Asansörün mevcutDurum (Boşta, Hareket Halinde, Kapılar Açık vb.) bilgisinin kapsüllenmesi (encapsulation), asansörün kendi başına hareket edememesini ve sadece Programlayici tarafından verilen komutlarla hareketEt() metodunu çalıştırmasını sağlar.</br>

Merkezi Dağıtım: Katlardan gelen asansorCagir() talepleri doğrudan bir asansöre gitmez. Önce Programlayici'ya düşer. Simülasyonda da gördüğünüz o bekleme ve en uygun asansörün seçilme mantığı enUygunAsansoruBul() metodu içinde hesaplanır.
