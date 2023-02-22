# Efektif Go

Bu döküman 28.08.2022 tarihinde [Effective Go](https://go.dev/doc/effective\_go) adlı yazıdan tercüme edilmiştir.

## Giriş

Go yeni bir dildir. Mevcut dillerden fikirler ödünç almasına rağmen, efektif _(etkili)_ yazılmış Go programlarını, muadili olduğu diğer dillerde yazılmış olan programlardan farklı kılan olağandışı özelliklere sahiptir. Bir C++ veya Java programının Go'ya doğrudan çevirisinin tatmin edici bir sonuç vermesi pek olası değildir. Java programları Go ile değil Java ile yazılır. Öte yandan, sorunu Go perspektifinden düşünmek, başarılı ancak oldukça farklı bir program üretebilir. Diğer bir deyişle, iyi bir şekilde Go yazmak, Go'nun özelliklerini ve kullanım tarzını iyi anlamaktan geçer. Yazdığınız programları diğer Go programcılarının anlamasını kolaylaştırmak için, isimlendirme, yazılacak programın yapısını belirleme ve biçimlendirme gibi kuralları bilmek önemlidir.

Bu belge, anlaşılır _(temiz)_, deyimsel Go kodu yazmak için ipuçları verir ve [dil belirtimi](https://go.dev/ref/spec), [Go Turu](https://go.dev/tour/welcome/1) ve [Go Kodu Nasıl Yazılır?](https://go.dev/doc/code.html) gibi konuların genişletilmiş halini içerir.

Ocak 2022'de eklenen not: Bu belge, Go'nun 2009'da piyasaya sürülmesi için yazılmıştır ve o zamandan beri önemli ölçüde güncellenmemiştir. Dilin kendisinin nasıl kullanılacağını anlamak için iyi bir rehber olmasına rağmen, dilin kararlılığı sayesinde kütüphaneler hakkında çok az şey söylüyor ve yazıldığından bu yana Go ekosisteminde yapılan derleme sistemi, test etme, modüller ve polimorfizm gibi önemli değişiklikler hakkında hiçbir şey söylemiyor. Buradaki bilgiler okuyucuya faydalı olacaktır fakat yazılanların eksiksiz olmadığını anlamanızda fayda var. Bu konu hakkında bir açıklama için Rob Pike'ın açtığı issue olan [bu adresi](https://github.com/golang/go/issues/28782) ziyaret edebilirsiniz. _(Kısaca, kütüphaneler üzerinden konuşmanın, başlangıçta Go'yu anlatmanın doğru bir yolu olmadığından bahsediyor.)_

### Örnekler

[Go paketi kaynakları](https://go.dev/src/), yalnızca temel kütüphane olarak değil, aynı zamanda dilin nasıl kullanılacağına ilişkin örnekler olarak da hizmet etmeyi amaçlamaktadır. Ayrıca, paketlerin birçoğu, [bunun gibi](https://go.dev/pkg/strings/#example\_Map) doğrudan [golang.org](https://golang.org/) web sitesinden çalıştırabileceğiniz çalışan, bağımsız yürütülebilir örnekler içerir _(Yer alan konuyu denemek için **Örnek** kısmına göz atabilirsiniz)_. Bir soruna nasıl yaklaşılacağı veya bir şeyin nasıl uygulanabileceği hakkında bir sorunuz varsa, kitaplıktaki belgeler, kodlar ve örnekler sizlere cevaplar, fikirler ve arka plan sağlayabilir.

## Biçimlendirme _(Formatting)_

Biçimlendirme sorunları en tartışmalı ancak en az sonuç doğuran sorunlardır. İnsanlar farklı biçimlendirme stillerine uyum sağlayabilir, ancak gerekmemeleri daha iyidir ve herkes aynı stile bağlı kalırsa konuya daha az zaman ayrılır. Sorun, bu Ütopya'ya uzun bir kuralcı stil kılavuzu olmadan nasıl yaklaşılacağıdır.

Go ile alışılmadık bir yaklaşım izliyoruz ve çoğu biçimlendirme sorununu makinenin halletmesine izin veriyoruz. `gofmt` programı _(aynı zamanda kaynak dosya düzeyinden ziyade paket düzeyinde çalışan `go fmt` olarak da mevcuttur)_ bir Go programını okur ve kaynağı standart bir girinti _(indent)_ ve dikey hizalama stilinde yayar, yorumları korur ve gerekirse yeniden biçimlendirir. Eğer kendiniz de denemek isterseniz `gofmt` komutunu çalıştırabilirsiniz ve karşılaştığınız sonuç beklentinizi karşılamıyor ise yazdığınız kodları değiştirebilir _(hatalı bir çıktı olduğunu düşünüyorsanız hata bildiriminde bulunabilirsiniz)_ veya bu yöntemi uygulamaktan vazgeçebilirsiniz.

Örnek olarak bir struct'ın alanlarındaki yorumları hizalamak için zaman harcamanıza gerek yoktur. Bunu `gofmt` sizin için yapabilir. Örnek bir girdi:

```go
type T struct {
    name string // name of the object
    value int // its value
}
```

`gofmt`'den sonra aşağıdaki gibi olacaktır.

```go
type T struct {
    name    string // name of the object
    value   int    // its value
}
```

Standart paketlerdeki tüm Go kodları `gofmt` ile biçimlendirilmiştir.

Diğer biçimlendirme özelliklerine kısaca değinelim:

#### Girintileme

Girinti için tab kullanıyoruz ve `gofmt` bunları varsayılan olarak yayar. Boşlukları yalnızca gerekiyorsa kullanın.

#### Satır Uzunluğu

Go'da satır uzunluğu limitli değildir. Uzunluktan kaynaklı satırın ekrana sığmamasından endişelenmeyin. Girinti ile beraber alt satırdan kodunuza devam edebilirsiniz.

#### Parantezler

Go, C ve Java'dan daha az parantez gerektirir: kontrol yapıları _(if, for, switch)_ sözdizimlerinde parantez içermez. Ayrıca, operatör öncelik hiyerarşisi daha kısa ve nettir, aşağıdaki gibi,

```go
x<<8 + y<<16
```

Örnekteki gibi diğer dillerden farklı olarak, aralığın ne anlama geldiğini anlayabilir.

## Yorumlar

Go, C stili `/* */` blok yorumları ve C++ stili `//` satır yorumları sağlar. Satır yorumları normdur; blok yorumları çoğunlukla paket yorumları olarak görünür, ancak bir ifade içinde veya büyük kod alanlarını devre dışı bırakmak için kullanışlıdır.

Üst düzey tanımlamarın üstüne yazılan yorum satırları eğer arada satır boşluk yoksa tanımlamanın belgesi olma görevini görür. Bu "belge yorumları", belirli bir Go paketi veya komutu için birincil belgelerdir. Doküman yorumları hakkında daha fazla bilgi için bkz. [Go Doc Yorumlar](https://go.dev/doc/comment)

## İsimler

İsimler Go'da diğer dillerde olduğu kadar önemlidir. Hatta anlamsal etkileri de vardır: Bir paketin dışında bir adın görünürlüğü, ilk karakterinin büyük harf olup olmadığına göre belirlenir. Bu nedenle, Go programlarındaki adlandırma kuralları hakkında konuşmak için biraz zaman harcamaya değer.

### Paket İsimleri

Bir paket içe aktarıldığında, paket adı içerikler için bir erişimci olur. Sonrasında

```go
import "bytes"
```

şeklinde içe aktarma yapıldığında `bytes.Buffer` olarak kullanıldığında `bytes` paketi içindeki dışa aktarılan `Buffer` kullanılabilir. Paketi kullanan herkesin içeriğine atıfta bulunmak için aynı adı kullanabilmesi yararlıdır, bu da paket adının iyi olması gerektiği anlamına gelir: kısa, öz, çağrışım yapması mantıklı olandır. Kural olarak, paketlere küçük harfli, tek kelimelik adlar verilir; alt çizgiye veya karışık Caps'e _(büyük ve küçük harf karışım kelimeler)_ gerek olmamalıdır. Kısa olmasındaki sebep, paketinizi kullanan herkesin kolaylıkla yazabilecek olmasıdır. Ve paket ismi çakışmaları hakkında önceden endişelenmemiz gerekli değildir. Paket adı, içe aktarma işlemleri için yalnızca varsayılan addır. Tüm kaynak kodlarında benzersiz olması gerekmez ve nadiren bir çakışma durumunda, içe aktarılırken yerel olarak kullanmak için farklı bir ad seçilebilir. Her durumda, karışıklık nadirdir çünkü içe aktarmadaki dosya adı yalnızca hangi paketin kullanıldığını belirler.

Başka bir kural, paket adının kaynak dizininin temel adı olmasıdır. `src/encoding/base64` içindeki paket `encoding/base64` olarak içe aktarılır ancak `base64` adına sahiptir, `encoding_base64` ve `encodingBase64` şeklinde çağrılmaz.

Bir paketin ithalatçısı _(importer)_, içeriğine atıfta bulunmak için import adını kullanır, bu nedenle paketteki dışa aktarılan adlar, tekrarı önlemek için bu yöntemi kullanabilir. _(Test ettikleri paketin dışında çalışması gereken testleri basitleştirebilecek, ancak aksi takdirde kaçınılması gereken `import .` yöntemini kullanmayın.)_. Örneğin, `bufio` paketindeki arabelleğe alınmış okuyucu türüne `BufReader` değil, `Reader` adı verilir, çünkü kullanıcılar bunu açık ve özlü bir ad olan `bufio.Reader` olarak görür. Ayrıca, içe aktarılan varlıklar her zaman paket adlarıyla beraber çağrıldığından, `bufio.Reader` ve `io.Reader` gibi çağırmalar birbirleri ile çakışmaz. Benzer şekilde, yeni `ring.Ring` örneklerini oluşturma işlevi (Go'daki bir kurucunun -constructor- tanımıdır) normalde `NewRing` olarak adlandırılırdı, ancak paket tarafından dışa aktarılan tek tür `Ring` olduğundan ve paket `ring` olarak adlandırıldığından, paketin istemcileri `ring.New` olarak görmesi yeterli olduğu için sadece `New` olarak adlandırılır. İyi isimler seçmenize yardımcı olması için paket yapısını kullanın.

Diğer bir kısa örnek ise `once.Do`; `once.Do(setup)` çağrımı iyi okunur fakat `once.DoOrWaitUntilDone(setup)` gibi bir isimlendirme yapılması önerilmez. Uzun adlar otomatik olarak işleri daha okunaklı hale getirmez. Yararlı bir doküman yorumu, genellikle fazladan uzun bir addan daha değerli olabilir.

### Getters (Alıcılar)

Go, setters _(alıcılar)_ ve setters _(atayıcılar)_ için otomatik destek sağlamaz. Alıcıları ve atayıcıları kendiniz sağlamakta yanlış bir şey yoktur ve bunu yapmak genellikle uygundur, ancak `Get`'i alıcının adına koymak ne deyimsel _(idiomatic)_ ne de gereklidir. Örnek olarak `owner` _(küçük harf, dışa aktarılmamış)_ adında bir alanınız varsa, alıcı yöntemi `GetOwner` değil, `Owner` _(büyük harf, dışa aktarılmış)_ olarak adlandırılmalıdır. Dışa aktarma için büyük harf adlarının kullanılması, hook'un _(burada yapıyı değiştirmeden işlem yapabilmeyi sağlayan araç anlamında kullanılmış)_ struct içerisindeki alanı fonksiyondan ayırt etmesini sağlar. Bu mantığın devamı olarak gerekirse bir atayıcı fonksiyonu muhtemelen `SetOwner` olarak adlandırılacaktır. Her iki isim de pratikte iyi okunuyor:

```go
owner := obj.Owner()
if owner != user {
    obj.SetOwner(user)
}
```

### Interface (Arayüz) İsimleri

Geleneksel olarak, tek fonksiyonlu interface'ler, içerisindeki fonksiyon adında yola çıkılarak bir aracı adı oluşturmak için interface adının sonuna `-er` eki veya benzer bir değişiklikle adlandırılır: `Reader`, `Writer`, `Formatter`, `CloseNotifier` vb.

Bu tür isim kullanımlarını amacı, kapsadıkları fonksiyonların isimlerini kendi _(interface)_ isimlerine atfetmektir. `Read`, `Write`, `Close`, `Flush`, `String` vb. kurallı signature'lara _(fonksiyonun ismi, girdileri ve çıktılarını gösteren imza)_ ve anlamlara sahiptir. Karışıklığı önlemek için, aynı signature `(imza)` ve anlama sahip olmadıkça, fonksiyonunuza bu adlardan birini vermeyin. Tersine, türünüz `(type)` iyi bilinen bir türdeki fonksiyonla aynı anlama sahip bir fonksiyonda uygulanırsa, ona aynı adı veya imzayı vermeyin;  örneğin `string` tipine dönüştürücü fonksiyonunuzu `String` değil `ToString` olarak adlandırın.

### MixedCaps (Büyük-Küçük Karışık İsimlendirme) <a href="#mixed-caps" id="mixed-caps"></a>

Son olarak, Go'daki kural, çok kelimeli adlar yazmak için alt çizgi yerine `MixedCaps` veya `mixedCaps` kullanmaktır.

## Noktalı Virgüller (Semicolons)

Aynı C'deki gibi, Go da biçimsel dilbilgisi ifadeleri sonlandırmak için noktalı virgül kullanır, ancak C'den farklı olarak, bu noktalı virgüller kaynakta görünmez. Bunun yerine lexer _(sözlük)_, tarama yaparken otomatik olarak noktalı virgül eklemek için basit bir kural kullanır, böylece giriş metni _(yani kullanıcının yazdığı kod)_ çoğunlukla bunlardan arınmış olur.

Kural basitçe şudur, satırda veri tipleri _(int, float64 gibi)_ kullanılarak tanılama yapılmış ise, aşağıdaki gibi

```
break continue fallthrough return ++ -- ) }
```

gibi terim veya belirteçler kullanılmış ise lexer her zaman bunların sonlarına noktalı virgül ekler. Bu lexer'ın anladığı dilde “yeni satır, bir ifadeyi sonlandırabilecek bir belirteçten sonra geliyorsa, noktalı virgül eklemeliyim” şeklinde özetlenebilir.

Parantez kapanışından hemen önce noktalı virgül de atlanabilir _(uygulanmayabilir)_, bu nedenle aşağıdaki gibi bir ifade

```go
go func() { for { dst <- <-src } }()
```

üzerinde noktalı virgül gerekli değildir. Deyimsel _(idiomatic)_ Go programlarında, yalnızca başlatıcı _( `i:= 0` gibi)_, koşul _( `i < 10` gibi)_ ve devam öğelerini _( `i++` gibi)_ ayırmak için `for` döngüsü yan tümceleri gibi yerlerde noktalı virgül bulunur. Ayrıca, bu şekilde kod yazmanız durumunda, bir satırdaki birden çok ifadeyi ayırmak için de gereklidirler.

Noktalı virgül ekleme kurallarının bir sonucu, bir kontrol yapısının açılış ayracı _(if, for, switch veya select)_ sonraki satıra koyamamanızdır. Bunu yaparsanız, ayraçtan önce istenmeyen etkilere neden olabilecek bir noktalı virgül eklenir. Bu şekilde yazmanızda bir sıkıntıyla karşılaşmayacakken;

```go
if i < f() {
    g()
}
```

süslü parantezi aşağıda başlatmanız hatalı olacaktır. Örnek bir yanlış görelim;

```go
if i < f()  // yanlış!
{           // yanlış!
    g()
}
```

## Kontrol Yapıları

Go'nun kontrol yapıları, C'dekilerle ilişkilidir, ancak önemli şekillerde farklılık gösterir. `do` veya `while` döngüsü yoktur, yalnızca biraz genelleştirilmiş bir şekli vardır; `switch` daha esnektir; `if` ve `switch`, `for` gibi isteğe bağlı bir başlatma ifadesini kabul eder; `break` ve `continue` ifadeleri, neyin kesileceğini veya devam edileceğini belirlemek için isteğe bağlı bir etiket alır; ve bir tip anahtarı _(switch)_ ve çok yollu iletişim çoklayıcıyı içeren `select` gibi yeni kontrol yapıları vardır. Sözdizimi _(syntax)_ de biraz farklıdır: parantez yoktur ve gövdeler her zaman ayraçla _(süslü parantezlerle)_ sınırlandırılmalıdır.

### If

Go'da basit bir `if` şöyle görünür:

```go
if x > 0 {
    return y
}
```

Zorunlu parantezler, birden çok satırda basit `if` ifadeleri yazmayı teşvik eder. Her halükarda bunu yapmak iyi bir stildir, özellikle de gövde bir `return` veya `break` gibi bir kontrol ifadesi içerdiğinde.

`if` ve `switch` bir başlatma deyimini kabul ettiğinden, yerel bir değişken ayarlamak için kullanılan bir deyimi görmek yaygındır.

```go
if err := file.Chmod(0664); err != nil {
    log.Print(err)
    return err
}
```

Go kütüphanelerinde, bir `if` deyimi sonraki deyimle devam etmediğinde (`else` veya `else if` ile devam etmediğinde), ve gövde `break`, `continue`, `goto` veya `return` ile sona erdiğinde, gereksiz `else`'in atlandığını göreceksiniz.

```go
f, err := os.Open(name)
if err != nil {
    return err
}
codeUsing(f)
```

Bu, kodun bir dizi hata koşuluna karşı koruma sağlaması gereken yaygın bir duruma bir örnektir. Başarılı kontrol akışı sayfa boyunca ilerlerse kod iyi okunur ve ortaya çıkan hata durumlarını ortadan kaldırır. Hata durumları `return` ifadesiyle sona erme eğiliminde olduğundan, ortaya çıkan kodun `else` gibi ifadelere ihtiyacı yoktur.

```go
f, err := os.Open(name)
if err != nil {
    return err
}
d, err := f.Stat()
if err != nil {
    f.Close()
    return err
}
codeUsing(f, d)
```

### Yeniden Tanımlama ve Atama

Önceki bölümdeki son örnek, `:=` kısa tanımlama formunun nasıl çalıştığının bir ayrıntısını gösterir. `os.Open`'ı çağıran tanımlama şöyledir:

```go
f, err := os.Open(name)
```

Bu ifade, `f` ve `err` olmak üzere iki değişken tanımlar. Birkaç satır sonra, `f.Stat`'a yapılan çağrı şöyledir:

```go
d, err := f.Stat()
```

Yukarıda verilen örneklerin alt alta yazıldığında durumlarda, çalışması gayet doğaldır. `f.Stat()` ile tanımlama yapılan yerde yeni bir `d` değişkeni tanımlar, `err` değişkeni ise zaten tanımlı olduğu için `err` değişkenine sadece bir atama yapar.

Bir `:=` kısa tanımlamasında, bir `v` değişkeni, önceden tanımlanmış olsa bile, aşağıdakiler koşuluyla görünebilir:

* bu tanımlama, `v`'nin mevcut tanımlaması ile aynı kapsamdadır _(scope)_ _(`v` zaten bir dış kapsamda tanımlanmışsa, tanımlama yeni bir `§` değişkeni yaratacaktır)_,
* başlatmadaki karşılık gelen değer (if ve switch içi gibi tanımlamalar) `v`'ye atanabilir ve
* tanımlama tarafından oluşturulan en az bir başka değişken vardır.

Bu olağandışı özellik, örneğin uzun bir `if-else` zincirinde tek bir `err` değeri kullanmayı kolaylaştıran saf pragmatizmdir. Sık kullanıldığını göreceksiniz.

Burada, Go'da fonksiyon parametrelerinin kapsamının ve `return` değerlerinin, gövdeyi çevreleyen parantezlerin dışında sözcüksel olarak görünseler de, fonksiyon gövdesiyle aynı olduğunu belirtmekte fayda var.

### For

Go'daki `for` döngüsü C'dekine benzer fakat tam olarak aynısı değildir. Go `for` ve `while`'ı birleştirir ve `do-while`'ı barındırmaz. Yalnızca birinde noktalı virgül bulunan üç formu vardır.

```go
// C'deki for gibi
for init; condition; post { }

// C'deki while gibi
for condition { }

// C'deki for(;;) gibi
for { }
```

Kısa tanımlamalar, index değişkenini doğrudan döngü içinde bildirmeyi kolaylaştırır.

```go
sum := 0
for i := 0; i < 10; i++ {
    sum += i
}
```

Bir `array`, `slice`, `string` veya `map` üzerinde döngü yapıyorsanız veya bir `channel`'dan okuyorsanız, bir `range` yan tümcesi döngüyü yönetebilir.

```go
sum := 0
for _, value := range array {
    sum += value
}
```

Boş tanımlayıcının daha sonraki bir bölümde açıklandığı gibi birçok kullanımı vardır.

String'lerde, `range` sizin için daha fazla iş yapar ve UTF-8'i ayrıştırarak tek tek Unicode kod noktalarını ayırır. Hatalı kodlamalar bir bayt tüketir ve yedek `rune` `U+FFFD`'yi üretir. (İlişkili yerleşik türle birlikte `rune` adı, tek bir Unicode kod noktası için Go terminolojisidir. Ayrıntılar için [dil belirtimi](https://go.dev/ref/spec#Rune\_literals)ne bakın.) Döngü;

```go
for pos, char := range "日本\x80語" { // \x80 geçerli olmayan bir UTF-8 encoding
    fmt.Printf("%#U karakteri %d bayt posizyonunda başlıyor\n", char, pos)
}
```

Bu kodun çıktısı aşağıdaki gibi olacaktır.

```
U+65E5 '日' karakteri 0 bayt posizyonunda başlıyor
U+672C '本' karakteri 3 bayt posizyonunda başlıyor
U+FFFD '�' karakteri 6 bayt posizyonunda başlıyor
U+8A9E '語' karakteri 7 bayt posizyonunda başlıyor
```

Son olarak, Go virgül operatörüne sahip değildir ve `++` ile `--` karşılaştırma ifadeleri değillerdir.  Bu nedenle, bir `for` içinde birden çok değişken çalıştırmak istiyorsanız, paralel atama kullanmalısınız _(bu `++` ve `--`'yi engellese de)_.

```go
// a dizisini ters çevirir
for i, j := 0, len(a)-1; i < j; i, j = i+1, j-1 {
    a[i], a[j] = a[j], a[i]
}
```

### Switch

Go'daki `switch` C'dekinden daha geneldir. Karşılaştırılan değer _integer_ veya _constant_ olması gerekmez, `case`'ler bir eşleşme bulunana kadar yukarıdan aşağıya doğru değerlendirilir ve eşleşme sağlandığında `true` olarak değerlendirilir. Bu nedenle bir `if`, `else if` veya `if else` zincirini `switch` ile yazmak mümkündür ve bazı yerlerde bu şekilde kullanılması daha uygun olabilir.

```go
func unhex(c byte) byte {
    switch {
    case '0' <= c && c <= '9':
        return c - '0'
    case 'a' <= c && c <= 'f':
        return c - 'a' + 10
    case 'A' <= c && c <= 'F':
        return c - 'A' + 10
    }
    return 0
}
```

Otomatik geçiş _(fall through)_ yoktur, ancak `case`'ler virgülle ayrılmış liste şeklinde sunulabilir.

```go
func shouldEscape(c byte) bool {
    switch c {
    case ' ', '?', '&', '=', '#', '+', '%':
        return true
    }
    return false
}
```

Go'da diğer C-benzeri diller kadar yaygın olmasalar da, bir `switch`'i erken sonlandırmak için `break` deyimleri kullanılabilir. Ancak bazen, `switch'`ten değil, çevreleyen bir döngüden çıkmak gerekir ve Go'da bu, döngüye bir etiket koyarak ve bu etiketi "kırarak" gerçekleştirilebilir. Bu örnek her iki kullanımı da göstermektedir.

```go
Loop: //etiket ismi verdik
    for n := 0; n < len(src); n += size {
        switch {
        case src[n] < sizeOne:
            if validateOnly {
                break
            }
            size = 1
            update(src[n])

        case src[n] < sizeTwo:
            if n+1 >= len(src) {
                err = errShortInput
                break Loop //hangi etiketi break edeceğimizi belirttik
            }
            if validateOnly {
                break
            }
            size = 2
            update(src[n] + src[n+1]<<shift)
        }
    }
```

Elbette, `continue` ifadesi isteğe bağlı bir etiketi de kabul eder, ancak yalnızca döngüler için geçerlidir.

Bu bölümü kapatmak için, iki `switch` ifadesi kullanan bayt dilimleri için bir karşılaştırma yordamı:

```go
// Compare, iki bayt dilimini karşılaştıran bir tamsayı döndürür,
// lexicographically (sözbilimsel olarak).
// Sonuç if a == b, -1 if a < b, and +1 if a > b olursa 0 olacaktır
func Compare(a, b []byte) int {
    for i := 0; i < len(a) && i < len(b); i++ {
        switch {
        case a[i] > b[i]:
            return 1
        case a[i] < b[i]:
            return -1
        }
    }
    switch {
    case len(a) > len(b):
        return 1
    case len(a) < len(b):
        return -1
    }
    return 0
}
```

### Type Switch

Bir `interface` değişkeninin dinamik türünü keşfetmek için `switch` kullanılabilir. Böyle bir `type switch`'in parantezi içerisinde değişkenin tipi alınır ve devamındaki `case`'ler ile karşılaştırılır.&#x20;

```go
var t interface{}
t = functionOfSomeType()
switch t := t.(type) {
default:
    fmt.Printf("bilinmeyen tip %T\n", t)     // %T ile t'nin tipini yazdırabiliriz
case bool:
    fmt.Printf("boolean %t\n", t)             // t'nin tipi bool
case int:
    fmt.Printf("integer %d\n", t)             // t'nin tipi int
case *bool:
    fmt.Printf("pointer to boolean %t\n", *t) // t'nin tipi *bool
case *int:
    fmt.Printf("pointer to integer %d\n", *t) // t'nin tipi *int
}
```

## Fonksiyonlar

### Çoklu Döndürülen Değerler

Go'nun sıra dışı özelliklerinden biri, fonksiyonların ve metodların birden çok değer döndürebilmesidir. Bu form, C programlarındaki birkaç beceriksiz deyimi geliştirmek için kullanılabilir.

C'de, bir yazma hatası, geçici bir konumda gizlenen hata koduyla negatif bir sayım _(sayısal olarak hata döndürme)_ ile bildirilir. Go'da `Write` bir sayı ve bir hata döndürebilir: "Evet, bazı baytlar yazdınız ama hepsini değil çünkü cihazı doldurdunuz". `os` paketindeki dosyalar üzerindeki `Write` metodunun imzası şudur:

```go
func (file *File) Write(b []byte) (n int, err error)
```

ve yukarıdaki fonksiyonun dökümantasyonunda denildiği gibi, `n` yazılan bayt sayısını döndürür ve `err` ise `n != len(b)` olması durumunda `nil` olmayan bir `error` döndürür. Bu yaygın olarak kullanılan bir yöntemdir. Detaylar için hata yakalamaya _(error handling)_ bakabilirsiniz.

Benzer bir yaklaşım, bir referans parametresini simüle etmek için bir pointer'ı (işaretçi) bir dönüş değerine iletme ihtiyacını ortadan kaldırır. İşte bir bayt dilimindeki _(slice)_ bir konumdan bir sayıyı almak, sayıyı ve bir sonraki konumu döndürmek için basit bir fonksiyon.

```go
func nextInt(b []byte, i int) (int, int) {
    for ; i < len(b) && !isDigit(b[i]); i++ {
    }
    x := 0
    for ; i < len(b) && isDigit(b[i]); i++ {
        x = x*10 + int(b[i]) - '0'
    }
    return x, i
}
```

Bunu, girdi olan `b` slice'ındaki sayıları taramak (iterate) için kullanabilirsiniz, şöyle:

```go
    for i := 0; i < len(b); {
        x, i = nextInt(b, i)
        fmt.Println(x)
    }
```

### İsimli Sonuç Parametreleri

Bir Go fonksiyonunun dönüş _(return)_ veya diğer bir tabir ile sonuç _(result)_ "parametreleri", tıpkı gelen parametrelere verildiği gibi, adlar verilebilir ve düzenli değişkenler olarak kullanılabilir. Adlandırıldıklarında, fonksiyon başladığında türleri için sıfır değerlerine başlatılırlar; fonksiyon bağımsız değişken içermeyen bir dönüş ifadesi yürütürse, sonuç parametrelerinin geçerli değerleri döndürülen değerler olarak kullanılır.

Return isimleri zorun değillerdir ve kodlarınızı daha basit ve anlaşılır kılarlar. `nextInt`'in sonuçlarını _(return'ünü)_ adlandırırsak, hangi `int`'in döndürüldüğü açık hale gelir.

```go
func nextInt(b []byte, pos int) (value, nextPos int) {
```

Adlandırılmış sonuçlar başlatıldığından ve süslenmemiş bir dönüşe bağlı olduğundan _(yani `return` teriminin yanına `return` edilecek şeylerin yazılmadığı durum)_, hem basitleştirebilir hem de netleştirebilirler. İşte onları iyi kullanan bir `io.ReadFull` sürümü:

```go
func ReadFull(r Reader, buf []byte) (n int, err error) {
    for len(buf) > 0 && err == nil {
        var nr int
        nr, err = r.Read(buf)
        n += nr
        buf = buf[nr:]
    }
    return
}
```

### Defer

Go'daki `defer` ifadesi, bir fonksiyon çağrısını ertelemeye yarar ve ertelenen işlem bulunduğu kod bloğu tamamlandığında gerçekleştirilir. Bu işlem genelde bir işlem sonucu yapılan atamaların serbest bırakılması _(örneğin `Close` ve `cancel` gibi fonksiyonlar)_ için kullanılan, alışılmadık fakat etkili bir yöntemdir. Kurallı örnekler, bir mutex'in kilidini _(`mutex.Unlock()` gibi)_ açmak veya bir dosyayı kapatmaktır _(`f.Close()` gibi)_.

```go
// Contents fonksiyonu, dosyanın içeriğini string olarak çıkartan bir fonksiyondur.
func Contents(filename string) (string, error) {
    f, err := os.Open(filename)
    if err != nil {
        return "", err
    }
    defer f.Close()  // f.Close fonksiyonu Contents fonksiyonu tamamlanmadan önce çalışacaktır.

    var result []byte
    buf := make([]byte, 100)
    for {
        n, err := f.Read(buf[0:])
        result = append(result, buf[0:n]...) // result'un sonuna ekliyor.
        if err != nil {
            if err == io.EOF {
                break
            }
            return "", err  // Eğer fonksiyon burada return olursa f burada kapatılır.
        }
    }
    return string(result), nil // Eğer fonksiyon burada return olursa f burada kapatılır.
}
```

`Close` gibi bir fonksiyona yapılan çağrıyı ertelemenin iki avantajı vardır. İlk olarak, dosyayı kapatmayı asla unutmayacağınızı garanti eder, daha sonra fonksiyonu yeni bir dönüş yolu eklemek için düzenlerseniz yapmanız kolay bir hatadır. İkincisi, kapanışı dosya açmaya yakın bir yere koymak, fonksiyonun sonuna yerleştirmekten çok daha net ve basittir.

Ertelenen fonksiyonun argümanları _(fonksiyon bir yöntemse alıcıyı (getter) içerir)_, çağrı yürütüldüğünde değil, erteleme yürütüldüğünde değerlendirilir. Bu, fonksiyon yürütülürken değişkenlerin değerlerinin değişmesiyle ilgili endişelerden kaçınmanın yanı sıra, tek bir ertelenmiş çağrı sitesinin birden çok fonksiyon yürütmesini erteleyebileceği anlamına gelir. İşte saçma bir örnek:

```go
for i := 0; i < 5; i++ {
    defer fmt.Printf("%d ", i)
}
```

Ertelenen fonksiyonlar LIFO _(son giren - ilk çıkar)_ sırasına göre yürütülür, bu nedenle bu kod, fonksiyon döndüğünde `4 3 2 1 0`'ın yazdırılmasına neden olur. Daha makul bir örnek, program aracılığıyla fonksiyonun yürütülmesini izlemenin basit bir yoludur. Bunun gibi birkaç basit izleme _(tracing)_ rutini yazabiliriz:

```go
func trace(s string)   { fmt.Println("izleniyor:", s) }
func untrace(s string) { fmt.Println("bırakılıyor:", s) }

// Onları bu şekilde kullan:
func a() {
    trace("a")
    defer untrace("a")
    // birşeyler yap....
}
```

Ertelenen fonksiyonlara ilişkin argümanların, erteleme yürütüldüğünde değerlendirildiği gerçeğinden yararlanarak daha iyisini yapabiliriz. İzleme rutini, izlemeyi kaldırma rutininin argümanını ayarlayabilir. İşte örnek:

```go
func trace(s string) string {
    fmt.Println("izleniyor:", s)
    return s
}

func un(s string) {
    fmt.Println("bırakılıyor:", s)
}

func a() {
    defer un(trace("a"))
    fmt.Println("a'da")
}

func b() {
    defer un(trace("b"))
    fmt.Println("b'de")
    a()
}

func main() {
    b()
}
```

Çıktısı şu sonucu verir:

```
izleniyor: b
b'de
izleniyor: a
a'da
bırakılıyor: a
bırakılıyor: b
```

Diğer dillerden blok düzeyinde _(block-level)_ kaynak yönetimine alışmış programcılar için erteleme tuhaf görünebilir. Ancak ertelemenin kullanıldığı en güçlü uygulama alanı blok-tabanlı değil, fonksiyon tabanlıdır. `panic` _(panik)_ ve `recover` _(iyileştirme)_ bölümünde, olasılıklarının başka bir örneğini göreceğiz.

## Veri (Data)

### &#x20;`new` ile Tahsis _(Allocation) Etme_

{% hint style="info" %}
_Bu bölümde tabir olarak "tahsis etmek", "ayırmak" çevirileri "allocation" için kullanılmıştır. Özetle hafıza üzerinde bir alan ayrılması anlamına gelir._
{% endhint %}

Go, yerleşik fonksiyonlar `new` ve `make` olmak üzere iki tahsis ilkesine sahiptir. Farklı şeyler yaparlar ve farklı türlere uygulanırlar, bu da kafa karıştırıcı olabilir, ancak kurallar basittir. Önce `new`'den bahsedelim. Bellek tahsis eden yerleşik bir fonksiyondur, ancak diğer bazı dillerdeki adlarından farklı olarak belleği başlatmaz, yalnızca sıfırlar _(yani yazar atama işlemi yapmadığını söylüyor)_. Yani, `new(T)`, `T` türünde yeni bir öğe için sıfırlanmış depolama tahsis eder ve adresini, `*T` türünde bir değer olarak döndürür. Go terminolojisinde, `T` türünde yeni tahsis edilen sıfır değerine bir işaretçi _(pointer)_ döndürür. `new` tarafından döndürülen bellek sıfırlandığından, veri yapılarınızı tasarlarken her türün sıfır değerinin daha fazla başlatma _(initializing)_ olmadan kullanılabileceği şekilde düzenlemeniz yararlı olur. Bu, veri yapısını `new` ile yeni bir tane oluşturabileceği ve hemen çalışmaya başlayabileceği anlamına gelir. Örneğin, `bytes.Buffer` dökümanı, "`Buffer`'ın sıfır değeri, kullanıma hazır bir `buffer`'dır." diye belirtir. Benzer şekilde, `sync.Mutex`'in açık bir kurucusu (constructor) veya `Init` metodu yoktur. Bunun yerine, bir `sync.Mutex` için sıfır değeri, kilidi açılmış bir `mutex` `(unlocked mutex)` olarak tanımlanır.

Sıfır-değer-kullanışlıdır _(zero-value-is-useful)_ özelliği geçişli olarak çalışır. Şimdi bir tip belirtimini düşünelim.

```go
type SyncedBuffer struct {
    lock    sync.Mutex
    buffer  bytes.Buffer
}
```

`SyncedBuffer` tipindeki değerler de tahsis veya sadece belirtim üzerine hemen kullanıma hazırdır. Sonraki snippet'te, hem `p` hem de `v`, daha fazla düzenleme olmadan doğru şekilde çalışacaktır.

```go
p := new(SyncedBuffer)  // type *SyncedBuffer
var v SyncedBuffer      // type  SyncedBuffer
```

### Kurucu ve Bileşik Değişmezler (Constructors and composite literals) <a href="#composite_literals" id="composite_literals"></a>

Bazen sıfır değeri yeterince iyi değildir ve `os` paketinden türetilen bu örnekte olduğu gibi bir başlatıcı _(initializer)_ kurucu gereklidir.

```go
func NewFile(fd int, name string) *File {
    if fd < 0 {
        return nil
    }
    f := new(File)
    f.fd = fd
    f.name = name
    f.dirinfo = nil
    f.nepipe = 0
    return f
}
```

Bu şekilde kullanmak, her tanımlama yapıldığında uzun olacağı için, daha kısa bir yol olan aşağıdaki yöntemi deneyebiliriz:

```go
func NewFile(fd int, name string) *File {
    if fd < 0 {
        return nil
    }
    f := File{fd, name, nil, 0}
    return &f
}
```

C'den farklı olarak, yerel bir değişkenin adresini döndürmenin tamamen uygun olduğunu unutmayın; değişkenle ilişkili depolama, fonksiyon döndükten sonra varlığını sürdürür. Aslında, bir bileşik değişmezin adresini almak, her değerlendirildiğinde yeni bir örnek tahsis eder, bunun için son iki satırı birleştirmemiz daha mantıklı olur.

```go
return &File{fd, name, nil, 0}
```

Bileşik değişmezin alanları sırayla düzenlenir ve hepsinin mevcut olması gerekir. Bununla birlikte, öğeleri açıkça `anahtar:değer` _(`key:value`)_ çiftleri olarak etiketleyerek, başlatıcılar, eksik olan alanlara otomatik olarak sıfır değerlerini verir. Aşağıdaki gibi de olabilirdi:

```go
return &File{fd: fd, name: name}
```

Sınırlayıcı bir durum olarak, bir bileşik değişmez hiç alan içermiyorsa, tür için sıfır değeri oluşturur. Yani `new(File)` ve `&File{}` ifadeleri eşdeğerdir.

Diziler _(arrays)_, dilimler _(slices)_ ve map'ler için, alan etiketleri uygun şekilde indeks veya map anahtarları _(key)_ olacak şekilde bileşik değişmez değerler de oluşturulabilir. Bu örnekte, başlatmalar _(init)_, farklı oldukları sürece `Enone`, `Eio` ve `Einval` değerlerinden bağımsız olarak çalışır.

```go
a := [...]string   {Enone: "hata yok", Eio: "Eio", Einval: "geçersiz argüman"}
s := []string      {Enone: "hata yok", Eio: "Eio", Einval: "geçersiz argüman"}
m := map[int]string{Enone: "hata yok", Eio: "Eio", Einval: "geçersiz argüman"}
```

### make ile Tahsis Etme
Tahsis etmeye geri dönelim. Yerleşik fonksiyon olan  `make(T, args)`, `new(T)`'den farklı bir amaca hizmet eder. Yalnızca dilimler _(slices)_, eşlemeler _(maps)_ ve kanallar _(channels)_ oluşturur ve T türünde _(*T değil)_ başlatılmış (sıfırlanmamış) bir değer döndürür. Ayrımın nedeni, bu üç türün aslında kullanımdan önce başlatılması gereken veri yapılarına referansları temsil etmesidir. Örneğin bir dilim _(slice)_, verilere `(bir dizi içinde)`, uzunluk _(len)_ ve kapasiteye _(cap)_ yönelik bir işaretçi içeren üç öğeli bir tanımlayıcıdır. Bu öğeler başlatılıncaya kadar dilim _(slice)_ sıfırdır. Dilimler _(slices)_, eşlemeler _(maps)_ ve kanallar _(channels)_ için `make`, dahili veri yapısını başlatır ve değeri kullanıma hazırlar. Örneğin,

```go
make([]int, 10, 100)
```

100 int'lik bir dizi (array) ayırır ve ardından dizinin ilk 10 öğesini işaret eden 10 uzunluğunda ve 100 kapasiteli bir dilim yapısı oluşturur. _(Bir dilim oluşturulurken kapasite atlanabilir; daha fazla bilgi için dilimler bölümüne bakın.)_ Buna karşılık, `new([]int)` yeni tahsis edilmiş, sıfırlanmamış olarak, yani sadece bir işaretçi döndürür.

Bu örnekler, `new` ve `make` arasındaki farkı göstermektedir.

```go
var p *[]int = new([]int)       // bir dilim yapısı tahsis eder; *p == nil; nadiren kullanışlıdır
var v  []int = make([]int, 100) // v dilimi şimdi 100 int'lik yeni bir diziye atıfta bulunuyor

// Gereksiz karışıklık:
var p *[]int = new([]int)
*p = make([]int, 100, 100)

// Deyimsel (mantıklı):
v := make([]int, 100)
```

`make` öğesinin yalnızca haritalar _(maps)_, dilimler _(slices)_ ve kanallar _(channels)_ için geçerli olduğunu ve bir işaretçi döndürmediğini unutmayın. Açık bir işaretçi elde etmek için `new` ile tahsis edin veya bir değişkenin adresini açıkça alın.

### Diziler (Arrays)

Diziler _(arrays)_, ayrıntılı bellek düzenini planlarken kullanışlıdır ve bazen ayırmayı _(tahsisi)_ önlemeye yardımcı olabilir, ancak öncelikle bir sonraki bölümün konusu olan dilimler _(slices)_ için bir yapı taşıdır. Bu konunun temellerini atmak için diziler _(arrays)_ hakkında birkaç kelimeyi burada bulabilirsiniz.

Dizilerin Go ve C'de çalışma biçimleri arasında büyük farklılıklar vardır. Go'da,

- Diziler değerlerdir. Bir diziyi diğerine atamak tüm öğeleri kopyalar.
- Özellikle, bir diziyi bir fonksiyona iletirseniz, dizinin bir işaretçisini değil, bir kopyasını alır.
- Bir dizinin boyutu, türünün bir parçasıdır. [10]int ve [20]int türleri farklıdır.

**value** özelliği yararlı olabilir ama aynı zamanda pahalı olabilir; C benzeri davranış ve verimlilik istiyorsanız, diziye bir işaretçi iletebilirsiniz.

```go
func Sum(a *[3]float64) (sum float64) {
    for _, v := range *a {
        sum += v
    }
    return
}

array := [...]float64{7.0, 8.5, 9.1}
x := Sum(&array)  // Adresini verelim
```
Ama bu tarz bile deyimsel Go değil. Bunun yerine dilimleri _(slices)_ kullanın.

### Dilimler (Slices)

Dilimler, veri dizilerine daha genel, güçlü ve kullanışlı bir arabirim sağlamak için dizileri sarar. Dönüşüm matrisleri gibi açık boyutu olan öğeler dışında, Go'daki dizi programlamanın çoğu basit diziler yerine dilimlerle yapılır.

Dilimler, altta yatan bir diziye referanslar tutar ve bir dilimi diğerine atarsanız, her ikisi de aynı diziye başvurur. Bir fonksiyon, bir dilim bağımsız değişkeni alırsa, dilimin öğelerinde yaptığı değişiklikler, alttaki diziye bir işaretçi iletmeye benzer şekilde çağıran tarafından görülebilir. Bu nedenle bir `Read` fonksiyonu, bir işaretçi ve bir sayı yerine bir dilim bağımsız değişkenini kabul edebilir; dilim içindeki uzunluk, okunacak veri miktarının üst sınırını belirler. İşte `package os`'taki `File` type'ın `Read` methodunun imzası:

```go
func (f *File) Read(buf []byte) (n int, err error)
```
Method, okunan bayt sayısını ve varsa bir hata değeri döndürür. Daha büyük bir arabelleğinin _(buf)_ ilk 32 baytını okumak için, arabelleği dilimleyin _(burada bir fiil olarak kullanılır)_.

```go
n, err := f.Read(buf[0:32])
```
Bu tür dilimleme yaygın ve verimlidir. Aslında verimliliği bir an için bir kenara bırakırsak, aşağıdaki kod parçası arabelleğin ilk 32 baytını da okuyacaktı.

```go
var n int
var err error
for i := 0; i < 32; i++ {
	nbytes, e := f.Read(buf[i:i+1])  // bir byte oku.
	n += nbytes
	if nbytes == 0 || e != nil {
		err = e
		break
	}
}
```

Bir dilimin uzunluğu, alttaki dizinin sınırları içinde kaldığı sürece değiştirilebilir; sadece kendisinin bir dilimine atayın. Yerleşik fonksiyon başlığıyla erişilebilen bir dilimin kapasitesi, dilimin alabileceği maksimum uzunluğu bildirir. İşte bir dilime veri eklemek için bir fonksiyon. Veri kapasiteyi aşarsa, dilim yeniden tahsis edilir. Ortaya çıkan dilim döndürülür. Fonksiyon, sıfır dilimine uygulandığında `len` ve `cap`'in geçerli olduğu gerçeğini kullanır ve `0` döndürür.

```go
func Append(slice, data []byte) []byte {
    l := len(slice)
    if l + len(data) > cap(slice) {  // yeniden tahsis etme
		// Gelecekteki büyüme için gerekenin iki katını tahsis edin.
        newSlice := make([]byte, (l+len(data))*2)
        // copy fonksiyonu önceden bildirilir ve herhangi bir dilim türü için çalışır.
        copy(newSlice, slice)
        slice = newSlice
    }
    slice = slice[0:l+len(data)]
    copy(slice[l:], data)
    return slice
}
```

Dilimi daha sonra döndürmeliyiz çünkü `Append` dilimin öğelerini değiştirebilse de `slice`'ın kendisi _(işaretçiyi, uzunluğu ve kapasiteyi tutan çalışma zamanı (runtime) veri yapısı)_ değere göre geçirilir.

Bir dilime ekleme fikri o kadar kullanışlıdır ki, yerleşik ekleme fonksiyonu tarafından ilgilenilir. Bu fonksiyonun tasarımını anlamak için biraz daha bilgiye ihtiyacımız var, bu yüzden ona daha sonra döneceğiz.

### İki Boyutlu Dilimler

Go'nun dizileri ve dilimleri tek boyutludur. Bir 2B dizi veya dilimin eşdeğerini oluşturmak için, bir diziler dizisi veya dilimler dilimi tanımlamak gerekir, bunun gibi:

```go
type Transform [3][3]float64  // 3x3'lük bir dizi, gerçekten bir dizi dizisi.
type LinesOfText [][]byte     // Bir byte dilimleri dilimi.
```
Dilimler değişken uzunlukta olduğundan, her bir iç dilimin farklı uzunlukta olması mümkündür. Bu, `LinesOfText` örneğimizde olduğu gibi yaygın bir durum olabilir: her satırın bağımsız bir uzunluğu vardır.

```go
text := LinesOfText{
    []byte("Now is the time"),
    []byte("for all good gophers"),
    []byte("to bring some fun to the party."),
}
```

Bazen, örneğin piksel tarama satırlarını işlerken ortaya çıkabilecek bir durum olan bir 2B dilimi tahsis etmek gerekir. Bunu başarmanın iki yolu var. Birincisi, her dilimi bağımsız olarak tahsis etmektir; diğeri, tek bir dizi tahsis etmek ve tek tek dilimleri bunun içine işaret etmektir. Hangisinin kullanılacağı uygulamanıza bağlıdır. Dilimler büyüyebilir veya küçülebilirse, bir sonraki satırın üzerine yazılmasını önlemek için bağımsız olarak tahsis edilmelidir; değilse, nesneyi tek bir tahsisle oluşturmak daha verimli olabilir. Başvuru için, burada iki yöntemin eskizleri bulunmaktadır. İlk olarak, her seferinde bir satır:

```go
// Üst-seviye bir dilim tahsis et.
picture := make([][]uint8, YSize) // her y birimi için bir satır.
// Dilimleri her satıra ayırarak satırlar üzerinde döngü yapın.
for i := range picture {
    picture[i] = make([]uint8, XSize)
}
```

Ve şimdi satırlara bölünmüş tek bir tahsis olarak:

```go
// Önceki ile aynı olarak, üst seviye bir dilim tahsis et.
picture := make([][]uint8, YSize) // her y birimi için bir satır.
// tüm pixelleri tutmak için büyük bir dilim tahsis et.
pixels := make([]uint8, XSize*YSize) // Resim [][]uint8 olmasına rağmen []uint8 tipine sahip.
// Kalan piksel diliminin önünden her satırı dilimleyerek satırlar üzerinde döngü yapın.
for i := range picture {
    picture[i], pixels = pixels[:XSize], pixels[XSize:]
}
```

### Eşlemeler (Maps)

Eşlemeler _(maps)_, bir türdeki _(anahtar)_ değerleri başka bir türdeki _(öğe veya değer)_ değerlerle ilişkilendiren kullanışlı ve güçlü yerleşik bir veri yapısıdır. Anahtar, tamsayılar, kayan noktalı ve karmaşık sayılar, dizeler, işaretçiler, arabirimler _(interfaces)_ _(dinamik tür eşitliği desteklediği sürece)_, yapılar ve diziler gibi eşitlik operatörünün tanımlandığı herhangi bir tür olabilir. Dilimler üzerinde eşitlik tanımlanmadığından eşleme anahtarı olarak kullanılamazlar. Dilimler gibi, eşlemeler de altta yatan bir veri yapısına referanslar içerir. Bir eşlemeyi, eşlemenin içeriğini değiştiren bir fonksiyona iletirseniz, değişiklikler çağıranda görünür olacaktır.

Eşlemeler, iki nokta üst üste ile ayrılmış anahtar/değer çiftleri ile olağan bileşik hazır bilgi sözdizimi kullanılarak oluşturulabilir, bu nedenle başlatma sırasında bunları oluşturmak kolaydır.

```go
var timeZone = map[string]int{
    "UTC":  0*60*60,
    "EST": -5*60*60,
    "CST": -6*60*60,
    "MST": -7*60*60,
    "PST": -8*60*60,
}
```

Eşleme değerleri atamak ve getirmek sözdizimsel olarak anahtarın bir tamsayı olması gerekmemesi dışında diziler ve dilimler için aynı şeyi yapmak gibi görünür.

```go
offset := timeZone["EST"]
```

Eşlemede bulunmayan bir anahtarla bir eşleme değeri getirme girişimi, eşlemedeki girişlerin türü için sıfır değerini döndürür. Örneğin, eşleme tamsayılar içeriyorsa, var olmayan bir anahtarın aranması 0 değerini döndürür. Bir küme, `bool` değer türüyle bir eşleme olarak uygulanabilir. Değeri kümeye koymak için eşleme girişini `true` olarak ayarlayın ve ardından basit indeksleme ile test edin.

```go
attended := map[string]bool{
    "Ann": true,
    "Joe": true,
    ...
}

if attended[person] { // person eşlemede bulunmuyorsa false dönecektir
    fmt.Println(person, "buluşmadaydı")
}
```

Bazen eksik bir girişi sıfır değerinden ayırmanız gerekir. `"UTC"` için bir giriş var mı yoksa eşlemede hiç olmadığı için `0` mı? Çoklu atama şeklinde ayrımını yapabilirsiniz.

```go
var seconds int
var ok bool
seconds, ok = timeZone[tz]
```

Bariz nedenlerden dolayı buna **“comma ok”** _(virgül tamam)_ deyimi denir. Bu örnekte, `tz` mevcutsa, saniyeler uygun şekilde ayarlanacak ve `ok` `true` olacaktır; değilse, saniye sıfıra ayarlanır ve `ok` `false` olur. İşte onu güzel bir hata raporuyla bir araya getiren bir fonksiyon:

```go
func offset(tz string) int {
    if seconds, ok := timeZone[tz]; ok {
        return seconds
    }
    log.Println("unknown time zone:", tz)
    return 0
}
```

Gerçek değer hakkında endişelenmeden eşlemede var olup olmadığını test etmek için, değer için olağan değişken yerine [boş tanımlayıcıyı (_)](change) kullanabilirsiniz.

```go
_, present := timeZone[tz]
```

Bir eşleme girdisini silmek için argümanları silinecek eşleme ve anahtar olan yerleşik silme fonksiyonunu kullanın. Anahtar zaten eşlemede olmasa bile bunu yapmak güvenlidir.

```go
delete(timeZone, "PDT")  // Şimdi standart saatte
```

### Yazdırma

Go'da biçimlendirilmiş yazdırma, C'nin `printf` ailesine benzer bir stil kullanır ancak daha zengin ve daha geneldir. Fonksiyonlar `fmt` paketinde bulunur ve adları büyük harfle yazılır: `fmt.Printf`, `fmt.Fprintf`, `fmt.Sprintf` vb. Dize _(String)_ fonksiyonları _(Sprintf vb.)_, sağlanan bir arabelleği doldurmak yerine bir dize _(string)_ döndürür.

Bir biçim dizesi _(string'i)_ sağlamanız gerekmez. `Printf`, `Fprintf` ve `Sprintf`'in her biri için başka bir fonksiyon çifti vardır, örneğin `Print` ve `Println`. Bu fonksiyonlar bir biçim dizesi almaz, bunun yerine her bağımsız değişken için varsayılan bir biçim oluşturur. `Println` içerisine değer verildiğinde ayrıca bağımsız değişkenler arasına bir boşluk ekler ve çıktıya yeni bir satır eklerken, `Print` sürümleri yalnızca işlenen her iki tarafta da bir dize değilse boşluk ekler. Bu örnekte her satır aynı çıktıyı üretir.

```go
fmt.Printf("Hello %d\n", 23)
fmt.Fprint(os.Stdout, "Hello ", 23, "\n")
fmt.Println("Hello", 23)
fmt.Println(fmt.Sprint("Hello ", 23))
```

Biçimlendirilmiş yazdırma fonksiyonları `fmt.Fprint` ve arkadaşları, `io.Writer` arabirimini uygulayan herhangi bir nesneyi ilk argüman olarak alır; `os.Stdout` ve `os.Stderr` değişkenleri bilinen örneklerdir.

Burada işler C'den sapmaya başlar. İlk olarak, `%d` gibi sayısal biçimler imza veya boyut için bayrak almaz; bunun yerine yazdırma yordamları, bu özelliklere karar vermek için bağımsız değişkenin türünü kullanır.

```go
var x uint64 = 1<<64 - 1
fmt.Printf("%d %x; %d %x\n", x, x, int64(x), int64(x))
```

aşağıdaki çıktıyı verir,

```
18446744073709551615 ffffffffffffffff; -1 -1
```

Tamsayılar için ondalık gibi yalnızca varsayılan dönüştürmeyi istiyorsanız, `%v` _(value'nun v'si)_ tümünü yakalama biçimini kullanabilirsiniz; sonuç tam olarak `Print` ve `Println`'in üreteceği şeydir. Ayrıca, bu format herhangi bir değeri, hatta dizileri, dilimleri, yapıları ve eşlemeleri yazdırabilir. Burada, önceki bölümde tanımlanan saat dilimi eşlemesi için bir `print` ifadesi bulunmaktadır.

```go
fmt.Printf("%v\n", timeZone)  // ya da sadece fmt.Println(timeZone)
```

aşağıdaki çıktıyı verir,

```
map[CST:-21600 EST:-18000 MST:-25200 PST:-28800 UTC:0]
```

Eşlemeler için, `Printf` ve arkadaşları çıktıyı sözlüksel olarak anahtara göre sıralar.

Bir yapı yazdırılırken, değiştirilmiş biçim `%+v`, yapının alanlarına adlarıyla açıklama ekler ve herhangi bir değer için alternatif biçim `%#v`, değeri tam Go sözdiziminde yazdırır.

```go
type T struct {
    a int
    b float64
    c string
}
t := &T{ 7, -2.35, "abc\tdef" }
fmt.Printf("%v\n", t)
fmt.Printf("%+v\n", t)
fmt.Printf("%#v\n", t)
fmt.Printf("%#v\n", timeZone)
```

aşağıdaki çıktıyı verir,

```
&{7 -2.35 abc   def}
&{a:7 b:-2.35 c:abc     def}
&main.T{a:7, b:-2.35, c:"abc\tdef"}
map[string]int{"CST":-21600, "EST":-18000, "MST":-25200, "PST":-28800, "UTC":0}
```

_(Ampersand '&' işaretine dikkat edin.)_ Bu alıntılanan `string` biçimi, `string` veya `[]byte` türünde bir değere uygulandığında `%q` yoluyla da kullanılabilir. Alternatif biçim `%#q`, mümkünse bunun yerine ters tırnak kullanır. _(`%q` biçimi aynı zamanda tamsayılar _(int)_ ve rünler (rune) için de geçerlidir ve tek tırnaklı bir rune sabiti üretir.)_ Ayrıca, `%x` string'ler (dizeler), bayt dizileri ve bayt dilimleri üzerinde olduğu kadar tamsayılar üzerinde de çalışır, uzun bir onaltılık dize ve bir boşluk oluşturur _(% x)_ biçiminde baytlar arasına boşluk koyar.

Başka bir kullanışlı biçim, bir değerin türünü yazdıran `%T`'dir.

```go
fmt.Printf("%T\n", timeZone)
```

aşağıdaki çıktıyı verir,

```
map[string]int
```

Özel bir tür için varsayılan biçimi kontrol etmek istiyorsanız, tüm yapmanız gereken, tür üzerinde imza `String()` dizesiyle bir yöntem tanımlamaktır. Basit `T` tipimiz için bu şöyle görünebilir.

```go
func (t *T) String() string {
    return fmt.Sprintf("%d/%g/%q", t.a, t.b, t.c)
}
fmt.Printf("%v\n", t)
```

aşağıdaki sonucu verir,

```
7/-2.35/"abc\tdef"
```

_(`T` tipi değerlerin yanı sıra `T` işaretçilerini de yazdırmanız gerekiyorsa, `String` alıcısı değer türünde olmalıdır; bu örnek, yapı türleri için daha verimli ve deyimsel olduğu için bir işaretçi kullanmıştır. Daha fazla bilgi için [işaretçiler vs. değer alıcıları](change) bölümüne bakın.)_

`String` methodumuz `Sprintf`'i çağırabilir çünkü yazdırma yordamları tamamen yeniden girilebilir ve bu şekilde sarılabilir. Bununla birlikte, bu yaklaşımla ilgili anlaşılması gereken önemli bir ayrıntı vardır: `Sprintf`'i, `String` methodunuza süresiz olarak tekrarlanacak şekilde çağırarak bir `String` methodu oluşturmayın. Bu, `Sprintf` çağrısı alıcıyı doğrudan bir dizi olarak yazdırmaya çalışırsa, bu da methodu tekrar çağırırsa olabilir. Bu örneğin gösterdiği gibi, yaygın ve yapılması kolay bir hatadır.

```go
func (m MyString) String() string {
    return fmt.Sprintf("MyString=%s", m) // Hata: sonsuza kadar sürer.
}
```

Düzeltmesi de kolaydır: bağımsız değişkeni, methodu olmayan temel dize (string) türüne dönüştürün.

```go
type MyString string
func (m MyString) String() string {
    return fmt.Sprintf("MyString=%s", string(m)) // OK: ilkel string'e dönüştürün.
}
```

[Atama bölümü](change)nde, bu yinelemeyi önleyen başka bir teknik göreceğiz.

Başka bir yazdırma tekniği, bir yazdırma rutininin argümanlarını doğrudan başka bir rutine iletmektir. `Printf`'in imzası, formattan sonra rastgele sayıda parametrenin (isteğe bağlı türde) görünebileceğini belirtmek için son bağımsız değişkeni olarak `...interface{}` türünü kullanır.

```go
func Printf(format string, v ...interface{}) (n int, err error) {
```

`Printf` fonksiyonunda `v`, `[]interface{}` türünde bir değişken gibi davranır, ancak başka bir değişken fonksiyona iletilirse, normal bir bağımsız değişken listesi gibi davranır. İşte yukarıda kullandığımız `log.Println` fonksiyonunun uygulaması. Gerçek biçimlendirme için bağımsız değişkenlerini doğrudan `fmt.Sprintln`'e iletir.

```go
// Println, standart logger'a fmt.Println biçiminde yazdırır.
func Println(v ...interface{}) {
    std.Output(2, fmt.Sprintln(v...))  // output (int, string) şeklinde iki parametre alır.
}
```

Derleyiciye `v`'yi bir bağımsız değişkenler listesi olarak ele almasını söylemek için `Sprintln`'e iç içe çağrıda `v`'den sonra `...` yazarız; aksi takdirde `v`'yi tek bir dilim bağımsız değişkeni olarak iletirdi

Yazdırmak için burada ele aldığımızdan daha fazlası var. Ayrıntılar için `fmt` paketi için `godoc` belgelerine bakın.

Bu arada, bir `...` parametresi belirli bir türde olabilir, örneğin bir tamsayı listesinden en azını seçen bir `min` fonksiyonu için `...int`:

```go
func Min(a ...int) int {
    min := int(^uint(0) >> 1)  // en büyük int
    for _, i := range a {
        if i < min {
            min = i
        }
    }
    return min
}
```

### Append _(Ekle)_

Artık, `append` yerleşik fonksiyonunun tasarımını açıklamak için ihtiyacımız olan eksik parçaya sahibiz. `Append`'in imzası, yukarıdaki özel `Append` fonksiyonumuzdan farklıdır. Şematik olarak, şöyle:

```go
func append(slice []T, elements ...T) []T
```

Burada `T`, herhangi bir tür için yer tutucudur. Go'da, `T` türünün arayan tarafından belirlendiği bir fonksiyonu gerçekten yazamazsınız. Bu nedenle `append` yerleşiktir: derleyiciden desteğe ihtiyacı vardır. `Append`'in yaptığı, öğeleri dilimin sonuna eklemek ve sonucu döndürmektir.

Bizim el ile yazdığımız `Append`'te olduğu gibi, sonucun döndürülmesi gerekiyor. Basit bir örnek:

```go
x := []int{1,2,3}
x = append(x, 4, 5, 6)
fmt.Println(x)
```

`[1 2 3 4 5 6]` yazdırır. Bu nedenle, ekleme biraz `Printf` gibi çalışır ve rastgele sayıda argüman toplar.

Peki ya `Append`'imizin yaptığını yapmak ve bir dilime bir dilim eklemek istersek? Kolay: yukarıdaki Çıktı çağrısında yaptığımız gibi çağrı sitesinde `...` kullanın. Bu snippet, yukarıdakiyle aynı çıktıyı üretir.

```go
x := []int{1,2,3}
y := []int{4,5,6}
x = append(x, y...)
fmt.Println(x)
```

`...` olmadan, türler yanlış olacağından derlenmez; `y`, `int` türünde değil.

## Tanımlama _(Initialization)_

C veya C++'daki tanımlamadan yüzeysel olarak çok farklı görünmese de, Go'daki tanımlama daha güçlüdür. Tanımlama sırasında karmaşık yapılar oluşturulabilir ve tanımlanan nesneler arasındaki, hatta farklı paketler arasındaki sıralama sorunları doğru bir şekilde ele alınır.

### Sabitler _(Constants)_

Go'daki sabitler tam da budur - sabit. Fonsiyonlarda yerel olarak tanımlansalar bile derleme zamanında oluşturulurlar ve yalnızca sayılar, karakterler _(rünler)_, dizeler veya boolean'lar olabilirler. Derleme zamanı kısıtlaması nedeniyle, bunları tanımlayan ifadeler, derleyici tarafından değerlendirilebilen sabit ifadeler olmalıdır. Örneğin, `1<<3` sabit bir ifadeyken, `math.Sin(math.Pi/4)` fonksiyonu, `math.Sin` fonksiyonu çağrısının çalışma zamanında gerçekleşmesi gerektiğinden değildir.

Go'da, numaralandırılmış _(enumerated)_ sabitler, `iota` Numaralandırıcı kullanılarak oluşturulur. `iota` bir ifadenin parçası olabildiği ve ifadeler dolaylı olarak tekrarlanabildiği için, karmaşık değer kümeleri oluşturmak kolaydır.

```go
type ByteSize float64

const (
    _           = iota // boş tanımlayıcı kullanarak ilk elemanı görmezden gel
    KB ByteSize = 1 << (10 * iota)
    MB
    GB
    TB
    PB
    EB
    ZB
    YB
)
```

Herhangi bir kullanıcı tanımlı türe `String` gibi bir method ekleyebilme özelliği, rastgele değerlerin yazdırılmak üzere kendilerini otomatik olarak biçimlendirmesini mümkün kılar. Çoğu zaman yapılara _(struct)_ uygulandığını görseniz de bu teknik, `ByteSize` gibi ondalık sayı türleri gibi skaler türler için de kullanışlıdır.

```go
func (b ByteSize) String() string {
    switch {
    case b >= YB:
        return fmt.Sprintf("%.2fYB", b/YB)
    case b >= ZB:
        return fmt.Sprintf("%.2fZB", b/ZB)
    case b >= EB:
        return fmt.Sprintf("%.2fEB", b/EB)
    case b >= PB:
        return fmt.Sprintf("%.2fPB", b/PB)
    case b >= TB:
        return fmt.Sprintf("%.2fTB", b/TB)
    case b >= GB:
        return fmt.Sprintf("%.2fGB", b/GB)
    case b >= MB:
        return fmt.Sprintf("%.2fMB", b/MB)
    case b >= KB:
        return fmt.Sprintf("%.2fKB", b/KB)
    }
    return fmt.Sprintf("%.2fB", b)
}
```

`YB` ifadesi `1.00YB` olarak yazdırılırken `ByteSize(1e13)` `9.09TB` olarak yazdırılır. `ByteSize`'ın `String` ethodunu uygulamak için `Sprintf`'in burada kullanılması güvenlidir (süresiz olarak tekrar etmekten kaçınır)

Bu sefer bir dönüştürme nedeniyle değil, `Sprintf`'i bir dize _(string)_ biçimi olmayan `%f` ile çağırdığı için: `Sprintf`, yalnızca bir dize istediğinde `String` yöntemini çağırır ve `%f` bir ondalık sayılı değer istiyor.

### Değişkenler _(Variables)_

Değişkenler, tıpkı sabitler gibi tanımladabilir, ancak tanımlayıcı, çalışma zamanında hesaplanan genel bir ifade olabilir.

```go
var (
    home   = os.Getenv("HOME")
    user   = os.Getenv("USER")
    gopath = os.Getenv("GOPATH")
)
```

### init Fonksiyonu _(The init Function)_

Son olarak, her kaynak dosya, gereken durumu ayarlamak için kendi niladic _(parametresiz fonksiyon)_ `init` fonksiyonunu tanımlayabilir. _(Aslında her dosya birden çok `init` işlevine sahip olabilir.)_  `init`, paketteki tüm değişken tanımlayıcılarını değerlendirdikten sonra çağrılır ve bunlar yalnızca içe aktarılan tüm paketler başlatıldıktan sonra değerlendirilir.

Bildirim olarak ifade edilemeyen tanımlamaların yanı sıra, `init` fonksiyonlarının yaygın bir kullanımı, gerçek yürütme başlamadan önce program durumunun doğruluğunu onaylamak veya onarmak içindir.

```go
func init() {
    if user == "" {
        log.Fatal("$USER not set")
    }
    if home == "" {
        home = "/home/" + user
    }
    if gopath == "" {
        gopath = home + "/go"
    }
    // gopath --gopath flag'i girilince ezilecektir.
    flag.StringVar(&gopath, "gopath", gopath, "override default GOPATH")
}
```

## Methodlar

### İşaretçiler vs. Değerler

`ByteSize` ile gördüğümüz gibi, herhangi bir adlandırılmış tür için methodlar tanımlanabilir _(işaretçi veya arabirim hariç)_; alıcının bir yapı _(struct)_ olması gerekmez.

Yukarıdaki dilim tartışmasında, bir `Append` fonksiyonu yazdık. Bunun yerine dilimler üzerinde bir method olarak tanımlayabiliriz. Bunu yapmak için, önce methodu bağlayabileceğimiz adlandırılmış bir tür bildiririz ve ardından methodun alıcısını o türün bir değeri yaparız.

```go
type ByteSlice []byte

func (slice ByteSlice) Append(data []byte) []byte {
    // İçerik aslında yukarıdaki append fonksiyonu ile aynı olacak
}
```

Bu, yine de methodun güncellenmiş dilimi döndürmesini gerektirir. Bir `ByteSlice`'a işaretçiyi alıcısı olarak alma methodunu yeniden tanımlayarak bu beceriksizliği ortadan kaldırabiliriz, böylece method arayanın diliminin üzerine yazabilir.

```go
func (p *ByteSlice) Append(data []byte) {
    slice := *p
    // içerik return olmadan yukarıdaki gibi
    *p = slice
}
```

Aslında daha da iyisini yapabiliriz. Fonksiyonumuzu standart bir `Write` yöntemi gibi görünecek şekilde değiştirirsek, aşağıdaki gibi,

```go
func (p *ByteSlice) Write(data []byte) (n int, err error) {
    slice := *p
    // tekrar yukarıdaki gibi.
    *p = slice
    return len(data), nil
}
```

o zaman `*ByteSlice` türü kullanışlı olan standart `io.Writer` arabirimini karşılar. Örneğin, birine yazdırabiliriz.

```go
var b ByteSlice
    fmt.Fprintf(&b, "This hour has %d days\n", 7)
```

Bir `ByteSlice` adresini iletiyoruz çünkü yalnızca `*ByteSlice` `io.Writer`'ı karşılıyor. Alıcılar için işaretçiler ve değerler hakkındaki kural, değer methodlarının işaretçiler ve değerler üzerinde çağrılabilmesi, ancak işaretçi methodlarının yalnızca işaretçiler üzerinde çağrılabilmesidir.

Bu kural, işaretçi methodlarının alıcıyı değiştirebilmesi nedeniyle ortaya çıkar; onları bir değer üzerinde çağırmak, methodun değerin bir kopyasını almasına neden olur, bu nedenle herhangi bir değişiklik yapılır. Dolayısıyla dil bu hatayı kabul etmez. Yine de kullanışlı bir istisna var. Değer adreslenebilir olduğunda, dil, adres işlecini _(&)_ otomatik olarak ekleyerek bir değer üzerinde bir işaretçi methodunu çağırmaya ilişkin genel durumla ilgilenir. Örneğimizde, `b` değişkeni adreslenebilir, yani sadece `b.Write` ile onun `Write` methodunu çağırabiliriz. Derleyici bunu bizim için `(&b).Write` olarak yeniden yazacak.

Bu arada, bir bayt diliminde `Write` kullanma fikri, `bytes.Buffer` uygulamasının merkezinde yer alır.

## Arabirimler _(Interfaces)_ ve Diğer Tipler

### Arabirimler _(Interfaces)_

Go'daki arabirimler, bir nesnenin davranışını belirlemenin yolunu sağlar: eğer bir şey bunu yapabiliyorsa, o zaman burada kullanılabilir mantığını sağlar. Zaten birkaç basit örnek gördük; özel yazıcılar _(writer)_ bir `String` methoduyla uygulanabilirken `Fprintf`, `Write` methoduyla her şeye çıktı üretebilir. Yalnızca bir veya iki methodu olan arabirimler Go kodunda yaygındır ve genellikle bu methoddan türetilen bir ad verilir, örneğin `Write`'ı uygulayan bir şey için `io.Writer`.

Bir tür, birden çok arabirim uygulayabilir. Örneğin, bir koleksiyon, `Len()`, `Less(i, j int) bool` ve `Swap(i, j int)` içeren `sort.Interface` öğesini uygularsa, `sort` paketindeki yordamlara göre sıralanabilir ve ayrıca özel biçimlendiricisi vardır. Bu yapmacık örnekte `Sort` her ikisini de karşılar.

```go
type Sequence []int

// sort.Interface için gerekli olan methodlar.
func (s Sequence) Len() int {
    return len(s)
}
func (s Sequence) Less(i, j int) bool {
    return s[i] < s[j]
}
func (s Sequence) Swap(i, j int) {
    s[i], s[j] = s[j], s[i]
}

// Asıl dilimin üzerine etki etmemesi için kopyalama methodu
func (s Sequence) Copy() Sequence {
    copy := make(Sequence, 0, len(s))
    return append(copy, s...)
}

// Yazdırma için method - yazdırmadan önce elemanları sıralar.
func (s Sequence) String() string {
    s = s.Copy() // bir kopya oluştur; argümanın üzerine yazma.
    sort.Sort(s)
    str := "["
    for i, elem := range s { // O(N²) (Düşündüğünüz şey değil); bir sonraki örnekte düzeltilecek.
        if i > 0 {
            str += " "
        }
        str += fmt.Sprint(elem)
    }
    return str + "]"
}
```
_(Çevirmen açıklaması: O(N²) düşündüğünüz şey değil demektir.)_


