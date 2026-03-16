


Cyber-LLM Tehdit Analiz Platformu Projesi

Hazırlayan : Elif Ece İŞÇİ 


1. Proje Özeti
Günümüz siber güvenlik ekosisteminde, saniyeler içerisinde binlerce log verisi üretilmektedir. Bir güvenlik analistinin bu ham verileri (IP adresleri, port bilgileri, hata kodları) manuel olarak inceleyip anlamlı bir saldırı raporuna dönüştürmesi zaman ve kaynak açısından verimsizdir.
Bu projenin amacı: Ham sistem loglarını Doğal Dil İşleme (NLP) teknikleri ve Büyük Dil Modelleri (LLM) kullanarak otomatik olarak analiz eden, tehdit seviyesini belirleyen ve saniyeler içinde aksiyon planı sunan bir yapay zeka asistanı geliştirmektir.

2. Problem Tanımı
Günümüzde siber güvenlik ekipleri çok sayıda log, olay ve alarm ile karşı karşıyadır. Bu verilerin manuel olarak analiz edilmesi zaman kaybına, kritik tehditlerin gözden kaçmasına ve operasyonel maliyetlerin artmasına neden olmaktadır. Meydana gelen siber olay verilerinin hangi tehdit seviyesinde olduğunu (Düşük–Orta–Kritik) hızlı ve tutarlı biçimde belirleyebilecek otomatik bir sınıflandırma sisteminin eksikliğini gidermek hedeflenmiştir.

3. Çözüm Yaklaşımı
Bu problem, LLM tabanlı hibrit bir mimari ile ele alınmıştır.
Geliştirilen çözüm iki temel katmandan oluşmaktadır:
1.	LLM Tabanlı Anlamsal Analiz Katmanı
	Siber olay/log verilerinin doğal dil temsillerini işler.
	Olayların bağlamını, ciddiyetini ve potansiyel etkisini yorumlar.
	LLM çıktılarından sayısal ve kategorik öznitelikler türetilir.
2.	Makine Öğrenmesi Tabanlı Sınıflandırma Katmanı
	LLM tarafından üretilen öznitelikler modele girdi olarak verilir.
	Tehdit seviyesi (Düşük – Orta – Kritik) tahmini yapılır.

Bu hibrit yaklaşım sayesinde, LLM’in güçlü bağlamsal anlama yetenekleri ile geleneksel sınıflandırma algoritmalarının kararlı yapısı birleştirilmiştir.


4.1 Gereksinim Analizi
Fonksiyonel Gereksinimler:
	Veri setini okuyabilme
	Veri ön işleme uygulayabilme
	Çok sınıflı (multi-class) sınıflandırma yapabilme
	Model performansını ölçebilme
	Karışıklık matrisi ve sınıflandırma raporu üretebilme
Fonksiyonel Olmayan Gereksinimler:
	Düşük gecikme süresi
	Yorumlanabilir sonuçlar
	CPU üzerinde çalışabilirlik
	Genişletilebilir mimari

4.2 Sistem Tasarımı
Bu proje aşağıdaki katmanlardan oluşur:
LLM Katmanı (TinyLlama – 1.1B)
•	Log verilerini doğal dil bağlamında analiz eder.
•	Tehdit türü, seviye ve çözüm önerisi üretir.
Prompt Engineering + Chain-of-Thought
•	Modelin düşünme adımlarını zorunlu hale getirir.
•	Daha tutarlı ve açıklanabilir çıktılar sağlar.
GPU + 4-bit Quantization
•	Google Colab T4 GPU üzerinde verimli çalışmayı sağlar.
•	Bellek kullanımını ciddi biçimde düşürür.
İstatistiksel Post-Processing
•	LLM çıktıları basit mantık kurallarıyla sınıflandırılır.
•	Görselleştirme için veri üretilir.
Gradio Web Arayüzü
•	Kullanıcı dostu, gerçek zamanlı demo ortamı sunmaktadır.

4.3 Kullanılan Metotlar ve Algoritmalar
LLM Tabanlı Yöntemler:
•	Önceden eğitilmiş Large Language Model (LLM) kullanımı
•	Olay metinlerinden bağlam ve tehdit çıkarımı
•	Prompt engineering ile tehdit odaklı çıktı üretimi

