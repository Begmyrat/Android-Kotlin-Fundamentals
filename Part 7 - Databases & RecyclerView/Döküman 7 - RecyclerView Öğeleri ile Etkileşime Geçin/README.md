# <a name="1"></a>RecyclerView Öğeleri ile Etkileşime Geçin

- [Başlangıç kodunu alın & uygulamadaki değişiklikleri inceleyin](#a)
- [Öğeleri tıklanabilir yapın](#b)
- [Öğe tıklamalarını yönetin](#c)

Sleep-tracker uygulamasının, aşağıdaki şekilde gösterildiği gibi fragmentlarla temsil edilen iki ekranı vardır.

![app image](https://developer.android.com/codelabs/kotlin-android-training-grid-layout/img/76d78f63f88c3c86.png)
![app image](https://developer.android.com/codelabs/kotlin-android-training-grid-layout/img/43590f0a4c00e138.png)

Solda gösterilen ilk ekranda, izlemeyi başlatmak ve durdurmak için butonlar vardır. Ekran, kullanıcının tüm uyku verilerini gösterir. **Clear** butonu, uygulamanın kullanıcı için topladığı tüm verileri kalıcı olarak siler. Sağda gösterilen ikinci ekran, uyku kalitesi derecelendirmesini seçmek içindir.

Bu uygulama, uyku verilerini sürdürmek (persist) için bir UI controller, `ViewModel` ve `LiveData` ve bir `Room` veritabanı ile basitleştirilmiş bir mimari kullanır.

![architecture image](https://developer.android.com/codelabs/kotlin-android-training-grid-layout/img/49f975f1e5fe689.png)

Bu aşamada, bir kullanıcı ızgaradaki bir öğeye dokunduğunda aşağıdaki gibi bir ayrıntı ekranı açan yanıt verme yeteneği eklersiniz. Bu ekranın kodu (ragment, view model ve navigation) başlangıç uygulamasıyla sağlanır ve tıklama işleme mekanizmasını uygularsınız.

![detay image](https://developer.android.com/codelabs/kotlin-android-training-interacting-with-items/img/1018f2610bca049.png)

## <a name="a"></a>Aşama 1 : Başlangıç kodunu alın & uygulamadaki değişiklikleri inceleyin

>Önemli: Bu aşama için başlangıç uygulaması, önceki aşamadaki son TrackMySleepQuality uygulamasının parçası olmayan ek layoutlar, resourcelar, and utilityler sağlar. Bu aşama üzerinde çalışmak için sağlanan başlangıç kodunu kullanmanızı öneririz.

### Adım 1: Başlangıç uygulamasını alın

1. GitHub'dan RecyclerViewClickHandler-Starter kodunu indirin ve projeyi Android Studio'da açın.
2. Sleep-tracker starter uygulamasını build edin ve çalıştırın.

#### [Opsiyonel] Uygulamayı önceki aşamadan kullanmak istiyorsanız uygulamanızı güncelleyin

Bu codelab için GitHub'da sağlanan başlangıç uygulamasından çalışacaksanız bir sonraki adıma geçin.

Önceki aşamada oluşturduğunuz kendi sleep-tracker uygulamanızı kullanmaya devam etmek istiyorsanız, mevcut uygulamanızı details-screen fragment koduna sahip olacak şekilde güncellemek için aşağıdaki talimatları izleyin.

>İpucu: Dosya sisteminden Android Studio'ya dosya kopyalamak için bunları kopyalayıp yapıştırabilir veya sürükleyip bırakabilirsiniz.

1. Mevcut uygulamanızla devam ediyor olsanız bile, dosyaları kopyalayabilmek için GitHub'dan RecyclerViewClickHandler-Starter kodunu alın.
2. `sleepdetail` paketindeki tüm dosyaları kopyalayın.
3. `layout` klasöründe, `fragment_sleep_detail.xml` dosyasını kopyalayın.
4. `sleep_detail_fragment` için navigation'ı ekleyen `navigation.xml` dosyasının güncellenmiş içeriğini kopyalayın.
5. `database` paketinde, `SleepDatabaseDao`'da yeni `getNightWithId()` metodunu ekleyin:

```

/**
 * Verilen nightId ile geceyi seçer ve döndürür.
*/
@Query("SELECT * from daily_sleep_quality_table WHERE nightId = :key")
fun getNightWithId(key: Long): LiveData<SleepNight>

```

6. `res/values/strings`'e aşağıdaki string resource'unu ekleyin

```

<string name="close">Close</string>

```

7. Data binding'i güncellemek için uygulamanızı clean ve rebuild edin.

### Adım 2: Uyku ayrıntıları ekranı için kodu inceleyin

Bu aşamada, uyku gecesi için bir click handler (tıklama işleyicisi) uygulayacaksınız. Bir kez tıklandığında, uygulama o belirli uyku gecesi hakkında ayrıntıları gösteren bir fragment'a gidecektir. Başlangıç kodunuz zaten bu `SleepDetailFragment` için fragment ve navgation graph'i içerir, çünkü bu oldukça fazla koddur (ve fragmentlar ve navigation bu aşamanın bir parçası değildir). Aşağıdaki kod ayrıntılarını öğrenin:

1. Uygulamanızda `sleepdetail` paketini bulun. Bu paket, bir fragment, view model ve bir gecelik uykunun ayrıntılarını görüntüleyen fragment için bir view model factory içerir.
2. `sleepdetail` paketinde, `SleepDetailViewModel` kodunu açın ve inceleyin. Bu view model, constructor'da bir `SleepNight` için bir anahtar ve bir DAO alır.

Class'ın body'si, verilen anahtar için `SleepNight`'ı alacak koda ve **Close** düğmesine basıldığında `SleepTrackerFragment`'e geri navigation'ı kontrol etmek için `navigationToSleepTracker` değişkenine sahiptir.

`getNightWithId()` fonksiyonu, bir `LiveData<SleepNight>` döndürür ve `SleepDatabaseDao`'da (`database` paketinde) tanımlanır.

3. `sleepdetail` paketinde `SleepDetailFragment` kodunu açın ve inceleyin. Data binding, view model ve navigation için observer kurulumuna dikkat edin.
4. `sleepdetail` paketinde `SleepDetailViewModelFactory` kodunu açın ve inceleyin.
5. layout klasöründe, `fragment_sleep_detail.xml` dosyasını inceleyin. View model'dan her view'da görüntülenecek verileri almak için `<data>` tag'inde tanımlanan `sleepDetailViewModel` değişkenine dikkat edin.

Layout, uyku kalitesi için bir `ImageView`, kalite derecelendirmesi için bir `TextView`, uyku uzunluğu için bir `TextView` ve ayrıntı fragment'ını kapatmak için bir `Button` içeren bir `ConstraintLayout` içerir.

6. `navigation.xml` dosyasını açın. `sleep_tracker_fragment` için, `sleep_detail_fragment`'a yeni action'a dikkat edin.

Yeni action, `action_sleep_tracker_fragment_to_sleepDetailFragment`, uyku izleyici fragment'ından ayrıntılar ekranına yapılan navigation'dır.

## <a name="b"></a>Aşama 2 : Öğeleri tıklanabilir yapın
## <a name="c"></a>Aşama 3 : Öğe tıklamalarını yönetin