Makine Öğrenmesi Yöntemleri:
•	Çok sınıflı sınıflandırma (Multi-class Classification)
•	LLM çıktılarının sayısallaştırılması
•	Precision, Recall, F1-score ile performans değerlendirme
Bu yapı, LLM’in doğrudan karar verici değil, akıllı bir özellik çıkarıcı (feature extractor) olarak konumlandırılmasını sağlamıştır.


4.4 Donanım Tercihi: 
Cyber-LLM projesi, Google Colab tarafından sağlanan ücretsiz ve kurulum gerektirmeyen bulut altyapısı üzerinden NVIDIA T4 GPU’nun yüksek paralellik sunan CUDA çekirdeklerini ve Tensor Core desteğini kullanarak gerçekleştirilmiştir. 
Vektör ve matris tabanlı işlemlerde CPU’ya oranla ciddi bir hızlanma sağlayan bu donanım tercihi, model eğitimi ve değerlendirme süreçlerini optimize ederken aynı zamanda orta ölçekli siber güvenlik projeleri için ideal bir enerji-verimlilik dengesi sunmaktadır.
Platformun sunduğu donanımdan bağımsız geliştirme imkânı, akademik ve profesyonel projelerde hızlı prototipleme yapılmasına olanak tanıyarak kurulum zahmetini ortadan kaldırmaktadır. Ayrıca bu altyapı, mevcut sistemi gelecekte LSTM veya Transformer gibi daha karmaşık derin öğrenme mimarilerine taşımak için gereken ölçeklenebilir temeli şimdiden hazır hale getirmektedir.


4.5 Gerçekleştirim (Implementation)
4-bit Kuantizasyon yöntemi  :
BitsAndBytesConfig: Bu yapı, 4-bit Kuantizasyon (Sıkıştırma) yöntemini uygular.
	“load_in_4bit=True” : Modelin ağırlıklarını 32-bit'ten 4-bit'e düşürerek VRAM kullanımını %75-80 oranında azaltır.
	“bnb_4bit_quant_type=’nf4’ “: "NormalFloat4" siber güvenlik gibi teknik metinlerde, standart 4-bit'e göre daha yüksek doğruluk (accuracy) sağlayan özel bir veri tipidir.
	“bnb_4bit_compute_dtype=torch.float16” : Hesaplamalar sırasında hızı artırmak için 16-bit hassasiyet (yarım hassasiyet) kullanır.

Tokenizasyon ve Model Yükleme
•	AutoTokenizer: İnsan dilindeki log metnini, modelin matematiksel olarak anlayabileceği sayısal parçalara böler.
•	device_map="auto": Modelin katmanlarını ekran kartı (GPU) ve işlemci (CPU) arasında en verimli şekilde paylaştırarak "Out of Memory" (bellek yetersiz) hatalarını önler.

"Chain of Thought" (Düşünce Zinciri) Prompt Mühendisliği
Kodun içindeki prompt yapısı, modelin sadece tahmin yürütmesini değil, mantıklı bir analiz sürecini takip etmesini sağlar:
•	Yöntem: Modele "Analist Modu: AKTİF" diyerek bir rol tanımlanır ve analiz adımları (1. Tehdit Seviyesi, 2. Saldırı Tipi, 3. Çözüm) madde madde istenir.
•	Sonuç: Bu yöntem, modelin rastgele metin üretmek yerine siber güvenlik standartlarında (Reasoning) bir rapor oluşturmasını sağlar.

Analiz Mantığı ve Parametreler (model.generate)
•	max_new_tokens=200: Üretilecek yanıtın uzunluğunu sınırlar, böylece gereksiz kelime kalabalığını önler ve yanıt hızını artırır.
•	temperature=0.3: Yanıtın ne kadar "yaratıcı" veya "kesin" olacağını belirler. 0.3 gibi düşük bir değer, siber güvenlikte ihtiyaç duyulan ciddi ve tutarlı yanıtların üretilmesini sağlar.


İstatistiksel Görselleştirme ve Veri İşleme
Modelin ürettiği metinsel rapor, kod tarafından arka planda analiz edilir:
•	Anahtar Kelime Tespiti: if "kritik" in response.lower() yapısı, LLM'in ürettiği metni tarayarak saldırı tipini belirler ve stats sözlüğünü günceller.
•	create_stat_plot: Matplotlib kullanarak bu istatistikleri görsel bir bar grafiğine dönüştürür. io.BytesIO() kullanımı sayesinde grafik diskte yer kaplamadan doğrudan bellek üzerinden arayüze aktarılır.

Gradio Web Arayüz Katmanı
Kullanıcının projeyi bir "ürün" gibi deneyimlemesini sağlayan katmandır:
•	gr.Blocks: Esnek bir yerleşim düzeni kurar (sol tarafta giriş kutusu, sağ tarafta sonuç raporu ve grafik).
•	theme=gr.themes.Soft(): Arayüze profesyonel bir siber güvenlik paneli görünümü kazandırır.
•	demo.launch(debug=True): Uygulamayı canlıya alır ve geliştiriciye hataları anlık olarak görme imkanı tanır.
Kod yapısı; ileride REST API, web arayüzü veya gerçek zamanlı sistemlerle entegre edilebilecek modülerlikte tasarlanmıştır.

4.6 Test ve Doğrulama
Model performansı aşağıdaki metriklerle değerlendirilmiştir.
Genel Model Performansı
•	Accuracy (Doğruluk): %66.67
•	Precision (Kesinlik): %66.67
•	Recall (Duyarlılık): %72.22
•	F1 Score: %65.56



Detaylı Sınıflandırma Raporu
Sınıf	Precision	Recall	F1-Score	Support
Düşük	0.50	1.00	0.67	1
Kritik	1.00	0.67	0.80	3
Orta	0.50	0.50	0.50	2
Karışıklık Matrisi (Confusion Matrix)
[[2 1 0]
 [0 1 1]
 [0 0 1]]

Model Performans Özeti:
•  Güçlü Yanlar :Model, "Kritik" olarak işaretlediği tüm vakalarda %100 doğruluk (Precision) payıyla çalışarak yanlış alarm üretmediğini kanıtlamıştır. Siber güvenlikte en önemli başarı olan "en riskli saldırıyı hatasız tanımlama" konusunda model oldukça güvenilirdir.
•  Zayıf Yanlar ve Karışıklıklar: Modelin en çok "Orta" seviyeli tehditlerde zorlandığı, bunları bazen "Düşük" bazen de "Kritik" sınıflarıyla karıştırdığı görülmektedir. Karışıklık matrisine göre, 3 kritik vakadan 1 tanesinin "Orta" olarak tahmin edilmesi, sistemin saldırı şiddetini bazen olduğundan düşük algıladığını göstermektedir.
•  Gelişim İhtiyacı: Toplam 6 örnek (support) üzerinden yapılan bu test, istatistiksel olarak daha geniş bir veri setiyle desteklenmelidir. Orta seviye teşhislerdeki belirsizliği gidermek için modelin siber güvenlik jargonuna yönelik Fine-Tuning ile eğitilmesi öncelikli gelişim adımıdır.

5. Proje Çıktıları
•	Çalışan çok sınıflı tehdit seviyesi sınıflandırma modeli
•	Performans metrikleriyle desteklenmiş analiz çıktıları
•	Karar destek amaçlı kullanılabilir sonuçlar
•	Akademik ve endüstriyel raporlamaya uygun yapı

6. Nihai Değerlendirme ve Gelecek Çalışmalar (Future Works)
Bu proje, LLM merkezli olacak şekilde geliştirilmeye açıktır. Planlanan ileri seviye özellikler şunlardır:
Donanım Ölçekleme: NVIDIA H100 veya A100 gibi yüksek kapasiteli GPU'lar kullanılarak Llama 3 (70B) modellerine geçiş yapılmasıyla doğruluk oranının arttırmak mümkündür.
Fine-Tuning: Modele özel siber güvenlik veri setleriyle "İnce Ayar" yapılarak teknik jargon başarısı artırılacaktır.
RAG Entegrasyonu: Bu projeye güncel CVE (açıklık) veritabanları modele bağlanarak modelin dünya üzerindeki en yeni saldırılardan anında haberdar olması sağlanacaktır.
Groq API Entegrasyonu: Bu entegrasyonu sağlayarak kısıtlı bilgisayar donanımı yerine Groq bulutundaki devasa GPU'larda çok daha hızlı bir şekilde çalıştırılabilir.

7. Sonuç
Bu çalışma, sınırlı veri koşullarında dahi siber tehdit seviyelerinin makine öğrenmesi ile sınıflandırılabileceğini göstermiştir. Model, özellikle kritik tehditlerin tespiti açısından tatmin edici sonuçlar üretmiştir. Geliştirilebilir mimarisi sayesinde proje, ileri seviye siber güvenlik karar destek sistemlerine temel oluşturabilecek niteliktedir.

